<properties 
	pageTitle="Windows 通用 app SDK 版本資訊" 
	description="Azure Mobile Engagement - Windows 通用 app SDK 版本資訊"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

#Windows 通用 app SDK 版本資訊

##3\.3.1 (02/18/2016)

-   修正 Web 通知的 HTML 內容與 SDK HTML 頁面之間的衝突。
-   穩定性改進。

##3\.3.0 (01/22/2016)

-   修正在格式化以發行模式執行的 UWP 應用程式時當機的問題。
-   修正 Universal 8.1 應用程式的通知中 1 像素邊界的問題。
-   動作 URL 現在可使用 ms-appx 及 ms-appdata 配置。
-   穩定性改進。

##3\.2.0 (11/20/2015)

-   新增對 Windows 10 通用 Windows 平台應用程式的支援。
-   新增推播通到共用功能，以修正通道衝突 (現已與 Azure 通知中樞相容)。
-   修正初始化之後立即要求裝置 ID 時發生的當機。
-   主控台記錄檔改善。
-   修正解析未處理的例外狀況時發生的當機。

##3\.1.0 (05/21/2015)

-   Mobile Engagement 裝置識別碼現在是根據在安裝時產生的 GUID。

##3\.0.1 (2015/04//29)

-   已修復影響 SDK 在部分 Windows Phone WinRT 應用程式上的初始化錯誤。

##3\.0.0 (2015/04/03)

-   導入了跨平台 app (Windows 和 Windows Phone WinRT) 的 Mobile Engagement SDK。
-   預設通知圖示已更新。
-   按一下通知時，會傳回系統通知動作回饋。
-   已修復系統通知有時在點選後於應用程式內重播的狀況。

##2\.0.0 (2015/02/17)

-   Azure Mobile Engagement 的最初發行版本
-   連接字串組態取代 appId/sdkKey 組態。
-   增強安全性。

 

<!---HONumber=AcomDC_0302_2016-------->