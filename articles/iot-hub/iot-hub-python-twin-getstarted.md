---
title: Azure IoT Hub デバイス ツインの使用 (Python) | Microsoft Docs
description: Azure IoT Hub デバイス ツインを使用してタグを追加し、IoT Hub クエリを使用する方法。 Azure IoT SDK for Python を使用して、シミュレートされたデバイス アプリと、タグを追加して IoT Hub クエリを実行するサービス アプリを実装します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 62385f4bd07f4b80dc3d571d409e16c7e0dca205
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667855"
---
# <a name="get-started-with-device-twins-python"></a>デバイス ツインの概要 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

このチュートリアルの最後には、次の 2 つの Python コンソール アプリが完成します。

* **AddTagsAndQuery.py**: Python バックエンド アプリ。タグを追加してデバイス ツインのクエリを実行します。

* **ReportConnectivity.py**: Python アプリ。以前作成したデバイス ID を使用して IoT ハブに接続するデバイスをシミュレートし、接続の状態を報告します。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

前提条件のインストール手順は次のとおりです。

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>デバイス アプリを作成する

このセクションでは、 **{Device ID}** に関連付けられたデバイス ツインに場所のメタデータを追加する Python コンソール アプリを作成します。 その後、レドモンドにあるデバイスで、携帯ネットワーク接続を報告しているものを選択して、IoT ハブに格納されているデバイス ツインに対してクエリを実行します。

1. コマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。 SDK をインストールしたら、コマンド プロンプトを閉じます。

   ```
   pip install azure-iothub-service-client
   ```

2. テキスト エディターを使用して、新しい **AddTagsAndQuery.py** ファイルを作成します。

3. Service SDK から必要なモジュールをインポートする次のコードを追加します。

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. 次のコードを追加し、`[IoTHub Connection String]` および `[Device Id]` のプレースホルダーを、前のセクションで作成した IoT ハブとデバイス ID の接続文字列に置き換えます。
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. 次のコードを **AddTagsAndQuery.py** ファイルに追加します。

     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 最初のコードで **Registry** オブジェクトを初期化した後、**deviceId** のデバイス ツインを更新し、最後に 2 つのクエリを実行します。 最初のクエリでは、**Redmond43** 工場にあるデバイスのデバイス ツインのみを選択し、2 番目のクエリでは携帯ネットワーク経由で接続しているデバイスのみを選択します。

6. 次のコードを **AddTagsAndQuery.py** の末尾に追加して、**iothub_service_sample_run** 関数を実装します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. 以下を使用してアプリケーションを実行します。

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    **Redmond43** にあるすべてのデバイスを照会するクエリの結果には、1 件のデバイスが表示され、携帯ネットワークを使用するデバイスに絞り込んだ結果には 0 件のデバイスが表示されます。

    ![Redmond にあるすべてのデバイスを表示する最初のクエリ](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

次のセクションでは、接続情報を報告し、前のセクションのクエリの結果を変更するデバイス アプリを作成します。

## <a name="create-the-device-app"></a>デバイス アプリを作成する

このセクションでは、 **{Device ID}** としてハブに接続し、デバイス ツインのレポートされるプロパティに携帯ネットワークを使用しているという情報を含めるよう更新する Python コンソール アプリを作成します。

1. コマンド プロンプトを開き、**Azure IoT Hub Service SDK for Python** をインストールします。 SDK をインストールしたら、コマンド プロンプトを閉じます。

    ```
    pip install azure-iothub-device-client
    ```

2. テキスト エディターを使用して、新しい **ReportConnectivity.py** ファイルを作成します。

3. Service SDK から必要なモジュールをインポートする次のコードを追加します。

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. 次のコードを追加し、`[IoTHub Device Connection String]` プレースホルダーを、前のセクションで作成した IoT ハブ デバイスの接続文字列に置き換えます。

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. 次のコードを **ReportConnectivity.py** ファイルに追加して、デバイス ツインの機能を実装します。

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 前のコードでは、**Client** オブジェクトを初期化した後、デバイスのデバイス ツインを取得して、報告されるプロパティに接続情報を含めるよう更新します。

6. 次のコードを **ReportConnectivity.py** の末尾に追加して、**iothub_client_sample_run** 関数を実装します。

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. デバイス アプリを実行する:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    デバイス ツインが更新されたこと示す確認メッセージが表示されます。

    ![ツインの更新](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. これで、デバイスが接続情報を報告したため、両方のクエリで表示されるようになります。 戻って、クエリをもう一度実行します。

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    今回は、 **{Device ID}** が両方のクエリ結果に表示されるはずです。

    ![2 番目のクエリ](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Portal で新しい IoT Hub を構成し、IoT Hub の ID レジストリにデバイス ID を作成しました。 バックエンド アプリからデバイスのメタデータをタグとして追加し、シミュレート対象デバイス アプリでデバイス ツインのデバイスの接続情報を報告するよう記述しました。 さらに、レジストリを使用してこの情報を照会する方法も学習しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要](quickstart-send-telemetry-python.md)に関するチュートリアルでデバイスからテレメトリを送信する。

* [必要なプロパティを使用してデバイスを構成する](tutorial-device-twins.md)方法に関するチュートリアルで、デバイス ツインの必要なプロパティを使用してデバイスを構成する。

* [ダイレクト メソッドの使用](quickstart-control-device-python.md)に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。