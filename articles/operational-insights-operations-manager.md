<properties 
   pageTitle="Operational Insights 的 Operations Manager 考量"
   description="如果您搭配使用 Microsoft Azure Operational Insights 與 Operations Manager，則您的組態將依賴 Operations Manager 代理程式和管理群組的散佈，以收集資料並傳送給 Operational Insights 服務以進行分析"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Operational Insights 的 Operations Manager 考量

如果您搭配使用 Microsoft Azure Operational Insights 與 Operations Manager，則您的組態將依賴 Operations Manager 代理程式和管理群組的散佈，以收集資料並傳送給 Operational Insights 服務以進行分析。不過，如果您使用直接連接到 Web 服務的代理程式，則您不需要 Operations Manager。搭配使用 Operational Insights 與 Operations manager 時請考慮下列問題。

## Operational Insights 軟體功能和需求

Operational Insights 包含雲端中的 Web 服務，及直接連接至 Web 服務的代理程式或 Operations Manager 代理程式，及管理您環境中電腦的 Operations Manager 管理群組。

選取 Operations Manager 代理程式 (用於收集資料) 和管理群組 (用於管理代理程式，並將資料傳送至 Operational Insights 服務) 前，請確定瞭解下列事項：

- Operations Manager 代理程式是安裝在您想要從中收集和分析資料的任何伺服器上。

- Operations Manager 管理群組會將資料從代理程式傳送到 Operational Insights Web 服務。它不會分析任何資料。

- Operations Manager 管理群組必須具有網際網路存取權，才能將資料上傳至 Web 服務。

- 為求最佳效果，請不要在也是網域控制站的電腦上安裝 Operations Manager 管理伺服器。

- Operations Manager 代理程式必須有網路連線到 Operations Manager 管理群組，如此才能傳送資料。

Operational Insights 可以使用 System Center 健全狀況服務來收集並分析資料。Operations Manager 取決於 System Center 健全狀況服務。當您檢視伺服器安裝的程式時，您會看到 System Center Operations Manager 代理程式軟體，特別是在 [新增/移除程式] 中。請勿移除這些軟體，因為 Operational Insights 依存它們。如果移除 Operations Manager 代理程式軟體，將無法再運作 Operational Insights。

## 與 Operations Manager 共存

使用 Operations Manager 時，Operational Insights 只能在 System Center Operations Manager 2012 R2 或 System Center Operations Manager 2012 SP1 中與 Operations Manager 代理程式搭配使用。不支援舊版的 System Center Operations Manager。由於 Operations Manager 代理程式可用來收集資料，因此它會使用特定認證 (動作帳戶或執行身分帳戶) 來支援某些分析的工作負載，例如 SharePoint 2012。

## Operational Insights 和 SQL Server 2012

使用 Operations Manager 時，System Center 健全狀況服務會在本機系統帳戶下執行。在早於 SQL Server 2008 R2 的 SQL Server 版本中，預設會啟用本機系統帳戶，並且是系統管理員伺服器角色的成員。在 SQL Server 2012，本機系統登入不是系統管理員伺服器角色的一部分。因此當您使用 Operational Insights 時，它完全無法監視 SQL Server 2012 執行個體，且不是所有規則都會產生警示。

## 網際網路和內部網路連線能力

使用直接與 Web 服務連接的代理程式時，代理程式需要具備網際網路的存取權，才能將資料傳送至 Web 服務，並從 Web 服務接收更新的組態資訊。

使用 Operational Insights 時，管理伺服器需要網際網路的存取權，才能將資料傳送至 Web 服務，並從 Web 服務接收更新的組態資訊。不過，您的代理程式不需要存取網際網路。如果您在未連線到網際網路的伺服器上有 Operations Manager 代理程式，您可以使用 Web 服務，但前題是它們可與網際網路連線的管理伺服器通訊。

## 叢集支援

執行 Windows Server 2012、Windows Server 2008 R2 或設定為 Windows 容錯移轉叢集一部分的 Windows Server 2008 電腦支援 Operations Manager 代理程式。您可以在 Operational Insights 入口網站中檢視叢集。在 [伺服器] 頁面上，叢集會識別為 TYPE=CLUSTER (而不是 TYPE=AGENT，即識別實體電腦的方式)。

探索和組態規則是在叢集的主動和被動節點上執行，但會忽略被動節點上產生的任何警示。如果節點會從被動轉移為主動，則會自動顯示該節點的警示，而且不需要您介入。

某些警示可能會產生兩次，視產生警示的規則而定。比方說，藉由檢查作業系統為實體伺服器和叢集產生的警示，從而偵測不正確驅動程式的規則

不支援被動節點的組態分析。

Operational Insights 不支援將執行 Windows Server 的電腦 (屬於相同的容錯移轉叢集) 分組或連結。

## 調整 Operational Insights 環境

當您計劃 Operational Insights 部署 (特別是當您分析想要透過單一管理群組轉送資料的 Operations Manager 代理程式數目時)，請考慮伺服器的檔案空間容量。

請考慮下列變數：

- 每個管理群組的代理程式數目

- 每日從代理程式傳送到管理群組的資料平均大小。根據預設，每個代理程式每日會將 CAB 檔案上傳到管理群組兩次。CAB 檔案的大小取決於伺服器的組態 (例如 SQL Server 引擎和數字資料庫的數目) 和伺服器的健全狀況 (例如產生的警示數目)。在大部分情況下，每日上傳大小通常小於 100 KB。

- 管理群組中保存資料的保存期間 (預設值為 5 天)

例如，如果您假設每個代理程式在預設的保存期間內，每日上傳的大小為 100 KB，您需要為管理群組準備下列儲存體：

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>代理程式數目</b></td>
		<td><b>管理群組所需的估計空間</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2.5 MB (5 個代理程式 x 100 KB 資料/天 x 5 天 = 2,500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 個代理程式 x 100 KB 資料/天 x 5 天 = 25,000 KB)</td>
    </tr>

    </tbody>
    </table>

## 地理位置

如果您想要分析來自不同地理位置的伺服器資料，請考慮在每個位置設定一個管理群組。這可以改善從代理程式到管理群組傳輸資料的效能。


<!--HONumber=52-->