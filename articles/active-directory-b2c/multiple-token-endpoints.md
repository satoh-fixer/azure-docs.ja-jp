---
title: OWIN ベースの Web アプリケーションで複数のトークン発行者をサポートする - Azure Active Directory B2C
description: .NET Web アプリケーションで複数のドメインによって発行されたトークンをサポートできるようにする方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 31ab19b8b3adbef1f0ea573af13b98750d278db8
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716717"
---
# <a name="support-multiple-token-issuers-in-an-owin-based-web-application"></a>OWIN ベースの Web アプリケーションで複数のトークン発行者をサポートする

この記事では、[Open Web Interface for .NET (OWIN)](http://owin.org/) が実装されている Web アプリと API で複数のトークン発行者のサポートを有効にする方法について説明します。 複数のトークン エンドポイントのサポートは、Azure Active Directory (Azure AD) B2C アプリケーションを *login.microsoftonline.com* から *b2clogin.com* に移行する場合に便利です。

以下のセクションでは、[Microsoft OWIN][katana] ミドルウェア コンポーネント (Katana) を使用する Web アプリケーションおよび対応する Web API で複数の発行者を有効にする方法の例について説明します。 コード例は Microsoft OWIN ミドルウェアに固有のものですが、一般的な手法は他の OWIN ライブラリにも適用できます。

> [!NOTE]
> この記事は、`login.microsoftonline.com` を参照するアプリケーションを現在デプロイしていて、推奨される `b2clogin.com` エンドポイントへの移行を希望している Azure AD B2C のお客様を対象としています。 新しいアプリケーションを設定する場合は、指示に従って [b2clogin.com](b2clogin.md) を使用してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を続ける前に、次の Azure AD B2C リソースを用意しておく必要があります。

* お使いのテナントに作成された[ユーザー フロー](tutorial-create-user-flows.md)または[カスタム ポリシー](active-directory-b2c-get-started-custom.md)

## <a name="get-token-issuer-endpoints"></a>トークン発行者のエンドポイントを取得する

最初に、アプリケーションでサポートする各発行者に対するトークン発行者エンドポイントの URI を取得する必要があります。 Azure AD B2C テナントによってサポートされている *b2clogin.com* エンドポイントと *login.microsoftonline.com* エンドポイントを取得するには、Azure portal で次の手順を使用します。

まず、既存のユーザー フローのいずれかを選択します。

1. [Azure portal](https://portal.azure.com) で、自分の Azure AD B2C テナントに移動します。
1. **[ポリシー]** で **[ユーザー フロー (ポリシー)]** を選択します
1. 既存のポリシー (*B2C_1_signupsignin1* など) を選択し、 **[ユーザー フローを実行します]** を選択します
1. ページの上部近くにある **[ユーザー フローを実行します]** の見出しの下で、ハイパーリンクを選択して、そのユーザー フローの OpenID Connect 検出エンドポイントに移動します。

    ![Azure portal の今すぐ実行ページの既知の URI ハイパーリンク](media/multi-token-endpoints/portal-01-policy-link.png)

1. ブラウザーに表示されるページで、`issuer` の値を記録します。次に例を示します。

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. **[ドメインの選択]** ドロップダウンを使って他のドメインを選択し、前の 2 つのステップを再度実行して、その `issuer` の値を記録します。

次のような 2 つの URI が記録されるはずです。

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>カスタム ポリシー

ユーザー フローではなくカスタム ポリシーを使っている場合は、同様のプロセスを使って発行者の URI を取得できます。

1. Azure AD B2C テナントに移動します
1. **[Identity Experience Framework]** を選択します
1. 証明書利用者ポリシーの 1 つを選択します (たとえば *B2C_1A_signup_signin*)
1. **[ドメインの選択]** ドロップダウンを使って、ドメインを選択します (*yourtenant.b2clogin.com* など)
1. **[OpenID Connect 検出エンドポイント]** の下に表示されるハイパーリンクを選択します
1. `issuer` の値を記録します
1. 他のドメインに対してステップ 4 から 6 を実行します (*login.microsoftonline.com* など)

## <a name="get-the-sample-code"></a>サンプル コードの取得

両方のトークン エンドポイント URI が手に入ったので、コードを更新して、両方のエンドポイントが有効な発行者であることを指定する必要があります。 例の手順を確認するには、サンプル アプリケーションをダウンロードまたは複製し、次にサンプルを更新して、両方のエンドポイントを有効な発行者としてサポートします。

アーカイブ [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive] をダウンロードします

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Web API で複数の発行者を有効にする

このセクションでは、コードを更新し、両方のトークン発行者エンドポイントが有効であることを指定します。

1. Visual Studio で **B2C-WebAPI-DotNet.sln** ソリューションを開きます
1. **TaskService** プロジェクトの *TaskService\\App_Start\\**Startup.Auth.cs*** ファイルをエディターで開きます。
1. 次の `using` ディレクティブをファイルの先頭に追加します。

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] プロパティを [`TokenValidationParameters`][tokenvalidationparameters] の定義に追加し、前のセクションで記録した両方の URI を指定します。

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` は MSAL.NET によって提供され、*Startup.Auth.cs* のコードの次のセクションで OWIN ミドルウェアによって使用されます。 複数の有効な発行者を指定すると、OWIN アプリケーションのパイプラインでは、両方のトークン エンドポイントが有効な発行者であることが認識されます。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

前に説明したように、他の OWIN ライブラリでは、通常、複数の発行者をサポートするために同様の機能が提供されています。 すべてのライブラリの例を提供することはこの記事の範囲外ですが、ほとんどのライブラリで同様の手法を使用できます。

## <a name="switch-endpoints-in-web-app"></a>Web アプリでエンドポイントを切り替える

Web API で両方の URI がサポートされるようになったので、今度は b2clogin.com エンドポイントからトークンを取得するように、Web アプリケーションを更新する必要があります。

たとえば、**TaskWebApp** プロジェクトの *TaskWebApp\\**Web.config*** ファイルで `ida:AadInstance` の値を変更することにより、新しいエンドポイントを使うようにサンプル Web アプリケーションを構成できます。

TaskWebApp の *Web.config* の `ida:AadInstance` の値を、`login.microsoftonline.com` ではなく `{your-b2c-tenant-name}.b2clogin.com` を参照するように変更します。

変更前:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

変更後 (`{your-b2c-tenant}` は実際の B2C テナントの名前に置き換えます):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Web アプリの実行中にエンドポイントの文字列が作成されるとき、b2clogin.com ベースのエンドポイントを使ってトークンが要求されます。

## <a name="next-steps"></a>次の手順

この記事では、複数の発行者エンドポイントからトークンを受け入れるために、Microsoft OWIN ミドルウェア (Katana) を実装する Web API を構成する方法について説明しました。 お気付きかもしれませんが、TaskService プロジェクトと TaskWebApp プロジェクトの *Web.Config* ファイルには、独自のテナントに対してプロジェクトをビルドして実行するときに変更する必要のある文字列が、他にもいくつかあります。 それらの動作を確認する場合はプロジェクトを適切に変更してかまいませんが、その完全なチュートリアルはこの記事の範囲外です。

Azure AD B2C によって生成されるさまざまな種類のセキュリティ トークンについて詳しくは、「[Azure Active Directory B2C のトークンの概要](active-directory-b2c-reference-tokens.md)」をご覧ください。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
