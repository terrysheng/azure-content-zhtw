<properties
 pageTitle="雲端服務的大小"
 description="列出 Azure 雲端服務 Web 和背景工作角色不同的大小。"
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/14/2015"
 ms.author="adegeo"/>

# 雲端服務的大小

本主題描述雲端服務角色執行個體 (Web 角色和背景工作角色)的可用大小和選項。同時也提供計劃使用這些資源時，需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱＜[計算 Azure 所提供的裝載選項](fundamentals-application-models.md)＞。

> [AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 ＜[Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)＞

## 用於 Web 和背景工作角色執行個體的大小

下列考量可協助您決定大小：

* D 系列 VM 執行個體是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。  

*   Dv2 系列是原始 D 系列的延續，擁有更強大的 CPU。Dv2 系列 CPU 比 D 系列 CPU 快約 35%。它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。Dv2 系列的記憶體和磁碟組態和 D 系列一樣。

    Dv2 系列的區域可用性將以此排程為基礎：2015 年 10 月 15 日：美國東部 2、美國中部、美國中北部、美國西部 2015 年 11 月 15 日：美國東部、歐洲北部、歐洲西部 2016 年 1 月 16 日：美國中南部、亞太地區東部、亞太地區東南部、日本東部、日本西部、澳洲東部、澳洲東南部、巴西南部

* 因系統需求，Web 角色和背景工作角色比 Azure 虛擬機器需要更多的暫存磁碟空間。系統檔案保留 4 GB 的空間供 Windows 分頁檔和 2 GB 的空間供 Windows 傾印檔案。

* 作業系統磁碟包含 Windows 客體 OS，並且包含 Program Files 資料夾 (包括透過啟動工作完成的安裝，除非您指定另一個磁碟)、登錄變更、System32 資料夾和 .NET Framework。

* 本機資源磁碟包含 Azure 記錄檔和設定檔、Azure 診斷 (其中包括 IIS 記錄檔)，以及您所定義的任何本機儲存體資源。

* 應用程式磁碟是您解壓縮 .cspkg 的地方，並包含您的網站、二進位檔、角色主機處理序、啟動工作、web.config 等等。

* A8/A10 和 A9/A11 虛擬機器大小有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，其會連線到遠端直接記憶體存取 (RDMA) 網路，以利在虛擬機器之間進行快速通訊。A8 和 A9 執行個體是專為執行期間，節點之間需要常數和低延遲通訊的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

|大小|CPU<br>核心|記憶體|磁碟大小|
|---|---|---|---|
|特小型|1|768 MB|作業系統 = 客體 OS 大小<br/>本機資源 = 15384 MB<br/>應用程式 = 大約 1.5 GB|
|小型|1|1\.75 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 225304 MB<br/>應用程式 = 大約 1.5 GB|
|中型|2|3\.5 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 496664 MB<br/>應用程式 = 大約 1.5 GB|
|大型|4|7 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 1018904 MB<br/>應用程式 = 大約 1.5 GB|
|特大型|8|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 2083864 MB<br/>應用程式 = 大約 1.5 GB|
|A5|2|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 496664 MB<br/>應用程式 = 大約 1.5 GB|
|A6|4|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 1018904 MB<br/>應用程式 = 大約 1.5 GB|
|A7|8|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 2083864 MB<br/>應用程式 = 大約 1.5 GB
|A8|8|56 GB|OS = 客體 OS 大小<br/>本機資源 = 1856172 MB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量事項，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 運算密集型執行個體</a>。</blockquote>|
|A9|16|112 GB|OS = 客體 OS 大小<br/>本機資源 = 1856172 MB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量事項，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 運算密集型執行個體</a>。</blockquote>|
|A10|8|56 GB|OS = 客體 OS 大小<br/>本機資源 = 1856172 MB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量事項，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 運算密集型執行個體</a>。</blockquote>|
|A11|16|112 GB|OS = 客體 OS 大小<br/>本機資源 = 1856172 MB<br/>應用程式 = 大約 1.5 GB<blockquote> 注意：如需使用此大小的資訊與考量事項，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 運算密集型執行個體</a>。</blockquote>|
|標準\_D1|1|3\.5 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 46104 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D2|2|7 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 97304 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D3|4|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 199704 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D4|8|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 404504 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D11|2|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 97304 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D12|4|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 199704 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D13|8|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 404504 MB<br/>應用程式 = 大約 1.5 GB|
|標準\_D14|16|112 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 814104 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D1\_v2|1|3\.5 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 46104 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D2\_v2|2|7 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 97304 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D3\_v2|4|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 199704 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D4\_v2|8|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 404504 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D5\_v2|16|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 814104 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D11\_v2|2|14 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 97304 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D12\_v2|4|28 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 199704 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D13\_v2|8|56 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 404504 MB<br/>應用程式 = 大約 1.5 GB|
|Standard\_D14\_v2|16|112 GB|作業系統 = 客體 OS 大小<br/>本機資源 = 814104 MB<br/>應用程式 = 大約 1.5 GB|
## 設定雲端服務大小

您可以指定角色執行個體的虛擬機器大小，作為服務定義檔所描述之服務模型的一部分。角色大小決定 CPU 核心數目、記憶體容量，以及配置給執行中執行個體的本機檔案系統大小。根據您應用程式的資源需求選擇角色大小。

以下是將 Web 角色執行個體的角色大小設定為小型的範例：


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>

針對指定的本機資源大小，確定其小於或等於上表中的最大本機資源大小。
## 後續步驟

[設定 Azure 的雲端服務](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=Oct15_HO3-->