<properties
	pageTitle="Azure Batch 服務基本概念 | Microsoft Azure"
	description="了解如何將 Azure Batch 服務用於大規模的平行工作負載和 HPC 工作負載 "
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="marsma"/>

# Azure Batch 的基本概念

Azure Batch 可讓您在雲端有效率地執行大規模的平行和高效能運算 (HPC) 應用程式。它是一項平台服務，可排程要在一組受管理的虛擬機器上執行的計算密集型工作，而且可以調整計算資源以符合工作的需求。

使用 Batch 服務可讓您以程式設計方式定義 Azure 計算資源來執行大規模的批次作業。您可以依需要或依排程執行這些作業，而不需要手動設定和管理 HPC 叢集、個別的虛擬機器、虛擬網路或作業排程器。

## Batch 的使用案例

Batch 是一項受管理的 Azure 服務，可用於*批次處理*或*批次運算* -- 執行大量的類似工作以得到期望的結果。定期處理、轉換和分析大量資料的組織最常使用批次運算。

Batch 很適合處理本質平行 (也稱為「超簡單平行」) 的應用程式和工作負載。本質平行工作負載可容易分割成多個工作，在多部電腦上同時執行。

![平行工作][1]<br/>

常見使用這項技術處理的一些工作負載範例如下：

* 財務風險模型
* 氣候和水文資料分析
* 影像轉譯、分析和處理
* 媒體編碼及轉碼
* 基因序列分析
* 工程壓力分析
* 軟體測試

Batch 也可以執行平行計算 (最後加上歸納步驟)，以及執行更複雜的 HPC 工作負載，例如[訊息傳遞介面 (MPI)](batch-mpi.md) 應用程式。

如需 Batch 與 Azure 中其他 HPC 解決方案選項的比較，請參閱 [Batch 和 HPC 解決方案](batch-hpc-solutions.md)。

>[AZURE.NOTE] 在此階段，Batch 只支援在 Windows Server 型虛擬機器執行的工作負載。

## 使用 Batch 進行開發

當您建置決方案來使用 Azure Batch 處理平行工作負載時，您可以使用 Batch API 來程式設計。Batch API 可讓您建立和管理計算節點 (虛擬機器) 集區，以及排程作業及工作在這些節點上執行。您撰寫的用戶端應用程式或服務會使用 Batch API 來與 Batch 服務進行通訊。您可以為組織有效率地處理大量工作負載，或提供前端服務給客戶，讓他們可以在一個、數百個或數千個節點上，依需要或依排程執行作業和工作。您也可以在 [Azure Data Factory][data_factory] 之類的工具所管理的大型工作流程中使用 Batch。

> [AZURE.TIP] 當您準備鑽研 Batch API 以深入了解它所提供的功能時，請參閱 [Azure Batch 功能概觀](batch-api-basics.md)。

### 您需要的 Azure 帳戶

當您開發 Batch 解決方案時，您將在 Microsoft Azure 中使用下列帳戶。

- **Azure 帳戶和訂用帳戶** - 如果您沒有 Azure 訂用帳戶，您可以啟用 [MSDN 訂戶權益][msdn_benefits]，或是註冊[免費 Azure 帳戶][free_account]。當您建立帳戶時，將會為您建立預設訂用帳戶。

- **Batch 帳戶** - 當您的應用程式與 Batch 服務互動時，可以使用帳戶名稱、帳戶的 URL 及存取金鑰做為認證。所有 Batch 資源 (如集區、計算節點、作業和工作) 都與 Batch 帳戶相關聯。您可以在 Azure 入口網站中[建立和管理 Batch 帳戶](batch-account-create-portal.md)。

- **儲存體帳戶** - Batch 內建就支援處理 [Azure 儲存體][azure_storage]中的檔案。幾乎每個 Batch 案例都會使用 Azure 儲存體來預備檔案 (用於工作執行的程式及它們處理的資料)，以及儲存工作所產生的輸出資料。若要建立儲存體帳戶，請參閱[關於 Azure 儲存體帳戶](./../storage/storage-create-storage-account.md)。

### Batch 開發程式庫和工具

若要使用 Azure Batch 建置解決方案，您可以使用 Batch .NET 用戶端程式庫、PowerShell，甚至直接發出 REST API 呼叫。請使用任何或所有這些工具來開發在 Batch 中執行作業的用戶端應用程式和服務。

- [Batch .NET][api_net] 用戶端程式庫 - 大部分 Batch 解決方案都是使用 Batch .NET 用戶端程式庫建置，您可以[透過 NuGet 取得][api_net_nuget]它。

- [Batch Management .NET][api_net_mgmt] 用戶端程式庫 - 也可以[透過 NuGet 取得][api_net_mgmt_nuget]，在您的用戶端應用程式或服務中，使用 Batch Management .NET 用戶端程式庫以程式設計方式管理 Batch 帳戶。

- [Batch REST][batch_rest] API - Batch REST API 提供與 Batch .NET 用戶端程式庫相同的所有功能。事實上，Batch .NET 程式庫本身會在幕後使用 Batch REST API 與 Batch 服務互動。

