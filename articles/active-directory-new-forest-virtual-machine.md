<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory forest" pageTitle="Install an Active Directory forest on an Azure virtual network" metaKeywords="" description="A tutorial that explains how to create a new Active Directory forest on a virtual machine (VM) on an Azure Virtual Network." metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Install a new Active Directory forest in Azure" authors="Justinha"  solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft"  />

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />

# 在 Azure 虛擬網路上安裝新的 Active Directory 樹系

本主題說明如何在 [Azure 虛擬網路][Azure 虛擬網路]上，於 Azure 虛擬網路的虛擬機器 (VM) 上建立新的 Windows Server Active Directory 環境。在此案例中，Azure 虛擬網路並未連線至內部部署網路。

您也可能對以下相關主題有興趣：

-   您可以選擇性地[使用管理入口網站精靈設定站台對站台 VPN][使用管理入口網站精靈設定站台對站台 VPN] (英文)，然後安裝新樹系，或將內部部署樹系延伸至 Azure 虛擬網路。如需相關步驟，請參閱[在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台][在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台] (英文)。
-   如需在 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的概念指引，請參閱[在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針][在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針] (英文)。
-   如需在含 AD DS 的 Azure 上建立測試實驗室環境的逐步指引，請參閱[測試實驗室指南：Azure 中的 Windows Server 2012 R2 基本組態][測試實驗室指南：Azure 中的 Windows Server 2012 R2 基本組態] (英文)。

## 目錄

-   [這與內部部署有何不同？][這與內部部署有何不同？]
-   [步驟 1. 建立 Azure 虛擬網路][步驟 1. 建立 Azure 虛擬網路]
-   [步驟 2：建立 VM 來執行網域控制台與 DNS 伺服器角色][步驟 2：建立 VM 來執行網域控制台與 DNS 伺服器角色]
-   [步驟 3：安裝 Windows Server Active Directory][步驟 3：安裝 Windows Server Active Directory]
-   [步驟 4：設定 Azure 虛擬網路的 DNS 伺服器][步驟 4：設定 Azure 虛擬網路的 DNS 伺服器]
-   [步驟 5：建立網域成員的 VM 並加入網域][步驟 5：建立網域成員的 VM 並加入網域]

## <span id="differ"></span></a>這與內部部署有何不同？

在 Azure 或在內部部署上安裝網域控制台，差異並不會很多。下表列出主要差異。

| 若要設定...                       | 內部部署                                                    | Azure 虛擬網路                                                                 |
|-----------------------------------|-------------------------------------------------------------|--------------------------------------------------------------------------------|
| **網域控制站的 IP 位址**          | 在網路介面卡內容上指派靜態 IP 位址                          | 透過 DHCP 取得 IP 位址，並選擇性地執行 Set-AzureStaticVNetIP Cmdlet 使它變靜態 |
| **DNS 用戶端解析程式**            | 在網域成員的網路介面卡內容上設定慣用與替代的 DNS 伺服器位址 | 在虛擬網路內容上設定 DNS 伺服器位址                                            |
| **Active Directory 資料庫儲存體** | 選擇性地將預設儲存位置變更為不是 C:\\                       | 您需要將預設儲存位置變更為不是 C:\\                                            |

## <span id="createvnet"></span></a>步驟 1：建立 Azure 虛擬網路

1.  登入 [Azure 管理入口網站][Azure 管理入口網站]。
2.  建立虛擬網路。按一下 [網路] \> [建立虛擬網路]。使用下表中的值來完成精靈。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">在此精靈頁面上…</th>
    <th align="left">指定這些值</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>虛擬網路詳細資料</strong></td>
    <td align="left"><p>Name:輸入虛擬網路的名稱</p>
    <p>Region:選擇最近的區域</p>
    <p>同質群組：<strong>建立新的同質群組</strong></p>
    <p>同質群組名稱：輸入同質群組的名稱</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>DNS 與 VPN</strong></td>
    <td align="left"><p>將 DNS 伺服器留空</p>
    <p>不要選取任何 VPN 選項</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>虛擬網路位址空間</strong></td>
    <td align="left"><p>子網路名稱：輸入子網路的名稱</p>
    <p>起始 IP：<strong>10.0.0.0</strong></p>
    <p>CIDR： <strong>/24 (256)</strong></p></td>
    </tr>
    </tbody>
    </table>

## <span id="createvm"></span></a>步驟 2：建立 VM 來執行網域控制台與 DNS 伺服器角色

