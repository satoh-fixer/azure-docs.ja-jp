---
title: CloudSimple による Azure VMware ソリューションの作成 - サービス
description: Azure portal で CloudSimple サービスを作成する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6986e0a7e6eee6dbbd43c72a415b01df7da7da51
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812448"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>CloudSimple による Azure VMware ソリューションの作成 - サービス

CloudSimple による Azure VMware ソリューションを開始するには、Azure portal で CloudSimple による Azure VMware ソリューション サービスを作成します。

## <a name="before-you-begin"></a>開始する前に

ゲートウェイ サブネットに /28 CIDR ブロックを割り当てます。  CloudSimple サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットは、エッジ ネットワーク サービスを作成するときに使用され、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 CloudSimple 環境と通信するネットワークと重複しないようにしてください。  CloudSimple と通信するネットワークとしては、オンプレミスのネットワークや Azure Virtual Network があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。

2. **CloudSimple Services** を検索します。

    ![CloudSimple サービスを検索します。](media/create-cloudsimple-service-search.png)

3. **CloudSimple サービス**を選択します。

4. 新しいサービスを作成するには、**Add** (追加) をクリックします。

    ![CloudSimple サービスを追加します。](media/create-cloudsimple-service-add.png)

5. CloudSimple サービスを作成するサブスクリプションを選択します。

6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。

7. サービスを識別する名前を入力します。

8. サービス ゲートウェイの CIDR を入力します。 既存のサブネットと重複しない /28 サブネットを指定します。  これらには、オンプレミスのサブネット、Azure サブネット、または予定されているすべての CloudSimple サブネットが含まれます。 サービスを作成した後は、CIDR を変更できません。

    ![CloudSimple サービスの作成](media/create-cloudsimple-service.png)

9. Click **OK**.

サービスが作成され、サービスの一覧に追加されます。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドを作成する](https://docs.azure.cloudsimple.com/create-private-cloud/)方法を学習する
* [プライベート クラウドの環境を構成する](quickstart-create-private-cloud.md)方法を学習する
