---
title: Azure Functions で関数を無効にする方法
description: Azure Functions 1.x と 2.x で関数を無効および有効にする方法について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 183056d01146194b2854a70df790802e1a0bb839
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782235"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions で関数を無効にする方法

この記事では、Azure Functions で関数を無効にする方法について説明します。 関数を*無効にする*には、その関数用に定義された自動トリガーをランタイムが無視するようにします。 そのための方法は、ランタイムのバージョンとプログラミング言語によって異なります。

* Functions 2.x:
  * すべての言語について方法は 1 つ
  * C# クラス ライブラリについてはオプションの方法あり
* Functions 1.x:
  * スクリプト言語
  * C# クラス ライブラリ

## <a name="functions-2x---all-languages"></a>Functions 2.x - すべての言語

Functions 2.x で関数を無効にするには、`AzureWebJobs.<FUNCTION_NAME>.Disabled` の形式でアプリの設定を使用します。 Azure CLI を使用して、プログラムでこの設定を作成および変更できます。 [Azure portal](https://portal.azure.com) の関数の **[管理]** タブから、これを行うこともできます。 

### <a name="azure-cli"></a>Azure CLI

Azure CLI でアプリの設定を作成および変更するには、[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドを使います。 `AzureWebJobs.QueueTrigger.Disabled` という名前のアプリの設定を作成して `true` に設定することにより `QueueTrigger` という名前の関数を無効にするコマンドを次に示します。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

関数を再度有効にするには、値 `false` を指定して同じコマンドを再実行します。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>ポータル

関数の **[管理]** タブにある **[関数の状態]** スイッチを使用することもできます。このスイッチを機能させるには、`AzureWebJobs.<FUNCTION_NAME>.Disabled` アプリ設定を作成および削除します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - C# クラス ライブラリ

Functions 2.x のクラス ライブラリでは、すべての言語に対して有効な方法を使用することをお勧めします。 ただし、必要に応じて、[Functions 1.x の場合と同様の Disable 属性を使用する](#functions-1x---c-class-libraries)ことができます。

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - スクリプト言語

C# スクリプトや JavaScript などのスクリプト言語の場合は、*function.json* ファイルの `disabled` プロパティを使用して関数をトリガーしないようにランタイムに通知できます。 このプロパティは、`true` またはアプリ設定の名前に設定できます。

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

2 つ目の例では、`true` または 1 に設定されている IS_DISABLED という名前のアプリ設定がある場合に関数が無効になります。

Azure Portal でファイルを編集するか、または関数の **[管理]** タブで **[関数の状態]** スイッチを使用できます。ポータルのスイッチを機能させるには、*function.json* ファイルを変更します。

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - C# クラス ライブラリ

Functions 1.x のクラス ライブラリでは、`Disable` 属性を使用して、関数がトリガーしないようにします。 次の例に示すように、コンストラクターのパラメーターを指定せずに属性を使用できます。

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

コンストラクターのパラメーターを指定せずに属性を使用するには、プロジェクトを再コンパイルおよび再デプロイして関数の無効状態を変更する必要があります。 より柔軟性に優れた方法でこの属性を使用するには、次の例に示すように、ブール値のアプリ設定を参照するコンストラクターのパラメーターを含めます。

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

この方法では、アプリ設定を変更することで、関数を有効および無効にすることができます。再コンパイルや再デプロイは必要ありません。 アプリ設定を変更すると関数アプリが再起動されるため、無効状態の変更がすぐに認識されます。

> [!IMPORTANT]
> `Disabled` 属性は、クラス ライブラリの関数を無効にする唯一の方法です。 クラス ライブラリの関数用に生成される *function.json* ファイルは直接編集することを意図したものではありません。 そのファイルを編集する場合、`disabled` プロパティに対してどのような処理を行っても効果はありません。
>
> **[管理]** タブの **[関数の状態]** スイッチについても同様です。このスイッチを機能させるには、*function.json* ファイルを変更する必要があるためです。
>
> また、無効でない関数が無効であるとポータルに表示される可能性があることに注意してください。

## <a name="next-steps"></a>次の手順

この記事は、自動トリガーの無効化に関する記事です。 トリガーについて詳しくは、[トリガーとバインド](functions-triggers-bindings.md)に関するページをご覧ください。