1.  按一下 [新增]  \> [計算]  \> [虛擬機器] \> [從組件庫] 。
2.  使用下表中的值來完成精靈。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">在此精靈頁面上…</th>
    <th align="left">指定這些值</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>作業系統</strong></td>
    <td align="left">選取 <strong>Windows Server 2012 R2 Datacenter</strong></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>虛擬機器組態</strong></td>
    <td align="left"><p>發行日期：今天的日期</p>
    <p>機器名稱：指定唯一值</p>
    <p>層：標準</p>
    <p>大小：選取任何大小</p>
    <p>使用者名稱：輸入名稱。此使用者帳戶將是內建 Administrators 群組的成員。</p>
    <p>密碼：必須至少 8 個字元，並且包括下列 3 種字元：</p>
    <ul>
    <li>大寫字母</li>
    <li>小寫字母</li>
    <li>數字</li>
    <li>特殊字元</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left"><strong>雲端服務</strong></td>
    <td align="left"><p>雲端服務：<strong>建立新的雲端服務</strong></p>
    <p>雲端服務名稱：接受預設值</p>
    <p>區域/同質群組/虛擬網路：選取您已建立的虛擬網路</p>
    <p>虛擬網路子網路：選取您已建立的子網路。</p>
    <p>儲存體帳戶：<strong>使用自動產生的儲存體帳戶</strong></p>
    <p>可用性設定組：<strong>None</strong></p>
    <p>Endpoints:接受預設值</p></td>
    </tr>
    <tr class="even">
    <td align="left"><strong>VM 代理程式</strong></td>
    <td align="left">選取 [安裝 VM 代理程式]</td>
    </tr>
    </tbody>
    </table>

