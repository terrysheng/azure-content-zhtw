<properties
   pageTitle="在 mbed 上使用 C 連接裝置 | Microsoft Azure"
   description="描述如何在 mbed 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
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
   ms.date="11/10/2015"
   ms.author="dobett"/>


# 將裝置連接至 IoT 套件遠端監視預先設定方案

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 在 mbed 上建置並執行範例 C 的解決方案

下列指示描述將[啟用 mbed 的 Freescale FRDM-K64F][lnk-mbed-home] 裝置連接至遠端監視解決方案的步驟。

### 將裝置連接到網路和桌上型電腦

1. 使用乙太網路纜線將 mbed 裝置連接到您的網路。這是必要的步驟，因為範例應用程式需要透過網際網路存取。

2. 請參閱 [mbed 使用者入門][lnk-mbed-getstarted]將您的 mbed 裝置連接至桌上型電腦。

3. 如果桌上型電腦執行的是 Windows，請參閱 [PC 組態][lnk-mbed-pcconnect]設定序列埠存取 mbed 裝置。

### 建立 mbed 專案並匯入範例程式碼

1. 在您的 Web 瀏覽器中，移至 mbed.org [開發人員網站](https://developer.mbed.org/)。如果您還沒有註冊，您會看到建立新帳戶的選項 (它是免費的)。否則，請使用您的帳戶認證登入。然後按一下頁面右上角的 [編譯器]。如此應該會帶您前往工作區管理介面。

2. 請確定您使用的硬體平台出現在視窗的右上角，或按一下右手邊的圖示來選取您的硬體平台。

3. 在主功能表上按一下 [匯入]。然後按一下 [按一下這裡] 從 mbed 地球標誌旁的 URL 連結匯入。

    ![][6]

4. 在快顯視窗中，輸入範例程式碼的連結 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

    ![][7]

5. 您可以在匯入這個專案的 mbed 編譯器中看到匯入的各種程式庫。某些是由 Azure IoT 小組提供和維護 ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/)、[proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/))，而有些則是可以在 mbed 程式庫目錄中取得的協力廠商程式庫。

    ![][8]

6. 開啟 remote\_monitoring\\remote\_monitoring.c，在檔案中尋找下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. 以您的裝置資料取代 [Device Id] 和 [Device Key]。

8. 您可以使用 IoT 中心主機名稱裝置資料來填寫 IoTHub 名稱和 IoTHub 後置詞。例如，若您的 IoT 中樞主機名稱是 Contoso.azure-devices.net，Contoso 會 是您的 IoT 中樞名稱，而它之後的所有項目就是後置詞：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### 建置並執行程式

1. 按一下 [編譯] 來建置程式。您可以安全地略過任何警告，但如果建置產生錯誤，請修正錯誤後再繼續。

2. 如果建置成功，會產生具有您的專案名稱的 .bin 檔案。將 .bin 檔案複製到裝置。將 .bin 檔案儲存到裝置會導致重設對裝置的目前終端機工作階段。重新連接時，請再次手動重設終端機，或啟動新的終端機。這可讓 mbed 裝置重設並開始執行程式。

3. 使用 SSH 用戶端應用程式 (例如 PuTTY) 連接至裝置。您可以判斷您的裝置會使用的序列埠，藉由檢查 Windows 裝置管理員。


4. 在 PuTTY 中，按一下 [序列] 連接類型。裝置最有可能以 115200 速度連接，因此請在 [速度] 方塊中輸入該值。然後按一下 [開啟]：

    ![][11]

5. 程式開始執行。連線時如果程式沒有自動啟動，您可能必須重設面板 (按 CTRL + Break 或按面板的重設按鈕)。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

<!---HONumber=AcomDC_0128_2016-->