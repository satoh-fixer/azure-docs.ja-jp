---
title: VMware Solution by CloudSimple - Azure パブリック IP アドレス
description: VMware Solution by CloudSimple でのパブリック IP アドレスとその利点について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812526"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple のパブリック IP アドレスの概要

インターネット リソースでは、パブリック IP アドレスを使うことにより、プライベート IP アドレスにあるプライベート クラウド リソースへのインバウンドの通信を行うことができます。 プライベート IP アドレスは、仮想マシンまたはソフトウェア ロード バランサーのいずれかです。 プライベート IP アドレスは、プライベート クラウドの vCenter 上にあります。 パブリック IP アドレスを使うと、プライベート クラウドで実行されているサービスをインターネットに公開することができます。

パブリック IP アドレスは、割り当てを解除するまで、そのプライベート IP アドレス専用になります。 1 つのパブリック IP アドレスは、1 つのプライベート IP アドレスにのみ割り当てることができます。

パブリック IP アドレスに関連付けられたリソースでは、常に、インターネット アクセスに対してパブリック IP アドレスが使われます。 既定では、パブリック IP アドレスではアウトバウンドのインターネット アクセスのみが許可されます。  パブリック IP アドレスでの着信トラフィックは拒否されます。  インバウンド トラフィックを許可するには、特定のポートに対してそのパブリック IP アドレスのファイアウォール規則を作成します。

## <a name="benefits"></a>メリット

パブリック IP アドレスを使ってインバウンドの通信を行うと、以下のものが提供されます。

* 分散型サービス拒否 (DDoS) 攻撃に対する防御。 この保護は、パブリック IP アドレスに対して自動的に有効になります。
* トラフィックの常時監視と一般的なネットワーク レベル攻撃のリアルタイム軽減。 これらの防御は、Microsoft Online Services によって使われるものと同じ防御です。
* Azure グローバル ネットワークのスケール全体。 ネットワークを使って、リージョン間で攻撃トラフィックを分散および軽減できます。  

## <a name="next-steps"></a>次の手順

* [パブリック IP アドレスを割り当てる](https://docs.azure.cloudsimple.com/public-ips/)方法を確認してください
