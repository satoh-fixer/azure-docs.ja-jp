---
title: Azure Sentinel Preview に CEF データを接続する | Microsoft Docs
description: Azure Sentinel に CEF データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 1cc661509a28bb57bed0361b48cdeda5e6338e54
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679307"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>共通イベント形式を使用して外部ソリューションを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel と外部ソリューションを接続できます。これにより、ログ ファイルを Syslog に保存できるようになります。 ご利用のアプライアンスでログを Syslog 共通イベント形式 (CEF) として保存できる場合、Azure Sentinel との統合によってデータ全体で簡単に分析とクエリを実行できます。

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

## <a name="how-it-works"></a>動作のしくみ

Azure Sentinel とご利用の CEF アプライアンスとの間の接続では、次の 3 つの手順が行われます。

1. アプライアンスが、Microsoft Monitoring Agent に基づいて、必要な形式で必要なログを Azure Sentinel Syslog エージェントに送信できるように、アプライアンスで次の値を設定する必要があります。 Azure Sentinel エージェントの Syslog デーモンでこれらのパラメーターを変更できれば、ご利用のアプライアンスでもこれらのパラメーターを変更することができます。
    - プロトコル = UDP
    - ポート = 514
    - ファシリティ = Local4
    - 形式 = CEF
2. Syslog エージェントでは、データを収集して Log Analytics に安全に送信します。ここでデータが解析および改良されます。
3. エージェントによってデータが Log Analytics ワークスペースに保存されるため、分析、相関ルール、ダッシュボードを使用して、必要に応じてクエリできます。

> [!NOTE]
> エージェントは、複数のソースからログを収集できますが、専用のコンピューターにインストールする必要があります。


 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

これ以外の場合は、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントを展開します。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="step-1-configure-your-syslog-vm"></a>手順 1:Syslog VM の構成

エージェントを専用の Linux マシン (VM またはオンプレミス) に展開して、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 

> [!NOTE]
> 組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 


1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Common Event Format (CEF)]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** で、マシンの種類として Azure またはオンプレミスを選択します。 
1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、 **[接続]** をクリックします。
1. **[Download and install agent for Azure Linux virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、マシンを選択し、 **[接続]** をクリックします。 **[Download and install agent for non-Azure Linux virtual machines]\(Azure Linux 以外の仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、 **[ダイレクトエージェント]** 画面で、 **[Linux 用エージェントのダウンロードとオンボード]** の下にあるスクリプトを実行します。
1. CEF コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
1. これらのコマンドをコピーし、アプライアンス上で実行します。
    - rsyslog.d を選択した場合:
              
       1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            
       1. syslog デーモン `sudo service rsyslog restart` を再起動します。<br> 詳細については、[rsyslog に関するドキュメント](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)を参照してください
           
    - syslog-ng を選択した場合:
       1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。

        3. syslog デーモン `sudo service syslog-ng restart` を再起動します。 <br>詳細については、[syslog-ng に関するドキュメント](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)を参照してください。
1. 次のコマンドを使用して、Syslog エージェントを再起動します: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します。`tail /var/opt/microsoft/omsagent/log/omsagent.log`

Log Analytics で CEF イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>手順 2:Common Event Format (CEF) ログを Syslog エージェントに転送する

CEF 形式の Syslog メッセージを Syslog エージェントに送信するように、セキュリティ ソリューションを設定します。 エージェント構成に表示されているパラメーターと同じものを使用していることを確認します。 通常、これらは次のようになります。

- ポート 514
- ファシリティ local4

## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. 必ず正しいファシリティを使用してください。 ファシリティは、アプライアンスと Azure Sentinel で同じである必要があります。 どのファシリティ ファイルを Azure Sentinel で使用しているかをチェックし、ファイル `security-config-omsagent.conf` でそれを修正することができます。 

2. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します。`tcpdump -A -ni any  port 514 -vv` このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 正しいポートと正しい機能でソース アプライアンスからログを受信していることを確認してください。

3. 送信するログが [RFC 3164](https://tools.ietf.org/html/rfc3164) に準拠していることを確認します。

4. Syslog エージェントを実行しているコンピューターで、コマンド `netstat -a -n:` を使用して、これらのポート 514、25226 が開いており、リッスンしていることを確認します。 このコマンドの詳しい使用方法については、[netstat(8) - Linux man ページ](https://linux.die.net/man/8/netstat)を参照してください。 正しくリッスンしている場合、次のように表示されます。

   ![Azure Sentinel ポート](./media/connect-cef/ports.png) 

5. ログを送信しているポート 514 をリッスンするように、デーモンが設定されていることを確認します。
    - rsyslog の場合:<br>ファイル `/etc/rsyslog.conf` に次の構成が含まれていることを確認します。

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      詳細については、[imudp:UDP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) および [imtcp:TCP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module) に関するページを参照してください

   - syslog-ng の場合:<br>ファイル `/etc/syslog-ng/syslog-ng.conf` に次の構成が含まれていることを確認します。

           # source s_network {
            network( transport(UDP) port(514));
             };
     詳細については、「[syslog-ng Open Source Edition 3.16 - Administration Guide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)」 (syslog-ng オープンソース エディション 3.16 - 管理者ガイド) を参照してください。

1. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します。`tcpdump -A -ni any  port 25226 -vv` このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 エージェントでログが受信されていることを確認します。

6. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` 型の下に未加工の形式で表示されます。

7. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log` を調べます。 ログ形式の不一致エラーがある場合は、`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` に移動してファイル `security_events.conf` を調べ、ログの正規表現形式がこのファイルのものと一致していることを確認します。

8. Syslog メッセージの既定のサイズが 2,048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します: `message_length_limit 4096`


## <a name="next-steps"></a>次の手順
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

