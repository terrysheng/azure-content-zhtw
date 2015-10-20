<properties
	pageTitle="開始使用預先設定解決方案 | Microsoft Azure"
	description="遵循此教學課程學習如何部署 Azure IoT Suite 預先設定解決方案。"
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="araguila"/>

# 教學課程：IoT 預先設定解決方案入門

## 簡介

Azure IoT Suite 預先設定解決方案將數個 Azure IoT 服務連接在一起，以示範滿足商業物聯網 (IoT) 案例的端對端解決方案。

本教學課程示範如何佈建預先設定解決方案**遠端監視**。其中也說明如何檢視遠端監視預先設定解決方案的基本功能。

若要完成本教學課程，您需要下列項目：

-   有效的 Azure 訂用帳戶。

    如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][]。

## 佈建遠端監視預先設定解決方案

1.  登入 https://www.azureiotsuite.com，然後按一下 [+] 建立新的解決方案。

2.  選取 [遠端監視] 為解決方案類型。

3.  輸入遠端監視預先設定解決方案的 [解決方案名稱]。

4.  驗證您想要佈建此解決方案的 [區域] 和 [訂用帳戶]。

5.  按一下 [建立方案]。

## 檢視遠端監視方案儀表板

1.  當佈建完成且預先設定解決方案的磚指示 [就緒] 時，按一下 [啟動] 以在新的索引標籤中開啟遠端監視解決方案儀表板。

2.  根據預設，左側功能表中已選取 [儀表板]。這是解決方案的儀表板。

## 檢視解決方案裝置清單

1.  按一下左側功能表中的 [裝置]，以瀏覽至此解決方案的裝置清單。

2.  佈建時您會看到已佈建 4 部模擬裝置。

3.  按一下清單中個別的裝置，以檢視該裝置相關的詳細資料。

## 傳送命令至裝置

1.  按一下所選模擬裝置之裝置詳細資料窗格中的 [傳送命令]。

2.  選取命令清單中的 [PingDevice]。

3.  按一下 [傳送命令]。

4.  在命令於 [命令歷程記錄] 中出現時檢視命令的狀態。

## 新增模擬裝置

1.  按一下 [←] (返回箭號) 返回裝置清單。

2.  按一下左下角的 [+ 新增裝置] 來新增裝置。

3.  按一下 [模擬裝置] 的 [新增]。

4.  選取 [自行定義裝置識別碼]，然後新增唯一的 [裝置識別碼] 名稱。

5.  按一下 [建立]。

6.  按一下 [←] (返回箭號) 返回裝置清單。

7.  在裝置清單中查看裝置「執行」。

## 檢視和編輯解決方案規則

1.  請注意 [解決方案儀表板] 中的 [警示歷程記錄] 資料表。

2.  這些警示是由 [規則] 中定義的 **AlarmTemp** 輸出觸發。

3.  按一下左側功能表中的 [規則]，以瀏覽至此解決方案的規則。

4.  佈建時您會看到已經啟用 1 項規則。

5.  按一下清單中的規則來檢視該規則相關的詳細資料。

6.  按一下規則屬性窗格中的 [編輯]。

7.  將 [臨界值] 變更為 30 並維持所有其他屬性相同。

8.  按一下 [儲存及檢視規則]。

9.  返回 [解決方案儀表板] 中的 [警示歷程記錄] 資料表，並注意因更新規則而變更的觸發程序。

## 後續步驟

您已經建置一個可運作的預先設定解決方案，接下來可以進行下列案例：

-   [自訂預先設定解決方案指南][]

-   [IoT Suite 概觀][]

[Azure 免費試用]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fzh-TW%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20target=

[自訂預先設定解決方案指南]: https://azure.microsoft.com/documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions/
[IoT Suite 概觀]: https://azure.microsoft.com/documentation/articles/iot-suite-overview/

<!---HONumber=Oct15_HO3-->