3.  預設指派給 VM 的動態 IP 位址在雲端服務執行期間有效。但是，如果 VM 關閉，此位址就會變更。您可以[執行 Set-AzureStaticVNetIP Azure PowerShell Cmdlet][執行 Set-AzureStaticVNetIP Azure PowerShell Cmdlet] (英文來指派靜態 IP 位址，如此一來，即使您需要關閉 VM，IP 位址仍會存在。
4.  將一個額外的磁碟附加至 VM，以儲存 Active Directory 資料庫、記錄及 SYSVOL。
  5.  按一下 [VM]\> [附加]\> [連接空的磁碟]。
  6.  指定大小 (例如，10 GB)，並接受所有其他預設值。
7.  登入 VM，並格式化這個額外的磁碟。
  8.  按一下 [連接] 登入 VM，按一下 [開啟] 建立 RDP 工作階段，然後再按一下 [連接]。
  9.  將認證變更為您指定的新使用者名稱與密碼。
  10. 在伺服器管理員中，按一下 [工具] \> [電腦管理]。
  11. 按一下 [磁碟管理]，然後按一下 [確定] 初始化新磁碟。
  12. 以滑鼠右鍵按一下磁碟名稱，然後按一下 [新增簡單磁碟區]。完成精靈來格式化新的磁碟機。

## <span id="installad"></span></a>步驟 3：安裝 Windows Server Active Directory

使用您在內部部署上使用的同一套例行動作來[安裝 AD DS][安裝 AD DS] (英文)(亦即，您可以使用 UI、回應檔案或 Windows PowerShell)。您需要提供系統管理員認證，才能安裝新樹系。若要指定 Active Directory 資料庫、記錄檔與 SYSVOL 的位置，請將預設儲存位置從作業系統磁碟機變更為您連接至 VM 的額外資料磁碟。

在 DC 安裝完成之後，請重新連線至 VM 並登入 DC。記得要指定網域認證。

</p>
## <span id="dns"></span></a>步驟 4：設定 Azure 虛擬網路的 DNS 伺服器

1.  按一下 [虛擬網路]，按兩下您已建立的虛擬網路，然後按一下 [設定]。
2.  在 [DNS 伺服器] 下，輸入 DC 的名稱與 DIP，然後按一下 [儲存]。
3.  選取 VM 並按一下 [重新啟動]，以觸發 VM 利用新 DNS 伺服器的 IP 位址來設定 DNS 解析程式設定。

## <span id="domainmembers"></span></a>步驟 5：建立網域成員的 VM 並加入網域

建立額外的 VM 來佈建網域成員電腦。您可以使用 UI 或 Azure PowerShell。如果使用 UI，只需遵循您建立第一個 VM 時所用的相同步驟。然後，透過與在內部部署中相同的動作，將 VM 加入至網域。如果使用 Azure PowerShell，則您可以佈建 VM，並使它們在第一次啟動時加入網域。

這個範例會建立執行 Windows Server 2012 R2 Datacenter 並加入網域的 VM：DC2。佈建 DC2 之後，請以網域管理員身分登入，並將其升級為複本 DC。

您可以執行 Get-AzureVMImage 以取得映像名稱。例如，若要傳回 Windows Server 2012 R2 的映像清單，請執行 Get-AzureVMImage | where-object {$\_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"}。

    '

    cls

    Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
    Select-AzureSubscription -SubscriptionName "Free Trial"

    #Deploy a new VM and join it to the domain
    #-------------------------------------------
    #Specify my DC's DNS IP (10.0.0.4)
    $myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'

    # OS Image to Use
    $image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
    $service = 'DC2'
    $AG = 'YourAffinityGroup'
    $vnet = 'YourVirtualNetwork'
    $pwd = 'P@ssw0rd'
    $size = 'Small'

    #VM Configuration
    $vmname = 'DC2'
    $MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

    New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

如果重新執行指令碼，則您需要提供唯一值給 $service。您可以執行 Test-AzureName -Service *service name*，其會在已取得名稱時傳回。

## 另請參閱

-   [在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針][在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針]  (英文)

-   [在管理入口網站中設定純雲端虛擬網路][在管理入口網站中設定純雲端虛擬網路]

-   [在管理入口網站中設定站對站 VPN][在管理入口網站中設定站對站 VPN]

-   [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台][1]

-   [Windows Azure IT 專業人員 IaaS：(01) 虛擬機器基本概念][Windows Azure IT 專業人員 IaaS：(01) 虛擬機器基本概念] (英文)

-   [Windows Azure IT 專業人員 IaaS：(05) 建立虛擬網路和跨單位連線][Windows Azure IT 專業人員 IaaS：(05) 建立虛擬網路和跨單位連線] (英文)

-   [Azure 虛擬網路][Azure 虛擬網路]

-   [如何安裝和設定 Azure PowerShell][如何安裝和設定 Azure PowerShell]

-   [Azure PowerShell][Azure PowerShell]

-   [Azure 管理 Cmdlet][Azure 管理 Cmdlet]

-   [設定 Azure VM 靜態 IP 位址][設定 Azure VM 靜態 IP 位址]

-   [如何將靜態 IP 指派給 Azure VM (英文)][如何將靜態 IP 指派給 Azure VM (英文)] 

-   [安裝新的 Active Directory 樹系][安裝 AD DS]

-   [簡介 Active Directory 網域服務 (AD DS) 虛擬化 (層級 100)][簡介 Active Directory 網域服務 (AD DS) 虛擬化 (層級 100)]

-   [測試實驗室指南：Azure 中的 Windows Server 2012 R2 基本組態][測試實驗室指南：Azure 中的 Windows Server 2012 R2 基本組態] (英文)

  [Azure 虛擬網路]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx
  [使用管理入口網站精靈設定站台對站台 VPN]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn133795.aspx
  [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制台]: http://www.windowsazure.com/zh-tw/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [在 Azure 虛擬機器上部署 Windows Server Active Directory 的方針]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156090.aspx
  [測試實驗室指南：Azure 中的 Windows Server 2012 R2 基本組態]: http://www.microsoft.com/zh-tw/download/details.aspx?id=41684
  [這與內部部署有何不同？]: #differ
  [步驟 1. 建立 Azure 虛擬網路]: #createvnet
  [步驟 2：建立 VM 來執行網域控制台與 DNS 伺服器角色]: #createvm
  [步驟 3：安裝 Windows Server Active Directory]: #installad
  [步驟 4：設定 Azure 虛擬網路的 DNS 伺服器]: #dns
  [步驟 5：建立網域成員的 VM 並加入網域]: #domainmembers
  [Azure 管理入口網站]: https://manage.windowsazure.com
  [執行 Set-AzureStaticVNetIP Azure PowerShell Cmdlet]: http://msdn.microsoft.com/zh-tw/library/windowsazure/dn630228.aspx
  [安裝 AD DS]: http://technet.microsoft.com/library/jj574166.aspx
  [在管理入口網站中設定純雲端虛擬網路]: http://msdn.microsoft.com/zh-tw/library/dn631643.aspx
  [在管理入口網站中設定站對站 VPN]: http://msdn.microsoft.com/zh-tw/library/dn133795.aspx
  [1]: http://azure.microsoft.com/zh-tw/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/
  [Windows Azure IT 專業人員 IaaS：(01) 虛擬機器基本概念]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT 專業人員 IaaS：(05) 建立虛擬網路和跨單位連線]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
  [如何安裝和設定 Azure PowerShell]: http://www.windowsazure.com/zh-tw/documentation/articles/install-configure-powershell/
  [Azure PowerShell]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156055.aspx
  [Azure 管理 Cmdlet]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj152841
  [設定 Azure VM 靜態 IP 位址]: http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address
  [如何將靜態 IP 指派給 Azure VM (英文)]: http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/
  [簡介 Active Directory 網域服務 (AD DS) 虛擬化 (層級 100)]: http://technet.microsoft.com/zh-tw/library/hh831734.aspx
