<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. 按一下 [連接] 建立並下載 [遠端桌面通訊協定] 檔案 (.rdp file)。按一下 [開啟] 使用這個檔案。

5. 在 [遠端桌面] 視窗中按一下 [連接] 以繼續。

	![繼續連接](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 在 [Windows 安全性] 視窗中，輸入虛擬機器上系統管理帳戶的認證，然後按一下 [確定]。

 	>[AZURE.TIP]在大部分情況下，您將使用建立虛擬機器時所指定的使用者名稱和密碼。請檢查使用者名稱以確定它有正確的網域資訊：
	>
	>- 如果虛擬機器屬於貴公司的網域，請確定使用者名稱包含該網域的名稱。
	>- 如果虛擬機器不屬於網域，請在該行開頭加上 '' 以移除任何網域資訊，或使用 VM 名稱做為網域名稱。例如，`\MyUserName` 或 `MyTestVM\MyUserName`。
	>- 如果虛擬機器是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

7.	按一下 [是] 驗證虛擬機器的身分識別並完成登入。

	![驗證機器的身分識別](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->