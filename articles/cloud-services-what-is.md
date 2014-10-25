<properties linkid="manage-services-what-is-a-cloud-service" urlDisplayName="What is a Cloud Service" pageTitle="What is a cloud service - Azure service management" metaKeywords="Azure cloud services intro, cloud services overview, cloud services basics" description="An introduction to the cloud service in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="What is a cloud service?" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# 什麼是雲端服務？

當您建立應用程式並在 Azure 中執行它時，其程式碼和組態併稱為 Azure 雲端服務 (在舊版 Azure 中，稱為「託管服務」)。

建立雲端服務可讓您在 Azure 中部署多層次 Web 應用程式，以定義多個角色來分散處理，還可靈活地調整應用程式的規模。雲端服務由一或多個 Web 角色和/或背景工作角色所組成，各有其自己的應用程式檔案和組態。Azure 網站和虛擬機器也可啟用 Azure 上的 Web 應用程式。雲端服務的主要優點是能夠支援更複雜的多層式架構。如需詳細的比較，請參閱 [Azure 網站、雲端服務與虛擬機器的比較][]。

在雲端服務中，Azure 可以為您維護基礎結構，包括執行例行性維護、修補作業系統，以及嘗試從服務和硬體故障中回復。如果每一個角色至少定義兩個執行個體，則可執行大部分的維護及您自己的服務升級，完全不會中斷服務。在雲端服務中，每一個角色至少必須有兩個執行個體，才符合 Azure 服務等級協定，而此協定可保證從外部連接至面向網際網路的角色至少保持在 99.95% 的連線時間。

每一個雲端服務有兩個可供您部署服務封裝和組態的環境。在將雲端服務升級到生產環境之前，您可以先在預備環境中測試雲端服務。將預備的雲端服務升級到生產環境只需要交換兩個環境的虛擬 IP 位址 (VIP)，就是如此簡單。

## 概念

-   **雲端服務角色：**雲端服務角色由應用程式檔案和組態所組成。一個雲端服務可以有兩種角色：

> -   **Web 角色：**Web 角色提供專用的 Internet Information Services (IIS) Web 伺服器，用來代管前端 Web 應用程式。

> -   **背景工作角色：**背景工作角色內代管的應用程式可以執行非同步、長時間或永久的工作，且不受使用者互動或輸入所影響。

-   **角色執行個體：**角色執行個體是執行應用程式碼和角色組態的虛擬機器。一個角色可以在服務組態檔中定義多個執行個體。

-   **客體作業系統：**雲端服務的客體作業系統是一個在執行應用程式碼的角色執行個體 (虛擬機器) 上安裝的作業系統。

-   **雲端服務元件：**需要三個元件才能將應用程式部署成為 Azure 中的雲端服務：

> -   **服務定義檔：**雲端服務定義檔 (.csdef) 定義服務模型，包括角色數目。

> -   **服務組態檔：**雲端服務組態檔 (.cscfg) 為雲端服務和個別角色提供組態設定，包括角色執行個體的數目。

> -   **服務封裝：**服務封裝 (.cspkg) 包含應用程式碼和服務定義檔。

-   **雲端服務部署：**雲端服務部署是部署到 Azure 預備或生產環境的雲端服務執行個體。您可以同時在預備和生產環境中維護部署。

-   **部署環境：** Azure 為雲端服務提供兩個部署環境：您可以先在*預備環境*中測試部署，然後再將部署升級到*生產環境*。這兩個環境的差別只在於用來存取雲端服務的虛擬 IP 位址 (VIP)。在預備環境中，雲端服務的全域唯一識別碼 (GUID) 可在 URL 中識別雲端服務 (*GUID*.cloudapp.net)。在生產環境中，URL 是以指派給雲端服務的簡易 DNS 前置詞為基礎 (例如，*myservice*.cloudapp.net)。

-   **交換部署：**若要將 Azure 預備環境中的部署升級到生產環境，您可以交換用於存取這兩個部署的 VIP 來「交換」部署。部署之後，雲端服務的 DNS 名稱會指向已在預備環境中的部署。

-   **最小與詳細資訊監視：** *最小監視*，這是雲端服務的預設值，使用從主機作業系統收集的角色執行個體 (虛擬機器) 的效能計數器。*詳細資訊監視*會按照角色執行個體內的效能資料來收集其他度量，以便進一步分析應用程式處理期間發生的問題。如需詳細資訊，請參閱＜[如何監視雲端服務][]＞。

-   **Azure 診斷：**「Azure 診斷」是可讓您從 Azure 中執行的應用程式來收集診斷資料的 API。必須對雲端服務角色啟用「Azure 診斷」，才能啟動詳細資訊監視。如需詳細資訊，請參閱[啟用 Azure 診斷][]。

-   **連結資源：**若要顯示雲端服務對其他資源的相依性，例如 Azure SQL Database 執行個體，您可以將資源「連結」至雲端服務。在預覽管理入口網站中，您可以在 [連結的資源] 頁面上檢視連結的資源、在儀表板上檢視資源的狀態，以及在 [調整] 頁面上隨著服務角色來調整連結的 SQL Database 執行個體。就此意義而言，連結資源並不會將資源連接到應用程式。您必須在應用程式碼中設定連接。

-   **調整雲端服務：** 增加角色已部署的角色執行個體 (虛擬機器) 數目可擴大雲端服務。減少角色執行個體可縮小雲端服務。在預覽管理入口網站中，您可以在調整服務角色時變更 SQL Database 版本和資料庫大小上限，以調整連結的 SQL Database 執行個體。

-   **Azure 服務等級協定 (SLA)：**當每一個角色部署兩個以上的執行個體時，Azure 運算 SLA 可保證存取雲端服務期間至少維持在 99.95% 的時間。另外，當角色執行個體的程序未執行時，將以 99.9% 的時間來起始偵測和更正動作。如需詳細資訊，請參閱[服務等級協定][]。

  [Azure 網站、雲端服務與虛擬機器的比較]: http://azure.microsoft.com/zh-tw/documentation/articles/choose-web-site-cloud-service-vm/
  [如何監視雲端服務]: http://azure.microsoft.com/zh-tw/manage/services/cloud-services/how-to-monitor-a-cloud-service/
  [啟用 Azure 診斷]: http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-dotnet-diagnostics/
  [服務等級協定]: http://azure.microsoft.com/zh-tw/support/legal/sla/
