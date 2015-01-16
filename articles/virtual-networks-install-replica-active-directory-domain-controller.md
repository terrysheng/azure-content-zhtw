<properties urlDisplayName="Replica domain controller" pageTitle="在 Azure 中安裝複本網域控制站" metaKeywords="" description="指導如何從 Azure 虛擬機器上的 Corp Active Directory 樹系安裝網域控制站。" metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#在 Azure 虛擬網路中安裝複本 Active Directory 網域控制站

本教學課程將逐步引導您完成在[Azure 虛擬網路](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx)上，從虛擬機器 (VM) 之企業 Active Directory 樹系安裝額外網域控制站的步驟。在本教學課程中，VM 的虛擬網路已連接公司網路。如需在 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的概念指引，請參閱[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156090.aspx)。

##目錄##

* [必要條件](#Prerequisites)
* [步驟 1：驗證 YourPrimaryDC 的靜態 IP 位址](#verifystaticip)
* [步驟 2：安裝企業樹系](#installforest)
* [步驟 3：建立子網路和站台](#subnets)
* [步驟 4：在 CloudSite 中安裝額外的網域控制站](#cloudsite)
* [步驟 5：驗證安裝](#validate)
* [步驟 6：佈建在開機時便已加入網域的虛擬機器](#provisionvm)
* [步驟 7：備份網域控制站](#backup)
* [步驟 8：測試驗證和授權](#test)


<h2><a id="Prerequisites"></a>必要條件</h2>

在 Azure 虛擬網路與公司網路之間設定的-	[管理入口網站中設定站對站 VPN](http://msdn.microsoft.com/zh-tw/library/dn133795.aspx)。
-	在虛擬網路中建立雲端服務。
-	在屬於虛擬網路的雲端服務中部署兩部 VM (指定要放置 VM 的子網路)。如需詳細資訊，請參閱[將虛擬機器新增至虛擬網路](http://azure.microsoft.com/zh-tw/documentation/articles/virtual-networks-add-virtual-machine/)。其中一部 VM 的大小必須為 L 或更大，以便連接兩個資料磁碟。需要資料磁碟用來儲存：
	- Active Directory 資料庫和記錄。
	- 系統狀態備份。
-	具備兩部 VM (YourPrimaryDC 和 FileServer) 的企業網路。
-	如果您需要讓外部使用者解析 Active Directory 中帳戶的名稱，則必須部署網域名稱系統 (DNS) 基礎結構。在此情況下，將 DNS 伺服器安裝在網域控制站上之前，您應該先建立 DNS 區域委派，或允許 Active Directory 網域服務安裝精靈建立委派。如需建立 DNS 區域委派的詳細資訊，請參閱[建立區域委派](http://technet.microsoft.com/library/cc753500.aspx)。
-	在安裝於 Azure VM 的 DC 上，設定 DNS 用戶端解析程式設定，如下所示：
	- 慣用 DNS 伺服器：內部部署 DNS 伺服器
	- 其他 DNS 伺服器：迴路位址或 (如果可能的話) 執行於相同虛擬網路之 DC 上的其他 DNS 伺服器。

<div class="dev-callout"> 
<b>注意</b>
<p>您需要提供自己的 DNS 基礎結構，才能支援 Azure 虛擬網路中的 AD DS。Azure 提供的此版本 DNS 基礎結構不支援某些 AD DS 要求的功能，如動態 SRV 資源記錄登錄。 </p>
</div>

<div class="dev-callout"> 
<b>注意</b>
<p>如果您已經完成<a href="/zh-tw/manage/services/networking/active-directory-forest/">在 Azure 中安裝新的 Active Directory 樹系</a>中的步驟，可能需要先移除 Azure 虛擬網路之網域控制器上的 AD DS，才能開始進行本教學課程。如需移除 AD DS 之方式的詳細資訊，請參閱<a href="http://technet.microsoft.com/zh-tw/library/cc771844(v=WS.10).aspx">從網域移除網域控制站</a>。</p>
</div>


<h2><a id="verifystaticip"></a>步驟 1：驗證 YourPrimaryDC 的靜態 IP 位址</h2>

1. 登入企業網路上的 YourPrimaryDC。

2. 在伺服器管理員中按一下 [檢視網路連線]。

3. 以滑鼠右鍵按一下本機區域網路連線，然後按一下 [內容]。

4. 按一下 [網際網路通訊協定第 4 版 (TCP/IPv4)]，然後按一下 [內容]。

5. 確認伺服器是否具有已指派的靜態 IP 位址。 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>步驟 2：安裝企業樹系</h2>

1. 在 VM 的 RDP 工作階段中，按一下 [**開始**] 並輸入 **dcpromo**，然後按 ENTER 鍵。

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. 在 [歡迎使用] 頁面中按 [**下一步**]。

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. 在 [作業系統相容性] 頁面中按 [**下一步**]。

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. 在 [選擇部署組態] 頁面中按一下 [**在新樹系內建立新網域**]，然後按 [**下一步**]。 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. 在 [命名樹系根網域] 頁面中輸入樹系根網域的完整網域名稱 (FQDN) **corp.contoso.com**，然後按 [**下一步**]。 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. 在 [設定樹系功能等級] 頁面中按一下 [**Windows Server 2008 R2**]，然後按 [**下一步**]。

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. 在 [其他網域控制站選項] 頁面中按一下 [**DNS 伺服器**]，然後按 [**下一步**]。

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. 當以下 DNS 委派警告出現時，按一下 [**是**]。

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. 在 [Location for Active Directory database, log files and SYSVOL] 頁面中輸入或選取檔案的位置，然後按 [**下一步**]。

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. 在 [Directory Services Restore Administrator] 頁面中輸入並確認 DSRM 密碼，然後按 [**下一步**]。

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. 在 [摘要] 頁面中確認選取項目，然後按 [**下一步**]。 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. 在 Active Directory 安裝精靈完成後，依序按一下 [**完成**] 和 [**立即重新啟動**] 以完成安裝。 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>步驟 3：建立子網路和站台</h2>

1. 在 YourPrimaryDC 上依序按一下 [開始]、[系統管理工具] 及 [Active Directory 站台及服務]。
2. 按一下 [**網站**]、以滑鼠右鍵按一下 [**子網路**]，然後按一下 [**新增子網路**]。

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. 在 [**Prefix::**] 中輸入 **10.1.0.0/24**、選取 **Default-First-Site-Name** 網站物件，然後按一下 [**確定**]。

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. 以滑鼠右鍵按一下 [**網站**]，然後按一下 [**新增網站**]。

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. 在 [名稱] 中輸入 **CloudSite**、選取 **DEFAULTIPSITELINK**，然後按一下 [**確定**]。 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. 按一下 [**確定**] 以確認網站是否建立。 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. 以滑鼠右鍵按一下 [**子網路**]，然後按一下 [**新增子網路**]。

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. 在 [**Prefix::**] 中輸入 **10.4.2.0/24**、選取 **CloudSite** 網站物件，然後按一下 [**確定**]。

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>步驟 4：在 CloudSite 中安裝額外的網域控制站</h2>

1. 登入 YourVMachine，按一下 [**開始**] 並輸入 **dcpromo**，然後按 ENTER 鍵。

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. 在 [歡迎使用] 頁面中按 [**下一步**]。

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. 在 [作業系統相容性] 頁面中按 [**下一步**]。

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. 在 [選擇部署組態] 頁面中依序按一下 [**現有樹系**]、[**新增現有網域的網域控制站**] 和 [**下一步**]。

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. 在 [網路認證] 頁面中，確認您是否在 **corp.contoso.com** 網域中安裝網域控制站，然後輸入 Domain Admins 群組成員的認證 (或使用 corp\administrator 認證)。 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. 在 [選取網域] 頁面中按 [**下一步**]。 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. 在 [選取站台] 頁面中確認已選取 CloudSite，然後按 [**下一步**]。

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. 在 [其他網域控制站選項] 頁面中按 [**下一步**]。 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. 在 [靜態 IP 指定] 警告中，按一下 [**是，電腦將使用 DHCP 伺服器自動指派的 IP 位址 (不建議)**]。

	**重要事項** 

	儘管 Azure 虛擬網路中的 IP 位址為動態，不過其租用會延續到 VM 的持續時間結束為止。因此，您不需要在安裝於虛擬網路之網域控制站上設定靜態 IP 位址。在 VM 中設定靜態 IP 位址會導致通訊失敗。


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. 當 DNS 委派警告出現時，按一下 [**是**]。

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. 在 [Location for Active Directory database, log files and SYSVOL] 頁面中按一下 [瀏覽]，接著輸入或選取資料磁碟上的位置以容納 Active Directory 檔案，然後按 [**下一步**]。 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. 在 [Directory Services Restore Administrator] 頁面中輸入並確認 DSRM 密碼，然後按 [**下一步**]。

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. 在 [摘要] 頁面中按 [**下一步**]。

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. 在 Active Directory 安裝精靈完成後，依序按一下 [**完成**] 和 [**立即重新啟動**] 以完成安裝。 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>步驟 5：驗證安裝</h2>

1. 重新連接 VM。

2. 按一下 [**開始**]、以滑鼠右鍵按一下 [**命令提示字元**]，然後按一下 [**以系統管理員身分執行**]。 

3. 輸入以下命令，然後按 ENTER 鍵：'Dcdiag /c /v'

4. 確認測試是否成功執行。 

在設定 DC 之後，執行以下 Windows PowerShell Cmdlet 以佈建額外的虛擬機器，並讓它們在佈建時自動加入網域。在佈建 VM 時，您必須設定 VM 的 DNS 用戶端解析程式設定。請替換為正確的網域名稱、VM 名稱等。 

如需使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure PowerShell](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055.aspx) 和 [Azure 管理 Cmdlet](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj152841)。


<h2><a id="provisionvm"></a>步驟 6：佈建在開機時便已加入網域的虛擬機器</h2>

1. 若要建立在首次開機時便已加入網域的額外虛擬機器，請開啟 Azure PowerShell ISE、貼入以下指令碼、將預留位置取代為您自己的值，然後再執行。 

	若要判斷網域控制站的內部 IP 位址，請按一下網域控制站執行所在的虛擬網路名稱。 

	在以下範例中，網域控制站的內部 IP 位址是 10.4.3.1。Add-AzureProvisioningConfig 亦採用 -MachineObjectOU 參數 (若有指定，需要在 Active Directory 有完整的辨別名稱)，能讓您設定該容器中所有虛擬機器上的群組原則。

	在虛擬機器佈建完成後，請藉由指定使用「使用者主體名稱 (UPN)」格式 (如 administrator@corp.contoso.com) 的網域帳戶來登入虛擬機器。 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-zh-tw-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>步驟 7：備份網域控制站</h2>


1. 連接至 YourVMachine。

2. 依序按一下 [**開始**]、[**伺服器管理員**]、[**新增功能**]，然後選取 [**Windows Server Backup 功能**]。遵循指示安裝 Windows Server Backup。

3. 依序按一下 [**開始**]、[**Windows Server Backup**] 和 [**備份一次**]。
 
4. 按一下 [**不同選項**]，然後按 [**下一步**]。

5. 按一下 [**完整伺服器**]，然後按 [**下一步**]。

6. 按一下 [**本機磁碟機**]，然後按 [**下一步**]。

7. 選取未裝載作業系統檔案的目的地磁碟機或 Active Directory 資料庫，然後按 [下一步]。

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. 確認您選取的備份設定，然後按一下 [**備份**]。

<h2><a id="test"></a>步驟 8：測試驗證和授權</h2>

1. 為了測試驗證和授權，請在 Active Directory 中建立網域使用者帳戶。 
登入每個站台的用戶端 VM，並在 VM 上建立共用資料夾

2. 使用不同的帳戶、群組及權限來測試共用資料夾的存取。 

## 另請參閱

-  [Azure 虛擬網路](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx)

-  [Windows Azure IT 專業人員 IaaS：(01) 虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01) (英文)

-  [Windows Azure IT 專業人員 IaaS：(05) 建立虛擬網路和跨單位連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05) (英文)

-  [Azure PowerShell](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055.aspx)

-  [Azure 管理 Cmdlet](http://msdn.microsoft.com/zh-tw/library/windowsazure/jj152841)
