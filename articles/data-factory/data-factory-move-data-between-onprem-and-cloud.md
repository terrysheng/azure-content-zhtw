<properties 
	pageTitle="在內部部署和雲端之間使用 Azure Data Factory 移動資料" 
	description="了解如何在內部部署和雲端之間使用資料管理閘道和 Azure Data Factory 移動資料。" 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2016" 
	ms.author="spelluru"/>

# 利用資料管理閘道在內部部署來源和雲端之間移動
現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。資料處理站會利用資料管理閘道順暢地進行整合。資料處理站管理閘道器是您可以安裝內部部署以啟用混合式管線的代理程式。

本文提供整合內部部署資料存放區與雲端資料存放區的整合，以及使用資料處理站進行雲端處理的概觀。這篇文章是根據[資料移動活動](data-factory-data-movement-activities.md)一文和其他資料處理站核心概念文章。下列概觀假設您已熟悉管線、活動、資料集和複製活動等資料處理站概念。

資料閘道提供下列功能：

1.	在相同資料處理站內建立內部部署資料來源和雲端資料來源的模型及移動資料。
2.	具有用於監視和管理的單一窗格，可利用資料處理站雲端儀表板看見閘道器的狀態。
3.	安全地管理內部部署資料來源的存取權。
	1. 不需要變更公司防火牆。閘道器只會使輸出 HTTP 連線開啟網際網路。
	2. 利用您的憑證加密內部部署資料存放區的認證。
4.	有效率地移動資料 – 資料會以平行方式傳輸，且系統會採用自動重試邏輯，修復間歇性網路問題。

## 使用資料管理閘道的考量
1.	「資料管理閘道」的單一執行個體可用於多個內部部署資料來源，但是請注意，**單一閘道器執行個體只會繫結至一個 Azure 資料處理站**，不能與另一個資料處理站共用。
2.	單一電腦上**只能安裝一個資料管理閘道的執行個體**。假設您有兩個需要存取內部部署資料來源的 Data Factory，您需要將繫結至不同 Data Factory 的閘道器個別安裝在兩部內部部署電腦上。
3.	雖然閘道器不需要和資料來源位在相同的電腦上，但越接近資料來源可縮短閘道器連線到資料來源的時間。建議您安裝閘道器的電腦不同於裝載內部部署資料來源的電腦，如此閘道器才不會與資料來源爭奪資源。
4.	您可以有「多個閘道器在不同電腦上，但連接至相同的內部部署資料來源」。例如，您可能有兩個閘道器用於服務兩個 Data Factory，但相同的內部部署資料來源都向這兩個 Data Factory 註冊。
5.	若您已在電腦上安裝用於 **Power BI** 案例的閘道器，請於另一台電腦上安裝另一個用於 Azure Data Factory 的閘道器。
6.	您必須**使用閘道器，即使您使用 ExpressRoute 也一樣**。 
7.	您應該將您的資料來源視為內部部署資料來源 (亦即在防火牆後面)，即使您使用 **ExpressRoute** 和**使用閘道器**建立服務與資料來源之間的連接也一樣。 

## 閘道器安裝 - 必要條件
1.	支援的**作業系統**版本包括 Windows 7、Windows 8/8.1、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2。
2.	建議閘道器電腦的「組態」至少為 2 GHz、4 核心、8 GB RAM 和 80 GB 磁碟。
3.	如果主機電腦休眠，閘道器即無法回應資料要求。因此，安裝閘道器之前，請先在電腦上設定適當的「電源計劃」。如果電腦已設定為休眠，安裝閘道器時會提示訊息。

因為複製活動執行會以特定的頻率發生，在電腦上的資源使用量 (CPU、記憶體) 也會遵循與尖峰和閒置時間相同的模式。資源使用率也仰賴要移動的資料量。當多個複製工作正在進行中時，您將觀察到資源使用量會在尖峰時段增加。雖然上述情況是組態下限，根據資料移動的特定負載，您的組態最好還是要具備比上述組態下限還要多的資源。

## 安裝
資料管理閘道的安裝方式為從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載 MSI 安裝套件。MSI 也可用來將現有的資料管理閘道升級至最新的版本，並保留所有設定。您可以依照以下的逐步解說，從 Azure 入口網站尋找連至 MSI 套件的連結。


### 安裝最佳作法：
1.	為閘道器設定主機電腦上的電源計劃，使電腦不休眠。如果主機電腦休眠，閘道器即無法回應資料要求。
2.	您應該備份與閘道器相關聯的憑證。

## 連接埠和安全性考量

