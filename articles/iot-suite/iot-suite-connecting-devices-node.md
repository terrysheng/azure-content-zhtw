<properties
   pageTitle="使用 Node.js 連接裝置 |Microsoft Azure"
   description="描述如何使用 Node.js 中已寫入的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# 將裝置連接至 IoT 套件遠端監視預先設定方案

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 使用 node.js 將裝置資料傳送到遠端監視方案

1. 若要複製 *Microsoft Azure IoT SDK* GitHub 儲存機制，並將 *Microsoft Azure IoT 裝置 SDK for Node.js* 安裝至您的桌面環境，請遵循[準備您的開發環境][lnk-github-prepare]指示。

2. 從您的本機複本 [azure-iot-sdks][lnk-github-repo] 儲存機制，將下列兩個檔案從 node/device/samples 資料夾複製到您裝置的資料夾中：

  - packages.json
  - remote\_monitoring.js

3. 開啟 remote\_monitoring.js 檔案並尋找下列變數：

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. 以您裝置的連接字串取代 **[IoT 中樞裝置連接字串]**。您可以在遠端監視方案的儀表板中找到 IoT 中樞主機名稱、裝置識別碼和裝置金鑰。裝置連接字串使用的格式如下：

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

5. 如果 IoT 中樞主機名稱是 **contoso** 且裝置識別碼是 **mydevice**，連接字串看起來像這樣：

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

6. 儲存檔案。在包含這些檔案的資料夾中執行下列命令：

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md

<!---HONumber=Nov15_HO4-->