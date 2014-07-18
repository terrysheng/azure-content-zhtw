<properties  linkid="manage-services-cross-premises-connectivity" urlDisplayName="Cross-premises Connectivity" pageTitle="Create a cross-premises virtual network - Azure" metaKeywords="" description="Learn how to create an Azure Virtual Network with cross-premises connectivity." metaCanonical="" services="virtual-network" documentationCenter="" title="Create a Virtual Network for Site-to-Site Cross-Premises Connectivity" authors="" solutions="" manager="" editor="" />

<h1>建立虛擬網路進行網站對網站交叉部署連線</h1>   


本教學課程將逐步引導您建立交叉部署虛擬網路。我們要建立的連線類型為網站對網站連線。如果您想要使用憑證和 VPN 用戶端來建立點對網站
VPN，請參閱[使用管理入口網站精靈設定點對站 VPN][1]。

本教學課程假設您先前沒有使用 Azure 的經驗。目的在於協助您熟悉建立網站對網站虛擬網路所需的步驟。如果您在尋找有關虛擬網路的設計案例和進階資訊，請參閱[虛擬網路][2]。

完成本教學課程後，您將擁有可部署 Azure 服務和虛擬機器的虛擬網路，之後便可以透過它們來與公司的網路直接通訊。

如需新增虛擬機器，以及將內部部署 Active Directory 延伸到 Azure 虛擬網路的相關資訊，請參閱以下內容：

* [如何建立自訂虛擬機器][3]

* [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制站][4]

如需在 Azure 虛擬機器上部署 AD DS 的指導方針，請參閱[在 Azure 虛擬機器中部署 Windows Server Active Directory 的指導方針][5]。

如需其他虛擬網路組態程序和設定，請參閱 [Azure 虛擬網路組態工作][6]。

## 目標

在本教學課程中，您將了解：

* 如何設定可新增 Azure 服務的基本 Azure 虛擬網路。

* 如何設定與公司網路通訊的虛擬網路。

## 必要條件

* 至少有一個有效作用中訂閱的 Windows Live 帳戶。

* 要用於虛擬網路和子網路的位址空間 (以 CIDR 表示法表示)。

* DNS 伺服器的名稱和 IP 位址 (如果您想要將內部部署 DNS 伺服器用於名稱解析)。

* 具有公用 IPv4 位址的 VPN 裝置。您需要 IP 位址才能完成精靈。VPN 裝置不能位於 NAT
  後方，且必須滿足最低裝置標準。如需詳細資訊，請參閱[關於虛擬網路的 VPN 裝置][7]。
  
  注意：您可以將 RRAS 當做 VPN 解決方案的一部分。不過本教學課程不會逐步引導您進行 RRAS 的組態步驟。
  
  如需 RRAS 組態資訊，請參閱[路由及遠端存取服務範本][8]。

* 具備設定路由器的經驗或可協助您完成此步驟的助手。

* 本機網路 (內部部署網路) 的位址空間。

## 高階步驟

1.  [建立虛擬網路](#CreateVN)

2.  [啟動閘道並收集網路管理員的資訊](#StartGateway)

3.  [設定 VPN 裝置](#ConfigVPN)

## <a name="CreateVN">建立虛擬網路</a>

**若要建立可連接公司網路的虛擬網路：**

1.  登入 [Azure 管理入口網站][9]。

2.  按一下螢幕左下角的 **新增**。在導覽窗格中依序按一下 **網路** 和 **虛擬網路**。按一下 **Custom Create** 開始組態精靈。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_01_OpenVirtualNetworkWizard.png)

3.  在 **虛擬網路詳細資料** 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需詳細資料頁面中之設定的詳細資訊，請參閱[關於在管理入口網站中設定虛擬網路][10]的**虛擬網路詳細資料頁面**一節。

* **名稱：**為虛擬網路命名。輸入 *YourVirtualNetwork*。

* **同質群組：**在下拉式清單中，選取 **建立新的同質群組**。同質群組可用來將 Azure 服務實際群組至同一個資料中心，以提升效能。您只能將一個虛擬網路指派到一個同質群組。

* **區域：**在下拉式清單中選取需要的區域。將在指定區域中的資料中心內建立虛擬網路。

* **同質群組名稱：**新同質群組的名稱。輸入 *YourAffinityGroup*。
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_02_VirtualNetworkDetails.png)

1.  在 **DNS Servers and VPN Connectivity** 頁面中輸入以下資訊，然後按一下右下角的下一步箭頭。
	<div class="dev-callout">

	<b>注意</b>
	<p>您可以在此頁面中同時選取 [**Point-To-Site**] 和 [**Site-To-Site**]
	等組態。就本教學課程的目的，我們只會選擇設定
	[**Site-To-Site**]。如需本頁面中之設定的詳細資訊，請參閱[關於在管理入口網站中設定虛擬網路][10]的「**DNS Servers and VPN Connectivity**」頁面。</p>
	</div>


* **DNS 伺服器：**輸入要用於名稱解析之 DNS 伺服器的名稱和 IP 位址。這一般是用於內部部署名稱解析的 DNS 伺服器。此設定不會建立 DNS 伺服器。輸入名稱 *YourDNS* 和 IP 位址 *10.1.0.4*。
* **Configure Point-To-Site VPN：**將此欄位保留空白。
* **Configure Site-To-Site VPN：**選取核取方塊。
* **LOCAL NETWORK：**從下拉式清單選取 **Specify a New Local Network**。
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_03_DNSServersandVPNConnectivity.png)

