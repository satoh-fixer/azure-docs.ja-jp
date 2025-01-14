---
layout: LandingPage
description: 仮想マシンのデプロイのトラブルシューティングを行う方法について説明します。
title: Azure Virtual Machines のトラブルシューティングのドキュメント | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: 1db5829abd52a1b010d38dd8e9151ab2df3f1de5
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854404"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Azure Virtual Machines のトラブルシューティング

- 割り当ての失敗
    - [割り当ての失敗](allocation-failure.md)
    - [クラシック デプロイの割り当ての失敗](allocation-failure-classic.md)
- [ブート診断](boot-diagnostics.md)
- RDP
    - [RDP のリセット](reset-rdp.md)
    - [RDP のトラブルシューティング](troubleshoot-rdp-connection.md)
    - [RDP のトラブルシューティングの詳細](detailed-troubleshoot-rdp.md)
    - [特定のエラーのトラブルシューティング](troubleshoot-specific-rdp-errors.md)
- SSH 
    - [SSH のトラブルシューティング](troubleshoot-ssh-connection.md)
    - [SSH のトラブルシューティングの詳細](detailed-troubleshoot-ssh-connection.md)
    - [一般的なエラー メッセージ](error-messages.md)
- [Windows VM エージェントのオフライン インストール](install-vm-agent-offline.md)
- VM のパフォーマンスのトラブルシューティング
    - [VM のパフォーマンスの問題](performance-diagnostics.md)
    - Windows
        - [PerfInsights を使用する方法](how-to-use-perfinsights.md)
        - [パフォーマンス診断拡張機能](performance-diagnostics-vm-extension.md)
    - Linux
        - [PerfInsights を使用する方法](how-to-use-perfinsights-linux.md)
- VM を再デプロイする
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- VM パスワードのリセット
    - [Windows](reset-local-password-without-agent.md)
    - [Linux](reset-password.md)
- [NIC のリセット](reset-network-interface.md)
- [VM の再起動またはサイズ変更](restart-resize-error-troubleshooting.md)
- シリアル コンソールの使用
    - [Linux VM](serial-console-linux.md)
        - [シリアル コンソールの GRUB およびシングル ユーザー モード](serial-console-grub-single-user-mode.md)
        - [シリアル コンソールの NMI および SysRq](serial-console-nmi-sysrq.md)
    - [Windows VM](serial-console-windows.md)
        - [CMD と PowerShell コマンド](serial-console-cmd-ps-commands.md)
- [ストレージ リソースの削除時のエラー](storage-resource-deletion-errors.md      )
- [接続された VHD による VM の予期しない再起動](unexpected-reboots-attached-vhds.md)
- [Windows のアクティブ化に関する問題](troubleshoot-activation-problems.md)
- [アプリケーション アクセスに関する問題](troubleshoot-app-connection.md)
- デプロイのトラブルシューティング
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [デバイス名が変更されている](troubleshoot-device-names-problems.md)
- VM の復旧方法
    - Windows
        - [PowerShell](troubleshoot-recovery-disks-windows.md)
        - [Azure Portal](troubleshoot-recovery-disks-portal-windows.md)
    - Linux
        - [CLI](troubleshoot-recovery-disks-linux.md)
    - [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)
- [ブート エラー](boot-error-troubleshoot.md)
- [BitLocker のエラー](troubleshoot-bitlocker-boot-error.md)
- [ファイル システム エラーのチェック](troubleshoot-check-disk-boot-error.md)
- [ブルー スクリーン エラー](troubleshoot-common-blue-screen-error.md)
- [調整エラー](troubleshooting-throttling-errors.md)
- [入れ子になった仮想化の使用](troubleshoot-vm-by-use-nested-virtualization.md)
- [システムの再起動を理解する](understand-vm-reboot.md)