- [Batch PowerShell Cmdlet][batch_ps] - [Azure PowerShell](./../powershell-install-configure.md) 模組中的 Batch PowerShell Cmdlet 可讓您使用 PowerShell 來管理 Batch 資源。

- [Azure Batch 總管][batch_explorer] - Batch 總管是可[透過 GitHub 取得][github_samples]的其中一個 Batch .NET 範例應用程式。使用 Visual Studio 2013 或 2015 建置此 Windows Presentation Foundation (WPF) 應用程式，在您開發及偵錯 Batch 解決方案時，使用它來瀏覽和管理 Batch 帳戶中的資源。在 Batch 總管介面中只需按幾下滑鼠，就可以檢視作業、集區和工作詳細資訊、從計算節點下載檔案，甚至使用您取得的遠端桌面 (RDP) 檔案從遠端連接到節點。

- [Microsoft Azure 儲存體總管][storage_explorer] - 嚴格來說，雖然儲存體總管不算是 Azure Batch 工具，但卻是您開發和偵錯 Batch 解決方案時的另一個很實用的工具。

## 案例：相應放大平行工作負載

使用 Batch API 來與 Batch 服務互動的一個常見案例涉及在一組計算節點上相應放大本質平行工作，例如轉譯 3D 場景的影像。例如，這組計算節點可能是您的「轉譯伺服器陣列」，提供數十、數百或甚至數千個核心來呈現作業。

下圖顯示一個常見的 Batch 工作流程，其中有一個用戶端應用程式或託管服務使用 Batch 執行平行工作負載。

![Batch 解決方案工作流程][2]

在這個常見的案例中，應用程式或服務執行下列步驟，在 Azure Batch 中處理計算工作負載：

1. 將**輸入檔**和處理這些檔案的**應用程式**上傳到您的 Azure 儲存體帳戶。輸入檔可以是您的應用程式將會處理的任何資料，例如財務模型化資料或要轉碼的視訊檔案。應用程式檔案可以是任何用於處理資料的應用程式，例如 3D 轉譯應用程式或媒體轉碼器。

2. 在 Batch 帳戶中建立計算節點的 Batch **集區** -- 這些是將執行工作的虛擬機器。您需要指定屬性，例如[節點大小](./../cloud-services/cloud-services-sizes-specs.md)、其作業系統，以及節點加入集區時要安裝的應用程式在 Azure 儲存體中的位置 (您在步驟 #1 中上傳的應用程式)。您也可以設定集區來隨著工作所產生的工作負載而[自動調整](batch-automatic-scaling.md) - 動態調整集區中的計算節點數目。

3. 建立 Batch **作業**在計算節點集區上執行工作負載。當您建立作業時，您需要將它與 Batch 集區建立關聯。

4. 將**工作**加入至作業。當您將工作加入至作業時，Batch 服務會自動排程工作在集區中的計算節點上執行。每一項工作會使用您上傳的應用程式來處理輸入檔。

    - 4a.工作執行之前，它可以將它要處理的資料 (輸入檔) 下載至它被指派的計算節點。如果應用程式未安裝在節點上 (請參閱步驟 2#)，您可以從這裡下載它。下載完成時，工作就會在它被指派的節點上執行。

5. 工作執行時，您可以查詢 Batch 來監視作業及其工作的進度。用戶端應用程式或服務會透過 HTTPS 而與 Batch 服務通訊，因為您可能會監視數千個計算節點上執行的數千個工作，請務必[有效率地查詢 Batch 服務](batch-efficient-list-queries.md)。

6. 當工作完成時，它們可以將其輸出資料上傳至 Azure 儲存體。您也可以直接從計算節點擷取檔案。

7. 當您的監視偵測到作業中的工作已完成時，用戶端應用程式或服務可以下載輸出資料來進一步處理或評估。

請記住，這只是使用 Batch 的一種方式，此案例只描述它可用的幾項功能。例如，您可以在每個計算節點上[以平行方式執行多項工作](batch-parallel-node-tasks.md)，也可以使用[作業準備和完成工作](batch-job-prep-release.md)來準備作業的節點，然後再清除。

## 後續步驟

既然您已看過範例 Batch 案例，現在可以更深入探索服務，了解如何使用它來計算密集型平行工作負載。

- [開始使用適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md)，了解如何使用 C# 和 Batch .NET 程式庫來執行上述的技巧。這應該是您學習如何使用 Batch 服務的第一站。

- 查看 [Batch 功能概觀](batch-api-basics.md)，深入了解 Batch 提供來處理計算密集型工作負載的 API 功能。

- 除了 Batch 總管，其他在 [GitHub 上的程式碼範例][github_samples]也示範如何使用 Batch .NET 程式庫中的許多 Batch 功能。

- 查看 [Batch 學習路徑][learning_path]，了解您在學習使用 Batch 時可用的資源。

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

<!---HONumber=AcomDC_0224_2016-->