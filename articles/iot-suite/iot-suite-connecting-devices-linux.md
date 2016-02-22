<properties
   pageTitle="在 Linux 上使用 C 連接裝置| Microsoft Azure"
   description="描述如何在 Linux 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="dobett"/>


# 將裝置連接至 IoT 套件遠端監視預先設定方案

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 在 Linux 上建置並執行範例 C 的解決方案

1. 若要複製 *Microsoft Azure IoT SDK* GitHub 儲存機制，並將 *Microsoft Azure IoT 裝置 SDK for C* 安裝至您的 Linux 桌面環境，請遵循[設定 Linux 開發環境][lnk-setup-linux]指示。

2. 在文字編輯器中開啟檔案 **c/serializer/samples/remote\_monitoring/remote\_monitoring.c**。

3. 在檔案中，找到下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. 從遠端監視方案的儀表板將 [裝置識別碼] 和 [裝置金鑰] 取代為裝置的值。

5. 使用儀表板中的 **IoT 中樞主機名稱**取代 **[IoTHub 名稱]** 和 **[IoTHub 後置詞，也就是 azure-devices.net**]。例如您的 **IoT 中樞主機名稱**為 **contoso.azure-devices.net**，則使用 **contoso** 取代 **[IoTHub 名稱]**，並使用 **azure-devices.net** 取代 [**IoTHub 後置詞，也就是 azure-devices.net**]，如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 儲存您的變更並建置範例。若要建立您的範例，可以執行 **c/build\_all/linux** 目錄中的 build.sh 指令碼。建置指令碼會建立 **cmake** 資料夾來儲存編譯範例程式。

7. 執行 **cmake/serializer/samples/remote\_monitoring/remote\_monitoring** 範例應用程式。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0211_2016-->