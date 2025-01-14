---
title: Python を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: Azure IoT device SDK for Python を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: a529933cf4af572deacab1ae3c615ec0a0eca68f
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667867"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

この記事では、[IoT Hub のファイル アップロード機能](iot-hub-devguide-file-upload.md)を使用してファイルを [Azure Blob Storage](../storage/index.yml) にアップロードする方法を説明します。 このチュートリアルでは、次の操作方法について説明します。

* ファイルをアップロードするためのストレージ コンテナーを安全に提供します。

* Python クライアントを使用して、IoT ハブ経由でファイルをアップロードします。

[デバイスから IoT ハブへのテレメトリの送信](quickstart-send-telemetry-python.md)に関するクイックスタートでは、IoT Hub のデバイスからクラウドへの基本的なメッセージング機能が示されます。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 デバイスからファイルをアップロードする必要がある場合も、IoT Hub のセキュリティを信頼性を使用できます。

> [!NOTE]
> IoT Hub Python SDK は現在、 **.txt** ファイルなどの文字ベースのファイルのアップロードのみをサポートしています。

このチュートリアルの最後に、Python コンソール アプリを実行します。

* **FileUpload.py** は、Python デバイス SDK を使用してファイルをストレージにアップロードします。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

前提条件のインストール手順は次のとおりです。

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

このセクションでは、IoT Hub にファイルをアップロードするデバイス アプリを作成します。

1. コマンド プロンプトで次のコマンドを実行して **azure-iothub-device-client** パッケージをインストールします。

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. テキスト エディターを使用して、BLOB ストレージにアップロードするテスト ファイルを作成します。

    > [!NOTE]
    > IoT Hub Python SDK は現在、 **.txt** ファイルなどの文字ベースのファイルのアップロードのみをサポートしています。

3. テキスト エディターを使用して、作業フォルダーに **FileUpload.py** ファイルを作成します。

4. **FileUpload.py** ファイルの先頭に次の `import` ステートメントと変数を追加します。 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. ファイル内で、`[Device Connection String]` を IoT Hub デバイスの接続文字列に置き換えます。 `[Full path to file]` を、作成したテスト ファイル、またはアップロードするデバイス上の任意のファイルのパスに置き換えます。 `[File name for storage]` を、BLOB ストレージにアップロードした後のファイルに指定する名前に置き換えます。 

6. **upload_blob** 関数のコールバックを作成します。

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. 次のコードを追加して、クライアントに接続し、ファイルをアップロードします。 `main` ルーチンも含めます。

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. **UploadFile.py** ファイルを保存して閉じます。

## <a name="run-the-application"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. 作業フォルダーのコマンド プロンプトで、次のコマンドを実行します。

    ```cmd/sh
    python FileUpload.py
    ```

2. 次のスクリーン ショットは、**FileUpload** アプリからの出力を示しています。

    ![simulated-device アプリからの出力](./media/iot-hub-python-python-file-upload/1.png)

3. ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

    ![アップロードされたファイル](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
