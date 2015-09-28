<!--author=alkohli last changed: 9/17/15-->


#### 設定和註冊裝置

1. 存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。如需相關指示，請參閱[使用 PuTTY 來連接至裝置序列主控台](#use-putty-to-connect-to-the-device-serial-console)。**請務必確實依照此程序，否則將無法存取主控台。**

2. 在開啟的工作階段中，按 Enter 鍵一次以取得命令提示字元。

3. 系統將提示您選擇想要為裝置設定的語言。指定語言，然後按 Enter 鍵。

    ![StorSimple 設定和註冊裝置 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)

4. 在顯示的序列主控台功能表中，選擇選項 1 以使用完整存取權進行登入。

    ![StorSimple 註冊裝置 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
  
     完成步驟 5-12，為裝置設定最小的必要網路設定。**這些設定步驟必須在裝置的主動控制器上執行。** 序列主控台功能表會在橫幅訊息中指出控制站狀態。如果您未連接到主動控制器，請中斷連線，然後連接到主動控制器。

5. 在命令提示字元中，輸入您的密碼。預設裝置密碼是 **Password1**。

6. 輸入以下命令：

     `Invoke-HcsSetupWizard`

7. 安裝精靈將協助您設定裝置的網路設定。請提供下列資訊：
   - 適用於 DATA 0 網路介面的 IP 位址
   - 子網路遮罩
   - 閘道器
   - 適用於主要 DNS 伺服器的 IP 位址
   - 適用於主要 NTP 伺服器的 IP 位址
   
      >[AZURE.NOTE]您可能需要等候幾分鐘，以套用子網路遮罩和 DNS 設定。如果您收到「裝置尚未就緒。」的錯誤訊息，請檢查主動控制器之 DATA 0 網路介面上的實體網路連線。

8. (選用) 設定 Web Proxy 伺服器。雖然 Web Proxy 設定是選用的，但**請注意，如果您使用 Web Proxy，就只能在此處設定它**。如需詳細資訊，請參閱[設定裝置的 Web Proxy](storsimple-configure-web-proxy.md)。如果您在此步驟中遇到任何問題，請參考 [Web Proxy 設定期間發生的錯誤](storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings)中的疑難排解指導方針。
 

      >[AZURE.NOTE]您可以隨時按 Ctrl + C 來結束安裝精靈。您在發出此命令之前套用的所有設定都將保留。

9. 基於安全性理由，裝置系統管理員密碼會在第一個工作階段之後過期，您必須變更該密碼，才能進行後續工作階段。出現提示時，請提供裝置系統管理員密碼。有效的裝置系統管理員密碼長度必須介於 8 到 15 個字元。密碼必須包含小寫字元、大寫字元、數字和特殊字元的組合。

10. 同時在此處設定 StorSimple Snapshot Manager 密碼。向執行 StorSimple Snapshot Manager 的 Windows 主機驗證裝置時，您可以使用此密碼。出現提示時，請提供 14 到 15 個字元的密碼。密碼必須包含下列其中三項的組合：大寫、小寫、數字和特殊字元。

    ![StorSimple 註冊裝置 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)

    您可以從 StorSimple Manager 服務介面重設 StorSimple Snapshot Manager 密碼。如需詳細步驟，請移至[使用 StorSimple Manager 服務變更 StorSimple 的密碼](storsimple-change-passwords.md)。

	若要疑難排解此步驟中的任何問題，請參考[與密碼相關的錯誤](storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords)中的疑難排解指導。

11. 安裝精靈的最後一個步驟是向 StorSimple Manager 服務註冊您的裝置。基於此因素，您需要使用在步驟 2 中取得的服務註冊金鑰。提供註冊金鑰之後，您可能需要等待 2-3 分鐘，才能註冊裝置。

	若要疑難排解任何可能的裝置註冊失敗，請參閱[裝置註冊期間發生錯誤](storsimple-troubleshoot-deployment.md#errors-during-device-registration)。如需詳細的疑難排解，您也可以參考[逐步說明的疑難排解範例](storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example)。

12. 註冊裝置之後，隨即會出現服務資料加密金鑰。複製這個金鑰，並將它儲存在安全的位置。
	
	> [AZURE.WARNING]這個金鑰需要與服務註冊金鑰搭配使用，來向 StorSimple Manager 服務註冊其他裝置。如需這個金鑰的詳細資訊，請參閱[StorSimple 安全性](../articles/storsimple/storsimple-security.md)。

     ![StorSimple 註冊裝置 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)

     若要從序列主控台視窗複製文字，只需選取該文字。然後您應該能夠將它貼到剪貼簿或任何文字編輯器中。請勿使用 Ctrl + C 來複製服務資料加密金鑰。使用 Ctrl + C 將導致安裝精靈結束。如此一來，裝置系統管理員密碼和 StorSimple Snapshot Manager 密碼將不會變更，而裝置將還原為預設密碼。

13. 結束序列主控台。

14. 返回管理入口網站，並完成下列步驟：
  1. 按兩下 StorSimple Manager 服務以存取 [快速入門] 頁面。
  2. 按一下 [檢視連接的裝置]。
  3. 在 [裝置] 頁面上，藉由查閱狀態來確認裝置已成功連接到服務。裝置狀態應該是 [線上]。如果裝置狀態為 [離線]，請等待數分鐘，讓裝置上線。
   
    ![StorSimple 裝置頁面](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png)
  
      >[AZURE.IMPORTANT]裝置為線上狀態之後，插入您在這個步驟開始時已拔除的網路纜線。

裝置已成功註冊之後若未成為上線狀態，您可以執行 `Test-HcsmConnection -Verbose` 以確定網路連線能力良好。如需此 Cmdlet 的詳細用法，請前往 [Test-HcsmConnection 的 Cmdlet 參考](https://technet.microsoft.com/library/dn715782.aspx)。

![提供的影片](./media/storsimple-configure-and-register-device/Video_icon.png)**提供的影片**

若要觀看影片示範如何使用 Windows PowerShell for StorSimple 設定和註冊裝置，請按一下[這裡](http://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/)。

<!---HONumber=Sept15_HO3-->