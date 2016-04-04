<!--author=SharS last changed: 03/17/2016-->

#### 下載 Hofix

執行下列步驟以下載軟體更新。

1. 啟動 Internet Explorer 並瀏覽至 [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com)。

2. 如果這是您在此電腦上第一次使用 Microsoft Update Catalog，請在系統提示您安裝 Microsoft Update Catalog 附加元件時，按一下 [安裝]。![安裝目錄](./media/storsimple-install-update-option-1/HCS_InstallCatalog-include.png)

3. 在 Microsoft Update Catalog 的 [搜尋] 方塊中，輸入您要下載的 Hotfix 的知識庫 (KB) 編號，例如 **3063418**，然後按一下 [搜尋]。

4. 您會看到 **StorSimple Update 1.2 Appliance Update** 套件組合。按一下 [新增]。更新將會加入到購物籃中。

5. 搜尋上表中所列的其他任何 Hotfix (**3043005** 和 **3063416**)，然後加入每個購物籃。

5. 按一下 [**檢視購物籃**]。

    ![檢視購物籃](./media/storsimple-install-update-option-1/HCS_InstallBasket-include.png)

6. 按一下 [下載]。指定或**瀏覽**至您想要儲存下載項目的本機位置。更新便會下載到指定的位置，並放在與更新名稱相同的子資料夾中。資料夾也可以複製到裝置可連線的網路共用位置。

>   [AZURE.NOTE]
Hotfix 必須可同時從兩個控制器存取，以偵測來自對等控制器的任何潛在錯誤訊息。

#### 安裝及驗證一般模式 Hotfix
執行下列步驟來安裝及驗證一般模式 Hotfix。如果您已使用 Azure 入口網站安裝這些 Hotfix，請直接跳到[安裝及驗證維護模式 Hotfix](#to-install-and-verify-maintenance-mode-hotfixes)。

1. 若要安裝軟體更新，請存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。請依照[使用 PuTTy 連接到序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的詳細指示執行作業。在命令提示字元中，按 **Enter** 鍵。

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

	> [AZURE.NOTE] 有時在更新進行期間，Cmdlet 會回報 `False`。若要確保此 Hotfix 已完成，請等待幾分鐘的時間、重新執行此命令並確認 `RunInProgress` 為 `False`。如果的確為 False 的話，則 Hotfix 已完成。

8. 軟體更新完成後，請確認系統軟體版本。輸入以下命令：

    `Get-HcsSystem`

    您應該會看見下列版本：

    - HcsSoftwareVersion：6.3.9600.17584
    - CisAgentVersion：1.0.9049.0
    - MdsAgentVersion：26.0.4696.1433

	如果在套用更新後版本號碼並未變更，則表示此 Hotfix 未成功套用。若您看到這種情況，請連絡 [Microsoft 支援](storsimple-contact-microsoft-support.md)以取得進一步的協助。

9. 重複步驟 3-5 來安裝剩餘的一般模式 Hotfix (KB3043005)。

#### 安裝及驗證維護模式 Hotfix

請使用 KB3063416 來安裝磁碟韌體更新。這些是干擾性更新，且需要約 30-45 分鐘來完成。您可以藉由連接至裝置序列主控台，以選擇在預計的維護視窗中安裝這些更新。

若要安裝磁碟韌體更新，請依照下面的指示執行。

1. 使裝置處於維護模式。請注意，連線至處於維護模式的裝置時，您不應該使用 Windows PowerShell 遠端執行功能。透過裝置序列主控台連線時，您需要在裝置控制器上執行此 Cmdlet。輸入：

    `Enter-HcsMaintenanceMode`

	範例輸出如下所示。

		Controller0>Enter-HcsMaintenanceMode
		Checking device state...

		In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
		[Y] Yes [N] No (Default is "Y"): Y

		-----------------------MAINTENANCE MODE------------------------
		Microsoft Azure StorSimple Appliance Model 8100
		Name: Update1-8100-SHG0997879L76YD
		Software Version: 6.3.9600.17584
		Copyright (C) 2014 Microsoft Corporation. All rights reserved.
		You are connected to Controller0 - Passive
		---------------------------------------------------------------
		Serial Console Menu
		[1] Log in with full access
		[2] Log into peer controller with full access
		[3] Connect with limited access
		[4] Change language
		Please enter your choice>

	接著，兩個控制器就會重新啟動以進入維護模式。

3. 若要安裝磁碟韌體更新，請輸入：

    `Start-HcsHotfix -Path <path to update file> -Credential <credentials in domain\username format>`

    下方顯示一項範例輸出。

        Controller1>Start-HcsHotfix -Path \\10.100.100.100\share\DiskFirmwarePackage.exe -Credential contoso\john
    	Enter Password:
    	WARNING: In maintenance mode, hotfixes should be installed on each controller sequentially. After the hotfix is installed on this controller, install it on the peer controller.
    	Confirm
    	This operation starts a hotfix installation and could reboot one or both of the controllers. Are you sure you want to continue?
    	[Y] Yes [N] No (Default is "Y"): Y
    	WARNING: Installation is currently in progress. This operation can take several minutes to complete.

1.  使用 `Get-HcsUpdateStatus` 命令來監視安裝進度。當 `RunInProgress` 變成 `False` 時，即代表更新完成。

2.  安裝完成之後，維護模式 Hotfix 安裝所在的控制器將會重新開機。以具有完整存取權的選項 1 登入，並驗證磁碟韌體版本。輸入：

	`Get-HcsFirmwareVersion`

    預期的磁碟韌體版本為：

    `XMGG, XGEE, KZ50, F6C2, VR08`

    請在第二個控制器上執行 `Get-HcsFirmwareVersion` 命令，來驗證軟體版本已經更新。然後您就可以結束維護模式。針對每個裝置控制器輸入以下命令：

    `Exit-HcsMaintenanceMode`

1. 當您離開維護模式時，控制器便會重新啟動。在磁碟韌體更新已成功套用且裝置已結束維護模式後，返回 Azure 傳統入口網站。請注意，入口網站可能有 24 小時的時間不會顯示您已安裝維護模式更新。

<!---HONumber=AcomDC_0323_2016-->