<properties 
	pageTitle="Azure Mobile Engagement Windows 市集 SDK 內容" 
	description="適用於 Azure Mobile Engagement 的 Windows 市集 SDK 之最新的更新與程序" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#SDK 內容

本文件列出並描述 SDK 封存的內容。

## `Root` 資料夾

此資料夾包含軟體授權的複本及線上文件的連結。

`documentation.html` ：包含適用於 Windows 8 C\# Metro 應用程式之 Engagement SDK 線上文件的連結。

此資料夾也包含授權檔案。

## `/lib` 資料夾

此資料夾包含可在何處取得 Engagement SDK 的相關資訊。

`azuresdk-mobileengagement-windows-X.X.X.nupkg` ：適用於 SDK 整合的 Nuget 封裝。

## `/Resources` 資料夾

此資料夾包含 Engagement 需要的所有資源。您也可以自訂它們，以符合您的應用程式。

`EngagementConfiguration.xml` ：Engagement 的組態檔，您可以在此自訂 Engagement 的設定 (Engagement 連接字串、報告當機...)。

### /html 資料夾

`EngagementNotification.html` ：「通知」 `` 的 Web 檢視 html 設計。

`EngagementAnnouncement.html` ：「宣告」 ``的 Web 檢視 html 設計。

### /images 資料夾

`EngagementIconNotification.png` ：顯示在通知左側的品牌圖示。

`EngagementIconOk.png` ：動作或驗證按鈕之 reach 內容的 [確定] `` 圖示。

`EngagementIconNOK.png` ：Reach 內容頁面之驗證按鈕停用時的 `NOK` 圖示。

`EngagementIconClose.png` ：隱藏按鈕之 reach 通知和內容的 [關閉] `` 圖示。

### /overlay 資料夾

`EngagementOverlayAnnouncement.xaml` ：「宣告」 ``的 xaml 設計。

`EngagementOverlayAnnouncement.xaml.cs` ： `EngagementOverlayAnnouncement.xaml` 已連結的程式碼。

`EngagementOverlayNotification.xaml` ：「通知」 `` 的 xaml 設計。

`EngagementOverlayNotification.xaml.cs` ： `EngagementOverlayNotification.xaml` 已連結的程式碼。

`EngagementPageOverlay.cs` ：「重疊」 `` 宣告和通知顯示的程式碼。

## `/src/agent` 資料夾

此資料夾包含 EngagementPage。

`EngagementPage.cs` ：會自動向 Engagement 報告活動的頁面之基底類別。

<!--HONumber=47-->
