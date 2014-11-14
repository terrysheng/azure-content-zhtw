<properties umbracoNaviHide="0" pageTitle="儲存體帳戶概念 | Azure" metaKeywords="Azure storage, storage service, service, storage account, account, create storage account, create account" description="了解儲存體帳戶的概念。" urlDisplayName="How to: storage accounts" headerExpose="" footerExpose="" disqusComments="1" title="儲存體帳戶概念" services="storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# 儲存體帳戶概念

## 儲存體帳戶複寫選項

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

## 儲存體帳戶端點

儲存體帳戶的「端點」代表存取 Blob、資料表、佇列或檔案時所依據的最高層級命名空間。儲存體帳戶的預設端點具有下列格式：

-   Blob 服務：http://*mystorageaccount*.blob.core.windows.net

-   資料表服務：http://*mystorageaccount*.table.core.windows.net

-   佇列服務：http://*mystorageaccount*.queue.core.windows.net

-   檔案服務：http://*mystorageaccount*.file.core.windows.net

用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。例如，Blob 位址的格式可能如下：http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

## 儲存體帳戶安全性

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。

建議您避免將儲存體帳戶存取金鑰透露給其他任何人。如果您認為帳戶已被盜用，您可以從入口網站重新產生存取金鑰。請選取您的儲存體帳戶，然後選擇 [管理存取金鑰]。

若要允許存取儲存體資源但不要公開您的存取金鑰，您可以使用「共用存取簽章」。共用存取簽章可在您定義的間隔期間內，使用您所指定的權限，來存取帳戶中的資源。如需詳細資訊，請參閱[共用存取簽章教學課程][共用存取簽章教學課程]。

## 儲存體帳戶度量和記錄

-   **最小度量或詳細資訊度量** 您可以在儲存體帳戶的監視設定中設定最小度量或詳細資訊度量。「最小度量」所收集的度量是 Blob、資料表與佇列服務的彙總資料 (例如入口流量/出口流量、可用性、延遲和成功百分比)。「詳細資訊度量」則除了收集服務層級彙總，還會為相同的度量收集作業層級詳細資料。詳細資訊度量可供進一步分析在應用程式運作期間發生的問題。如需可用度量的完整清單，請參閱[儲存體分析度量資料表結構描述][儲存體分析度量資料表結構描述] (英文)。如需儲存體監視的詳細資訊，請參閱[關於儲存體分析度量][關於儲存體分析度量] (英文)。

-   **記錄** 記錄是儲存體帳戶中的可設定功能，能夠將讀取、寫入和刪除 Blob、資料表與佇列的要求進行記錄。您可以在 Azure 管理入口網站中設定記錄功能，但無法在管理入口網站中檢視記錄檔。記錄檔是在儲存體帳戶中受到儲存和存取 (在 $logs 容器中)。如需詳細資訊，請參閱[儲存體分析概觀][儲存體分析概觀]。

## 讓 Azure 儲存體和其他服務並存的同質群組

「同質群組」是將您的 Azure 服務和 VM 與 Azure 儲存體帳戶依地理位置而形成的群組。同質群組會將電腦工作負載置於相同的資料中心內或目標使用者對象附近，因而能改善服務效能。此外，當您儲存體帳戶中的資料是由同一同質群組中的服務存取時，出口流量並不會產生任何費用。

  [共用存取簽章教學課程]: ../storage-dotnet-shared-access-signature-part-1/
  [儲存體分析度量資料表結構描述]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343264.aspx
  [關於儲存體分析度量]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343258.aspx
  [儲存體分析概觀]: http://msdn.microsoft.com/zh-tw/library/windowsazure/hh343268.aspx
