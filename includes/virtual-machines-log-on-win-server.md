<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. 按一下 [連接] 建立並下載 [遠端桌面通訊協定] 檔案 (.rdp file)。按一下 [開啟] 使用這個檔案。

5. 在 [遠端桌面] 視窗中按一下 [連接] 以繼續。

	![繼續連接](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 在 [Windows 安全性] 視窗中，輸入虛擬機器上帳戶的認證，然後按一下 [確定]。

 	在大多數案例中，這是您在建立虛擬機器時所指定的本機帳戶使用者名稱和密碼。在此案例中，虛擬機器的名稱是網域，而它的輸入格式為 *vmname*&#92;*username*。
	
	如果虛擬機器隸屬於貴公司的網域，請確定使用者名稱會包含該網域的名稱且格式為 *Domain*&#92;*Username*。此帳戶也必須屬於系統管理員群組，或者已授與遠端存取 VM 的權限。
	
	如果虛擬機器是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

7.	按一下 [是] 來確認虛擬機器的身分識別，並完成登入。

	![驗證機器的身分識別](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Oct15_HO2-->