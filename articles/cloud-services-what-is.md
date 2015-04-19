<properties 
	pageTitle="什麼是雲端服務 - Azure 服務管理" 
	description="Azure 中的雲端服務簡介。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="adegeo"/>




# 什麼是雲端服務？
當您建立應用程式，並在 Azure 中執行它，其程式碼和組態併稱為 Azure 雲端服務 (在舊版 Azure 中稱為 *hosted service*)。

建立雲端服務可讓您在 Azure 中部署多層次 Web 應用程式，以定義多個角色來分散處理，還可靈活地調整應用程式的規模。雲端服務由一或多個 Web 角色和/或背景工作角色所組成，各有其自己的應用程式檔案和組態。Azure 網站和虛擬機器也可啟用 Azure 上的 Web 應用程式。雲端服務的主要優點是能夠支援更複雜的多層式架構。如需詳細的比較，請參閱 [Azure 網站、 雲端服務和虛擬機器的比較][Comparison]。

在雲端服務中，Azure 可以為您維護基礎結構，包括執行例行性維護、修補作業系統，以及嘗試從服務和硬體故障中回復。如果每一個角色至少定義兩個執行個體，則可執行大部分的維護及您自己的服務升級，完全不會中斷服務。在雲端服務中，每一個角色至少必須有兩個執行個體，才符合 Azure 服務等級協定，而此協定可保證從外部連接至面向網際網路的角色至少保持在 99.95% 的連線時間。 

每一個雲端服務有兩個可供您部署服務封裝和組態的環境。在將雲端服務升級到生產環境之前，您可以先在預備環境中測試雲端服務。將預備的雲端服務升級到生產環境只需要交換兩個環境的虛擬 IP 位址 (VIP)，就是如此簡單。 


## 概念 ##


- **雲端服務角色：**雲端服務角色包括應用程式檔案和組態。雲端服務可以有兩種角色：
 
>- **Web 角色：**Web 角色提供專用的 Internet Information Services (IIS) Web 伺服器，用來代管前端 Web 應用程式。

>- **背景工作角色：**背景工作角色內代管的應用程式可以執行非同步、長時間或永久的工作，且不受使用者互動或輸入所影響。

- **角色執行個體：**角色執行個體是用來執行應用程式程式碼和角色組態的虛擬機器。角色可以有多個執行個體，並定義於服務組態檔中。

- **客體作業系統：**雲端服務的客體作業系統是用來執行應用程式程式碼的角色執行個體 (虛擬機器) 上所安裝的作業系統。

- **雲端服務元件：**需要三個元件才能將應用程式部署為 Azure 中的雲端服務：

>- **服務定義檔：**雲端服務定義檔 (.csdef) 可定義服務模型，包括角色數目。

>- **服務組態檔：**雲端服務組態檔 (.cscfg) 提供雲端服務和個別角色的組態設定，包括角色執行個體數。

>- **服務封裝：**服務封裝 (.cspkg) 包含應用程式程式碼和服務定義檔。

- **雲端服務部署：**雲端服務部署是部署至 Azure 預備或生產環境的雲端服務執行個體。您可以同時在預備和生產環境中保有部署。

- **部署環境：**Azure 提供兩個雲端服務部署環境： *staging environment*，此環境可供您測試部署，然後再將它提升到 *production environment*。這兩種環境只能透過存取雲端服務時所使用的虛擬 IP 位址 (VIP) 來加以區別。在預備環境，雲端服務的全域唯一識別碼 (GUID) 會在 URL (*GUID*.cloudapp.net) 中識別 VIP。在生產環境中，URL 所依據的是指派給雲端服務且更容易使用的 DNS 前置詞 (例如， *myservice*.cloudapp.net)。

- **交換部署：**若要將 Azure 預備環境中的部署提升到生產環境，您可以透過切換用以存取兩個部署的 VIP 來「交換」部署。進行部署之後，雲端服務的 DNS 名稱會指向已位於預備環境中的部署。 

- **最小監視與詳細資訊監視：** *Minimal monitoring*是雲端服務的預設設定，其使用從角色執行個體 (虛擬機器) 的主機作業系統收集而來的效能計數器。 *Verbose monitoring*則會根據角色執行個體內的效能資料收集其他度量，以便進一步分析應用程式處理期間所發生的問題。如需詳細資訊，請參閱[如何監視雲端服務][HTMonitorCloudServices]。

- **Azure 診斷：** Azure 診斷是可讓您從 Azure 中執行的應用程式收集診斷資料的 API。必須對雲端服務角色啟用 Azure 診斷，才能開啟詳細資訊監視。如需詳細資訊，請參閱[在 Azure 中啟用診斷][CloudServicesDiagnostics]。

- **連結資源：**若要顯示您的雲端服務與其他資源 (例如 Azure SQL Database 執行個體) 的相依性，您可以將此資源「連結」到雲端服務。在預覽管理入口網站中，您可以在 [**連結的資源**] 頁面上檢視連結的資源、在儀表板上檢視其狀態，並在 [**調整**] 頁面上調整連結的 SQL Database 執行個體以及服務角色。以這種方式連結資源並不會將資源連接到應用程式；您必須在應用程式程式碼中設定這些連結。

- **調整雲端服務：**藉由增加對角色所部署的角色執行個體 (虛擬機器) 數目可擴大雲端服務。減少角色執行個體則可縮減雲端服務。在預覽管理入口網站中，您也可以在調整服務角色時，藉由變更 SQL Database 版本和資料庫大小上限，來調整連結的 SQL Database 執行個體。

- **Azure 服務等級協定 (SLA)：**Azure 運算 SLA 保證當您對每個角色部署兩個或以上的角色執行個體時，您可正常存取雲端服務的時間至少有 99.95% 。此外，當角色執行個體的程序未能執行時，有 99.9% 的時間會啟動偵測和修正動作。如需詳細資訊，請參閱[服務等級協定][SLA]。

[HTMonitorCloudServices]:http://azure.microsoft.com/ manage/services/cloud-services/how-to-monitor-a-cloud-service/
[SLA]: http://azure.microsoft.com/ support/legal/sla/
[CloudServicesDiagnostics]: http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/
[Comparison]: http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/

<!--HONumber=45--> 
