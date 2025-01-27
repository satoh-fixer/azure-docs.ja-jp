---
title: Azure Active Directory Identity Protection (更新版) とは | Microsoft Docs
description: Azure Active Directory Identity Protection (更新版) とは
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2987f8fb116bfcbb1698335c3aca6f1fd8eb633e
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717282"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>Azure Active Directory Identity Protection (更新版) とは

組織の ID の保護を強化するために、Identity Protection のエクスペリエンスが更新されました。 この更新されたエクスペリエンスでは、以下のものが提供されます。

- ユーザーに最も関連するリスク (ユーザー リスクとサインイン リスク) に関する再設計された管理者エクスペリエンス
- フィルター処理、並べ替え、スマート ダウンロードをサポートする強力な調査エクスペリエンス
- 侵害される可能性が最も高いユーザーへの対応に優先順位を付けるのに役立つように改善されたユーザー リスク計算
- リスク データへのプログラムによるアクセスを有効にする新しい API サポート
- ユーザーをすぐに保護できるようにする簡素化された管理者フィードバック プロセス
- リスク評価の精度を向上させるための新しい教師あり機械学習

現在、セキュリティは組織の最大の懸念事項です。 ほとんどのセキュリティ侵害は、攻撃者がユーザーの ID を盗むことにより環境にアクセスできるようになると発生します。 長年にわたって、攻撃者はサード パーティのセキュリティ違反をより巧妙に利用し、高度なフィッシング攻撃を使っています。 低い特権のユーザー アカウントであってもアクセス権を得た攻撃者はすぐに、比較的簡単に、横方向に移動し、重要な企業リソースにアクセスしてしまいます。 

これらの脅威に対応するために、Azure AD Identity Protection でサポートされる内容は次のとおりです。 

- 侵害された ID が悪用されるのを事前に防止する 
- 疑わしいアクティビティが検出されたときに自動的にリスクを軽減する 
- 潜在的な脆弱性に対処するために危険なユーザーとサインインを調査する  
- ユーザーのリスクが指定されたしきい値に達したときにアラートを表示する 

Azure AD Identity Protection は Azure Active Directory Premium P2 の一機能です。これを使用して、ユーザーの ID が侵害されたときや、アカウント所有者以外の誰かがその ID を使用してサインインしようとしたときに自動的に対応するようにポリシーを構成することができます。 こうしたポリシーと、Azure AD によって提供される他の条件付きアクセス制御により、自動的にアクセスをブロックするか、パスワードのリセットや多要素認証の適用などの軽減アクションを開始することができます。 さらに、Identity Protection では、組織内のリスクと潜在的な侵害について、より深い洞察を得るための監視およびレポート機能が提供されます。 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>リスク イベント

Azure AD Identity Protection では、次のようなリスク イベントが検出されます。 

| リスク イベントの種類 | 説明 | 検出の種類 |
| --- | --- | --- |
| 特殊な移動 | ユーザーの最近のサインインに基づき特殊と判断された場所からのサインイン。 | オフライン |
| 匿名の IP アドレス | 匿名の IP アドレスからのサインイン (例:Tor Browser、Anonymizer VPN)。 | リアルタイム |
| 通常とは異なるサインイン プロパティ | 指定されたユーザーで最近観察されていないプロパティを使用したサインイン。 | リアルタイム |
| マルウェアにリンクした IP アドレス | マルウェアにリンクした IP アドレスからのサインイン | オフライン |
| 漏洩した資格情報 | このリスク イベントは、ユーザーの有効な資格情報が漏洩したことを示します | オフライン |

## <a name="types-of-risk"></a>リスクの種類 

Identity Protection は、次の 2 種類のリスクに基づきます。

- サインイン リスク
- ユーザー リスク

### <a name="sign-in-risk"></a>サインイン リスク

サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。

サインイン リスクの評価には次の 2 種類があります。 

