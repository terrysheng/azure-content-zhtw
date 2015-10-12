> [AZURE.IMPORTANT]若要透過 Mobile Engagement 接收推播通知，您必須在應用程式中啟用 `Silent Remote Notifications`。您需要將遠端通知值新增到 Info.plist 檔案中的 UIBackgroundModes 陣列。

1. 開啟專案中的 `info.plist` 檔案
2. 以滑鼠右鍵按一下清單中最上方的項目 (`Information Property List`)，然後新增新的資料列

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)

3. 在新的資料列中輸入 `Required background modes`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)

4. 按一下向左鍵以展開資料列
5. 將下列的值加入至項目 0 `App downloads content in response to push notifications`

	![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)

6. 變更後，info.plist XML 應該包含下列索引鍵和值：

	    <key>UIBackgroundModes</key>
	    <array>
	    <string>remote-notification</string>
	    </array>
	    ...
    
7. 如果您使用 **Xcode 7** 和 **iOS 9**，您就必須執行下列額外步驟：
	- 在 [目標] > [建置設定]，將 [啟用 Bitcode] 設定為 [否]。請務必從頂端列選取 [全部]。 
	- 在 [目標] > [目標名稱] > [功能]，啟用 [推播通知]。

<!---HONumber=Oct15_HO1-->