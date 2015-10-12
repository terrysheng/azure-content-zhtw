###將推播憑證的存取權授與給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它對您憑證的存取權。這是藉由設定和輸入您的憑證到 Mobile Engagement 入口網站中而完成。請確定您已取得 .p12 憑證，如 [Apple 的文件](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)中所述。

1. 瀏覽至您的 Mobile Engagement 入口網站。請確認您的所在位置正確無誤，然後按一下底部的 [接洽] 按鈕：

	![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. 請在 Engagement 入口網站中按一下 [設定] 頁面。從該處按一下 [原生推送] 區段以上傳 p12 憑證：

	![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. 選取您的 p12，將它上傳並輸入您的密碼：

	![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>傳送通知至應用程式

現在，我們將建立簡單的推播通知活動，它會傳送推播至我們的應用程式：

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [Reach] 索引標籤

2. 按一下 [新公告] 以建立您的推送行銷活動。

	![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. 設定行銷活動的第一個欄位：

	![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

	- 	提供您的行銷活動**名稱**。 
	- 	將 [傳遞時間] 選取為 [僅從應用程式外]：這是主要為一些文字的簡單 Apple 推播通知類型。
	- 	在通知文字中，先輸入將成為推播第一行的**標題**。
	- 	然後輸入將成為第二行的**訊息**。

4. 向下捲動，在 [內容] 區段中選取 [僅限通知]。

	![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. 您已完成最基本的行銷活動設定。現在向下捲動，並按一下 [建立] 按鈕，以儲存推播通知行銷活動。

6. 最後，按一下 [啟用] 以傳送推播通知。

	![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

 

<!---HONumber=Oct15_HO1-->