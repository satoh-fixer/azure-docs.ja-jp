---
title: Azure VMware Solution by CloudSimple - CloudSimple のメンテナンスと更新
description: 計画したメンテナンスと更新のための CloudSimple サービス プロセスについて説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5d6eeecbecc89995c25e687cc6808ed3b0c5dc5c
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816207"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple のメンテナンスと更新

プライベート クラウド環境は、単一障害点がないように設計されます。

* ESXi クラスターは vSphere の高可用性で構成されます。 クラスターのサイズは、回復性のために予備のノードが少なくとも 1 つ使用するように設定されます。
* vSAN によって冗長プライマリ ストレージが与えられます。これには、単一障害に対する保護を提供するため、ノードが少なくとも 3 つ必要になります。 vSAN は、大規模なクラスターの回復性を上げるように構成できます。
* vCenter、PSC、NSX Manager の VM は、ストレージの不具合から守られるよう、RAID-10 ストレージ ポリシーで構成されます。 VM は vSphere HA により、ノード/ネットワークの不具合から守られます。
* ESXi ホストには、冗長性のあるファンと NIC があります。
* TOR スイッチとスパイン スイッチは、回復性を確保するよう、HA ペアで構成されます。

CloudSimple は次の VM のアップタイムと可用性を継続的に監視し、可用性 SLA を提供します。

* ESXi ホスト
* vCenter
* PSC
* NSX Manager

CloudSimple は次の不具合も継続的に監視します。

* ハード ディスク
* 物理 NIC ポート
* サーバー
* ファン
* 累乗
* スイッチ
* スイッチ ポート

ディスクまたはノードが故障した場合、影響が出た VMware クラスターに新しいノードが自動的に追加され、正常な状態にすぐに戻されます。

CloudSimple は、プライベート クラウドにある次の VMware 要素をバックアップし、保守管理し、更新します。

* ESXi
* vCenter プラットフォーム サービス
* コントローラー
* vSAN
* NSX

## <a name="back-up-and-restore"></a>バックアップと復元

CloudSimple バックアップの内容:

* vCenter、PSC、DVS ルールの夜間増分バックアップ。
* アプリケーション層のコンポーネントをバックアップするための vCenter ネイティブ API の使用。
* VMware 管理ソフトウェアの更新またはアップグレード前の自動バックアップ。
* TLS1.2 暗号化チャンネル経由で Azure にデータを転送する前の、vCenter によるソースでのデータ暗号化。 データは Azure BLOB に保存され、そこでリージョン間で複製されます。

[サポート要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開くことで復元を要求できます。

## <a name="maintenance"></a>メンテナンス

CloudSimple では、数種類の計画メンテナンスが行われます。

### <a name="backendinternal-maintenance"></a>バックエンド/内部のメンテナンス

このメンテナンスには通常、物理資産の再構成またはソフトウェア パッチのインストールが含まれます。 修理中の資産の通常利用には影響を与えません。 各物理ラックに冗長 NIC が入り、通常のネットワーク トラフィックとプライベート クラウドの運用には影響が出ません。 メンテナンス期間中、冗長帯域幅を全部使用することを組織が予想している場合にのみ、パフォーマンスに影響が出るかもしれません。

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple ポータル メンテナンス

CloudSimple のコントロール レベルまたはインフラストラクチャが更新されるとき、一部の限られたサービス ダウンタイムが要求されます。 現在のところ、メンテナンス間隔は月 1 回の頻度にできます。 頻度は時間の経過と共に下がると予想されます。 CloudSimple はポータル メンテナンスの通知を行い、間隔を可能な限り短くします。 ポータル メンテナンスの期間中、次のサービスは影響なしで作動し続けます。

* VMware 管理プレーンとアプリケーション
* vCenter アクセス
* すべてのネットワークとストレージ
* すべての Azure トラフィック

### <a name="vmware-infrastructure-maintenance"></a>VMware インフラストラクチャ メンテナンス

VMware インフラストラクチャの構成変更が必要になることがあります。  現在のところ、この間隔は 1-2 か月に 1 回ですが、頻度は時間の経過と共に下がると予想されます。 この種類のメンテナンスは通常、CloudSimple サービスの通常利用を中断することなく実行できます。 VMware メンテナンスの期間中、次のサービスは影響なしで作動し続けます。

* VMware 管理プレーンとアプリケーション
* vCenter アクセス
* すべてのネットワークとストレージ
* すべての Azure トラフィック

## <a name="updates-and-upgrades"></a>更新とアップグレード

CloudSimple は、プライベート クラウド内の VMware ソフトウェア (ESXi、vCenter、PSC、NSX) のライフサイクル管理を担います。

ソフトウェア更新の内容:

* **パッチ**。 VMware によってリリースされたセキュリティ パッチまたはバグ修正プログラム。
* **[Updates]** (更新)。 VMware スタック コンポーネントのマイナー バージョン変更。
* **アップグレード**。 VMware スタック コンポーネントのメジャー バージョン変更。

CloudSimple は、VMware から重要なセキュリティ パッチが利用できるようになると、直後にそれをテストします。 SLA に基づき、CloudSimple は 1 週間以内にプライベート クラウド環境にセキュリティ パッチをロールアウトします。

CloudSimple は、VMware ソフトウェア コンポーネントに四半期ごとのメンテナンス更新を提供します。 新しいメジャー バージョンの VMware ソフトウェアが利用できるようになると、CloudSimple は顧客と連携し、アップグレードに適したメンテナンス期間を調整します。

## <a name="next-steps"></a>次の手順

[Veeam を使用してワークロード VM をバックアップする](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)。