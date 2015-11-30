<!--author=SharS last changed: 11/16/15-->

#### 從 Windows PowerShell for StorSimple 安裝 Update 1.2

1. 執行下列步驟以下載軟體更新。

    1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com/v7/site/Home.aspx](http://catalog.update.microsoft.com/v7/site/Home.aspx)。
    2. 如果您是第一次使用者，系統會提示您安裝 Microsoft Update Catalog。按一下 [Install]。
    
        ![安裝目錄](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

    3. 您會看到目錄搜尋畫面。在搜尋方塊中輸入 **3063418** ，然後按一下 [**搜尋**]。

        ![搜尋目錄](./media/storsimple-install-update-option-1/HCS_SearchCatalog-include.png)

    4. 您會看到 **StorSimple Update 1.2 Appliance Update** 套件組合。按一下 [新增]。更新將會加入到購物籃中。

        ![更新套件組合](./media/storsimple-install-update-option-1/HCS_UpdateBundle-include.png)

    5. 按一下 [**檢視購物籃**]。
 
        ![檢視購物籃](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

    6. 按一下 [下載]。指定或**瀏覽**至您想要儲存下載項目的本機位置。更新將會下載到所選位置的 **StorSimple Update 1.2 Appliance Update 套件組合** (KB3063418) 資料夾中。資料夾也可以複製到裝置可連線的網路共用位置。
    
	此程序說明如何將軟體裝置更新安裝為 Hotfix、從 Microsoft Update 伺服器安裝磁碟韌體更新，以及從 Azure 入口網站安裝 LSI 驅動程式和 Windows 更新。不過您可以選擇將軟體、驅動程式和磁碟韌體更新皆安裝為 Hotfix。您接著會需要下載 StorSimple 1.2 SAS 控制器更新 (KB3043005) 和 StorSimple 1.2 磁碟韌體更新 (KB3063416) 並複製到相同的共用資料夾。若要將磁碟韌體更新安裝為 Hotfix，請遵循下列指示：[透過 Windows PowerShell for StorSimple 安裝維護模式 Hotfix](storsimple-update-device.md#install-hotfixes-via-windows-powershell-for-storsimple)。
    
	> [AZURE.NOTE]Hotfix 必須可同時從兩個控制器偵測任何來自對等控制器的潛在錯誤訊息。
            
2. 若要安裝軟體更新，請存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。請依照[使用 PuTTy 連接到序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的詳細指示執行作業。

3. 在命令提示字元中，按 **Enter** 鍵。

4. 選取 [**選項 1**] 以使用完整的存取權限登入裝置。

5. 若要安裝更新套件，請在命令提示字元中輸入：

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    在上述命令的共用路徑中使用 IP 而不是 DNS。只有當您要存取已驗證的共用位置時，才會用到認證參數。

	我們建議您使用認證參數來存取共用項目。即使是開放給「所有人」的共用項目，通常也不會開放給未經驗證的使用者。

    下方顯示一項範例輸出。

        ````
        Controller0>Start-HcsHotfix -Path \\10.100.100.100\share
        \hcsmdssoftwareupdate.exe -Credential contoso\John
      
        Confirm

        This operation starts the hotfix installation and could reboot one or
        both of the controllers. If the device is serving I/Os, these will not 
        be disrupted. Are you sure you want to continue?
        [Y] Yes [N] No [?] Help (default is "Y"): Y

        ````
 
6. 當系統提示您確認 Hotfix 安裝時，請輸入 **Y**。

7. 使用 `Get-HcsUpdateStatus` Cmdlet 來監視更新。

    下列範例輸出顯示更新進行中。更新正在進行中時，`RunInprogress` 會是 `True`。

        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 9/02/2015 10:36:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     下列範例輸出指出更新已完成。更新完成時，`RunInProgress` 將會是 `False`。

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 9/02/2015 10:56:13 PM
        LastUpdateTimestamp : 9/02/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

        ````
		

	> [AZURE.NOTE]有時在更新進行期間，Cmdlet 會回報 `False`。若要確保此 Hotfix 已完成，請等待幾分鐘的時間、重新執行此命令並確認 `RunInProgress` 為 `False`。如果的確為 False 的話，則 Hotfix 已完成。
	
8. 軟體更新完成後，請確認系統軟體版本。輸入以下命令：

    `Get-HcsSystem`

    您應該會看見下列版本：

    - HcsSoftwareVersion：6.3.9600.17584
    - CisAgentVersion：1.0.9049.0
    - MdsAgentVersion：26.0.4696.1433 
    
	如果在套用更新後版本號碼並未變更，則表示此 Hotfix 未成功套用。若您看到這種情況，請連絡 [Microsoft 支援](storsimple-contact-microsoft-support.md)以取得進一步的協助。
    
9. 您現在將安裝干擾性磁碟韌體更新，這需要約 30 到 45 分鐘的時間來完成。您可以藉由連接至裝置序列主控台，以選擇在預計的維護視窗中安裝這些更新。若要安裝磁碟韌體更新，請遵循[透過 Windows PowerShell for StorSimple 安裝維護模式更新](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)中的指示。

10. 在磁碟韌體更新已成功套用且裝置已結束維護模式後，返回 Azure 入口網站。維護模式更新在 24 小時內不會更新至入口網站。您可能需要等待，之後才能從 Azure 入口網站套用其餘的非干擾性升級。

11. 當您準備好要套用更新時，瀏覽至**維護**頁面，然後在頁面底部按一下 [掃描更新]。您會在有可用的更新時收到通知，包括驅動程式和 Windows 更新。按一下 [安裝更新] 開始安裝程序。成功安裝所有更新之後就完成了。





 
 

<!---HONumber=Nov15_HO4-->