### 一般考量
有兩個防火牆必須要考量：在組織的中央路由器執行的**公司防火牆**，以及在安裝閘道器的本機電腦上設定為精靈的 **Windows 防火牆**。如果您使用第三方防火牆，而不是 Windows 防火牆，請以下列建議做為參考，並適當地設定連接埠。如果您的組織中使用 Proxy 伺服器，也請參閱 [Proxy 伺服器考量](#proxy-server-considerations)。以下是一般考量：

**在設定閘道之前：**

- 對於**公司防火牆和 Windows 防火牆**，您都必須確定 **TCP** 連接埠 **80** 和 **443** 的連出規則已啟用，並選擇性地啟用連接埠 **9350** 至 **9354** 的規則。Microsoft Azure 服務匯流排會使用這些項目建立 Azure Data Factory 與資料管理閘道之間的連線。開啟連接埠 9350 到 9354 並非強制性，但加以開啟可改善 Azure Data Factory 與資料管理閘道之間的通訊效能。

**在閘道設定期間：**

- 根據預設，資料管理閘道安裝會在閘道器電腦上開啟**本機 Windows 防火牆**上的連入連接埠 **8050**。當您在 Azure 入口網站中設定內部部署連結服務時，**設定認證**應用程式將使用此連接埠將認證轉送至閘道器 (本文稍後將有詳細說明)；此連接埠無法從網際網路存取，因此您不需要在公司防火牆中將它開啟。
- 如果您不想讓閘道安裝在 閘道機器的 Windows 防火牆上開啟連接埠 8050，您可以使用下列命令安裝閘道，而不要設定防火牆。

		msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

如果未在閘道器電腦上開啟連入連接埠 8050，要設定內部部署連結服務時，您必須使用透過**設定認證**應用程式來設定資料存放區認證以外的機制。例如，您可以使用 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell Cmdlet。如需如何設定資料存放區認證的資訊，請參閱[設定認證和安全性](#setting-credentials-and-security)一節。


**將資料從來源資料存放區複製到接收資料存放區：**

您必須確定公司防火牆、閘道機器上的 Windows 防火牆和資料存放區本身都已適當啟用防火牆規則。這可讓閘道成功連接到來源和接收器。您必須為複製作業中的每個相關資料存放區啟用規則。

例如，若要**從內部部署資料存放區複製到 Azure SQL Database 接收器或 Azure SQL 資料倉儲接收器**，您必須讓 Windows 防火牆與公司防火牆都允許連接埠 **1433** 上的連出 **TCP** 通訊，且必須設定 Azure SQL Server 的防火牆設定，將閘道器電腦的 IP 位址新增至允許的 IP 位址清單。

### Proxy 伺服器考量
根據預設，資料管理閘道會採用 Internet Explorer 中的 Proxy 設定，並使用預設認證來存取它。如果這不符合您的需求，您可以依照下列方式進一步設定 **Proxy 伺服器設定**，以確保閘道器能夠連接到 Azure Data Factory：

1.	安裝資料管理閘道後，請在 [檔案總管] 中安全地複製 “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”，以備份原始檔案。
2.	以系統管理員身分啟動 Notepad.exe，並開啟文字檔 “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”。您會看見 system.net 的預設標記，如下所示：

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	然後，您可以在該父標記內加入 Proxy 伺服器詳細資料，例如 Proxy 位址：

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	在 Proxy 標記內可以有其他屬性，用以指定必要的設定，例如 scriptLocation。請參閱 [proxy 元素 (網路設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 以了解語法。

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. 將組態檔儲存到原始位置中，然後重新啟動資料管理閘道服務，以取得變更。您可以從 [開始] > [Services.msc] 執行此動作，或是藉由 [資料管理閘道器組態管理員] > 按一下 [停止服務] 按鈕、再按一下 [啟動服務] 來執行。如果服務未啟動，可能因為在已編輯的應用程式組態檔中加入了不正確的 XML 標記語法。

除了以上幾點以外，您也必須確定 Microsoft Azure 包含在公司的白名單中。有效 Microsoft Azure IP 位址清單可以從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=41653)下載。

### 防火牆和 Proxy 伺服器相關問題的可能徵兆：
如果發生如下的錯誤，有可能是原因防火牆或 Proxy 伺服器的組態不正確，而使資料管理閘道無法連接到 Azure Data Factory 驗證其本身。請參閱上一節，以確保您的防火牆和 Proxy 伺服器皆正確設定。

1.	當您嘗試註冊閘道器時，您會收到下列錯誤：「無法註冊閘道器金鑰。再次嘗試註冊閘道器金鑰之前，請確認資料管理閘道已處於連線狀態，且已啟動資料管理閘道主機服務。」
2.	當您開啟組態管理員時，您會看到「已中斷連接」或「連接中」狀態。檢視 Windows 事件記錄檔時，在 [事件檢視器] > [應用程式和服務記錄檔] > [資料管理閘道] 中，您會看到錯誤訊息，例如「無法連接到遠端伺服器」或「資料管理閘道的元件沒有回應，將會自動重新啟動。元件名稱：閘道。」

## 閘道器疑難排解：
您可以在 Windows 事件記錄檔的閘道器記錄檔中找到詳細資訊。您可以使用 **應用程式及服務記錄檔** > **資料管理閘道**下的 Windows **事件檢視器** 找到它們，而疑難排解閘道器的相關問題會在事件檢視器中尋找錯誤層級事件。

如果在您**變更憑證**之後閘道器停止運作，請使用 Microsoft 資料管理閘道器組態管理員工具或服務控制台小程式重新啟動 (停止再啟動) **資料管理閘道器服務**。如果您仍然看到錯誤，您可能必須提供資料管理閘道器服務使用者的明確權限，以存取憑證管理員 (certmgr.msc) 中的憑證。該服務的預設使用者帳戶為：**NT Service\\DIAHostService**。

## 使用資料閘道器 – 逐步解說
在本逐步解說中，您可以使用將資料從內部部署 SQL Server 資料庫移至 Azure Blob 的管線，來建立一個 Data Factory。

### 步驟 1：建立 Azure Data Factory
在這個步驟中，您可以使用 Azure 入口網站來建立名為 **ADFTutorialOnPremDF** 的 Azure Data Factory 執行個體。您也可以使用 Azure Data Factory Cmdlet 來建立 Data Factory。

1.	登入 [Azure 入口網站](https://portal.azure.com)之後，請按一下左下角的 [新增]，並選取 [建立] 刀鋒視窗中的 [資料分析]，然後按一下 [資料分析] 刀鋒視窗中的 [Data Factory]。

	![新增->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)
  
6. 在 [**新增 Data Factory**] 刀鋒視窗中：
	1. 輸入 **ADFTutorialOnPremDF** 做為名稱。
	2. 按一下 [**資源群組名稱**] 並選取 **ADFTutorialResourceGroup**。您可以選取現有的資源群組，或建立一個新的群組。若要建立新的資源群組：
		1. 按一下 [建立新的資源群組]。
		2. 在 [**建立資源群組**] 刀鋒視窗中，輸入資源群組的 [**名稱**]，然後按一下 [**確定**]。

7. 請注意，[**新增 Data Factory**] 刀鋒視窗上已核取 [**新增至開始面板**]。

	![新增至儀表板](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. 在 [**新增 Data Factory**] 刀鋒視窗中，按一下 [**建立**]。

	Azure Data Factory 的名稱在全域必須是唯一的。如果您收到錯誤：**Data Factory 名稱 “ADFTutorialOnPremDF” 無法使用**，請變更 Data Factory 名稱 (例如 yournameADFTutorialOnPremDF)，然後嘗試重新建立。執行此教學課程中的其餘步驟時，請使用此名稱來取代 ADFTutorialOnPremDF。

9. 按一下標題列上的 [通知] 按鈕，查看來自建立程序的通知，如下圖所示。再按一下按鈕可關閉通知視窗。

	![通知中樞](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. 建立完成之後，您會看到 **Data Factory** 刀鋒視窗，如下所示：

	![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

### 步驟 2：建立資料管理閘道
5. 在 **DATA FACTORY** 刀鋒視窗中，按一下 [**製作和部署**] 磚來啟動 Data Factory 的 [**編輯器**]。

	![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.	在 [Data Factory 編輯器] 中，按一下工具列的 [... (省略符號)]，選取 [新增資料閘道]。 

	![工具列上的 [新增資料閘道]](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. 在 [**建立**] 刀鋒視窗上，輸入 **adftutorialgateway** 做為 [**名稱**]，然後按一下 [**確定**]。 	

	![[建立閘道器] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. 在 [**設定**] 分頁中，按一下 [**直接安裝在此電腦上**]。這會下載閘道的安裝套件、在電腦上安裝、設定和註冊閘道。

	> [AZURE.NOTE]請使用 Internet Explorer 或 Microsoft ClickOnce 相容的 Web 瀏覽器。
	> 
	> 如果您使用 Chrome，請移至 [Chrome 線上應用程式商店](https://chrome.google.com/webstore/)，利用關鍵字 "ClickOnce" 搜尋，選擇其中一個 ClickOnce 延伸模組並安裝。
	>  
	> 您需要針對 Firefox 進行相同動作 (安裝附加元件)。例如，您可以從[這裡](https://addons.mozilla.org/firefox/addon/fxclickonce/)安裝。

	![閘道器 - [設定] 刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

	這是最簡單的方式 (一鍵)，透過單一步驟即可下載、安裝、設定和註冊閘道。您可以看到「Microsoft 資料管理閘道組態管理員」應用程式已安裝在電腦上。您也可以在此資料夾中找到執行檔 **ConfigManager.exe**：**C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared**。

	您也可以使用此刀鋒視窗中的連結手動下載與安裝閘道器，並使用 [**註冊金鑰**] 文字方塊中顯示的金鑰來加以註冊。
	
	如需有關閘道器的詳細資訊 (包括最佳作法和重要考量)，請參閱本文開頭的章節。

	>[AZURE.NOTE]您必須是本機電腦上的系統管理員，才能成功安裝和設定「資料管理閘道」。您可以將其他使用者加入至資料管理閘道使用者本機 Windows 群組。此群組的成員可以使用「資料管理閘道組態管理員」工具來設定閘道器。

5. 請等候幾分鐘，然後在電腦上啟動 [資料管理閘道器組態管理員] 應用程式。在 [搜尋] 視窗中，輸入**資料管理閘道器**以存取這個公用程式。您也可以在資料夾 **C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared** 中找到可執行檔 **ConfigManager.exe**

	![閘道器組態管理員](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. 請稍候，待這些值設定完成，如下所示：
	1. [狀態] 設定為 [已啟動]。
	2. [閘道器名稱] 設定為 **adftutorialgateway**。
	3. [執行個體名稱] 設定為 **adftutorialgateway**。
	4. [註冊] 設定為 [已註冊]。
	5. 底部的狀態列會顯示 [已連接到資料管理閘道雲端服務] 和一個綠色的核取記號。

8. 切換至 [憑證] 索引標籤。此索引標籤上指定的憑證可用來加密/解密您在入口網站指定之內部部署資料存放區的認證。按一下 [變更] 改為使用您自己的憑證。根據預設，閘道器會使用由 Data Factory 服務自動產生的憑證。

	![閘道器憑證組態](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)
9. (選擇性) 切換至 [診斷] 索引標籤，如果您想啟用詳細資訊記錄功能，以便用來排解閘道器的任何疑難問題，請勾選 [啟用詳細資訊記錄以進行疑難排解] 選項。在 [應用程式及服務記錄檔] -> [資料管理閘道器] 節點之下的 [事件檢視器] 中可找到記錄資訊。 

	![[診斷] 索引標籤](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

	您也可以使用此頁面來對使用閘道器的內部部署資料來源**測試連線**。
10. 在 Azure 入口網站中，依序在 [設定] 刀鋒視窗和 [新增資料閘道] 刀鋒視窗中，按一下 [確定]。
6. 左側的樹狀檢視中，[**資料閘道**] 下方應該會顯示 **adftutorialgateway**。如果按一下，應該會看到相關聯的 JSON。 
	

### 步驟 3：建立連結服務 
在此步驟中，您將建立兩個連結服務：**StorageLinkedService** 和 **SqlServerLinkedService****SqlServerLinkedService** 連結內部部署 SQL Server 資料庫，而 **StorageLinkedService** 連結服務則將 Azure Blob 存放區連結至 Data Factory。稍後在本逐步解說中，您將建立可將內部部署 SQL Server 資料庫的資料複製到 Azure Blob 存放區的管線。

#### 在內部部署 SQL Server 資料庫中新增連結服務
1.	在 [Data Factory 編輯器] 中，按一下工具列上的 [新增資料存放區]，然後選取 [SQL Server]。 

	![新增 SQL Server 連結服務](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.	在 [JSON 編輯器] 中，執行下列動作： 
	1. 為 **gatewayName** 指定 **adftutorialgateway**。	
	2. 如果您使用 Windows 驗證：
		1. 在 **connectionString** 中： 
			1. 將 **Integrated Security** 設定為 **true**。
			2. 指定資料庫的**伺服器名稱**和**資料庫名稱**。 
			2. 移除 **User ID** 和 **Password**。 
		3. 為 **userName** 和 **password** 屬性指定使用者名稱和密碼。
		
				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
            		"gatewayName": "adftutorialgateway",
            		"userName": "<Specify user name if you are using Windows Authentication>",
            		"password": "<Specify password for the user account>"
        		}

	4. 如果您使用 SQL 驗證：
		1. 在 **connectionString** 中指定資料庫的**伺服器名稱**、**資料庫名稱**、**User ID** 和 **Password**。       
		2. 從 JSON 中移除最後兩個 JSON 屬性：**userName** 和 **password**。
		3. 移除位於行尾的尾端 **, (逗號)**，其指定 **gatewayName** 屬性值。 

				"typeProperties": {
            		"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
	           		"gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
    		    }
	   
2.	按一下命令列上的 [部署]，部署 SQL Server 連結服務。

#### 新增 Azure 儲存體帳戶的連結服務
 
1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [新增資料存放區]，然後按一下 [Azure 儲存體]。
2. 在 [帳戶名稱] 中輸入您的 Azure 儲存體帳戶名稱。
3. 在 [帳戶金鑰] 中輸入您的 Azure 儲存體帳戶金鑰。
4. 按一下 [部署]，部署 **StorageLinkedService**。
   
 
### 步驟 4：建立輸入和輸出資料集
在此步驟中，您會建立代表複製作業的輸入和輸出資料的輸入和輸出資料集 (內部部署 SQL Server 資料庫 => Azure Blob 儲存體)。在建立資料集或資料表 (矩形資料集) 之前，您必須執行下列動作 (清單後列有詳細步驟)：

- 在您新增為 Data Factory 連結服務的 SQL Server 資料庫中，建立名為 **emp** 的資料表，並在資料表中插入幾個範例項目。
- 在您加入 Data Factory 作為連結服務的 Azure Blob 儲存體帳戶中，建立名為 **adftutorial** 的 Blob 容器。

### 準備用於教學課程的內部部署 SQL Server

1. 在您指定用於內部部署 SQL Server 連結服務 (**SqlServerLinkedService**) 的資料庫中，使用下列 SQL 指令碼在資料庫中建立 **emp** 資料表。


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL, 
			FirstName varchar(50),
			LastName varchar(50),
    		CONSTRAINT PK_emp PRIMARY KEY (ID)
		)
		GO
 

2. 在資料表中插入一些範例：


        INSERT INTO emp VALUES ('John', 'Doe')
		INSERT INTO emp VALUES ('Jane', 'Doe')



### 建立輸入資料表

1. 在 [Data Factory 編輯器] 中，按一下命令列上的 [新增資料集]，然後按一下 [SQL Server 資料表]。 
2.	使用下列文字取代右窗格中的 JSON：    

		{
		  "name": "EmpOnPremSQLTable",
		  "properties": {
		    "type": "SqlServerTable",
		    "linkedServiceName": "SqlServerLinkedService",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}

	請注意：
	
	- **type** 設定為 **SqlServerTable**。
	- **tableName** 設定為 **emp**。
	- **linkedServiceName** 設定為 **OnPremSqlLinkedService** (您已在步驟 2 中建立此連結服務)。
	- 針對並非由 Azure Data Factory 中另一個管線所產生的輸入資料表，您必須將 **external** 設定為 **true**。它代表輸入資料產生於 Azure Data Factory 服務外部。您可以使用 **Policy** 區段中的 **externalData** 元素，選擇性地指定任何外部資料原則。    

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。


### 建立輸出資料表

1.	在 [Data Factory 編輯器] 中，按一下命令列的 [新增資料集]，然後按一下 [Azure Blob 儲存體]。
2.	使用下列文字取代右窗格中的 JSON： 

		{
		  "name": "OutputBlobTable",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/outfromonpremdf",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}
  
	請注意：
	
	- **type** 設定為 **AzureBlob**。
	- **linkedServiceName** 設定為 **StorageLinkedService** (您已在步驟 2 中建立此連結服務)。
	- **folderPath** 設定為 **adftutorial/outfromonpremdf**，其中 outfromonpremdf 是 adftutorial 容器中的資料夾。您只需要建立 **adftutorial** 容器。
	- **availability** 設定為**每小時**，且 (**frequency** 設定為**小時**，**interval** 設定為 **1**)。Data Factory 服務會每小時在 Azure SQL Database 的 **emp** 資料表中產生輸出資料配量。 

	如果您沒有指定**輸入資料表**的 **fileName**，則輸入資料夾 (**folderPath**) 中的所有檔案/Blob 都會視為輸入。如果您在 JSON 中指定 fileName，則只有指定的檔案/Blob 會被視為輸入。如需範例，請參閱 [教學課程][adf-tutorial] 中的範例檔案。
 
	如果您未指定**輸出資料表**的 **fileName**，**folderPath** 中產生的檔案會依照下列格式命名：<Guid>.txt (例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.)。

	若要根據 **SliceStart** 時間動態地設定 **folderPath** 和 **fileName**，請使用 partitionedBy 屬性。在下列範例中，folderPath 使用 SliceStart (處理配量的開始時間) 中的年、月和日，fileName 使用 SliceStart 中的小時。例如，如果配量產生於 2014-10-20T08:00:00，folderName 設定為 wikidatagateway/wikisampledataout/2014/10/20，而 fileName 設定為 08.csv。

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考][json-script-reference]。

2.	按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署資料表] 訊息。
  

### 步驟 5：建立和執行管線
在此步驟中，您會建立**管線**，其中包含一個使用 **EmpOnPremSQLTable** 做為輸入和 **OutputBlobTable** 做為輸出的**複製活動**。

1.	在 **DATA FACTORY** 刀鋒視窗中，按一下 [**製作和部署**] 磚來啟動 Data Factory 的 [**編輯器**]。

	![[製作和部署] 磚](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.	按一下命令列的 [新增管線]。如果沒看到此按鈕，請按一下 [...] (省略符號) 展開命令列。
2.	使用下列文字取代右窗格中的 JSON：   


		{
		  "name": "ADFTutorialPipelineOnPrem",
		  "properties": {
		    "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
		    "activities": [
		      {
		        "name": "CopyFromSQLtoBlob",
		        "description": "Copy data from on-prem SQL server to blob",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpOnPremSQLTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "OutputBlobTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from emp"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	請注意：
 
	- 在 activities 區段中，只會有 **type** 設定為 **Copy** 的活動。
	- 活動的**輸入**設定為 **EmpOnPremSQLTable**，活動的**輸出**則設定為 **OutputBlobTable**。
	- 在 **transformation** 區段中，將 **SqlSource** 指定為**來源類型**，並將 **BlobSink** 指定為**接收類型**。
- **SqlSource** 的 **sqlReaderQuery** 屬性指定 SQL 查詢 **select * from emp**。

	將 **start** 屬性的值取代為目前日期，並將 **end** 值取代為隔天的日期。開始和結束日期時間都必須是 [ISO 格式](http://en.wikipedia.org/wiki/ISO_8601)。例如：2014-10-14T16:32:41Z。**end** 時間為選擇性項目，但在本教學課程中會用到。
	
	如果您未指定 **end** 屬性的值，則會以「**start + 48 小時**」計算。若要無限期地執行管線，請指定 **9/9/9999** 做為 **end** 屬性的值。
	
	您定義將根據為每個 Azure Data Factory 資料表定義的 **Availability** 屬性來處理資料配量的持續時間。
	
	在上述範例中，由於每小時即產生一個資料配量，共會有 24 個資料配量。
	
2. 按一下命令列的 [部署]，以部署資料集 (資料表是矩形的資料集)。確認您在標題列看到 [已成功部署管線] 訊息。
5. 現在，按一下 **X** 關閉 [**編輯器**] 刀鋒視窗。再按一次 **X**，關閉內含工具列和樹狀檢視的 [ADFTutorialDataFactory] 刀鋒視窗。如果您看到 [將捨棄未儲存的編輯] 訊息，請按一下 [確定]。
6. 您應該會回到 **ADFTutorialOnPremDF** 的 **DATA FACTORY** 刀鋒視窗。

**恭喜！** 您已成功建立 Azure Data Factory、連結服務、資料表和管線，以及排定的管線。

#### 在圖表檢視中檢視 Data Factory 
1. 在 **Azure 入口網站**中，按一下 **ADFTutorialOnPremDF** Data Factory 首頁上的 [圖表] 磚：

	![圖表連結](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. 您應該會看到如下所示的圖表：

	![圖表檢視](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

	您可以將管線和資料表放大、縮小、放大到 100%、縮放至適當比例和自動定位，以及顯示歷程資訊 (反白顯示所選取項目的上游和下游項目)。您可以按兩下物件 (輸入/輸出資料表或管線) 查看其屬性。

### 步驟 6：監視資料集和管線
在此步驟中，您將使用 Azure 入口網站來監視 Azure Data Factory 的運作情形。您也可以使用 PowerShell Cmdlet 來監視資料集和管線。如需監視的詳細資料，請參閱[監視和管理管線](data-factory-monitor-manage-pipelines.md)。

1. 巡覽至 **Azure 入口網站** (如果您已關閉它)
2. 如果 [**ADFTutorialOnPremDF**] 刀鋒視窗尚未開啟，請按一下**開始面板**上的 [**ADFTutorialOnPremDF**] 以開啟此刀鋒視窗。
3. 您應該會看到您在此刀鋒視窗上建立之資料表和管線的**計數**和**名稱**。

	![Data Factory 首頁](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. 現在，按一下 [資料集] 磚。
5. 在 [**資料集**] 刀鋒視窗中，按一下 [**EmpOnPremSQLTable**]。

	![EmpOnPremSQLTable 配量](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. 請注意，到目前為止已經產生資料配量，且它們的狀態為 [**就緒**]。這是因為您已將資料插入 SQL Server 資料庫中，而資料一直留存於其中。確認下方的 [問題配量] 區段中沒有顯示任何配量。


	[最近更新的配量] 和 [最近失敗的配量] 清單都依照 [上次更新時間] 排序。在下列情況中，配量的更新時間會變更。
    

	-  您以手動方式更新配量的狀態，例如，使用 **Set-AzureRmDataFactorySliceStatus** (或) 在配量的 [配量] 刀鋒視窗上按一下 [執行]。
	-  配量因為執行而變更狀態 (例如，開始執行、執行結束但失敗、執行結束且成功等)。
 
	按一下清單的標題或 **...** (省略符號)，查看更長的配量清單。按一下工具列上的 [篩選] 以篩選配量。
	
	若要改成依配量開始/結束時間來檢視資料配量，請按一下 [資料配量 (依配量時間)] 磚。

7. 現在，在 [**資料集**] 刀鋒視窗中，按一下 [**OutputBlobTable**]。

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. 確認到目前為止的配量已產生，且狀態為 [就緒]。等待到目前為止的配量的狀態設為 [就緒]。
9. 按一下清單中的任何資料配量，您應該會看到 [**資料配量**] 刀鋒視窗。

	![資料配量刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

	如果配量不是 [就緒] 狀態，您可以在 [未就緒的上游配量] 清單中看到未就緒且阻礙目前配量執行的上游配量。

10. 按一下底部清單中的 [活動執行]，可查看 [活動執行詳細資料]。

	![活動執行詳細資料刀鋒視窗][image-data-factory-activity-run-details]

11. 按一下 **X** 關閉所有刀鋒視窗，直到您
12. 回到 **ADFTutorialOnPremDF** 的起始刀鋒視窗為止。
14. (選擇性) 依序按一下 [管線] 及 [ADFTutorialOnPremDF]，然後深入檢視輸入資料表 (**已使用**) 或輸出資料表 (**已產生**)。
15. 使用「Azure 儲存體總管」之類的工具來驗證輸出。

	![Azure 儲存體總管](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## 在電腦之間移動閘道器
本節提供將閘道器用戶端從一台電腦移至另一台電腦的步驟。

2. 在入口網站中，瀏覽至 **Data Factory 首頁**，然後按一下 [連結服務] 磚。 

	![資料閘道連結](./media/data-factory-move-data-between-onprem-and-cloud/DataGatewaysLink.png) 
3. 在 [連結服務] 刀鋒視窗的 [資料閘道器] 區段中選取您的閘道器。
	
	![[連結服務] 刀鋒視窗與所選取的閘道器](./media/data-factory-move-data-between-onprem-and-cloud/LinkedServiceBladeWithGateway.png)
4. 在 [資料閘道器] 刀鋒視窗中，按一下 [下載並安裝資料閘道器]。
	
	![下載閘道器連結](./media/data-factory-move-data-between-onprem-and-cloud/DownloadGatewayLink.png) 
5. 在 [設定] 刀鋒視窗中，按一下 [下載並安裝資料閘道器]，並遵循指示將資料閘道器安裝在電腦上。 

	![設定刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/ConfigureBlade.png)
6. 讓 [Microsoft 資料管理閘道器組態管理員]保持開啟。 
 
	![組態管理員](./media/data-factory-move-data-between-onprem-and-cloud/ConfigurationManager.png)	
7. 在入口網站的 [設定] 刀鋒視窗中，按一下命令列上的 [重新建立金鑰]，然後按一下警告訊息中的 [是]。按一下金鑰文字旁的**複製按鈕**，將金鑰複製到剪貼簿。請注意，一旦重新建立索引鍵，舊電腦上的閘道器便會停止運作。  
	
	![重新建立索引鍵](./media/data-factory-move-data-between-onprem-and-cloud/RecreateKey.png)
	 
8. 將**金鑰**貼到電腦上 [資料管理閘道器組態管理員] 之 [註冊閘道器] 頁面的文字方塊中。(選擇性) 按一下 [顯示閘道器金鑰] 核取方塊以查看金鑰文字。
 
	![複製金鑰與註冊](./media/data-factory-move-data-between-onprem-and-cloud/CopyKeyAndRegister.png)
9. 按一下 [註冊] 透過雲端服務註冊閘道器。
10. 在 [指定憑證] 頁面上，按一下 [瀏覽]，以選取舊閘道器所使用的憑證，輸入**密碼**，然後按一下 [完成]。 
 
	![指定憑證](./media/data-factory-move-data-between-onprem-and-cloud/SpecifyCertificate.png)

	您可以執行下列步驟從舊閘道器中匯出憑證：啟動舊電腦上的 [資料管理閘道器組態管理員]，切換到 [憑證] 索引標籤，按一下 [匯出] 按鈕，然後遵循指示進行。 
10. 成功註冊閘道器後，閘道器組態管理員首頁上的 [註冊] 應會設定為 [已註冊]，[狀態] 會設定為 [已啟動]。 

## 設定認證和安全性

您也可以使用 [連結服務] 刀鋒視窗而不是使用 [Data Factory 編輯器] 建立 SQL Server 連結服務。
 
3.	在 Data Factory 首頁上，按一下 [連結服務] 磚。 
4.	在 [連結服務] 刀鋒視窗中，按一下命令列上的 [新增資料存放區]。 
4.	輸入 **SqlServerLinkedService** 作為**名稱**。 
2.	按一下 [類型] 旁的箭號，然後選取 [SQL Server]。

	![建立新的資料存放區](./media/data-factory-move-data-between-onprem-and-cloud/new-data-store.png)
3.	您可以在 [類型] 設定下進行更多設定。
4.	針對 [資料閘道器] 設定，選取您剛才建立的閘道器。 

	![SQL Server 設定](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-settings.png)
4.	在 [伺服器] 設定中輸入資料庫伺服器的名稱。
5.	在 [資料庫] 設定中輸入資料庫的名稱。
6.	按一下 [認證] 旁的箭號。

	![認證刀鋒視窗](./media/data-factory-move-data-between-onprem-and-cloud/credentials-dialog.png)
7.	在 [認證] 刀鋒視窗中，按一下 [按一下這裡設定認證]。
8.	在 [設定認證] 對話方塊中，執行下列動作：

	![設定認證對話方塊](./media/data-factory-move-data-between-onprem-and-cloud/setting-credentials-dialog.png) 
	1.選取您要 Data Factory 服務用來連接到資料庫的**驗證**。
	2.在 [使用者名稱] 設定中輸入可存取資料庫的使用者名稱。
	3.在 [密碼] 設定中輸入使用者的密碼。
	4.按一下 [確定] 關閉對話方塊。 
4. 按一下 [確定] 關閉 [認證] 刀鋒視窗。 
5. 按一下 [新增資料存放區] 刀鋒視窗中的 [確定]。 	
6. 確認 [連接服務] 刀鋒視窗中的 **SqlServerLinkedService** 狀態已設定為 [線上]。
![SQL Server 連結服務狀態](./media/data-factory-move-data-between-onprem-and-cloud/sql-server-linked-service-status.png)

如果您從閘道器電腦以外的另一台電腦存取入口網站，您必須確定「認證管理員」應用程式可以連接到閘道器電腦。如果應用程式無法連接閘道器電腦，它將不允許您設定資料來源的認證，以及測試資料來源的連接。

當您使用從 Azure 入口網站啟動的「設定認證」應用程式，設定內部部署資料來源的認證時，入口網站會利用閘道器電腦上，您在「資料管理閘道」組態管理員的 [認證] 索引標籤中指定的憑證，來加密認證。

如果您要尋找以 API 為基礎的方法來加密認證，可以使用 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell Cmdlet 來加密認證。此 cmdlet 會使用閘道器設定用來加密認證的憑證。您可以加密這個 Cmdlet 傳回的認證，並將其加入 JSON 檔案 (您將搭配 [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) Cmdlet 使用的檔案，或在入口網站之 Data Factory 編輯器的 JSON 程式碼片段中使用的檔案) 之 connectionString 的 EncryptedCredential 元素中。

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

**請注意：**如果您使用「設定認證」應用程式，它會如上方所示，在連結服務中自動設定加密的認證。

設定認證只能使用 Data Factory 編輯器，沒有其他方法。如果您使用編輯器建立 SQL Server 連結服務，並且以純文字輸入認證，認證就會使用 Data Factory 服務所擁有的憑證加密，而不使用閘道器設定要使用的憑證。雖然這種方法在某些情況下可能快一點，但也比較不安全。因此，建議您只在開發/測試用途才採用此方法。


## 使用 Azure PowerShell 建立和註冊閘道器 
本節說明如何使用 Azure PowerShell Cmdlet 建立和註冊閘道。

1. 在系統管理員模式下啟動 **Azure PowerShell**。 
2. **AzureResourceManager** 模式中可以使用 Azure Data Factory Cmdlet。執行下列命令以切換至 **AzureResourceManager** 模式。     

        switch-azuremode AzureResourceManager


2. 使用 **New-AzureRmDataFactoryGateway** Cmdlet 來建立邏輯閘道器，如下所示：

		New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**範例命令和輸出**：


		PS C:\> New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. 使用 **New-AzureRmDataFactoryGatewayKey** Cmdlet 來為新建立的閘道器產生註冊金鑰，並將金鑰儲存在本機變數 **$Key** 中：

		New-AzureRmDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**範例命令的輸出：**


		PS C:\> $Key = New-AzureRmDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. 在 Azure PowerShell 中，切換到資料夾：**C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\PowerShellScript**，然後執行與區域變數 **$Key** 相關聯的 **RegisterGateway.ps1** 指令碼 (如下列命令所示)，將您電腦上安裝的用戶端代理程式，註冊到您稍早建立的邏輯閘道器。

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. 您可以使用 **Get-AzureRmDataFactoryGateway** Cmdlet 取得 Data Factory 中的閘道器器清單。當 [狀態] 顯示為 [線上] 時，表示您的閘道器已就緒可供使用。

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

您可以使用 **Remove-AzureRmDataFactoryGateway** Cmdlet 移除閘道器，並使用 **Set-AzureRmDataFactoryGateway** Cmdlet 更新閘道器的說明。如需這些 Cmdlet 的語法及其他詳細資訊，請參閱 Data Factory Cmdlet 參考文件。


## 使用資料管理閘道進行複製的資料流
當您使用資料管線中的複製活動，將內部部署資料擷取至雲端以進行進一步的處理，或者匯出雲端中的結果資料回到內部部署資料存放區，複製活動都會在內部使用閘道器將資料從內部部署資料來源傳輸回雲端，反之亦然。

利用資料閘道器進行複製步驟的高層級資料流和摘要如下：
![使用閘道器的資料流](./media/data-factory-move-data-between-onprem-and-cloud/data-flow-using-gateway.png)

1.	資料開發人員會使用 [Azure 入口網站](http://portal.azure.com)或 [PowerShell Cmdlet](https://msdn.microsoft.com/library/dn820234.aspx)，為 Azure Data Factory 建立新的閘道器。 
2.	資料開發人員會使用「連結服務」面板，透過閘道器定義內部部署資料存放區的新連結服務。在設定連結服務資料的過程中，開發人員會使用設定認證應用程式指定驗證類型和認證，如逐步解說所示。設定認證應用程式對話方塊將會與資料存放區進行通訊以測試要儲存認證的連線與閘道器。
3.	在雲端中儲存認證之前，閘道器會利用與閘道器 (由資料開發人員提供) 相關聯的憑證加密認證。
4.	資料處理站移動服務會和閘道器進行通訊，以透過使用共用 Azure 服務匯流排佇列的控制通道，進行工作的排程和管理。必須開始複製活動工作時，資料處理站會將要求和認證資訊一起排入佇列。輪詢佇列之後，閘道器隨即啟動。
5.	閘道器會利用相同的憑證解密認證，然後利用適當的驗證類行連接到內部部署資料存放區。
6.	閘道器會根據複製活動在資料管線中的設定方式，將資料從內部部署存放區複製到雲端儲存體，或從雲端儲存體複製到內部部署資料存放區。請注意：在這個步驟中，閘道器會透過安全 (HTTPS) 通道直接與以雲端為基礎的儲存體服務 (例如 Azure Blob、Azure SQL 等) 通訊。

<!---HONumber=AcomDC_0114_2016-->