- **サインイン リスク (リアルタイム)** - サインイン リスク (リアルタイム) は、サインインの処理中にトリガーするすべてのリアルタイム検出に基づいています。  
- **サインイン リスク (集計)** - サインイン リスク (集計) はサインイン リスクの合計です。 機械学習モデルでは、次のことを考慮して計算が行われます。
   - リアルタイム検出 (前述)
   - オフライン検出 (サインインが行われた後に発生) 
   - サインインの他のすべての機能

### <a name="user-risk"></a>ユーザー リスク

ユーザー リスクは、特定の ID が侵害されている可能性があることを表します。 

ユーザー リスクは、ユーザーに関連付けられているすべてのリスクを考慮して計算されます。

- すべての危険なサインイン
- サインインにリンクされていないすべてのリスク イベント
- 現在のユーザー リスク
- 指定日までにユーザーに対して行われたリスクの修復または無視アクション

## <a name="how-identity-protection-detects-risk"></a>Identity Protection でリスクを検出する方法  

Azure AD では機械学習を使用して、異常および疑わしいアクティビティを検出します。その場合、サインイン中にリアルタイムに検出されたシグナルと、ユーザーおよびそのサインイン アクティビティに関連する非リアルタイム シグナルの両方を使用します。 Identity Protection では、このデータを使用し、各ユーザーの全体的なユーザー リスク レベルを判別して、ユーザー認証のたびにリアルタイム サインイン リスクを計算します。 Identity Protection では、これらのリスク検出を自動的に行うことができます。その場合、Identity Protection のユーザー リスクとサインイン リスクのポリシーを構成します。  

Identity Protection でリスクを検出する方法を理解するために、ユーザーのリスクとサインイン リスクという 2 つの重要な概念について説明します。 サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。 サインイン リスクには、リアルタイムと合計という 2 種類があります。 リアルタイム サインイン リスクは、特定のサインイン (匿名 IP アドレスからのサインインなど) 試行時に検出されます。 合計サインイン リスクは、検出されたリアルタイム サインイン リスクと、ユーザーのサインインに関連付けられている以降の非リアルタイム サインイン リスク イベントを集計したものです。 ユーザー リスクは、悪意のあるアクターによって特定の ID が侵害された可能性全体を表します。 ユーザー リスクには、次のような、特定のユーザーのすべてのリスク アクティビティが含まれます。

- リアルタイムのサインイン リスク
- 以降のサインイン リスク
- 危険なユーザーの検出。   

 ![Flow](./media/overview-v2/01.png)

特定のサインインに対する Identity Protection のリスク検出および応答のベースライン フローは、上の図にまとめられています。  

## <a name="common-scenarios"></a>一般的なシナリオ 

Contoso のある従業員の例を見てみましょう。 

1. 従業員が Tor Browser から Exchange Online にサインインしようとします。 サインイン時に、Azure AD によってリアルタイム リスク イベントが検出されます。 
2. Azure AD は、この従業員が匿名の IP アドレスからサインインしていることを検出して、中レベルのサインイン リスクをトリガーします。 
3. 従業員は MFA の入力を求められます。Contoso の IT 管理者が Identity Protection サインイン リスクの条件付きアクセス ポリシーを構成していたためです。 ポリシーによって、中以上のサインイン リスクに対する MFA が求められます。 
4. 従業員は MFA のプロンプトに合格して、Exchange Online にアクセスします。ユーザー リスク レベルは変更されません。 

バックグラウンドで何が行われたのでしょうか。 Tor Browser からのサインイン試行により、匿名 IP アドレスに対して Azure AD でリアルタイム サインイン リスクがトリガーされました。 Azure AD は、要求を処理したときに、Identity Protection に構成されているサインイン リスク ポリシーを適用しました。従業員のサインイン リスク レベルがしきい値 (中) に一致したためです。 従業員は以前に MFA に登録していたため、MFA のチャレンジに応答して合格することができました。 MFA のチャレンジに正常に合格できたことで、正当な ID 所有者である可能性が高いことが Azure AD に通知され、ユーザー リスク レベルは上がりません。 

しかし、サインインしようとしている人物が、この従業員でなかった場合は、どうなるでしょうか。 

