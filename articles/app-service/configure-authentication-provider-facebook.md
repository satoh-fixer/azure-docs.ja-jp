---
title: Facebook 認証の構成 - Azure App Service
description: App Services アプリケーションに Facebook 認証を構成する方法について説明します。
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: c9767ff1e6f0b31270f37842cf99d71cab561505
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033855"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>App Service アプリケーションを Facebook ログインを使用するように構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Facebook を使用するように Azure App Service を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには、 [facebook.com]にアクセスしてください。

## <a name="register"> </a>Facebook にアプリケーションを登録する
1. [Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。
3. (オプション) 開発者向けの Facebook アカウントを持っていない場合は、 **[Get Started]\(作業開始\)** をクリックして登録の手順に従います。
4. **[マイアプリ]**  >  **[新しいアプリの追加]** をクリックします。
5. **[Display Name] (表示名)** に、アプリの一意の名前を入力します。 **連絡先の電子メール**を指定してから、 **[Create App ID] (アプリ ID の作成)** をクリックし、セキュリティ チェックを完了します。 これで、新しい Facebook アプリケーションの開発者向けダッシュボードに移動します。
6. **[ダッシュ ボード]**  >  **[Facebook ログイン]**  >  **[設定]**  >  **[Web]** をクリックします。
1. 左側のナビゲーションで、 **[Facebook ログイン]** をクリックして **[設定]** をクリックします。
1. **[有効な OAuth リダイレクト URI]** に、`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` と入力し、 *\<app-name>* を Azure App Service アプリの名前に置き換えます。 **[変更を保存]** をクリックします。
8. 左側のナビゲーションで、 **[Settings] (設定)**  >  **[Basic] (基本)** をクリックします。 **[App Secret]** フィールドで、 **[表示]** をクリックします。 **[アプリ ID]** と **[App Secret]** の値をコピーします。 後で App Service アプリを Azure 内で構成するときにこれらの値を使用します。
   
   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   > 
   > 
9. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。 この時点では、管理者のみがこのアプリケーションにサインインできます。 他の Facebook アカウントを認証するには、 **[アプリのレビュー]** をクリックし、 **[\<<アプリ名> をパブリックにする]** を有効にして、Facebook 認証を使用した汎用パブリック アクセスを有効にします。

## <a name="secrets"> </a>Facebook の情報をアプリケーションに追加する
1. [Azure portal] にサインインし、ご自身の App Service アプリに移動します。 **[設定]**  >  **[認証/承認]** の順にクリックし、 **[App Service 認証]** が **[オン]** になっていることを確認します。
2. **[Facebook]** をクリックし、前の手順で取得したアプリ ID とアプリケーション シークレットの値を貼り付けます。必要に応じて、アプリケーションで必要なスコープを有効にし、 **[OK]** をクリックします。
   
    ![][0]
   
    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
3. (省略可能) Facebook によって認証されたユーザーしかサイトにアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Facebook]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Facebook にリダイレクトされます。
 
> [!CAUTION]
> この方法でのアクセスの制限は、アプリへのすべての呼び出しに適用されますが、これは、多くのシングルページ アプリケーションのように、一般公開されているホームページを必要とするアプリには適切でない場合があります。 このようなアプリケーションの場合は、[ここ](overview-authentication-authorization.md#authentication-flow)で説明しているように、アプリが手動で自身のログインを開始する、 **[匿名要求を許可する (操作不要)]** が望ましいと考えられます。

4. 認証の構成が終了したら、 **[保存]** をクリックします。

これで、アプリケーションで認証に Facebook を使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
