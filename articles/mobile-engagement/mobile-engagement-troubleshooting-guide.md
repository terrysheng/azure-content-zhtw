<properties 
   pageTitle="Azure Mobile Engagement 疑難排解指南" 
   description="Azure Mobile Engagement 的疑難排解指南" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="10/22/2015"
   ms.author="piyushjo"/>

# Azure Mobile Engagement - 疑難排解指南

## 簡介
下列疑難排解指南將協助您了解一些常見問題的根本原因，並可讓您自行疑難排解。

## 一般

一般而言，請務必確定下列事項：

1. 確定您已經如[使用者入門教學課程](mobile-engagement-windows-store-dotnet-get-started.md)中所述完成整合作業所需的所有步驟
2. 您使用的是最新版的平台 SDK。 
3. 同時在實際裝置和模擬器上進行測試，因為有些問題是只有模擬器才有的。 
4. 您並未達到 Mobile Engagement 的任何限制/節流，其相關內容記載在[這裡](azure-subscription-service-limits.md)。
5. 如果您無法連接到 Mobile Engagement 服務後端，或發現未能持續載入資料，則請檢查[這裡](https://azure.microsoft.com/status/)，確定目前沒有發生服務事件

## 「監視」問題

### 我沒有在 [監視] 索引標籤上看到我的裝置
[監視] 索引標籤會即時顯示與您的 Mobile Engagement 平台連接的裝置。如果您正在模擬器和裝置上進行偵錯，您應該會在這裡看到至少一個工作階段。如果已散發應用程式，您會看到 [使用中工作階段] 量測計即時反映出與平台連接的裝置。

如果您沒有在 [監視] 索引標籤上看見您的裝置，表示可能有 SDK 整合問題。您可以採取的某些常見疑難排解步驟如下：

1. 確定您有在行動應用程式中使用正確的連接字串，且其來自 [SDK 金鑰] 區段而非 [API 金鑰] 區段。連接字串會將您的行動應用程式連接到 Mobile Engagement 應用程式的執行個體，您會在其中的 [監視] 索引標籤上看到您的裝置。 
2. 針對 Windows 平台 - 如果您的頁面會覆寫 `OnNavigatedTo` 方法，請確定會呼叫 `base.OnNavigatedTo(e)`。
3. 如果您要將 Mobile Engagement 整合到現有的行動應用程式，則您也可以查看[這裡](mobile-engagement-windows-store-integrate-engagement.md)的進階整合步驟，確保您沒有遺漏任何步驟
4. 視您正在使用的平台而定，透過以 EngagementActivity 覆寫頁面來確定您正在傳送至少一個畫面/活動，詳細內容如[使用者入門教學課程](mobile-engagement-windows-store-dotnet-get-started.md)所述。

### 我已經中斷連接或關閉我的應用程式/模擬器，但仍看到 [監視] 索引標籤中顯示工作階段。 
如果您是目前連接到平台的唯一使用者，且您正使用模擬器來開啟應用程式，原因可能是模擬器的問題。一般而言，您必須確實回到模擬器上的 [首頁] 畫面，應用程式工作階段才會成功中斷連接。此外，在 Windows 平台上，當您使用 Visual Studio 進行偵錯時，您可能需要確定您有在 Visual Studio 中移至 [生命週期事件] 功能表列，然後按一下 [暫停] 來真正關閉工作階段。如需詳細資訊，請參閱 [Windows 教學課程](mobile-engagement-windows-store-dotnet-get-started.md)。

## 「分析」問題

### 我在 [分析] 索引標籤上看不到任何資料/重新整理的資料 
[分析] 資料會定期重新計算，最多可能需要 24 小時才能完成此重新整理作業。這並非即時資料，您會在 24 小時的期間內看到它進行重新整理。不過，請務必明確以 `EngagementActivity` 覆寫至少一頁或呼叫 `SendActivity`確定您會傳送至少一個畫面或活動到平台後端。

### 我在 [分析] 索引標籤上看到不正確的裝置擷取日期/時間
[分析] 的時間週期是根據使用者的裝置設定的日期。因此請確定裝置已正確設定日期。

## 「區段」問題

### 我建立了一個區段，但它呈現灰色或未顯示任何資料
目前不會即時建立區段。在彙總分析資料時會同時計算區段，因此最多可能需要 24 小時的時間。請稍後再回來查看，但在此同時，您也應該確定您的行動應用程式確實有傳送您據以形成區段的資料。例如，如果有事件指出並沒有行動裝置傳送 'foo'，則以 EventName = foo 作為準則所建立的區段不會有任何區段資料。您也應該檢查您的 SDK 整合情形，確定您的行動應用程式會正確地傳送資料。

## 「送達」或推播通知問題

### 我的推播訊息未傳遞 

1. 請先嘗試將通知傳送至測試裝置，確定所有元件 (行動應用程式、SDK 和服務) 都已正確連接且能夠傳送推播通知。 
2. 請一律先透過既未排程也未指定任何對象準則的活動來傳送最簡單的「應用程式外通知」。同樣地，這也是為了證明通知連線有正常運作。 
3. 如果您無法傳送應用程式內通知，則先嘗試傳送應用程式外通知也是不錯的第一步。 
4. 確定您的行動應用程式已正確設定「原生推播」。視平台而定，它會涉及金鑰 (Android、Windows) 或憑證 (iOS)。請參閱[使用者介面設定](mobile-engagement-user-interface-settings.md)
5. 使用者也可能透過行動作業系統封鎖了應用程式外通知，因此請確定不是這種情況。 
6. 確定您未在 [送達] 活動的 [活動] 區段中設定 [忽略對象，將透過 API 傳送推播給使用者] 選項，因為這將確保只可以透過 API 傳送推播通知。 
7. 確定您在測試推播活動時有同時使用透過 WIFI 與電信商網路連線的裝置，以排除網路連線是問題根源的可能性。
8. 確定裝置/模擬器上的系統日期/時間是否正確，因為裝置未同步也會干擾推播通知服務傳遞通知的能力。 

以下有更多平台特有的疑難排解指示：

1. **iOS** 

	- 確定憑證有效且未到期，仍可用於 iOS 推播通知。 
	- 確定您有在 Mobile Engagement 應用程式中正確設定 [生產] 憑證。 
	- 請確定您要測試的是*真正的實體裝置。* iOS 模擬器無法處理推播訊息。
	- 確定已在行動應用程式中正確設定 [Bundle Identifier]。請參閱[這裡](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)的指示
	- 測試時，請使用行動佈建設定檔中的「臨機」發佈。若您的應用程式使用「偵錯」進行編譯，您將無法收到通知

2. **Android**

	- 確定您已在行動應用程式的 AndroidManifest.xml 檔案中指定正確的專案編號，且後面接著 \\n 字元。 
	
	    	<meta-data android:name="engagement:gcm:sender" android:value="************\n" />
	    
	- 確定您未在 Android 資訊清單檔案中遺漏或錯誤設定任何權限。
	- 如果有收到系統通知，但未收到應用程式內通知，請檢閱[指定通知區段的圖示](mobile-engagement-android-get-started.md)，因為您可能未在 Android 資訊清單檔案中指定正確的圖示。 
	- 如果您要傳送 BigPicture 通知，則請確定如果您有外部影像伺服器，這些伺服器需要能夠支援 HTTP "GET" 和 "HEAD"。

3. **Windows**
	
	- 確定您已將應用程式關聯到有效的 Windows 市集應用程式。在 Visual Studio 中 - 您必須以滑鼠右鍵按一下專案並選取 [將應用程式與市集建立關聯] 選項，然後選取您在 Windows 市集中建立的應用程式。這個 Windows 市集應用程式，應該與您用來取得原生推播認證以在 Mobile Engagement 入口網站中進行設定的是同一個。
	- 如果有透過 `EngagementOverlay` 整合收到應用程式以外的推播通知，但未收到應用程式內通知，則請確定您的頁面中有根 Grid 元素。EngagementOverlay 會使用它在 xaml 檔案中找到的第一個 “Grid” 元素，在您的頁面上新增兩個 Web 檢視。如果您想要找出 Web 檢視的設定位置，您可以定義如下名為 "EngagementGrid" 的 Grid，不過，您必須確保高度和寬度足以容納兩個後續的 Web 檢視，這兩個檢視會將通知和下列公告顯示為應用程式內通知：
		
			<Grid x:Name="EngagementGrid"></Grid>

### 我建立了推播通知/公告/活動，但在它們已傳送通知給我之後，仍會顯示為「使用中」。這代表什麼意思？ 
您在 Mobile Engagement 中建立的 [活動] 之所以如此稱呼，是因為它是長時間執行的推播通知，意味著當有新裝置連線到行動參與平台，而只要裝置符合您在活動中設定的準則，就會自動對其傳送您在此處設定的通知。這不是一次性的單一通知設定。您必須手動按一下 [完成] 按鈕來終止行銷活動，才不會再繼續傳送通知。

### 我建立了推播活動，我也有成功收到通知，不過每當我開啟應用程式，總是得到相同的通知，即使我以前就已經對該通知採取過動作？ 
如果您在測試期間使用模擬器或某些測試架構 (像是 TestFlight)，就可能發生這個情況。其背後的原因是在每一個應用程式執行個體上，它會取得新的 DeviceID 並將它傳送給我們的後端，導致 Mobile Engagement 平台將它視為新裝置並傳送通知。

## 取得支援

如果您無法自行解決問題，您可以：

1. 在 StackOverflow 論壇與 [MSDN 論壇](https://social.msdn.microsoft.com/Forums/windows/zh-TW/home?forum=azuremobileengagement)的現有討論對話中，搜尋是否有您的問題，如果沒有，即在論壇中發問。 
2. 如果您發現缺少什麼功能，則請到我們的 [UserVoice 論壇](https://feedback.azure.com/forums/285737-mobile-engagement/)新增申請或進行投票
3. 如果您有 Microsoft 支援，則請提供下列詳細資料來開立支援事件： 
	- Azure 訂用帳戶識別碼
	- 平台 (例如 iOS、Android 等)
	- 應用程式識別碼
	- 活動識別碼 (若為推播通知方面的問題)
	- 裝置識別碼
	- Mobile Engagement SDK 版本 (例如 Android SDK v2.1.0)
	- 附有確切錯誤訊息與狀況的錯誤詳細資料

<!---HONumber=AcomDC_0128_2016-->