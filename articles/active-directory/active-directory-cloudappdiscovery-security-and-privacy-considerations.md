<properties 
	pageTitle="Cloud App Discovery 的安全性和隱私權考量" 
	description="本主題描述與 Cloud App Discovery 相關的安全性和隱私權考量。" 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Cloud App Discovery 的安全性和隱私權考量

Microsoft 致力於保護您的隱私權和保護資料安全，同時提供軟體和服務，協助您管理組織的安全性。<br>我們了解當您將資料委託給他人管理時，必須有投資嚴密的安全性工程，以及背後支援的專業知識，才能獲得您的信任。Microsoft 從實行安全的軟體開發週期到運作服務，均遵守嚴格的規範與安全性指導方針。<br>保全和保護資料在 Microsoft 是第一要務。

本主題說明在 Azure Active Directory Cloud App Discovery 內如何收集、處理以及保護資料




##概觀

Cloud App Discovery 是 Azure AD 的功能，裝載在 Microsoft Azure 中。<br>Cloud App Discovery 端點代理程式用來從受 IT 管理的電腦收集應用程式探索資料。<br>收集的資料會透過加密的通道安全地傳送到 Azure AD Cloud App Discovery 服務。<br>然後在 Azure 入口網站中就會看到組織的 Cloud App Discovery 資料。


<center>![Cloud App Discovery 的運作方式](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


下列各節會依照資訊的流程，並且描述資訊從您的組織移動到 Cloud App Discovery 服務時如何受到保護，最後到達 Cloud App Discovery 入口網站。



## 從您的組織收集資料

若要使用 Azure Active Directory 的 Cloud App Discovery 功能，深入了解您組織中的員工所使用的應用程式，您必須先在組織中的電腦部署 Azure AD Cloud App Discovery 端點代理程式。

Azure Active Directory 租用戶 (或其委派) 的系統管理員可以從 Azure 入口網站下載代理程式安裝封裝。您可以手動安裝，或使用 SCCM 或群組原則在組織中的多部電腦上安裝代理程式。

如需部署選項的進一步指示，請參閱 [Cloud App Discovery 群組原則部署指南](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)。


### 代理程式收集的資料

下列清單中所描述的資訊是連線到 Web 應用程式時由代理程式收集。只會針對系統管理員已設定進行應用程式探索的應用程式收集資訊。<br>您可以在入口網站的 [設定] 索引標籤下，選擇要收集中繼資料的應用程式清單。



> [AZURE.NOTE]如需詳細資訊，請參閱[開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 
**資訊類別**：使用者資訊<br>**描述**：<br>向目標 Web 應用程式提出要求之程序的 Windows 使用者名稱 (例如：DOMAIN\\username) 以及使用者的 Windows 安全性識別碼 (SID)。


**資訊類別**：處理資訊<br>**描述**：<br>向目標 Web 應用程式提出要求的程序名稱 (例如：“iexplore.exe”)

**資訊類別**：電腦資訊<br>**描述**：<br>安裝代理程式的電腦 NetBIOS 名稱。

**資訊類別**：App 流量資訊<br>**描述**：<br>

下列連線資訊：

- 來源 (本機電腦) IP 位址和目的地 IP 位址，以及連接埠號碼 

- 將要求送出的組織公用 IP 位址。

- 要求的時間

- 傳送及接收的流量

- IP 版本 (4 或 6)

- 僅限 TLS 連線：伺服器名稱指示延伸模組或伺服器憑證的目標主機名稱。

下列 HTTP 資訊：

- 方法 (GET、POST 等) 

- 通訊協定 (HTTP/1.1 等)

- 使用者代理程式字串

- 主機名稱

- 目標 URI (不含查詢字串)

- 內容類型資訊

- 查閱者 URL 資訊 (不含查詢字串)



> [AZURE.NOTE]所有非加密的連線都會收集上述 HTTP 資訊。如果是 TLS 連線，只有在入口網站中開啟 [深度檢查] 設定時，才會擷取這項資訊。設定預設為 [開啟]。如需詳細資訊，請參閱[開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 

 
### 代理程式的運作方式

代理程式安裝包含兩個元件：

- 使用者模式元件

- 核心模式驅動程式元件 (Windows 篩選平台驅動程式)



第一次安裝代理程式時，會在電腦上儲存特定電腦的受信任憑證，然後再使用這個憑證與 Cloud App Discovery 服務建立安全連線。<br>代理程式會透過這個安全連線從 Cloud App Discovery 服務定期擷取原則組態。<br>原則包括要監視哪些雲端應用程式，以及是否啟用自動更新等等相關資訊。

當電腦傳送和接收流量時，Cloud App Discovery 會分析該流量並擷取相關的中繼資料 (請參閱上表)。<br>代理程式每分鐘都會透過加密通道，將收集的中繼資料上傳到 Cloud App Discovery 服務。

如果是加密的連線，會執行一個額外的步驟以改進所收集資料的正確性。<br> 這稱為深度檢查。<br>驅動程式元件會攔截加密的流量，將本身插入加密的資料流。此元件會在電腦上建立自我簽署的根憑證，讓用戶端應用程式信任 Cloud App Discovery 代理程式。這個自我簽署的根憑證會標示為不可匯出，而且會加入 ACL 給系統管理員。目的是讓憑證永遠不要離開建立它的電腦。


### 尊重使用者隱私權

我們的目標是提供系統管理員工具，在詳細檢視應用程式使用方式和使用者隱私權之間，為組織設定一個適當的平衡。因此，我們在入口網站設定頁面提供下列旋鈕：

- **資料收集**：系統管理員可以選擇指定要對哪些應用程式或應用程式類別取得探索資料。


- **深度檢查**：系統管理員可以選擇指定代理程式是否要收集 SSL/TLS 連線的 HTTP 流量。



Cloud App Discovery 端點代理程式只會收集上表所描述的資訊。



> [AZURE.NOTE]如需詳細資訊，請參閱[開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 



## 傳送資料到 Cloud App Discovery

代理程式收集中繼資料之後，最多會在電腦上快取一分鐘，或快取的資料達到 5 MB 的大小為止。然後中繼資料會經過壓縮並透過安全連線傳送到 Cloud App Discovery 服務。

如果代理程式因為任何原因無法與 Cloud App Discovery 服務通訊，收集的中繼資料會儲存在只能由電腦上授權的使用者 (例如 Administrators 群組) 存取的本機檔案快取。<br>代理程式會自動嘗試重新傳送快取的中繼資料，直到 Cloud App Discovery 服務順利收到為止。



## 在服務端接收資料

代理程式會使用上述電腦特定的用戶端驗證憑證向 Cloud App Discovery 服務進行驗證，並且透過加密通道轉送資料。<br>Cloud App Discovery 服務的分析管線會以邏輯方式分割分析管線的所有階段，分別為每個客戶處理中繼資料。分析過的中繼資料會在入口網站中產生各種報告。

未處理的中繼資料和分析過的中繼資料最多儲存 180 天。此外，客戶可以選擇在自行選擇的 Azure blob 儲存體帳戶中擷取分析過的中繼資料。這對於要離線分析中繼資料，以及要保留較長時間的資料很有用。

## 使用 Azure 入口網站存取資料

為了保護所收集的中繼資料的安全，預設只有租用戶的全域系統管理員才能存取 Azure 入口網站的 Cloud App Discovery 功能。<br>不過，系統管理員可以選擇委派此存取權給其他使用者或群組。



> [AZURE.NOTE]如需詳細資訊，請參閱[開始使用 Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
 


要在入口網站中存取資料的所有使用者都必須擁有 Azure AD Premium 授權。



**其他資源**


* [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->