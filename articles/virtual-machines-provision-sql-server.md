<properties 
	pageTitle="在 Azure 中佈建 SQL Server 虛擬機器" 
	description="本教學課程會教您如何在 Azure 上建立及設定 SQL Server 虛擬機器。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcin"/>





# 在 Azure 上佈建 SQL Server 虛擬機器 #

Azure 虛擬機器組件庫涵蓋數個包含 Microsoft SQL Server 的映像。您可以從組件庫選取任一個虛擬機器映像，只要點擊幾下便可以將虛擬機器佈建至 Azure 環境。

在本教學課程中，您將：

* [連接 Azure 管理入口網站並從組件庫佈建虛擬機器](#Provision)
* [使用遠端桌面開啟虛擬機器及完成設定](#RemoteDesktop)
* [完成在另一部電腦上使用 SQL Server Management Studio 連接虛擬機器的組態步驟](#SSMS)
* [後續步驟](#Optional)

##<a id="Provision">連接 Azure 管理入口網站並從組件庫佈建虛擬機器</a>

1. 使用您的帳戶登入 [Azure 管理入口網站](http://manage.windowsazure.com)。如果您沒有 Azure 帳戶，請造訪 [Azure 免費試用](http://www.windowsazure.com/pricing/free-trial/)。

2. 在 Azure 管理入口網站中，依序按一下網頁左下角的 [**+新增**]、[**運算**]、[**虛擬機器**] 和 [**從組件庫**]。

3. 在 [**選擇映像**] 頁面上，按一下 [**SQL SERVER**]。然後選取 SQL Server 映像。按一下頁面右下方的 [下一步] 箭頭。 
![Choose an Image][Image34]


如需 Azure 支援的 SQL Server 映像最新的資訊，請參閱 [Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719) 文件集內的 [開始使用 Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294720) 主題。 

    >[AZURE.NOTE] 如果您擁有以 SQL Server Evaluation Edition 平台映像建立的虛擬機器，該虛擬機器無法升級為組件庫內以分鐘計費之版本的映像。您可以在以下兩個選項中選擇其中一項：
    
    > - 您可以使用組件庫內以分鐘計費的 SQL Server 版本來建立新虛擬機器，然後再遵循 [如何使用資料磁碟在 Azure 虛擬機器之間移轉 SQL Server 資料庫檔案和結構描述](http://go.microsoft.com/fwlink/p/?LinkId=294738) 中的步驟，將資料庫檔案移轉到這部新虛擬機器。**或者**，

    > - 您可以遵循 [升級為不同的 SQL Server 2014 版本 (安裝程式)](http://go.microsoft.com/fwlink/?LinkId=396915) 中的步驟，在 [Azure 上透過軟體保證的授權流動性](http://www.windowsazure.com/pricing/license-mobility/) 協定的規範下，將現有 SQL Server Evaluation Edition 執行個體升級為不同版本的 SQL Server。如需如何購買授權版本之 SQL Server 的詳細資訊，請參閱 [如何購買 SQL Server](http://www.microsoft.com/sqlserver/get-sql-server/how-to-buy.aspx)。
   

4. 在第一個 [**虛擬機器組態**] 頁面，請提供下列資訊：
	- **版本發行日期**。如果有多個映像，請選取最新映像。
	- 唯一的 [**虛擬機器名稱**]。
	- 在 [**新使用者名稱**] 方塊中，輸入此機器之本機系統管理員帳戶的唯一使用者名稱。
	- 在 [**新密碼**] 方塊中，輸入強式密碼。 
	- 在 [**確認密碼**] 方塊中，重新輸入密碼。
	- 從 [**大小**] 下拉式清單中選取適當的大小。 

	>[AZURE.NOTE] 虛擬機器的大小可在佈建期間指定：
 	> A2 是生產工作負載的最小建議大小。 
    > 使用 SQL Server Enterprise Edition 時，虛擬機器的最小建議大小為 A3。
    > 當您使用 SQL Server Enterprise Edition 時，請選取 A3 或以上的大小。
   	> 使用 SQL Server 2012 或 2014 Enterprise Optimized for Transactional Workloads 映像時，請選取 A4。  
   	> 使用 SQL Server 2012 或 2014 Enterprise Optimized for Data Warehousing Workloads 映像時，請選取 A7。 
   	> T選取的大小會限制可設定的資料磁碟數目。如需可用之虛擬機器大小和可連接至虛擬機器之資料磁碟數目的最新資訊，請參閱 [Azure 的虛擬機器大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。

	按一下右下角的 [下一步] 箭頭以繼續操作。

	![VM Configuration](./media/virtual-machines-provision-sql-server/4VM-Config.png)


5. 在第二個 [**虛擬機器組態**] 頁面上，請設定網路、儲存體和可用性的資源：
	- 在 [**雲端服務**] 方塊中，選擇 [**建立新的雲端服務**]。
	- 在 [**雲端服務 DNS 名稱**] 方塊中，提供所選 DNS 名稱的第一個部分，使其形成 **TESTNAME.cloudapp.net** 格式的名稱 
	- 如果您有多個訂閱可供選擇，請選取 [**訂閱**]。此選擇會決定可用的**儲存體帳戶**。
	- 在 [**區域/同質群組/虛擬網路**] 方塊中，選取代管這個虛擬映像的所在區域。
	- 在 [**儲存體帳戶**] 中，可以自動產生帳戶，或從清單中選取一個帳戶。變更 [**訂閱**] 以查看更多帳戶。 
	- 在 [**可用性設定組**] 方塊中，選取 [**(無)**]。
	- 閱讀及接受法律條款。
	

6. 按 [下一步] 箭頭以繼續。


7. 按一下右下角的核取記號以繼續操作。

8. 等待 Azure 準備好您的虛擬機器。虛擬機器應該會依序經歷以下狀態變更：

	- 啟動中 (佈建中)
	- 已停止
	- 啟動中 (佈建中)
	- 執行中 (佈建中)
	- 執行中
	

##<a id="RemoteDesktop">使用遠端桌面開啟虛擬機器及完成設定</a>

1. 佈建完成時，請按一下虛擬機器的名稱以前往 [儀表板] 頁面。按一下頁面底部的 [**連接**]。

3. 在 [**Windows 安全性**] 對話方塊中，提供在先前步驟中指定的本機系統管理員帳戶密碼。(系統可能會要求您確認虛擬機器的認證。)

4. 當您第一次登入這個虛擬機器時，可能需要完成數個程序，包括設定桌面、Windows Update，以及完成 Windows 初始組態工作 (sysprep)。待 Windows sysprep 完成後，SQL Server 安裝程式會完成組態工作。這些工作完成時，可能會導致幾分鐘的延遲。SQL Server 安裝程式完成時，`SELECT @@SERVERNAME` 才會傳回正確的名稱，而且 SQL Server Management Studio 才會出現在起始頁面​​。

使用 Windows 遠端桌面連接到虛擬機器之後，虛擬機器的運作方式與任何其他電腦很像。請依照正常方法使用 SQL Server Management Studio (於虛擬機器上運作) 連接 SQL Server 的預設執行個體。 

##<a id="SSMS">完成在另一部電腦上使用 SQL Server Management Studio 連接虛擬機器的組態步驟</a>

您必須先完成後續各小節描述的以下工作，才能從網際網路連接 SQL Server 的執行個體：

- [為虛擬機器建立 TCP 端點](#Endpoint)
- [在 Windows 防火牆中開啟 TCP 連接埠](#FW)
- [設定 SQL Server 以接聽 TCP 通訊協定](#TCP)
- [設定 SQL Server 以進行混合模式驗證](#Mixed)
- [建立 SQL Server 驗證登入](#Logins)
- [決定虛擬機器的 DNS 名稱](#DNS)
- [從另一部電腦連接 Database Engine](#cde)
- [從應用程式連接 Database Engine](#cdea)

連線路徑如以下圖表總結：

![Connecting to a SQL Server virtual machine][Image8b]

##<a id="Endpoint">為虛擬機器建立 TCP 端點</a>

虛擬機器必須具有端點才能接聽傳入 TCP 通訊。此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

1. 在 Azure 管理入口網站中按一下 [**虛擬機器**]。

	
2. 按一下新建立的虛擬機器。畫面上即會顯示您虛擬機器的相關資訊。
	

3. 選取頁面頂端附近的 [**端點**] 頁面，然後按一下頁面底部的 [**新增**]。
	

4. 在 [**將端點新增到虛擬機器**] 頁面中按一下 [**新增單一端點**]，然後按 [下一步] 箭頭以繼續操作。

	
5. 在 [**指定端點的詳細資料**] 頁面中，提供下列資訊。

	- 在 [**名稱**] 方塊中，提供端點的名稱。
	- 在 [**通訊協定**] 方塊中，選取 [**TCP**]。您可以在 [**公用連接埠**] 方塊中輸入 **57500**。同樣地，您可以在 [**私人連接埠**] 方塊中輸入 SQL Server 的預設接聽連接埠 **1433**。請注意，有許多組織會藉由選取不同連接埠號碼來避免惡意的安全性攻擊。 


6. 按一下打勾記號繼續。系統隨即會建立端點。
	

##<a id="FW">在 Windows 防火牆中為 Database Engine 的預設執行個體開啟 TCP 連接埠</a>

1. 透過 Windows 遠端桌面連接虛擬機器。登入後，在 [開始] 畫面中輸入 **WF.msc**，然後按 ENTER 鍵。 

	![Start the Firewall Program][Image12]
2. 在 [**具有進階安全性的 Windows 防火牆**] 的左窗格中，以滑鼠右鍵按一下 [**輸入規則**]，然後在動作窗格中按一下 [**新增規則**］。

	![New Rule][Image13]

3. 在 [**新內送規則精靈**] 對話方塊的 [**規則類型**] 下， 選取 [**連接埠**] 然後按 [**下一步**]。

4. 在 [**通訊協定及連接埠**] 對話方塊中，使用預設的 [**TCP**]。在 [**特定本機連接埠**] 方塊中，接著輸入 Database Engine 執行個體的連接埠號碼 (預設執行個體的連接埠號碼為 **1433**，也可以是在端點步驟中選擇的私人連接埠)。 

	![TCP Port 1433][Image14]

5. 按 [**下一步**]。

6. 在 [**動作**] 對話方塊中選取 [**允許連線**]，然後按 [**下一步**]。

	**安全性注意事項：**選取 [**僅允許安全連線**] 可以提供額外的安全性。如果您想要在您的環境中設定額外的安全性選項，請選取此選項。

	![Allow Connections][Image15]

7. 在 [**設定檔**] 對話方塊中選取 [**公用**]，然後按 [**下一步**]。 

    **安全性注意事項：**  選取 [**公用**] 即可允許透過網際網路存取。請盡可能選取較具有限制性的設定檔。

	![Public Profile][Image16]

8. 在 [**名稱**] 對話方塊中輸入此規則的名稱和描述，然後按一下 [**完成**]。

	![Rule Name][Image17]

視需要為其他元件開啟額外的連接埠。如需詳細資訊，請參閱 [設定 Windows 防火牆以允許 SQL Server 存取](http://msdn.microsoft.com/library/cc646023.aspx)。


###<a id="TCP">設定 SQL Server 以接聽 TCP 通訊協定</a>

1. 連接到虛擬機器時，在開始頁面上輸入 **SQL Server 組態管理員**，然後按 ENTER 鍵。
	
	![Open SSCM][Image9]

2. 在 SQL Server 組態管理員的主控台窗格中，展開 [**SQL Server 網路組態**]。

3. 在主控台窗格中，按一下 [**MSSQLSERVER 的通訊協定**] (預設的執行個體名稱)。在詳細資料窗格中以滑鼠右鍵按一下 [TCP]。依預設，組件庫映像的 TCP 應該是 [已啟用]。對於自訂映像，按一下 [**啟用**] (如果它的狀態是 [已停用])。

	![Enable TCP][Image10]

5. 在主控台窗格中，按一下 [**SQL Server 服務**]。在詳細資料窗格中，以滑鼠右鍵按一下 **SQL Server (_instance name_)** (預設執行個體是 **SQL Server (MSSQLSERVER)**)，然後按一下 [**重新啟動**]，以停止並重新啟動 SQL Server 執行個體。 

	![Restart Database Engine][Image11]

7. 關閉 SQL Server 組態管理員。

如需啟用 SQL Server Database Engine 之通訊協定的詳細資訊，請參閱 [啟用或停用伺服器網路通訊協定](http://msdn.microsoft.com/library/ms191294.aspx)。

###<a id="Mixed">設定 SQL Server 以進行混合模式驗證</a>

SQL Server Database Engine 須有網域環境才能使用 Windows 驗證。若要從另一部電腦連接 Database Engine，請設定 SQL Server 以進行混合模式驗證。混合模式驗證可允許 SQL Server 驗證和 Windows 驗證。如果您已設定 Azure 虛擬網路，可能就不需要設定混合模式驗證。如需詳細資訊，請參閱 [Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/?LinkId=294719) 文件集內的 [Azure 虛擬機器中的 SQL Server 連接性考量](http://go.microsoft.com/fwlink/?LinkId=294723)。

1. 連接到虛擬機器時，在開始頁面上輸入 **SQL Server 2014 Management Studio**，然後按一下選取的圖示。

	![Start SSMS][Image18]

	當您首次開啟 Management Studio 時，它必須建立使用者 Management Studio 環境。這可能需要花費幾分鐘的時間。

2. Management Studio 會出現 [**連接到伺服器**] 對話方塊。在 [**伺服器名稱**] 方塊中，輸入虛擬機器的名稱，以利用物件總管連接 Database Engine。除了虛擬機器名稱之外，您還可以使用 **(本機)**，也可以將一個句點當作 **伺服器名稱**。選取 [**Windows 驗證**]，然後在 [**使用者名稱**] 方塊中保留 **_your_VM_name_\your_local_administrator**。按一下 [**連接**]。

	![Connect to Server][Image19]

3. 在 SQL Server Management Studio 物件總管中，以滑鼠右鍵按一下 SQL Server 執行個體的名稱 (虛擬機器名稱)，然後按一下 [**屬性**]。

	![Server Properties][Image20]

4. 在 [**安全性**] 頁面的 [**伺服器驗證**] 下，選取 [**SQL Server 和 Windows 驗證模式**]，然後按一下 [**確定**]。

	![Select Authentication Mode][Image21]

5. 在 [SQL Server Management Studio] 對話方塊中按一下 [**確定**]，以確認重新啟動 SQL Server 的需求。

6. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後按一下 [**重新啟動**]。(如果 SQL Server Agent 處於執行狀態，您也必須將其重新啟動。)

	![Restart][Image22]

7. 在 [SQL Server Management Studio] 對話方塊中，按一下 [**是**] 同意重新啟動 SQL Server。

###<a id="Logins">建立 SQL Server 驗證登入</a>

若要從另一部電腦連接 Database Engine，您至少必須建立一個 SQL Server 驗證登入。

1. 在 SQL Server Management Studio 物件總管中，展開要建立新登入之伺服器執行個體的資料夾。

2. 以滑鼠右鍵按一下 [**安全性**] 資料夾，並指向 [**新增**]，然後選取 [**登入...**]。

	![New Login][Image23]

3. 在 [**登入 - 新增**] 對話方塊的 [**一般**] 頁面上，於 [**登入名稱**] 方塊中輸入新使用者的名稱。

4. 選取 [**SQL Server 驗證**]。

5. 在 [**密碼**] 方塊中，輸入新使用者的密碼。在 [**確認密碼**] 方塊中，再次輸入該組密碼。

6. 若要強制執行複雜性和強制性密碼原則選項，請選取 [**強制執行密碼原則**] (建議)。此為選取 SQL Server 驗證時的預設選項。

7. 若要強制執行逾期密碼原則選項，請選取 [**強制執行密碼逾期**] (建議)。您必須選取強制執行密碼原則才能啟用此核取方塊。此為選取 SQL Server 驗證時的預設選項。

8. 若要強制使用者在首次登入後建立新密碼，請選取 [**使用者必須在下次登入時變更密碼**] (如果此登入是供其他使用者使用，建議您選取此選項。如果此登入是供您自己使用，請勿選取此選項。)您必須選取強制執行密碼逾期才能啟用此核取方塊。此為選取 SQL Server 驗證時的預設選項。 

9. 在 [**預設資料庫**] 清單中，選取登入的預設資料庫。**master** 是此選項的預設值。如果您尚未建立使用者資料庫，請保留 **master** 的設定。

10. 在 [**預設語言**] 清單中，保留 [**預設**] 值。
    
	![Login Properties][Image24]

11. 如果您正在建立第一個登入，也許會想要將此登入指定為 SQL Server 系統管理員。若是如此，請在 [**伺服器角色**] 頁面中核取 [**sysadmin**]。 

	**安全性注意事項：** sysadmin 固定伺服器角色的成員擁有 Database Engine 的完整控制權。您應該謹慎限制此角色的成員資格。

	![sysadmin][Image25]

12. 按一下 [確定]。

如需 SQL Server 登入的詳細資訊，請參閱 [建立登入](http://msdn.microsoft.com/library/aa337562.aspx)。



###<a id="DNS">決定虛擬機器的 DNS 名稱</a>

若要從另一部電腦連接 SQL Server Database Engine，您必須知道虛擬機器的網域名稱系統 (DNS) 名稱。(這是網際網路用來識別虛擬機器的名稱。您可以使用 IP 位址，不過當 Azure 因備援或維護而移動資源時，IP 位址可能會改變。DNS 名稱是穩定的，因為它可以重新導向新的 IP 位址。)  

1. 在 Azure 管理入口網站 (或前一個步驟) 中，選取 [**虛擬機器**]。 

2. 在 [**虛擬機器執行個體**] 頁面的 [**快速概覽**] 欄中，尋找並複製虛擬機器的 DNS 名稱。

![DNS name][Image35]
	

### <a id="cde">從另一部電腦連接 Database Engine</a>
 
1. 在連接網際網路的電腦上開啟 SQL Server Management Studio。
2. 在 [**連接到伺服器**] 或 [**連接到 Database Engine**] 對話方塊的 [**伺服器名稱**] 方塊中，輸入虛擬機器的 DNS 名稱 (於上一個工作中決定) 和採用 *DNSName,portnumber* 格式的公用端點連接埠名稱 (例如 **tutorialtestVM.cloudapp.net,57500**)。
若要取得連接埠號碼，請登入 Azure 管理入口網站並尋找虛擬機器。在儀表板上按一下 [**端點**]，並使用指派給 [**MSSQL**] 的 [**公用連接埠**]。
![Public Port][Image36]
3. 在 [**驗證**] 方塊中，選取 [**SQL Server 驗證**]。
5. 在 [**登入**] 方塊中，輸入於先前工作中建立的登入名稱。
6. 在 [**密碼**] 方塊中，輸入於先前工作中建立的登入密碼。
7. 按一下 [**連接**]。

	![Connect using SSMS][Image33]

### <a id="cdea"> 從應用程式連接 Database Engine</a>

如果您可以使用 Management Studio 連接於 Azure 虛擬機器上運作的 SQL Server 執行個體，應該能使用與下文相似的連線字串進行連接。

	connectionString="Server=<DNS_Name>;Integrated Security=false;User ID=<login_name>;Password=<your_password>;"providerName="System.Data.SqlClient"

如需詳細資訊，請參閱 [如何疑難排解 SQL Server Database Engine 連線](http://social.technet.microsoft.com/wiki/contents/articles/how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)。

##<a id="Optional">後續步驟</a>
您已了解如何使用平台映像來建立及設定 Azure 虛擬機器上的 SQL Server。在 Azure 虛擬機器中使用 SQL Server 時，建議您遵循文件庫中 [Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719) 文件的詳細指引。此文件集含有一系列提供詳細指引的文章和教學課程。這一系列的內容包括以下小節：

[Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719)

[開始使用 Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294720)

[準備移轉到 Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294721)

- 如何使用資料磁碟在 Azure 虛擬機器之間移轉 SQL Server 資料庫檔案和結構描述

[Azure 虛擬機器中的 SQL Server 部署](http://go.microsoft.com/fwlink/p/?LinkId=294722)

- 如何使用 CSUpload，將資料磁碟中的 SQL Server 資料和安裝程式檔案從內部部署複製到 Azure
- 如何使用 Hyper-V 建立內部部署的基礎虛擬機器
- 如何使用現有的內部部署 SQL Server 磁碟在 Azure 中建立 SQL Server 虛擬機器
- 如何使用現有的內部部署 SQL Server 虛擬機器，在 Azure 中建立 SQL Server 虛擬機器 
- 如何使用 PowerShell 設定 Azure 中的 SQL Server 虛擬機器 
- 如何使用連接的資料磁碟來儲存資料庫檔案

[Azure 虛擬機器中的 SQL Server 連接性考量](http://go.microsoft.com/fwlink/p/?LinkId=294723)

- 教學課程：連接相同雲端服務中的 SQL Server 
- 教學課程：連接不同雲端服務中的 SQL Server 
- 教學課程：透過虛擬網路，將 ASP.NET 應用程式連接至 Azure 的 SQL Server 

[Azure 虛擬機器中的 SQL Server 效能考量](http://go.microsoft.com/fwlink/?LinkId=294724)

[Azure 虛擬機器中的 SQL Server 安全性考量](http://go.microsoft.com/fwlink/p/?LinkId=294725)

[疑難排解和監視 Azure 虛擬機器中的 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294726)

[Azure 虛擬機器中的 SQL Server 高可用性和災害復原](http://go.microsoft.com/fwlink/p/?LinkId=294727)

- 教學課程：Azure 中的 AlwaysOn 可用性群組 (GUI)
- 教學課程：Azure 中的 AlwaysOn 可用性群組 (PowerShell)
- 教學課程：AlwaysOn 可用性群組的接聽程式設定
- 教學課程：加入 Azure 複本精靈
- 教學課程：Azure 中因應災害復原的資料庫鏡像
- 教學課程：混合式 IT 中因應災害復原的資料庫鏡像 
- 教學課程：Azure 中提供高可用性的資料庫鏡像
- 教學課程：混合式 IT 中因應災害復原的記錄傳送 
- 針對 Azure 中的可用性群組接聽程式進行疑難排解

[Azure 虛擬機器中的 SQL Server 備份和還原](http://go.microsoft.com/fwlink/p/?LinkId=294728)

[Azure 虛擬機器中的 SQL Server Business Intelligence](http://go.microsoft.com/fwlink/p/?LinkId=294729)

- 透過 SQL Server BI 和 SharePoint 2010 使用 PowerShell 建立 Azure VM
- 透過 SQL Server BI 和 SharePoint 2013 使用 PowerShell 建立 Azure VM
- 使用 PowerShell 建立具有原生模式報表伺服器的 Azure VM

[Azure 虛擬機器中的 SQL Server 資料倉儲](http://msdn.microsoft.com/library/windowsazure/dn387396.aspx)

[Azure 虛擬機器中 SQL Server 適用的技術文件](http://msdn.microsoft.com/library/azure/dn248435.aspx)

- [白皮書：了解 Azure 虛擬機器中的 Azure SQL Database 和 SQL Server](http://azure.microsoft.com/documentation/articles/data-management-azure-sql-database-and-sql-server-iaas/)

- [白皮書：Azure 虛擬機器中的 SQL Server 應用程式模式和開發策略](http://msdn.microsoft.com/library/azure/dn574746.aspx)

- [白皮書：在 Azure 虛擬機器中部署 SQL Server Business Intelligence](http://msdn.microsoft.com/library/windowsazure/dn321998.aspx)

- [白皮書：Azure 虛擬機器中的 SQL Server 效能指引](http://msdn.microsoft.com/library/windowsazure/dn248436.aspx)

- [白皮書：Reporting Services 報告檢視器控制項和 Microsoft Azure 虛擬機器型報表伺服器](http://msdn.microsoft.com/library/azure/dn753698.aspx)

[Image5]: ./media/virtual-machines-provision-sql-server/5VM-Mode.png
[Image5b]: ./media/virtual-machines-provision-sql-server/5VM-Connect.png
[Image6]: ./media/virtual-machines-provision-sql-server/6VM-Options.png
[Image8b]: ./media/virtual-machines-provision-sql-server/SQLServerinVMConnectionMap.png
[Image9]: ./media/virtual-machines-provision-sql-server/9Click-SSCM.png
[Image10]: ./media/virtual-machines-provision-sql-server/10Enable-TCP.png
[Image11]: ./media/virtual-machines-provision-sql-server/11Restart.png
[Image12]: ./media/virtual-machines-provision-sql-server/12Open-WF.png
[Image13]: ./media/virtual-machines-provision-sql-server/13New-FW-Rule.png
[Image14]: ./media/virtual-machines-provision-sql-server/14Port-1433.png
[Image15]: ./media/virtual-machines-provision-sql-server/15Allow-Connection.png
[Image16]: ./media/virtual-machines-provision-sql-server/16Public-Profile.png
[Image17]: ./media/virtual-machines-provision-sql-server/17Rule-Name.png
[Image18]: ./media/virtual-machines-provision-sql-server/18Start-SSMS.png
[Image19]: ./media/virtual-machines-provision-sql-server/19Connect-to-Server.png
[Image20]: ./media/virtual-machines-provision-sql-server/20Server-Properties.png
[Image21]: ./media/virtual-machines-provision-sql-server/21Mixed-Mode.png
[Image22]: ./media/virtual-machines-provision-sql-server/22Restart2.png
[Image23]: ./media/virtual-machines-provision-sql-server/23New-Login.png
[Image24]: ./media/virtual-machines-provision-sql-server/24Test-Login.png
[Image25]: ./media/virtual-machines-provision-sql-server/25sysadmin.png
[Image28]: ./media/virtual-machines-provision-sql-server/28Add-Endpoint.png
[Image29]: ./media/virtual-machines-provision-sql-server/29Add-Endpoint-to-VM.png
[Image30]: ./media/virtual-machines-provision-sql-server/30Endpoint-Details.png
[Image31]: ./media/virtual-machines-provision-sql-server/31VM-Connect.png
[Image32]: ./media/virtual-machines-provision-sql-server/32DNS-Name.png
[Image33]: ./media/virtual-machines-provision-sql-server/33Connect-SSMS.png
[Image34]: ./media/virtual-machines-provision-sql-server/choose-sql-vm.png
[Image35]: ./media/virtual-machines-provision-sql-server/sql-vm-dns-name.png
[Image36]: ./media/virtual-machines-provision-sql-server/sql-vm-port-number.png

<!--HONumber=47-->
