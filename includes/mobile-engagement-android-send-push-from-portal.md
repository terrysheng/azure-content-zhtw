###授與 Mobile Engagement 的存取權給 GCM API 金鑰

若要讓 Mobile Engagement 以您的名義傳送推播通知，您必須授與 API 金鑰的存取權。完成此項作業的方法為在 Mobile Engagement 入口網站中設定並輸入您的金鑰。

1. 請從 Azure 傳統入口網站確認您在我們用於此專案的應用程式中，然後按一下底部的 [接洽] 按鈕。

	![](./media/mobile-engagement-android-send-push/engage-button.png)

2. 然後按一下 [設定] -> [原生推送] 區段來輸入 GCM 金鑰：

	![](./media/mobile-engagement-android-send-push/engagement-portal.png)

3. 在 [GCM 設定] 區段中，按一下 [API 金鑰] 前面的**編輯**圖示，如下所示：

	![](./media/mobile-engagement-android-send-push/native-push-settings.png)

4. 在快顯視窗中，貼上您先前取得的 GCM 伺服器金鑰，然後按一下 [確定]。

	![](./media/mobile-engagement-android-send-push/api-key.png)

##<a id="send"></a>傳送通知至應用程式

我們將建立一個簡單的推播通知活動，它會將推播通知傳送至我們的應用程式。

1. 瀏覽至您的 Mobile Engagement 入口網站中的 [觸達] 索引標籤

2. 按一下 [新增宣告] 來建立您的推播通知活動。

	![](./media/mobile-engagement-android-send-push/new-announcement.png)

3. 透過下列步驟來設定活動的第一個欄位：

	![](./media/mobile-engagement-android-send-push/campaign-first-params.png)

	a.為您的活動命名。

	b.將 [傳遞類型] 選取為 [系統通知 -> 簡易]：這是 Android 的簡易推播通知類型，具備一個標題和一小行文字。

	c.將 [傳遞時間] 選取為 [任何時候]，讓應用程式無論是否啟動，都會接收通知。

	d.在通知文字中，輸入**標題**，這在推播中會以粗體顯示。

	e.然後輸入您的**訊息**。

4. 向下捲動，在 [內容] 區段中選取 [僅限通知]。

	![](./media/mobile-engagement-android-send-push/campaign-content.png)

5. 您已完成能做的最基本活動設定。現在再次向下捲動，然後按一下 [建立] 按鈕來儲存活動。

6. 最後一個步驟：按一下 [啟動] 來啟用活動以傳送推播通知。

	![](./media/mobile-engagement-android-send-push/campaign-activate.png)

<!---HONumber=AcomDC_0330_2016-->