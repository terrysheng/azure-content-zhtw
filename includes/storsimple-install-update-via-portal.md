<!--author=SharS last changed: 9/17/15-->

#### 從管理入口網站安裝 Update 1.2

1. 在 [StorSimple 服務] 頁面上，選取您的裝置。瀏覽至 [**裝置**] > [**維護**]。

2. 按一下頁面底部的 [**掃描更新**]。將建立掃描可用更新的工作。工作成功完成時，系統將會通知您。

3. 在相同頁面的 [**軟體更新**] 區段中，您會看到新的軟體更新可供使用。我們建議您先檢閱版本資訊，然後再於裝置上套用 Update 1.2。

    ![安裝軟體更新](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. 按一下頁面底部的 [**安裝更新**]。

5. 系統將提示您進行確認。按一下 [確定]。

6. 將顯示 [**安裝更新**] 對話方塊。您的裝置應該要符合此對話方塊中所列出的檢查項目。在更新之前請先完成這些步驟。選取 [**我了解上述需求而且已準備好更新我的裝置**]。按一下核取圖示。

    ![確認訊息](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. 現在將開始一組自動預先檢查。其中包含：

	- **控制器健康狀況檢查**：確認這兩個裝置控制器的狀況良好且在線上。
	
	- **硬體元件健康狀況檢查**：確認您的 StorSimple 裝置上的所有硬體元件的狀況良好。
	
	- **DATA 0 檢查**：確認您的裝置已啟用 DATA 0。如果未啟用此介面，您必須啟用它，然後重試。
	
	- **DATA 2 和 DATA 3 檢查**：確認未啟用 DATA 2 和 DATA 3 網路介面。如果已啟用這些介面，將需要停用它們，然後嘗試更新您的裝置。只有在您要從執行 GA 軟體的裝置更新時，才需要執行這項檢查。執行 0.1、0.2 或 0.3 版的裝置將不需要這項檢查。
	
	- **閘道器檢查**：在任何執行 Update 1 之前版本的裝置上進行。這項檢查只能在執行 Update 1 之前版本軟體的所有裝置上執行，但是會在已為 DATA 0 以外的網路介面設定閘道器的裝置上無法執行。
 
	只有在上述所有的更新前檢查都順利完成之後，才會套用 Update 1.2。將會通知您更新前檢查正在進行中。
  
    ![前置檢查通知](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    下列是升級前檢查失敗的範例。您必須確認這兩個裝置控制器狀況良好且在線上。您也必須檢查硬體元件的健全狀態。在此範例中，需要注意控制器 0 及控制器 1 元件。如果您不能自行解決這些問題，您可能需要連絡 Microsoft 支援服務。

   	 ![前置檢查失敗](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	> [AZURE.NOTE]在 StorSimple 裝置上套用更新 1.2 之後，未來的更新將不再需要 DATA 2 和 DATA 3 檢查，以及閘道器檢查。但仍需要其他前置檢查。


8. 升級前檢查都成功完成後，將會建立更新工作。成功建立更新工作時，系統將會通知您。
 
    ![建立更新工作](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    接著，更新將套用到您的裝置上。
 
9. 若要監視更新工作的進度，請按一下 [**檢視工作**]。在 [工作] 頁面中，您可以看到更新進度。

    ![更新工作進度](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. 更新將需要幾個小時來完成。您可以隨時檢視工作的詳細資料。

    ![更新工作詳細資料](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. 工作完成後，瀏覽至 [**維護**] 頁面，並向下捲動至 [**軟體更新**]。

12. 確認您的裝置是否正在執行 **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**。[**上次更新日期**] 應該也已修改。

    ![維護頁面](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. 您現在會看到已有維護模式更新可供使用。這些更新為干擾性更新，會導致裝置產生停機時間，且只能透過您裝置的 Windows PowerShell 介面加以套用。請遵循[安裝維護模式更新](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)中的指示，以透過 Windows PowerShell for StorSimple 來安裝這些更新。

> [AZURE.NOTE]在某些情況下，在裝置上成功套用維護模式更新之後，指出維護模式更新可供使用的訊息可能會持續顯示長達 24 小時。

<!---HONumber=Sept15_HO4-->