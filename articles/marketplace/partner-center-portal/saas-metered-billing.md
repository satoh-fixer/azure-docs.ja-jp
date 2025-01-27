---
title: マーケットプレース測定サービスを使用した従量制課金 | Azure Marketplace
description: このドキュメントは、柔軟な課金モデルを使用して SaaS オファーを公開する ISV 向けのガイドです。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 4b24805cd59d1eb9d28591749d5169486e54d506
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250118"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>マーケットプレース測定サービスを使用した従量制課金

マーケットプレース測定サービスを使用すると、標準以外の単位に応じて課金される商業マーケットプレース プログラム内のサービスとしてのソフトウェア (SaaS) オファーを作成できます。  このオファーを公開する前に、帯域幅、チケット、処理された電子メールなどの課金ディメンションを定義します。  お客様はこれらのディメンションの使用量に応じて支払いを行い、システムでは課金対象のイベントの発生時にそのイベントの Marketplace の測定サービス API を使用して Microsoft にイベントを通知します。  

## <a name="prerequisites-for-metered-billing"></a>従量制課金のための前提条件

従量制課金を使用するには、SaaS オファーが次の条件を満たすことが必要です。

* [SaaS オファーの作成](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)に関する記事に記載されているように、[Microsoft オファーを通じた販売](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft)のためのすべてのオファー要件を満たしていること。
* お客様がプロビジョニングしてオファーに接続するための [SaaS Fulfillment API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) と統合していること。  
* サービスに対するお客様への課金について、**定額**料金モデルが構成されていること。  ディメンションは、定額料金モデルに対するオプションの拡張機能です。 
* 課金対象のイベントを Microsoft に通知するために [Marketplace の測定サービス API](./marketplace-metering-service-apis.md) と統合されていること。

>[!Note]
>マーケットプレース測定サービスは定額課金モデルでのみ使用でき、ユーザーあたりの課金モデルには適用されません。

## <a name="how-metered-billing-fits-in-with-pricing"></a>価格モデルに従量制課金を適用する方法

オファーをその価格モデルとともに定義する場合、オファーの階層を理解することが重要です。

* 個々の SaaS オファーは、Microsoft を経由して、あるいは経由せずに販売するように構成されています。  この設定は、オファーの公開後に変更することはできません。
* Microsoft を経由して販売するように構成された各 SaaS オファーは、1 つまたは複数のプランを持つことができます。 ユーザーは SaaS オファーをサブスクライブしますが、オファーはプランのコンテキスト内で Microsoft を経由して購入されます。
* 各プランには、**定額料金**または**ユーザーあたり**の価格モデルが関連付けられています。 あるオファー内のすべてのプランは、同じ価格モデルに関連付けられている必要があります。 たとえば、プランのうちの 1 つが定額価格モデルで、別のプランがユーザーあたりの価格モデルであるといったオファーは構成できません。
* 定額課金モデル用に構成された各プランには、少なくとも 1 つの定期的な料金 (0 ドルの場合もあります) が含まれています。
    * 定期的な**月額**料金: ユーザーがプランを購入した場合に毎月定期的に前払いされる定額の月額料金。
    * 定期的な**年間**料金: ユーザーがプランを購入した場合に毎年定期的に前払いされる定額の年間料金。
* 定期的な料金に加えて、プランには、定額料金に含まれない、使用量に応じてお客様に請求するために使用されるオプションのディメンションを含めることもできます。   各ディメンションは、サービスが [Marketplace の測定サービス API](./marketplace-metering-service-apis.md) を使用して Microsoft と通信する課金対象項目を表します。

## <a name="sample-offer"></a>サンプル オファー

たとえば、Contoso は、Contoso Notification Services (CNS) と呼ばれる SaaS サービスを使用する公開元です。 CNS を使用すると、お客様は電子メールまたはテキストを使用して通知を送信できます。 Contoso は、Azure のお客様にプランを公開するための商業マーケットプレース プログラムの公開元としてパートナー センターに登録されています。  次に示すように、CNS に関連付けられているプランが 2 つあります。

* 基本プラン
    * 10000 件の電子メールと 1000 件のテキストを月 0 ドルで送信する
    * 電子メールが 10000 件を超えると、100 件ごとに 1 ドル支払う
    * テキストが 1000 件を超えると、テキストごとに 0.02 ドル支払う
* Premium プラン
    * 50000 件の電子メールと 10000 件のテキストを月 350 ドルで送信する
    * 電子メールが 50000 件を超えると、100 件ごとに 0.5 ドル支払う
    * テキストが 10000 件を超えると、テキストごとに 0.01 ドル支払う

CNS サービスをサブスクライブする Azure のお客様は、選択したプランに基づいて、1 か月の無料使用量分のテキストと電子メールを送信できるようになります。  お客様の使用量が無料使用量を超えた場合、プランを変更したり別のことを行ったりする必要はありません。  Contoso は無料使用量の超過分を測定し、[Marketplace の測定サービス API](./marketplace-metering-service-apis.md) を使用して、追加使用についての使用状況イベントを Microsoft に送信し始めます。  Microsoft はこれに応じて、公開元によって指定された追加の使用量に対してお客様に請求します。

## <a name="billing-dimensions"></a>課金ディメンション

課金ディメンションは、ソフトウェアの使用について課金される方法をお客様に通知するために使用され、使用状況イベントを Microsoft に伝達するためにも使用されます。 定義は次のとおりです。

