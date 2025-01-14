---
title: ベースライン ポリシー エンドユーザーの保護 (プレビュー) - Azure Active Directory
description: ユーザーの多要素認証を要求する条件付きアクセス ポリシー
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: afcd9c9d3191caeabe182f499b5fd80cd8e1d8dd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608140"
---
# <a name="baseline-policy-end-user-protection-preview"></a>ベースライン ポリシー:エンド ユーザーの保護 (プレビュー)

多要素認証 (MFA) による保護が必要なアカウントは管理者アカウントだけであると考えがちです。 管理者は、機密情報への広範なアクセス権を持ち、サブスクリプション全体の設定に変更を加えることができます。 しかし、攻撃者はエンド ユーザーをターゲットにする傾向があります。 このような攻撃者は、アクセス権を取得した後、元々のアカウント所有者に代わって機密性の高い情報へのアクセスを要求したり、ディレクトリ全体をダウンロードして組織全体にフィッシング攻撃を仕掛けたりする可能性があります。 すべてのユーザーを対象にした保護を向上するための一般的な方法の 1 つは、多要素認証 (MFA) などのより強力な形式のアカウント検証を要求することです。

セキュリティと使いやすさの適度なバランスを実現するために、サインインのたびにユーザーに要求を出すべきではありません。 同じ場所の同じデバイスからのサインインなど、通常のユーザー動作を反映した認証要求はセキュリティ侵害の可能性が低いと言えます。 リスクが高いと見なされ、攻撃者の特徴を示すサインインのみに MFA のチャレンジを要求する必要があります。

エンド ユーザーの保護は、すべての管理者の役割を含む、ディレクトリ内のすべてのユーザーを保護するリスク ベースの MFA [ベースライン ポリシー](concept-baseline-protection.md)です。 このポリシーを有効にすると、すべてのユーザーは Authenticator アプリを使用して MFA に登録する必要があります。 ユーザーは MFA 登録プロンプトを 14 日間無視できますが、その後は MFA に登録するまでサインインからブロックされます。 MFA に登録すると、ユーザーはリスクの高いサインインの試行中にのみ、MFA を求められます。 侵害されたユーザー アカウントは、パスワードがリセットされてリスク イベントが解消されるまでブロックされます。

> [!NOTE]
> このポリシーは、ゲスト アカウントを含むすべてのユーザーに適用され、すべてのアプリケーションにログインするときに評価されます。

## <a name="recovering-compromised-accounts"></a>侵害されたアカウントの復旧

お客様を保護するため、Microsoft の漏洩した資格情報サービスでは、一般に公開されているユーザー名とパスワードの組み合わせを検索します。 それらが当社のユーザーのいずれかに一致する場合、そのアカウントをただちに保護するように支援します。 漏洩したサインイン情報を持っていると識別されたユーザーは、侵害されたと確認されます。 これらのユーザーはパスワードがリセットされるまでサインインからブロックされます。

Azure AD Premium ライセンスが割り当てられているユーザーは、自分のディレクトリでパスワード リセットのセルフサービス (SSPR) が有効になっていれば、この機能を通じてアクセスを復旧できます。 Premium ライセンスのないブロックされたユーザーは、管理者に連絡して手動によるパスワードのリセットを実行してもらい、フラグが設定されたユーザーのリスク イベントを無視してもらう必要があります。

### <a name="steps-to-unblock-a-user"></a>ユーザーをブロック解除する手順

ユーザーのサインイン ログを調べることで、ユーザーがポリシーによってブロックされていることを確認します。

1. 管理者は、**Azure portal** にサインインし、 **[Azure Active Directory]**  >  **[ユーザー]** > に移動して、ユーザーの名前をクリックしてサインインに移動します。
1. ブロックされたユーザーのパスワードのリセットを開始するには、管理者は **[Azure Active Directory]**  >  **[リスクのフラグ付きユーザー]** に移動する必要があります。
1. アカウントがブロックされているユーザーをクリックし、そのユーザーの最近のサインイン アクティビティに関する情報を表示します。
1. [パスワードのリセット] をクリックし、次のログイン時に変更する必要がある一時的なパスワードを割り当てます。
1. [すべてのイベントを無視する] をクリックしてユーザーのリスク スコアをリセットします。

これでユーザーはサインインし、自分のパスワードをリセットし、アプリケーションにアクセスできるようになります。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

**エンド ユーザーの保護**ポリシーはディレクトリ内のすべてのユーザー適用されるため、スムーズな展開のためにいくつか考慮すべき項目があります。 たとえば、MFA を実行できない、または実行すべきではないユーザーやサービス プリンシパルを Azure AD で特定する、ご自身の組織で使用されている先進認証に対応していないアプリケーションやクライアントを特定する、といった考慮事項です。

### <a name="legacy-protocols"></a>レガシ プロトコル

レガシ認証プロトコル (IMAP、SMTP、POP3 など) は、認証要求を行うためにメール クライアントによって使用されます。 これらのプロトコルは、MFA をサポートしていません。  Microsoft によって確認されているアカウントのセキュリティ侵害のほとんどでは、攻撃者がレガシ プロトコルを攻撃することによって MFA のバイパスを試みています。 アカウントにログインするときに確実に MFA を要求し、攻撃者が MFA をバイパスできないようにするために、このポリシーは、レガシ プロトコルから管理者アカウントに対して行われるすべての認証要求をブロックします。

> [!WARNING]
> このポリシーを有効にする前に、ユーザーがレガシ認証プロトコルを使用していないことを確認してください。 詳細については、[条件付きアクセスを使用して Azure AD へのレガシ認証をブロックする](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)方法に関するページを参照してください。

## <a name="enable-the-baseline-policy"></a>ベースライン ポリシーを有効にする

ポリシー **[ベースライン ポリシー: エンド ユーザーの保護 (プレビュー)]** は事前に構成されており、Azure portal の [条件付きアクセス] ブレードに移動すると一番上に表示されます。

このポリシーを有効にしてご自分のユーザーを保護するには:

1.  **Azure portal**  にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[条件付きアクセス]** の順に移動します。
1. ポリシーの一覧で、 **[ベースライン ポリシー: エンド ユーザーの保護 (プレビュー)]** を選択します。
1. **[ポリシーを有効にする]** を **[ポリシーをすぐに使用する]** に設定します。
1.  **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

* [条件付きアクセス ベースライン保護ポリシー](concept-baseline-protection.md)
* [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/fundamentals/steps-secure-identity.md)
* [Azure Active Directory の条件付きアクセスとは](overview.md)