1.  在 **Site-To-Site Connectivity** 頁面中輸入以下資訊，然後按一下頁面右下角的核取記號。如需本頁面中之設定的詳細資訊，請參閱[關於在管理入口網站中設定虛擬網路][10]的**站對站連線能力**頁面一節。

* **名稱：**輸入 *YourCorpHQ*。

* **VPN 裝置 IP 位置：**輸入 VPN 裝置的公用 IP 位址。如果您缺少這項資訊，請先取得該資訊後再前往精靈的下一個步驟。請注意，VPN 裝置不得位於 NAT 後方。如需有關 VPN 裝置的詳細資訊，請參閱[關於虛擬網路的 VPN 裝置][11]。

* **位址空間：**輸入 *10.1.0.0/16*。
* **新增位址空間：**本教學課程不需要額外的位址空間。
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_04_SitetoSite.png)

1.  在 **Virtual Network Address Spaces** 頁面中輸入以下資訊，然後按一下右下角的核取記號以設定網路。
    
    位址空間必須是以 CIDR 表示法指定的私人位址範圍 10.0.0.0/8、172.16.0.0/12 或 192.168.0.0/16 (如 RFC 1918 所指定)。如需此頁面中之設定的詳細資訊，請參閱[關於在管理入口網站中設定虛擬網路][10]的**虛擬網路位址空間頁面**一節。

* **位址空間：**按一下右上角的 **CIDR**，然後輸入以下資訊：
  * **起始 IP：**10.4.0.0
  * **CIDR：**/16

* **新增子網路：**輸入以下資訊：
  * 將 **Subnet-1** 重新命名為 *FrontEndSubnet* 並指派起始 IP *10.4.2.0/24*，然後按一下 **add subnet**。
  * **新增子網路**名為 *BackEndSubnet*，並指派起始 IP *10.4.3.0/24*。
  * **新增子網路**名為 *ADDNSSubnet*，並指派起始 IP *10.4.4.0/24*。
  * **新增閘道子網路**，並指派起始 IP *10.4.1.0/24*。
  * **確認**您現在已建立三個子網路和一個閘道子網路，然後按一下右下角的核取記號以建立虛擬網路。
  
	![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_05_VirtualNetworkAddressSpaces.png)

1.  按一下核取記號後，隨即會開始建立虛擬網路。當虛擬網路建立完成時，您可以在管理入口網站的 [網路] 頁面 [狀態] 下看見 [已建立]。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_06_VirtualNetworkCreatedStatus.png)

## <a name="StartGateway">啟動閘道</a>

在建立 Azure 虛擬網路後，請使用以下程序來設定虛擬網路閘道，以便建立站台對站台 VPN。此程序須有滿足最低需求的 VPN 裝置。如需有關
VPN 裝置和裝置組態的詳細資訊，請參閱[關於虛擬網路的 VPN 裝置][7]。

**若要啟動閘道：**

1.  當虛擬網路建立完成時，**網路** 頁面會將您的虛擬網路狀態顯示為 **已建立**。
    
    按一下 **名稱** 欄中的 **YourVirtualNetwork** 以開啟儀表板。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_07_ClickYourVirtualNetwork.png)

2.  按一下頁面頂端的 **儀表板**。在 [儀表板] 頁面上，於頁面底部按一下 **CREATE GATEWAY**。針對您要建立的閘道類型選取 **動態路由** 或 **靜態路由**。
    
    請注意，除了「站台對站台連線」，如果您還想要將此虛擬網路用於「點對站台連線」，必須選取 [動態路由] 做為閘道類型。在建立閘道之前，請確認 VPN 裝置可支援您要建立的閘道類型。請參閱[關於虛擬網路的 VPN 裝置]**7]。當系統提示您確認是否要建立閘道時，請按一下 [是**。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_08_CreateGateway.png)

3.  當閘道建立開始時，系統會顯示訊息來通知您閘道已啟動。
    
    閘道建立作業最多可能需要花費 15 分鐘的時間。

4.  待閘道建立完成後，您需要收集以下用來設定 VPN 裝置的資訊。

* 閘道 IP 位址
* 共用金鑰
* VPN 裝置組態指令碼範本
  
	接下來的步驟將逐步引導您完成此程序。

1.  若要找出閘道 IP 位址 - 閘道 IP 位址位於虛擬網路 **儀表板** 頁面。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_09_GatewayIP.png)