* **ディメンション識別子**: 使用状況イベントの生成中に参照される変更できない識別子。
* **ディメンション名**: ディメンションに関連付けられている表示名 (例: "送信されたテキスト メッセージ")。
* **計量単位**: 課金単位の説明 (例: "テキスト メッセージごと"、"電子メール 100 件あたり")。
* **単価**: ディメンションの 1 単位あたりの価格。  
* **月間契約の無料使用量**: 定期的な月額料金を支払ったお客様への 1 か月あたりのディメンションの数量。整数である必要があります。
* **年間契約の無料使用量**: 定期的な年間料金を支払ったお客様への 1 か月あたりのディメンションの数量。整数である必要があります。

課金ディメンションは、オファーのすべてのプランで共有されます。  一部の属性はすべてのプランのディメンションに適用され、その他の属性はプランに固有です。

ディメンション自体を定義する属性は、オファーのすべてのプランで共有されます。  オファーを公開する前に、あらゆるプランのコンテキストからこれらの属性に加えられた変更は、すべてのプランのディメンション定義に影響を与えます。  プランを公開すると、これらの属性は編集できなくなります。  これらの属性には、以下のようなものがあります。

* ID
* Name
* Unit of measure

ディメンションのその他の属性は各プランに固有であり、各プランで異なる値を持つことができます。  これらの値はプランを公開する前に編集でき、このプランのみが影響を受けます。  プランを公開すると、これらの属性は編集できなくなります。  これらの属性には、以下のようなものがあります。

* ユニットごとの料金
* 月次払いのお客様の無料使用量 
* 年次払いのお客様の無料使用量 

ディメンションには "有効化" と "無限" という 2 つの特殊な概念もあります。

* **有効化**は、このプランがこのディメンションに参加することを示します。  このディメンションに基づいて使用状況イベントを送信しない新しいプランを作成する場合は、このチェックボックスをオフのままにしておくことをお勧めします。  また、プランが最初に公開された後に追加された新しいディメンションは、既に公開されているプランに対して "無効" として表示されます。  無効なディメンションが、お客様から見たプランのディメンションの一覧に表示されるようになります。
* **無限**は無限大記号 "∞" で表され、このプランはこのディメンションに含まれるが、このディメンションに対して使用量を測定しないことを意味します。  これはお客様に、このディメンションによって表される機能がプランに含まれているが、使用量に制限がないことを示す場合に使用します。  使用量が無限のディメンションは、お客様に表示されるプランのディメンションの一覧に表示され、このプランに対して料金が発生しないことが表示されます。

>[!Note] 
>次のシナリオが明示的にサポートされています。 <br> - 新しいディメンションを新しいプランに追加できます。  新しいディメンションは、既に公開されているすべてのプランに対して有効になりません。 <br> - ディメンションを指定せずに**定額**プランを公開し、新しいプランを追加して、そのプランの新しいディメンションを構成することができます。 新しいディメンションは、既に公開されているプランに対して有効になりません。

## <a name="constraints"></a>制約

### <a name="trial-behavior"></a>無料試用版の動作

マーケットプレース測定サービスを使用した従量制課金は、無料試用版の提供と互換性がありません。  従量制課金と無料試用版の両方を使用するようにプランを構成することはできません。

### <a name="locking-behavior"></a>ロック動作

マーケットプレース測定サービスで使用されるディメンションは、お客様がサービスに対してどのように支払うかの了解事項となるため、いったんディメンションを公開したら、ディメンションのすべての詳細は編集できなくなります。  公開する前に、プランに対してディメンションを完全に定義しておくことが重要です。
  
オファーがディメンションと共に公開されると、そのディメンションについての以下のようなオファーレベルの詳細は変更できなくなります。

* ID
* Name
* Unit of measure

プランが公開されると、以下のようなプラン レベルの詳細を変更できなくなります。

* ユニットごとの料金
* 月間の無料使用量
* 年間の無料使用量
* プランに対してディメンションが有効になっているかどうか

### <a name="upper-limits"></a>上限

1 つのプランに対して構成できる一意のディメンションの最大数は 18 個です。

## <a name="get-support"></a>サポートを受ける

次のいずれかが発生した場合は、サポート チケットを開くことができます。

* Marketplace の測定サービス API に関する技術的な問題。
* お客様の側でのエラーまたはバグが原因でエスカレートする必要がある問題 (例: 間違った使用状況イベント)。
* 従量制課金に関連するその他の問題。 

サポート チケットを送信するには、次の手順に従ってください。

1. [サポート ページ](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)に移動します。 最初のいくつかのドロップダウン メニューは、自動的に入力されます。 マーケットプレース サポートのため、製品ファミリを **[Cloud and Online Services]\(クラウドとオンライン サービス\)** として、製品を **[Marketplace パブリッシャー]** として指定します。  この事前設定されたドロップダウン メニューの選択は変更しないでください。
2. [Select the product version]\(製品バージョンの選択\) で、 **[Live offer management]\(ライブ オファー管理\)** を選択します。
3. [Select a category that best describe the issue]\(問題を最もよく表しているカテゴリを選択する\) で、 **[SaaS アプリ]** を選択します。
4. [Select a problem that best describes the issue]\(問題を最もよく表している問題を選択する\) で、 **[metered billing]\(従量制課金\)**  を選択します。
5. **[次へ]** を選択すると、 **[問題の詳細]** ページが表示され、問題の詳細を入力できます。

公開元サポート オプションについては、「[パートナー センターでの商業マーケットプレース プログラムのサポート](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)」を参照してください。

## <a name="next-steps"></a>次の手順

- 詳細については、「[Marketplace の測定サービス API](./marketplace-metering-service-apis.md)」をご覧ください。
