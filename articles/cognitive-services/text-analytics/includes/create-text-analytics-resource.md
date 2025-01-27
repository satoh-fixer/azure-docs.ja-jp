---
title: Cognitive Services Text Analytics リソースを作成する
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics リソースを作成する方法について説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 6a9363472e1d5770232ca9a0b151fb309d9c4c75
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377445"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics リソースを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[Create a resource]\(リソースを作成)** を選択して、**AI + Machine Learning** > **Text Analytics** に移動します。
   または、[[Create Text Analytics]\(Text Analytics の作成\)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動します。
1. すべての必須設定を入力します。

    |Setting|値|
    |--|--|
    |Name|名前 (2 から 64 文字) を入力します。|
    |Subscription|適切なサブスクリプションを選択します。|
    |Location|近くの場所を選択します。|
    |価格レベル| **S** (Standard 価格レベル) を入力します。|
    |Resource group|使用可能なリソース グループを選択します。|

1. **[作成]** を選択して、リソースが作成されるまで待ちます。 ブラウザーが、新たに作成されたリソースのページに自動的にリダイレクトされます。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|Setting|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` のように表示されます。|
    |**キー**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`> です。|