2.  若要取得共用金鑰 - 共用金鑰位於虛擬網路 **儀表板** 頁面。按一下畫面底部的 [管理金鑰]，然後複製對話方塊所顯示的金鑰。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVNet_10_ManageSharedKey.png)

3.  下載 VPN 裝置組態指令碼範本。按一下儀表板上的 **Download VPN Device Script**。

4.  在 **Download a VPN Device Configuration Script** 對話方塊中，選取公司 VPN 裝置的廠商、平台及作業系統。按一下核取記號按鈕並儲存檔案。
    
    ![](./media/virtual-networks-create-site-to-site-cross-premises-connectivity/CreateCrossVnet_11_DownloadVPNDeviceScript.png)

如果您在下拉式清單中找不到您的 VPN 裝置，請參閱 MSDN 文件庫中的[關於虛擬網路的 VPN 裝置][7]，以取得其他指令碼範本。

## <a name="ConfigVPN">設定 VPN 裝置 (網路管理員)</a>

由於 VPN 裝置不盡相同，因此本內容僅提供高階程序。此程序應交由網路管理員來執行。

您可以從管理入口網站或[關於虛擬網路的 VPN 裝置][12]取得 VPN 組態指令碼，該文章亦說明與您選擇使用之路由組態相容的路由類型和裝置。

如需設定虛擬網路閘道的額外資訊，請參閱[在管理入口網站中設定虛擬網路閘道][13]，並查閱 VPN 裝置文件。

此程序的假設如下：

* 設定 VPN 裝置的人員熟悉選用裝置的設定作業。由於與虛擬網路相容的裝置繁多，且每個裝置系列均有專用的組態，下列步驟不會逐步引導您完成詳細的裝置組態。因此，設定裝置的人員務必熟悉裝置和裝置的組態設定。

* 您選擇使用的裝置與虛擬網路相容。請查閱[此處][7]以了解裝置相容性。

**若要設定 VPN 裝置：**

1.  修改 VPN 組態指令碼。您需要設定以下內容：
    
    a. 安全性原則
    
    b. 連入通道
    
    c. 連出通道

2.  執行經過修改的 VPN 組態指令碼以設定 VPN 裝置。

3.  執行以下任一命令以測試連線：
    
    <table  border="1">
    <tr>
    <th>-</th>
    
    <th>Cisco ASA</th>
    
    <th>Cisco ISR/ASR</th>
    
    <th>Juniper SSG/ISG</th>
    
    <th>Juniper SRX/J</th>
    
    </tr>
    
    	
    <tr>
    <td><b>檢查主要模式 SA</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto isakmp sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get ike cookie</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ike security-association</font>
    </td>
    
    </tr>
    
    	
    <tr>
    <td><b>檢查快速模式 SA</b>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show crypto ipsec sa</font>
    </td>
    
    <td><font  face="courier" size="-1">get sa</font>
    </td>
    
    <td><font  face="courier" size="-1">show security ipsec security-association</font>
    </td>
    
    </tr>
    
    </table>

## 後續步驟

為了將內部部署 Active Directory 延伸到剛建立的虛擬網路，請繼續進行以下教學課程中的內容：

* [如何建立自訂虛擬機器][3]

* [在 Azure 虛擬網路中安裝複本 Active Directory 網域控制站][4]

如果您想要將虛擬網路設定匯出到網路組態檔，以便備份組態或當做範本，請參閱[將虛擬網路設定匯出到網路組態檔][14]。

## 另請參閱

* [Azure 虛擬網路][2]

* [虛擬網路常見問題集][15]

* [使用網路組態檔設定虛擬網路][16]

* [將虛擬機器新增至虛擬網路][17]

* [關於虛擬網路的 VPN 裝置][11]

* [名稱解析][18]



[1]: http://go.microsoft.com/fwlink/?LinkId=296653
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx
[3]: http://go.microsoft.com/fwlink/?LinkID=294356
[4]: http://go.microsoft.com/fwlink/?LinkId=299877
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx
[6]: http://go.microsoft.com/fwlink/?LinkId=296652
[7]: http://go.microsoft.com/fwlink/?LinkID=248098
[8]: http://msdn.microsoft.com/library/windowsazure/dn133801.aspx
[9]: http://manage.windowsazure.com/
[10]: http://go.microsoft.com/fwlink/?LinkID=248092
[11]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156075.aspx
[12]: http://go.microsoft.com/fwlink/?LinkId=248098
[13]: http://go.microsoft.com/fwlink/?LinkId=299878
[14]: http://go.microsoft.com/fwlink/?LinkID=299880
[15]: http://msdn.microsoft.com/library/windowsazure/dn133803.aspx
[16]: http://msdn.microsoft.com/en-us/library/windowsazure/jj156097.aspx
[17]: http://www.windowsazure.com/en-us/manage/services/networking/add-a-vm-to-a-virtual-network/
[18]: http://go.microsoft.com/fwlink/?LinkId=248097