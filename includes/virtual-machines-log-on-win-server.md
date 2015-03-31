<properties services="virtual-machines" title="如何登入執行 Windows Server 的虛擬機器" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] 若您必須重設使用者名稱或密碼，或啟用虛擬機器的 RDP，可以使用 [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) 擴充功能。若要檢查需求或取得登入的疑難排解提示，請參閱 [透過 RDP 或 SSH 連線至 Azure 虛擬機器](http://go.microsoft.com/fwlink/p/?LinkId=398294)。

1. 如果您還沒有這麼做，請登入 [Azure 管理入口網站](http://manage.windowsazure.com)。

2. 按一下 [**虛擬機器**]，然後選取適當的虛擬機器。

3. 在命令列上，按一下 [**連接**]。

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. 按一下 [**開啟**] 以使用系統自動為虛擬機器建立的遠端桌面通訊協定檔案。
	
5. 按一下 [**連接**] 以繼續。

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. 在虛擬機器上，輸入系統管理帳戶的認證，然後按一下 [**確定**]。 

 >[AZURE.TIP] 在大部分情況下，您將使用建立虛擬機器時所指定的使用者名稱和密碼。請檢查使用者名稱以確定它有正確的網域資訊：

>- 如果 VM 屬於貴公司的網域，請確定使用者名稱包含該網域的名稱。
- 如果 VM 不屬於網域，請在行開頭加上 ' \' 以移除任何網域資訊，或使用 VM 名稱做為網域名稱。例如， `\MyUserName` 或 `MyTestVM\MyUserName`。 
- 如果 VM 是網域控制站，請輸入該網域的網域系統管理員帳戶的使用者名稱和密碼。

按一下 [是] 以驗證虛擬機器的身分識別。

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

您現在可以從遠端使用虛擬機器。


<!--HONumber=47-->