1. この従業員の資格情報を持つ悪意のあるアクターが、IP アドレスを隠そうとして Tor Browser からこの従業員の Exchange Online アカウントにサインインしようとします。 
2. Azure AD では、サインイン試行が匿名の IP アドレスからであることが検出され、リアルタイム サインイン リスクがトリガーされます。 
3. 悪意のあるアクターには MFA プロンプトが表示され、チャレンジの実行が求められます。これは、Contoso の IT 管理者によって、サインイン リスクが中以上の場合に MFA を求めるように Identity Protection サインイン リスクの条件付きアクセス ポリシーが構成されているためです。 
4. 悪意のあるアクターは MFA チャレンジに失敗し、この従業員の Exchange Online アカウントにアクセスすることはできません。 
5. 失敗した MFA プロンプトにより、リスク イベントがトリガーされて記録され、今後のサインインに対するユーザー リスクが上がります。 

悪意のあるアクターがこの従業員のアカウントにアクセスしようとしたので、次にこの従業員が次回サインインしようとしたときにどうなるかを見てみましょう。 

1. 従業員が Outlook から Exchange Online にサインインしようとします。 サインイン時に、Azure AD によってリアルタイム リスク イベントと、以前のユーザー リスクが検出されます。 
2. Azure AD ではリアルタイム サインイン リスクは検出されませんが、上記のシナリオの過去の危険なアクティビティによる高いユーザー リスクが検出されます。  
3. 従業員に、パスワードのリセットを求めるプロンプトが表示されます。これは、Contoso の IT 管理者が、高リスクのユーザーがログインしたときにはパスワードの変更を要求する Identity Protection ユーザー リスク ポリシーを構成していたからです。 
4. 従業員は SSPR と MFA に登録しているため、正常にパスワードをリセットします。 
5. パスワードをリセットすることで、従業員の資格情報はもう侵害されなくなり、ID は安全な状態に戻ります。 
6. 従業員の以前のリスク イベントは解決され、ユーザー リスク レベルは、資格情報の侵害の軽減に対応して、自動的にリセットされます。 

## <a name="how-do-i-configure-identity-protection"></a>Identity Protection を構成するにはどうすればよいか 

Identity Protection の使用を開始するには、まず、ユーザー リスク ポリシーとサインイン リスク ポリシーを構成します。 これらのポリシーが構成され、テスト グループに適用されたら、リスク イベントをシミュレーションし、ご利用の環境で Identity Protection がどのように応答するかを理解することができます。 以下のクイック スタート ガイドでは、前述のポリシーを設定し、環境内でテストする方法のチュートリアルを提供します。 

Identity Protection では、デプロイに関する管理アクティビティのバランスを取るための Azure AD での 3 つのロールがサポートされます。 

| Role | できること | できないこと |
| --- | --- | --- |
| 全体管理者 | Identity Protection へのフル アクセス、Identity Protection の配布準備 | |
| セキュリティ管理者 | Identity Protection へのフル アクセス | Identity Protection の配布準備、ユーザーのパスワードのリセット |
| セキュリティ閲覧者 | Identity Protection への読み取り専用アクセス | Identity Protection の配布準備、ユーザーの修復、ポリシーの構成、パスワードのリセット| 

詳しくは、「[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)」をご覧ください。
 
## <a name="licensing"></a>ライセンス

>[!NOTE]
> Identity Protection (更新版) のパブリック プレビュー期間中は、危険なユーザー レポートと危険なサインイン レポートにアクセスできるのは Azure AD Premium P2 のお客様のみとなります。

| 機能 | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- |
| ユーザー リスクのポリシー | はい | いいえ | いいえ |
| サインインのリスク ポリシー | はい | いいえ | いいえ |
| 危険なユーザー レポート | フル アクセス | 限定的な情報 | 限定的な情報 |
| リスクの高いサインイン レポート | フル アクセス | 限定的な情報 | 限定的な情報 |
| MFA 登録ポリシー | はい | いいえ | いいえ |

## <a name="next-steps"></a>次の手順 

Identity Protection の使用を開始する場合は、[サインイン リスク ポリシーの構成](quickstart-sign-in-risk-policy.md)に関するページを参照してください。 
