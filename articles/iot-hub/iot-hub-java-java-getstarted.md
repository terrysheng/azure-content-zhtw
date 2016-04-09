<properties
	pageTitle="開始使用適用於 Java 的 Azure IoT 中樞 | Microsoft Azure"
	description="請遵循此教學課程以開始搭配 Java 使用 Azure IoT 中樞。"
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# 開始使用適用於 Java 的 Azure IoT 中樞

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至解決方案後端。若要解決這個問題，IoT 中樞：

- 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊。
- 使用每一裝置的安全性認證和存取控制來啟用安全通訊。
- 包括適用於最受歡迎的語言和平台的裝置程式庫。

本教學課程說明如何：

- 使用 Azure 入口網站來建立 IoT 中樞。
- 在您的 IoT 中樞中建立裝置身分識別。
- 建立將遙測傳送到雲端後端的模擬裝置。

在本教學課程結尾處，您將會有三個 Java 主控台應用程式：

* **create-device-identity**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **read-d2c-messages**，其中顯示模擬的裝置所傳送的遙測。
* **simulated-device**，這會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並每秒傳送遙測訊息。

> [AZURE.NOTE] 文章 [IoT 中樞 SDK][lnk-hub-sdks] 提供可讓您可以用來建置兩個應用程式，以在裝置和您的方案後端上執行的各種 SDK 的相關資訊。

若要完成此教學課程，您需要下列項目：

+ Java SE 8。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。

+ Maven 3。<br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Maven。

+ 使用中的 Azure 帳戶。<br/>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

## 建立 IoT 中樞

您需要為您的模擬裝置建立 IoT 中樞以供連接。下列步驟顯示如何使用 Azure 入口網站完成這項工作。

1. 登入 [Azure 入口網站][lnk-portal]。

2. 在 Jumpbar 中，按一下 [新增]，然後按一下 [物聯網]，再按一下 [Azure IoT 中樞]。

    ![][1]

3. 在 [IoT 中樞] 刀鋒視窗中，選擇 IoT 中樞的組態。

    ![][2]

    * 在 [名稱] 方塊中，輸入 IoT 中樞的名稱。如果**名稱**有效且可用，[名稱] 方塊中會出現綠色的核取記號。
    * 選取一個**價格和級別層**。本教學課程不需要特定層。
    * 在**資源群組**中，建立新的資源群組，或選取現有的資源群組。如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源][lnk-resource-groups]。
    * 在 [位置] 中，選取要裝載您的 IoT 中樞的位置。  

4. 選擇好 IoT 中樞組態選項時，按一下 [建立]。Azure 建立您的 IoT 中樞可能需要幾分鐘的時間。若要檢查狀態，您可以在「開始面板」或 [通知] 面板中監視進度。

    ![][3]

5. 成功建立 IoT 中樞時，請開啟新 IoT 中樞的刀鋒視窗，記下 **Hostname**，然後按一下 [金鑰] 圖示。

    ![][4]

6. 按一下 [iothubowner] 原則，然後複製並記下 **iothubowner** 刀鋒視窗中的連接字串。

    ![][5]

7. 按一下 [IoT 中樞] 刀鋒視窗上的 [設定]，然後按一下 [設定] 刀鋒視窗上的 [傳訊]。記下 [傳訊] 刀鋒視窗上的**事件中樞相容名稱**和**事件中樞相容端點**。在建立 **read-d2c-messages** 應用程式時需要用到這些值。

    ![][6]

您現在已經建立 IoT 中樞，並擁有完成本教學課程其餘部分所需的 IoT 中樞主機名稱、IoT 中樞連接字串、事件中樞相容名稱和事件中樞相容端點。

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1. 在 read-d2c 資料夾的命令提示字元中，執行下列命令以開始監視 IoT 中樞：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. 在 simulated-device 資料夾的命令提示字元中，執行下列命令以開始將遙測資料傳送至 IoT 中樞：

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

## 後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置身分識別。您會將此裝置身分識別用於模擬的裝置，該裝置會將裝置對雲端訊息傳送至中樞，並建立另一個應用程式，以顯示中樞所接收的訊息。您可以利用下列教學課程繼續探索 IoT 中樞功能和其他 IoT 案例：

- [使用 IoT 中樞傳送雲端到裝置訊息][lnk-c2d-tutorial]，示範如何將訊息傳送到裝置，並處理 IoT 中樞所產生的傳送意見反應。
- [處理裝置到雲端訊息][lnk-process-d2c-tutorial]，示範如何可靠地處理來自裝置的遙測和互動式訊息。
- [從裝置上傳檔案][lnk-upload-tutorial]說明使用雲端到裝置訊息來幫助從裝置上傳檔案的模式。

<!-- Images. -->
[1]: ./media/iot-hub-java-java-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-java-java-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-java-java-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-java-java-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-java-java-getstarted/create-iot-hub5.png
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0323_2016-->