---
title: ベスト プラクティス - Azure App Service
description: Azure App Service のベスト プラクティスとトラブルシューティングについて説明します。
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: b4c7524415865f7db5d4514c14f8e45030620330
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636672"
---
# <a name="best-practices-for-azure-app-service"></a>Azure App Service のベスト プラクティス
この記事では、 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)を使用するためのベスト プラクティスを概説します。 

## <a name="colocation"></a>コロケーション
Web アプリやデータベースなど、ソリューションを構成する Azure リソースを別々のリージョンに配置すると、次のような影響が考えられます。

* リソース間の通信における待機時間の増大
* [Azure の料金](https://azure.microsoft.com/pricing/details/data-transfers)に関するページに記載されている、リージョン間の送信データ転送にかかる料金の請求

Web アプリ、データベース、コンテンツやデータを保持するためのストレージ アカウントなど、ソリューションを構成する Azure リソースは、同じリージョンに配置すること (コロケーション) をお勧めします。 リソースを作成する場合は、それらを必ず同じ Azure リージョン内に配置してください。ただし、特定のビジネスまたは設計上の理由で難しい場合はその限りでありません。 Premium App Service プラン アプリで現在利用できる [App Service の複製機能](app-service-web-app-cloning.md)を使うと、App Service アプリをデータベースと同じリージョンに移動することができます。   

## <a name="memoryresources"></a>アプリが予想よりも多くのメモリを消費している場合
監視またはサービスの推奨事項に示されている情報を基に、アプリが予想よりも多くのメモリを消費していることに気付いた場合は、[App Service の自動修復機能](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)を検討してください。 自動修復機能のオプションの 1 つでは、メモリのしきい値に基づいて独自の処置を行います。 処置の内容は、電子メールによる通知から、メモリ ダンプによる調査、さらに worker プロセスのリサイクルによるその場での軽減処理まで広範囲に及びます。 自動修復は、 [App Service サポート サイトの拡張機能](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)に関するこのブログ投稿の説明に沿って、Web.config またはわかりやすいユーザー インターフェイスを使用して構成することができます。   

## <a name="CPUresources"></a>アプリが予想よりも多くの CPU リソースを消費している場合
監視またはサービスの推奨事項の説明を基に、アプリが予想よりも多くの CPU リソースを消費していること、または CPU スパイクが繰り返し発生していることに気が付いた場合は、App Service プランのスケール アップまたはスケール アウトを検討してください。 アプリケーションがステートフルである場合は、スケール アップが唯一のオプションとなります。一方、アプリケーションがステートレスである場合、スケール アウトにより、柔軟性と拡張性を高めることができます。 

"ステートフル" アプリケーション対 "ステートレス" アプリケーションについて詳しくは、次のビデオをご覧ください:「[Planning a Scalable End-to-End Multi-Tier Application on Azure App Service (Azure App Service でスケーラブルなエンド ツー エンドの多層アプリケーションを計画する)](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)」。 App Service のスケール オプションおよび自動スケール オプションについて詳しくは、[Azure App Service での Web アプリのスケーリング](manage-scale-up.md)に関する記事をご覧ください。  

## <a name="socketresources"></a>ソケット リソースを使い果たした場合
送信 TCP 接続を使い果たしてしまう理由としては、一般的には、使っているライブラリが TCP 接続を再利用するように実装されていないことや、HTTP - Keep-Alive などの上位レベルのプロトコルが使われていないことなどが挙げられます。 App Service プランでアプリによって参照される各ライブラリのドキュメントを再確認し、送信接続が効率的に再利用されるようにコード内でライブラリが構成またはアクセスされるようにしてください。 また、ライブラリのドキュメントのガイダンスに従って適切に作成しリリースするか、接続のリークを防ぐためにクリーンアップを行ってください。 このようなクライアント ライブラリの調査中は、複数のインスタンスにスケール アウトすることで影響を軽減することができます。

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js と送信 http 要求
Node.js と多数の送信 http 要求を処理する場合は、HTTP - Keep-Alive の処理が重要です。 これは、[agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` パッケージを使用して、コード内で簡単に処理することができます。

ハンドラーで何もしない場合でも、`http` 応答を常に処理します。 応答を適切に処理しなかった場合、使用できるソケットがなくなるため、アプリケーションは最終的にスタックします。

`http` または `https` パッケージの処理例を次に示します。

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

複数のコアを備えた Linux コンピューターで App Service で実行している場合の別のベスト プラクティスは、PM2 を使用して複数の Node.js プロセスを開始して、アプリケーションを実行することです。 これは、コンテナーにスタートアップ コマンドを指定することによって実行できます。

4 つのインスタンスを開始する例を次に示します。

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>アプリのバックアップが失敗するようになった場合
アプリのバックアップが失敗する場合は、最も一般的な理由として、無効なストレージ設定と無効なデータベース構成の 2 つが考えられます。 通常、このようなエラーはストレージまたはデータベースのリソースに変更が加えられた場合か、これらのリソースへのアクセス方法が変更された場合 (バックアップ設定で選択したデータベースに関して資格情報が更新された場合など) に発生します。 バックアップは、通常はスケジュールに応じて実行され、ストレージへのアクセス (バックアップしたファイルの出力用) とデータベースへのアクセス (バックアップに含まれる内容のコピーと読み取り用) が必要になります。 これらのリソースのいずれかにアクセスできないと、バックアップは常に失敗します。 

バックアップ エラーが発生した場合は、最新のバックアップ結果を確認してどちらの種類のエラーが発生しているのかを把握します。 ストレージへのアクセス エラーが発生している場合は、バックアップ構成で使用しているストレージ設定を確認し、更新します。 データベースへのアクセス エラーが発生している場合は、アプリ設定に含まれる接続文字列を確認し、更新してから、必要なデータベースが正しく含まれるようにバックアップ構成を更新します。 アプリのバックアップについて詳しくは、[Azure App Service での Web アプリのバックアップ](manage-backup.md)に関する記事をご覧ください。

## <a name="nodejs"></a>新しい Node.js アプリを Azure App Service にデプロイする場合
Node.js アプリ向けの Azure App Service の既定の構成は、最も一般的なアプリのニーズに最も合うように設計されています。 Node.js アプリの構成で、パーソナライズされたチューニングの利点を活用してパフォーマンスの向上または CPU/メモリ/ネットワーク リソースのリソース使用量の最適化を行う場合には、「[Azure Web Apps でのノード アプリケーションのベスト プラクティスとトラブルシューティング ガイド](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)」をご覧ください。 この記事では、Node.js アプリでの構成に必要となる可能性のある iisnode 設定について、また、アプリが直面する可能性のあるさまざまなシナリオや問題を説明し、これらの問題に対処する方法が示されています。

