---
title: Azure Virtual Machines 上の SQL Server のリリース ノート | Microsoft Docs
description: Azure VM 上の SQL Server の新機能と機能改善について説明します。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: 61bf9d53b5ceda9420ae594680466a70b1e3d0cd
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882329"
---
# <a name="release-notes-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server のリリース ノート

Azure では、SQL Server のイメージを組み込んだ仮想マシン (VM) をデプロイできます。 この記事は、[Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) の最新リリースで導入された新機能と機能強化をまとめたものです。 この記事では、今回のリリースとは直接関連しないものの、同じタイム フレームで公開された注目すべきコンテンツの更新についても一覧表示しています。 他の Azure サービスの機能強化については、「[サービスの更新情報](https://azure.microsoft.com/updates)」を参照してください

## <a name="july-2019"></a>2019 年 7 月

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメント | 詳細 |
| --- | --- |
| **SQL VM を別のリージョンに移動する** | Azure Site Recovery を使用して、[異なるリージョン間で SQL Server VM を移行](virtual-machines-windows-sql-move-different-region.md)します。 |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>2019 年 6 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **新しい SQL IaaS インストール モード** | SQL Server サービスの再開を回避するため、SQL Server IaaS 拡張機能を[軽量モード](virtual-machines-windows-sql-server-agent-extension.md)でインストールできるようになりました。  |
| **SQL Server エディションの変更** | SQL Server VM の [エディション プロパティ](virtual-machines-windows-sql-change-edition.md)を変更できるようになりました。 |
| **SQL VM リソース プロバイダーの変更** | 新しい SQL IaaS モードを使用して、[SQL Server VM を SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)できます。 この機能には、[Windows 2008 のイメージ](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)が含まれます。|
| **Azure ハイブリッド特典を使用するライセンス持ち込みイメージ** | Azure Marketplace からデプロイされたライセンス持ち込みイメージを使用して、[ライセンスの種類を従量課金制に](virtual-machines-windows-sql-ahb.md#remarks)切り替えることができるようになりました。| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **Azure portal での新しい SQL Server VM の管理** | Azure portal で SQL Server VM を管理する新しい方法が導入されました。 詳細については、「[Azure portal で SQL Server VM を管理する](virtual-machines-windows-sql-manage-portal.md)」を参照してください。  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメント | 詳細 |
| --- | --- |
| **新しい SQL Server VM 管理ポータル** | 新しい SQL Server VM 管理ポータル エクスペリエンスに対して約 10 件の記事が更新されました。 | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **SQL Server 2008/2008 R2 の延長サポート** | "*そのまま*" Azure VM に移行することで、SQL Server 2008 および SQL Server 2008 R2 の[サポートを延長](virtual-machines-windows-sql-server-2008-eos-extend-support.md)します。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019 年 3 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **カスタム イメージのサポートの可否** | OS と SQL イメージをカスタマイズするために、[SQL Server IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md#installation)をインストールできるようになりました。これにより、機能が制限された[柔軟なライセンス](virtual-machines-windows-sql-ahb.md)が提供されます。 カスタム イメージを SQL リソース プロバイダーに登録するときに、ライセンスの種類として "AHUB" を指定します。 そうしないと、登録は失敗します。 | 
| **名前付きインスタンスのサポートの可否** | 既定のインスタンスが適切にアンインストールされている場合、名前付きインスタンスで [SQL Server IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md#installation)を使用できるようになりました。 | 
| **ポータルの機能強化** | SQL Server VM をデプロイするための Azure portal エクスペリエンスは、より使いやすくなるように改良されています。 詳細については、SQL Server VM のデプロイに関する簡単な[クイックスタート](quickstart-sql-vm-create-portal.md)と、より包括的な[ハウツー](virtual-machines-windows-portal-sql-server-provision.md) ガイドを参照してください。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **ポータルの改善** | [Azure portal](virtual-machines-windows-sql-ahb.md#change-the-license-for-vms-already-registered-with-the-resource-provider) を使用して、SQL Server VM のライセンス モデルを従量課金制からライセンス持ち込みに変更できるようになりました。|
|**Azure SQL Server VM CLI を使用した可用性グループのデプロイの簡略化** | 可用性グループを Azure の SQL Server VM にデプロイすることが以前より簡単になりました。 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) を使用すると、Windows フェールオーバー クラスター、内部ロード バランサー、および可用性グループのリスナーのすべてをコマンド ラインから作成できます。 詳細については、[Azure SQL Server VM CLI を使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する](virtual-machines-windows-sql-availability-group-cli.md)方法のページを参照してください。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **SQL Server クラスターの新しいリソース プロバイダー** | Windows フェールオーバー クラスターのメタデータを定義する新しいリソース プロバイダー (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)。 SQL Server VM を *SqlVirtualMachineGroups* に参加させることで、Windows Server フェールオーバー クラスター (WSFC) サービスをブートストラップし、VM をクラスターに参加させます。  |
|**Azure クイックスタート テンプレートを使用して自動化された可用性グループのデプロイの設定** |2 つの Azure クイックスタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそれに参加させ、リスナーを作成し、内部ロード バランサーを構成することができるようになりました。 詳細については、「[Azure クイックスタート テンプレートを使用して Azure VM で SQL Server の Always On 可用性グループを構成する](virtual-machines-windows-sql-availability-group-quickstart-template.md)」を参照してください。 | 
| **SQL VM リソース プロバイダーへの自動登録** | 今月以降にデプロイされる SQL Server VM は、自動的に新しい SQL Server リソース プロバイダーに登録されます。 今月より前にデプロイされた SQL Server VM は、まだ手動で登録する必要があります。 詳細については、「[Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する](virtual-machines-windows-sql-register-with-resource-provider.md)」を参照してください。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **新しい SQL VM リソース プロバイダー** |  新しいリソース プロバイダー (Microsoft.SqlVirtualMachine) を使用すると、SQL Server VM の管理が強化されます。 VM の登録の詳細については、「[Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する](virtual-machines-windows-sql-register-with-resource-provider.md)」を参照してください。 |
|**ライセンス モデルを切り替える** | Azure CLI または PowerShell を使用して、SQL Server VM のライセンス モデルを従量制と持ち込みの間で切り替えることができるようになりました。 詳細については、[Azure の SQL Server 仮想マシンのライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>その他のリソース

**Windows VM**:

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM のプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)
* [Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux 仮想マシンのプロビジョニング](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [よく寄せられる質問 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
