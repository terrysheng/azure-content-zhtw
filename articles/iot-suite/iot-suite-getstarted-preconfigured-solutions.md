<properties
	pageTitle="開始使用預先設定解決方案 | Microsoft Azure"
	description="遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/03/2015"
     ms.author="dobett"/>

# 教學課程：IoT 預先設定解決方案入門

## 簡介

Azure IoT Suite [預先設定的解決方案][lnk-preconfigured-solutions]結合了多項 Azure IoT 服務，以提供可實作常見 IoT 商務案例的端對端解決方案。

本教學課程示範如何佈建遠端監視預先設定的解決方案。其中也逐步說遠端監視預先設定的解決方案的基本功能。

若要完成本教學課程，您需要作用中的 Azure 訂用帳戶。

> [AZURE.NOTE]  如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk_free_trial]。

## 佈建遠端監視預先設定的解決方案

1.  使用您的 Azure 帳戶認證登入 [azureiotsuite.com][lnk-azureiotsuite]，然後按一下 **+** 建立新的解決方案。

2.  按一下 [遠端監視] 磚上的 [選取]。

3.  輸入遠端監視預先設定的解決方案的 [解決方案名稱]。

4.  驗證您要用來佈建此解決方案的 [區域] 和 [訂用帳戶]。

5.  按一下 [建立解決方案] 開始佈建程序。這通常需要數分鐘的執行時間。

## 檢視遠端監視解決方案儀表板

1.  當佈建完成且預先設定解決方案的磚指示 [就緒] 時，按一下 [啟動] 以在新的索引標籤中開啟遠端監視解決方案入口網站。

    ![][img-launch-solution]

2.  根據預設，此解決方案入口網站會顯示解決方案儀表板。您可以使用左側功能表選取其他檢視。

    ![][img-dashboard]

## 檢視解決方案裝置清單

1.  按一下左側功能表中的 [裝置]，以顯示此解決方案的裝置清單。

    ![][img-devicelist]

2.  您可以看到佈建程序所建立的四個模擬裝置。

3.  按一下裝置清單中的裝置，以檢視裝置詳細資料。

    ![][img-devicedetails]

## 傳送命令至裝置

1.  按一下所選裝置之裝置詳細資料窗格中的 [命令]。

    ![][img-devicecommands]

2.  選取命令清單中的 [PingDevice]。

3.  按一下 [傳送命令]。

4.  您可以在命令歷程記錄中看見命令的狀態。

    ![][img-pingcommand]

## 新增模擬裝置

1.  瀏覽回到裝置清單。

2.  按一下左下角的 [+ 新增裝置] 來新增裝置。

    ![][img-adddevice]

3.  按一下 [模擬裝置] 磚上的 [新增]。

    ![][img-addnew]

4.  選取 [自行定義裝置識別碼]，然後輸入唯一的裝置識別碼名稱，例如 **mydevice\_01**。

5.  按一下 [建立]。

    ![][img-definedevice]

6. 在 [新增模擬裝置] 的步驟 3 中，按一下 [完成] 以返回裝置清單。

7.  您可以在裝置清單中檢視 [執行中] 裝置。

    ![][img-runningnew]

## 檢視和編輯解決方案規則

1.  返回解決方案儀表板並檢視 [警示歷程記錄] 磚。

    ![][img-alarmhistory]

2.  **AlarmTemp** 規則會觸發這些警示。

3.  按一下左側功能表中的 [規則]，以檢視此解決方案的規則。

    ![][img-rules]

4.  預先設定的解決方案會佈建兩個規則。

5.  按一下規則清單中的 [溫度] 以檢視規則屬性。

6.  按一下規則屬性窗格中的 [編輯]。

    ![][img-displayrule]

7.  將 [臨界值] 變更為 30 並讓所有其他屬性維持相同。

8.  按一下 [儲存及檢視規則]。

    ![][img-editrule]

9.  返回 [解決方案儀表板] 中的 [警示歷程記錄] 資料表，然後觀察更新規則所造成的行為變更。

    ![][img-newhistory]

## 後續步驟

您已經建置一個可運作的預先設定解決方案，接下來可以進行下列案例：

-   [自訂預先設定解決方案指南][lnk-customize]
-   [預先設定的預防性維護解決方案概觀][lnk-predictive]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-alarmhistory]: media/iot-suite-getstarted-preconfigured-solutions/alarmhistory.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-displayrule]: media/iot-suite-getstarted-preconfigured-solutions/displayrule.png
[img-editrule]: media/iot-suite-getstarted-preconfigured-solutions/editrule.png
[img-newhistory]: media/iot-suite-getstarted-preconfigured-solutions/newhistory.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-predictive]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0128_2016-->