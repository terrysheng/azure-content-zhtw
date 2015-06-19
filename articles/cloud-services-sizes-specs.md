<properties 
 pageTitle="雲端服務的大小" 
 description="列出 Web 和背景工作角色不同的大小。" 
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
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# 雲端服務的大小

## 概觀 

本主題描述雲端服務角色執行個體 (Web 角色和背景工作角色)的可用大小和選項。同時也提供計劃使用這些資源時，需注意的部署考量。

Azure 虛擬機器和雲端服務是 Azure 所提供數種計算資源類型的其中兩種。如需說明，請參閱＜[計算 Azure 所提供的裝載選項](http://go.microsoft.com/fwlink/p/?LinkID=311926)＞。

>[AZURE.NOTE]若要查看相關的 Azure 限制，請參閱 ＜[Azure 訂用帳戶和服務限制、配額及條件約束](azure-subscription-service-limits.md)＞

###用於 Web 和背景工作角色執行個體的大小

下列考量可協助您決定大小：

*   執行個體現在可以設定為使用 D 系列 VM。這些是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。D 系列 VM 提供更快的處理器、較高的記憶體至核心比率和使用固態硬碟 (SSD) 的暫存磁碟。如需詳細資訊，請參閱 Azure 部落格的公告，[新 D 系列的虛擬機器大小](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)。  

*   因系統需求，Web 角色和背景工作角色比 Azure 虛擬機器需要更多的暫存磁碟空間。系統檔案保留 4 GB 的空間供 Windows 分頁檔和 2 GB 的空間供 Windows 傾印檔案。

*   作業系統磁碟包含 Windows 客體 OS，並且包含 Program Files 資料夾 (包括透過啟動工作完成的安裝，除非您指定另一個磁碟)、登錄變更、System32 資料夾和 .NET Framework。

*   本機資源磁碟包含 Azure 記錄檔和設定檔、Azure 診斷 (其中包括 IIS 記錄檔)，以及您所定義的任何本機儲存體資源。

*   應用程式磁碟是您解壓縮 .cspkg 的地方，並包含您的網站、二進位檔、角色主機處理序、啟動工作、web.config 等等。

*   A8/A10 和 A9/A11 虛擬機器大小有相同的容量。A8 和 A9 虛擬機器執行個體包含額外的網路介面卡，並連線到遠端的直接記憶體存取 (RDMA) 網路，以利虛擬機器之間進行快速通訊。A8 和 A9 執行個體專為在執行期間，需要常數和低度延遲節點的高效能計算應用程式所設計，例如，使用訊息傳遞介面 (MPI) 的應用程式。A10 和 A11 虛擬機器執行個體不包含額外的網路介面卡。A10 和 A11 執行個體專為不需要常數和低度延遲節點的高效能計算應用程式設計，也就是所謂的參數式或窘迫平行應用程式。

|大小|CPU<br>核心|記憶體|磁碟大小|
|---|---|---|---|
|特小型|1|768 MB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 19 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|小型|1|1.75 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 224 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|中型|2|3.5 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 489 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|大型|4|7 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 999 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|特大型|8|14 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 2,039 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|A5|2|14 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 489 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|A6|4|28 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 999 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|A7|8|56 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 2,039 GB</p><p>應用程式 = 大約 1.5 GB</p>
|A8|8|56 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 1.77 TB</p><p>應用程式 = 大約 1.5 GB</p><blockquote><p>[AZURE.NOTE]如需使用此大小的資訊及考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</p></blockquote>|
|A9|16|<p>112 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 1.77 TB</p><p>應用程式 = 大約 1.5 GB</p><blockquote><p>[AZURE.NOTE]如需使用此大小的資訊及考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</p></blockquote>|
|A10|8|<p>56 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 1.77 TB</p><p>應用程式 = 大約 1.5 GB</p><blockquote><p>[AZURE.NOTE]如需使用此大小的資訊及考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</p></blockquote>|
|A11|16|<p>112 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 1.77 TB</p><p>應用程式 = 大約 1.5 GB</p><blockquote><p>[AZURE.NOTE]如需使用此大小的資訊及考量，請參閱<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">關於 A8、A9、A10 和 A11 計算密集型執行個體</a>。</p></blockquote>|
|標準_D1|1|3.5 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 50 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D2|2|7 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 100 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D3|4|14 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 200 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D4|8|28 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 400 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D11|2|14 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 100 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D12|4|28 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 200 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D13|8|56 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 400 GB</p><p>應用程式 = 大約 1.5 GB</p>|
|標準_D14|16|112 GB|<p>作業系統 = 客體 OS 大小</p><p>本機資源 = 800 GB</p><p>應用程式 = 大約 1.5 GB</p>|

### 另請參閱

#### 概念

＜[設定 Azure 的雲端服務](https://msdn.microsoft.com/library/hh124108)＞

＜[設定雲端服務大小](https://msdn.microsoft.com/library/ee814754)＞

#### 其他資源

〈[Azure 訂用帳戶和服務限制、配額與限制](azure-subscription-service-limits.md)〉

 ＜[關於 A8、A9、A10 和 A11 計算密集型執行個體](http://go.microsoft.com/fwlink/p/?linkid=328042)＞

 [虛擬機器的大小](virtual-machines/virtual-machines-size-specs.md)

<!---HONumber=58-->