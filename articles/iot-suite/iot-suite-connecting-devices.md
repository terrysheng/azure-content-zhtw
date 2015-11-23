<properties
   pageTitle="將裝置連接到預先定義的解決方案 | Microsoft Azure"
   description="使用採用溫度和溼度資料的範例，描述如何將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   documentationCenter="na"
   authors="hegate"
   manager="timlt"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="hegate"/>


# 將您的裝置連接至 Azure IoT Suite 遠端監視方案


## 案例概觀

在此範例中，我們有三個模擬的資料來源：外部溫度、內部溫度和濕度。為了簡單起見，我們不會使用真實的感應器 (用戶端程式碼中自行產生資料)，但我們建議您在下一個步驟中連接您喜好的感應器和傳送實際資料。如需詳細資訊，請瀏覽「延伸您的方案」一節上連結的資源。

## 必要條件

### 佈建您的 IoT 套件

如果您尚未佈建您的遠端監視預先設定方案，您可以在[這裡](http://www.microsoft.com/zh-TW/server-cloud/internet-of-things/getting-started.aspx)進行佈建。


### 在遠端監視方案中佈建您的裝置
```
Note: if you have already provisioned a device on your solution, you can skip this step.
```
若要讓您的裝置連接到預先設定的方案，您必須從儀表板取得裝置認證。這將用於您的用戶端應用程式中，如此就可以識別您的裝置。依照此步驟

1.  在儀表板的左下角按一下 [新增裝置]。

    ![][1]

2.  在自訂裝置上，按一下 [新增] 按鈕。

    ![][2]

3.  輸入名稱 (例如 realdevice1) 來選擇您自己的裝置識別碼，然後按一下 [檢查識別碼] 以確定該名稱尚未使用。

    ![][3]

5. 請複製您提供的認證 (裝置識別碼、IoT 中樞主機名稱和裝置金鑰)。稍後在您的用戶端應用程式將會需要這些資訊，才能將裝置連接至方案。

    ![][4]

6. 請確定您的裝置在裝置區段上正確顯示。狀態會是「擱置中」。在建立裝置與雲端的連線之前，這是預期的行為。

    ![][5]

[1]: ./media/iot-suite-connecting-devices/suite0.png
[2]: ./media/iot-suite-connecting-devices/suite1.png
[3]: ./media/iot-suite-connecting-devices/suite2.png
[4]: ./media/iot-suite-connecting-devices/suite3.png
[5]: ./media/iot-suite-connecting-devices/suite5new.png

現在選擇您想要用來繼續您的範例的語言。在本教學課程中，我們建立了 C 和 node.js 的範例程式碼，但您也可以在 C Sharp 和 Java 中實作它。

## 使用 C 將裝置資料傳送到遠端監視方案


### 在 Linux 上執行您的裝置

1. 設定您的環境：如果您之前從未使用過我們的裝置 SDK，請在[這裡](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux)了解如何設定您的 Linux 環境。

1. 在文字編輯器中開啟檔案 **c/serializer/samples/serializer/remote\_monitoring.c**。

2. 在檔案中，找到下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
    
3. 以您的裝置資料取代 [Device Id] 和 [Device Key]。

4. 您可以使用 IoT 中樞主機名稱裝置資料來填寫 IoTHub 名稱和 IoTHub 後置詞。若要這樣做，您需要分割為 IoTHub + IoTHubSuffix。例如：若您的 IoT 中樞主機名稱是 "Contoso.azure-devices.net"，"Contoso" 會是 IoTHub 名稱，而其餘為後置詞。它看起來應該如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

5. 儲存您的變更並建置範例。若要建立您的範例，可以執行 **c/build\_all/linux** 目錄中的 build.sh 指令碼。

6. 執行 **c/serializer/samples/remote\_monitoring/linux/remote\_monitoring** 範例應用程式。

#### 將已註冊的裝置和資料視覺化

7. 回到遠端監視方案儀表板。您應該會看到在 [裝置] 清單上，裝置的狀態已變更為「執行中」。

    ![][18]

8. 按一下儀表板以查看傳入的資料。此範例會設定為傳送 50 個單位的內部溫度、55 個單位的外部溫度和濕度 50。請注意，儀表板預設只會顯示溫度和溼度。

8. 現在，前往[命令與控制](#command)區段，以了解如何從遠端監視方案變更您裝置上的溫度。


### 在 Windows 上執行您的裝置


1. 設定您的環境：如果您之前從未使用過我們的裝置 SDK，請在[這裡](https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows)了解如何設定您的 Windows 環境。

1. 啟動 Visual Studio 2015 的新執行個體。開啟在您儲存機制本機複本 **c\\serializer\\build\\windows** 資料夾中的 **remote\_monitoring.sln** 方案。

2. 在 Visual Studio 的 [方案總管] 中瀏覽至 [samples] 資料夾。開啟 **remote\_monitoring** 專案中的 **remote\_monitoring.c** 檔案。

2. 在檔案中，找到下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. 以您的裝置資料取代 [Device Id] 和 [Device Key]。

4. 您可以使用 IoT 中心主機名稱裝置資料來填寫 IoTHub 名稱和 IoTHub 後置詞。若要這樣做，您需要以此方式分割它：

    若您的 IoT 中心主機名稱是 Contoso.azure-devices.net，Contoso 會 是您的 IoTHub 名稱，而它之後的所有項目會是後置詞。它看起來應該如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. 在 [方案總管] 中，以滑鼠右鍵按一下 **remote\_monitoring** 專案，按一下 [偵錯]，然後按一下 [開始新執行個體] 來建置和執行範例。在應用程式傳送裝置到雲端訊息至 IoT 中心時，主控台會顯示訊息。

#### 將已註冊的裝置和資料視覺化

7. 回到遠端監視方案儀表板。您應該會看到在 [裝置] 清單上，裝置的狀態已變更為「執行中」。

    ![][18]

8. 按一下儀表板以查看傳入的資料。此範例會設定為傳送 50 個單位的內部溫度、55 個單位的外部溫度和濕度 50。請注意，儀表板預設只會顯示溫度和溼度。

8. 現在，前往[命令與控制](#command)區段，以了解如何從遠端監視方案變更您裝置上的溫度。

### 在 mbed 上執行您的裝置

下列指示描述將[啟用 mbed 的 Freescale FRDM-K64F](https://developer.mbed.org/platforms/FRDM-K64F/) 裝置連接至 Azure IoT 中樞的步驟。


#### 需求

- 需要的硬體：[啟用 mbed 的 Freescale K64F](https://developer.mbed.org/platforms/FRDM-K64F/) 或類似裝置。

#### 連接裝置

- 使用乙太網路纜線將機板連接到您的網路。這個步驟為必要項目，因為範例仰賴於網際網路存取。

- 使用 micro-USB 纜線將裝置插入電腦。請務必將纜線連接至裝置上正確的 USB 連接埠，如＜快速入門＞一節中[這裡](https://developer.mbed.org/platforms/frdm-k64f/)的圖片所示。

- 請遵循 [mbed 手冊上的指示](https://developer.mbed.org/handbook/SerialPC)從開發電腦設定與您裝置的序列連線。如果您在 Windows 上，請安裝[這裡](http://developer.mbed.org/handbook/Windows-serial-configuration#1-download-the-mbed-windows-serial-port)的 Windows 序列埠驅動程式。

#### 建立 mbed 專案並匯入範例程式碼

- 在您的 Web 瀏覽器中，移至 mbed.org [開發人員網站](https://developer.mbed.org/)。如果您還沒有註冊，您會看到建立新帳戶的選項 (它是免費的)。否則，請使用您的帳戶認證登入。然後按一下頁面右上角的 [編譯器]。如此應該會帶您前往工作區管理介面。

- 請確定您使用的硬體平台出現在視窗的右上角，或按一下右手邊的圖示來選取您的硬體平台。

- 在主功能表上按一下 [匯入]。然後按一下 [按一下這裡] 從 mbed 地球標誌旁的 URL 連結匯入。

    ![][6]

- 在快顯視窗中，輸入範例程式碼的連結 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/

    ![][7]

- 您可以在匯入這個專案的 mbed 編譯器中看到匯入的各種程式庫。某些是由 Azure IoT 小組提供和維護 ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[iothub\_http\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_http_transport/)、[proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/))，而有些則是可以在 mbed 程式庫目錄中取得的協力廠商程式庫。

    ![][8]

- 開啟 remote\_monitoring\\remote\_monitoring.c，在檔案中尋找下列程式碼：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

3. 以您的裝置資料取代 [Device Id] 和 [Device Key]。

4. 您可以使用 IoT 中心主機名稱裝置資料來填寫 IoTHub 名稱和 IoTHub 後置詞。若要這樣做，您需要以此方式分割它：

    若您的 IoT 中心主機名稱是 Contoso.azure-devices.net，Contoso 會 是您的 IoTHub 名稱，而它之後的所有項目會是後置詞。它看起來應該如下所示：

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

#### 建置並執行程式

- 按一下 [編譯] 來建置程式。您可以安全地略過任何警告，但如果建置產生錯誤，請修正錯誤後再繼續。

- 如果建置成功，會產生具有您的專案名稱的 .bin 檔案。將 .bin 檔案複製到裝置。將 .bin 檔案儲存到裝置會導致重設對裝置的目前終端機工作階段。重新連接時，請再次手動重設終端機，或啟動新的終端機。這可讓 mbed 裝置重設並開始執行程式。

- 使用 SSH 用戶端應用程式 (例如 PuTTY) 連接至裝置。您可以判斷您的裝置會使用的序列埠，藉由檢查 Windows 裝置管理員。


- 在 PuTTY 中，按一下 [序列] 連接類型。裝置最有可能以 115200 速度連接，因此請在 [速度] 方塊中輸入該值。然後按一下 [開啟]：

    ![][11]

程式開始執行。連線時如果程式沒有自動啟動，您可能必須重設面板 (按 CTRL + Break 或按面板的重設按鈕)。

#### 將已註冊的裝置和資料視覺化

7. 回到遠端監視方案儀表板。您應該會看到在 [裝置] 清單上，裝置的狀態已變更為「執行中」。![][18]

8. 按一下儀表板以查看傳入的資料。此範例會設定為傳送 50 個單位的內部溫度、55 個單位的外部溫度和濕度 50。請注意，儀表板預設只會顯示溫度和溼度。

8. 現在，前往[命令與控制](#command)區段，以了解如何從遠端監視方案變更您裝置上的溫度。



[6]: ./media/iot-suite-connecting-devices/mbed1.png
[7]: ./media/iot-suite-connecting-devices/mbed2a.png
[8]: ./media/iot-suite-connecting-devices/mbed3a.png
[9]: ./media/iot-suite-connecting-devices/suite6.png
[10]: ./media/iot-suite-connecting-devices/mbed5a.png
[11]: ./media/iot-suite-connecting-devices/mbed6.png
[12]: ./media/iot-suite-connecting-devices/mbed7.png

若要了解如何執行命令和控制，請移至本教學課程下方的該區段。

## 使用 node.js 將裝置資料傳送到遠端監視方案



-   在我們的 azure-iot-sdks 儲存機制中，找出下列檔案：packages.json (/node/common 下) 和 remote\_monitoring.js (node/device/samples/ 下)。將它們複製到您的裝置並將它們放在相同的資料夾。

- 開啟 remote-monitoring.js 檔案並尋找下列變數：


   ```
   var deviceID = "[DeviceID]";
   var deviceKey = "[Device Key]";
   var hubName = "[IoT Hub Name]";
   var hubSuffix = "[IoT Hub Suffix i.e azure-devices.net]";
   ```

-  以您的裝置資料取代 [Device Id] 和 [Device Key]。

-  您可以使用 IoT 中心主機名稱裝置資料來填寫 IoTHub 名稱和 IoTHub 後置詞。若要這樣做，您需要以此方式分割它：

   若您的 IoT 中心主機名稱是 Contoso.azure-devices.net，Contoso 會 是您的 IoTHub 名稱，而它之後的所有項目會是後置詞。它看起來應該如下所示：


   ```
   var deviceID = "mydevice";
   var deviceKey = "mykey";
   var hubName = "Contoso";
   var hubSuffix = "azure-devices.net";
   ```


- 儲存檔案。在目的地資料夾上執行下列命令：

```
npm install
node .
```

3.  以您在上一個步驟收集的資訊取代每個變數。儲存變更。


4. 開啟 shell (Linux) 或 Node.js 命令提示字元 (Windows) 並瀏覽至 **node\\samples** 資料夾。然後使用下列命令執行範例應用程式：

    ```
    node simple_sample_remotemonitoring.js
    ```

#### 將您的裝置與內送資料視覺化

6. 在預先設定的方案入口網站上，按一下裝置區段以確保裝置的狀態已變更為「執行中」，並且您可以看到所有製造商資料。

7. 在儀表板上按一下，然後在「要檢視的裝置」上選取您的裝置。您現在應該會看到您的遙測資料正在遠端監視方案上進行監視。


## <a name="command"></a>從儀表板命令並控制您的裝置

現在，您的裝置已連線，並從裝置傳送自我產生的溫度資料，您還可以從 IoT 中心遠端命令並控制裝置。您可以實作符合您的商務應用程式的多個類型命令。在此案例中，我們已實作溫度的變更，就好像有需要從方案控制它。若要傳送此命令，您應該：

-  在 [裝置] 清單上按一下您的裝置識別碼 (您可以在左側功能表上找到裝置區段)。

    ![][13]

- 在顯示裝置詳細資料的右側功能表中按一下 [傳送命令]


- 選取您想要執行的命令：在此案例中，我們選擇「設定溫度」，因為我們想要變更裝置設定的溫度。選取該命令並選擇溫度值。按一下 [傳送命令]，而新的溫度將推送到裝置。注意：您會在命令歷程記錄中看到命令的結果是「擱置中」。這是因為，為了簡單起見，此範例尚未在裝置中實作任何邏輯以回應 IoT 中心。您可以延伸方案來達成此目的。

    ![][14]

- 返回儀表板，並確定更新的資料正在傳入。您應該會看到遙測歷程記錄中顯示溫度的更新統計資料以及新資料。




[13]: ./media/iot-suite-connecting-devices/suite4.png
[14]: ./media/iot-suite-connecting-devices/suite7-1.png
[15]: ./media/iot-suite-connecting-devices/suite8a.png
[16]: ./media/iot-suite-connecting-devices/mbed4a.png
[17]: ./media/iot-suite-connecting-devices/suite9.png
[18]: ./media/iot-suite-connecting-devices/suite10.png


## 後續步驟

有多種方式可以延伸此範例的功能：連接真正的感應器到您的裝置來傳送實際日期、實作命令與控制功能等。請使用有關如何延伸遠端監視方案的[指南](articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md)來進一步了解。

<!---HONumber=Nov15_HO3-->