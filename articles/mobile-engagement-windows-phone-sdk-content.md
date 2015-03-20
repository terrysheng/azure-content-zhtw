<properties 
	pageTitle="Azure Mobile Engagement Windows Phone SDK 整合" 
	description="了解適用於 Azure Mobile Engagement 的 Windows Phone SDK 的內容 " 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />
	
#SDK 內容

本文件列出並描述 SDK 封存的內容。

## `Root` 資料夾

此資料夾包含軟體授權的複本及線上文件的連結。

`documentation.html`：包含適用於 Windows Phone 的 Engagement SDK 的線上文件之連結。

此資料夾也包含授權檔案。

## `/lib` 資料夾

此資料夾包含可在何處取得 Engagement SDK 的相關資訊。

`azuresdk-mobileengagement-windowsphone-X.X.X.nupkg` ：適用於 SDK 整合的 Nuget 封裝。

## `/Resources` 資料夾

此資料夾包含 Engagement 需要的所有資源。您也可以自訂它們，以符合您的應用程式。

`EngagementIconNotification.png` ：顯示在通知左側的品牌圖示。

`EngagementIconOk.png` ：觸達頁面 ApplicationBar 的「確定」圖示。

`EngagementIconCancel.png` ：觸達頁面 ApplicationBar 的 「取消」圖示。

`EngagementIconCloseLight.png` ：適用於 Windows Phone 淺色佈景主題的 Engagement 之觸達通知的「關閉」圖示。

`EngagementIconCloseDark.png` ：適用於 Windows Phone 暗色佈景主題的 Engagement 之觸達通知的「關閉」圖示。

`EngagementConfiguration.xml` ：Engagement 的組態檔，您可以在此自訂 Engagement 的設定 (Engagement 連接字串、報告當機...)。

## `/src/agent` 資料夾

此資料夾包含 EngagementPage。

`EngagementPage.cs` ：會自動向 Engagement 報告活動的頁面之基底類別。

## `/src/reach` 資料夾

最後，您會在此資料夾中找到各頁面的預設 XAML (及它們的 C\# 對應)。

您可以將它們做為您頁面的基礎。遵循註解以了解您所需執行的確切事項。

### TextView 公告

`EngagementDefaultTextViewAnnouncementPage.xaml`

`EngagementDefaultTextViewAnnouncementPage.xaml.cs`

### WebView 公告

`EngagementDefaultWebViewAnnouncementPage.xaml`

`EngagementDefaultWebViewAnnouncementPage.xaml.cs`

### 輪詢

`EngagementDefaultPollPage.xaml`

`EngagementDefaultPollPage.xaml.cs`

### 通知

`EngagementBasicNotificationView.xaml`

`EngagementBasicNotificationView.xaml.cs`

<!--HONumber=47-->
