<properties
	pageTitle="開始使用 Azure IoT 中樞 | Microsoft Azure"
	description="請遵循此教學課程以開始搭配 C# 使用 Azure IoT 中心。"
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# 教學課程：開始使用 IoT 中樞

## 簡介

Azure IoT 中心是一項完全受管理的服務，可讓數百萬個 IoT 裝置和一個應用程式後端進行可靠且安全的雙向通訊。IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至應用程式後端。為了簡化這種情況，Azure IoT 中心提供了可靠的裝置到雲端和雲端到裝置超規模傳訊，使用各裝置安全性認證與存取控制啟用安全通訊，並包含最受歡迎的語言和平台的裝置程式庫。

本教學課程示範如何使用 Azure 入口網站來建立 IoT 中心。它也示範如何在 IoT 中心中建立裝置身分識別、建立傳送裝置到雲端訊息的模擬裝置，並接收這些來自您雲端後端的訊息。

在本教學課程結尾處，您將會有三個 Windows 主控台應用程式：

* **CreateDeviceIdentity**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **ReadDeviceToCloudMessages**，這會讀取裝置到雲端訊息並顯示其內容，以及
* **SimulatedDevice**，這會使用先前建立的裝置身分識別連接到您的 IoT 中心，並每秒傳送裝置到雲端訊息。

> [AZURE.NOTE]IoT 中心透過 Azure IoT 裝置 SDK 提供許多裝置平台和語言的 SDK 支援 (包括 C、Java 和 Javascript)。如需如何將您的裝置與本教學課程中之程式碼連接 (通常是連接到 Azure IoT 中樞) 的逐步指示，請參閱 [Azure IoT 開發人員中心][]。適用於 Java 和 Node 的 Azure IoT 服務 SDK 即將推出。

若要完成本教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015，

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank")。

## 建立 IoT 中心

1. 登入 [Azure Preview 入口網站]。

2. 在 Jumpbar 中，按一下 [新增]，然後按一下 [物聯網]，再按一下 [IoT 中心]。

   	![][1]

3. 在 [新 IoT 中心] 刀鋒視窗中，針對 IoT 中心指定想要的組態。

   	![][2]

    * 在 [名稱] 方塊中，輸入識別 IoT 中心的名稱。驗證**名稱**時，[名稱] 方塊中會出現綠色的核取記號。
    * 依需求變更**定價和縮放層**。本教學課程不需要特定層。
    * 在 [資源群組] 方塊中，建立新的資源群組，或者選取現有的資源群組。如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源](resource-group-portal.md)。
    * 使用 [位置] 指定裝載您 IoT 中心的地理位置。  


4. 一旦建立新的 IoT 中心選項，請按一下 [建立]。可能需要幾分鐘的時間建立 IoT 中心。若要檢查狀態，您可以在「開始面板」上監視進度。或者，您也可以從 [通知] 區段監視進度。

    ![][3]


5. 順利建立 IoT 中心之後，開啟新的 IoT 中心刀鋒視窗、記下 URI，並選取頂端的 [金鑰] 圖示。

   	![][4]

6. 選取稱為 **iothubowner** 的共用存取原則，然後複製並記下正確的刀鋒視窗上的連接字串。

   	![][5]

您的 IoT 中心已建立，且您具備完成本教學課程所需的 URI 和連線字串。

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.	在 Visual Studio 中，以滑鼠右鍵按一下您的解決方案並選取 [設定啟始專案...]。選取 [多個啟始專案]，然後同時針對 **ProcessDeviceToCloudMessages** 和 **SimulatedDevice** App 選取 [啟動] 動作。

   	![][41]

2.	按下 **F5**，您應該會看到應用程式啟動、模擬應用程式傳送訊息，以及處理器應用程式接收訊息。

   	![][42]

## 後續步驟

在本教學課程中，您可以設定新的 IoT 中心、在中心的身分識別登錄中建立裝置身分識別，並使用該身分識別進行傳送裝置到雲端訊息之模擬裝置的程式設計。您可以利用下列教學課程繼續探索 IoT 中心功能和案例：

- [使用 IoT 中樞傳送雲端到裝置訊息][]，示範如何將訊息傳送到裝置，並處理 IoT 中樞所產生的傳送意見反應。
- [處理裝置到雲端訊息][]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案][]，說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

有關 IoT 中心的其他資訊：

* [IoT 中樞概觀][]
* [IoT 中樞開發人員指南][]
* [IoT 中樞指引][]
* [支援的裝置平台和語言][Supported devices]
* [Azure IoT 開發人員中心][]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Azure Preview 入口網站]: https://portal.azure.com/

[使用 IoT 中樞傳送雲端到裝置訊息]: iot-hub-csharp-csharp-c2d.md
[處理裝置到雲端訊息]: iot-hub-csharp-csharp-process-d2c.md
[從裝置上傳檔案]: iot-hub-csharp-csharp-file-upload.md

[IoT 中樞概觀]: iot-hub-what-is-iot-hub.md
[IoT 中樞指引]: iot-hub-guidance.md
[IoT 中樞開發人員指南]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Azure IoT 開發人員中心]: http://www.azure.com/develop/iot

<!---HONumber=Oct15_HO3-->