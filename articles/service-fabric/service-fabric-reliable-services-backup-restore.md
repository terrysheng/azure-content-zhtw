<properties
   pageTitle="Reliable Service 備份與還原 |Microsoft Azure"
   description="Service Fabric Reliable Service 備份與還原的概念文件"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="mcoskun"/>

# 備份與還原 Reliable Services

Azure Service Fabric 是高可用性平台，跨多個節點之間複寫狀態以維護這個高可用性。因此，即使叢集中的一個節點失敗，服務可以繼續。雖然這個由平台提供的內建備援對於一些特定情況可能已經足夠，但是服務最好能夠備份資料 (到外部存放區)。

例如，服務在下列案例中可能想要備份資料：

* 在整個 Service Fabric 叢集或執行指定資料分割的所有節點永久遺失時。舉例來說，當狀態不是異地複寫且您的整個叢集是在一個資料中心，而且整個資料中心中斷，就會發生這個事件。

* 不小心刪除或損毀狀態的系統管理錯誤。例如，這可能會在具備足夠權限的系統管理員錯誤地刪除服務時發生。

* 服務中造成資料損毀的錯誤。例如，當服務程式碼升級而開始將錯誤資料寫入「可靠的集合」時，就可能發生此情況。在這種情況下，可能必須將程式碼和資料還原成先前的狀態。

* 離線資料處理。對於獨立於服務來產生資料的商業智慧，離線處理資料相當方便。

備份/還原功能可以讓在 Reliable Services API 上建置的服務建立及還原備份。平台提供的備份 API 可以進行分割區狀態的備份，而不會封鎖讀取或寫入作業。還原 API 可以從所選的備份還原分割狀態。


## 備份作業

服務作者對於進行備份的時機與儲存備份的位置具有完整的控制權。

若要開始備份，服務必須叫用 **IReliableStateManager.BackupAsync**。備份只能從主要複本進行，且需要授與它們寫入狀態。

如下所示，**BackupAsync** 最簡單的多載會在名為接受 **backupCallback** 的 Func<< BackupInfo  bool >> 中進行。

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** 提供備份的相關資訊，包括執行階段儲存備份 (**BackupInfo.Directory**) 所在的資料夾位置。回呼函式預期會將 **BackupInfo.Directory** 移到外部存放區或另一個位置。此函式也會傳回 Bool，指出是否能夠順利將備份資料夾移動至目標位置。

下列程式碼示範如何使用 backupCallback，將備份上傳至 Azure 儲存體：

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

在上述範例中，**ExternalBackupStore** 是用來與 Azure Blob 儲存體連接的範例類別，**UploadBackupFolderAsync** 是壓縮資料夾並將它放在 Azure Blob 存放區的方法。

請注意：

- 在任何指定時間的每個複本傳遞只能有一個 **BackupAsync**。一次有多個 **BackupAsync** 呼叫會擲回 **FabricBackupInProgressException**，來將傳遞備份限制為一個。

- 如果當備份進行中時有複本容錯移轉，備份可能未完成。因此，容錯移轉完成之後，服務必須負責視需要叫用 **BackupAsync** 以重新啟動備份。

## 還原作業

一般而言，您可能需要執行還原作業的情況屬於下列其中一種：


- 服務資料分割遺失資料。例如，分割區的三分之二複本 (包括主要複本) 的磁碟損毀或抹除。新的主要複本可能需要從備份還原資料。

- 整個服務遺失。例如，系統管理員移除整個服務，因此服務和資料需要還原。

- 服務會複寫損毀的應用程式資料 (例如，因為應用程式錯誤)。在此情況下，服務必須升級或還原以移除損毀的原因，且必須還原未損毀的資料。

雖然有許多種可行的方法，我們會提供使用 **RestoreAsync** 從上述案例復原的一些範例。

## 分割區資料遺失

在此情況下，執行階段會自動偵測資料遺失，並且叫用 **OnDataLossAsync** API。

服務作者必須執行下列動作來復原：

- 覆寫 **IReliableStateManager** 以傳回新的 **ReliableStateManager**，並提供要在遺失資料時呼叫的回呼函式。

- 在包含服務備份的外部位置尋找最新的備份。

- 如果最新備份的狀態是在主要複本之後，則會傳回 False。這可確保新的主要複本不會覆寫為較舊的資料。

- 下載最新的備份 (並將已壓縮的備份解壓縮到備份資料夾)。

- 以備份資料夾路徑呼叫 **IReliableStateManager.RestoreAsync**。

- 如果還原成功，則會傳回 true。

以下是 **OnDataLossAsync** 方法與 **IReliableStateManager** 覆寫的範例實作。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE]RestorePolicy 預設設定為 [安全]。這表示如果 **RestoreAsync** API 偵測到備份資料夾包含早於或等於這個複本所包含之狀態的狀態，則它將會失敗且具有 ArgumentException。**RestorePolicy.Force** 可以用來略過這項安全檢查。

## 已刪除或遺失的服務

如果服務已移除，您必須先重新建立該服務，才可以還原資料。請務必以相同的組態建立服務 (例如分割配置)，如此才能順暢地還原資料。一旦服務啟動，還原資料的 API (上述的 **OnDataLossAsync**) 就必須在此服務的每個資料分割上叫用。達到這個目的的其中一種方法是在每個分割區上使用 **FabricClient.ServiceManager.InvokeDataLossAsync**。

從這裡開始，實作與上述案例相同。每個資料分割都需要從外部存放區還原最新的相關備份。有一點需要注意，分割識別碼現在可能已變更，因為執行階段會以動態方式建立分割識別碼。因此，服務需要儲存適當的分割資訊和服務名稱，來識別要針對每個分割區還原的正確最新備份。


## 損毀之應用程式資料的複寫

如果新部署的應用程式升級有錯誤，可能會造成資料損毀。例如，應用程式升級可能會開始以無效的區碼更新「可靠的字典」中的每個電話號碼記錄。在此情況下，因為 Service Fabric 並不知道要儲存的資料本質，所以會複寫無效的電話號碼。

偵測造成資料損毀的這類嚴重錯誤之後，您要做的第一件事是在應用程式層級凍結服務，並且在可行時升級至沒有錯誤的應用程式程式碼的版本。不過，即使在修正服務程式碼之後，資料仍可能會損毀並且因此需要還原資料。在這種情況下，還原最新的備份可能還不足夠，因為最新的備份也可能已損毀。因此，您必須尋找在資料損毀之前所做的最後一個備份。

如果不確定哪些備份正確，您可以部署新的 Service Fabric 叢集，並還原受影響的分割區備份，就像上述「已刪除或遺失的服務」案例一樣。針對每個資料分割，開始還原從最新到最舊的備份。一旦您找到沒有損毀的備份，就移動/刪除這個資料分割較新 (相較於備份) 的所有備份。針對每個資料分割重複此程序。現在，當在生產叢集中的資料分割上呼叫 **OnDataLossAsync** 時，在外部存放區中找到的最後一個備份會是上述程序所挑選的備份。

現在，可以使用「已刪除或遺失的服務」中的步驟，將服務備份的狀態還原至不良程式碼損毀狀態之前的狀態。

請注意：

- 當您還原時，還原的備份很有可能是早於資料遺失之前的分割區狀態。因此，您應該只能將還原當成最後手段，盡可能復原最多資料。

- 根據 FabricDataRoot 路徑和應用程式類型名稱的長度而定，代表備份資料夾路徑與備份資料夾內檔案路徑的字串可以大於 255 個字元。這會造成一些 **Directory.Move** 之類的 .NET 方法擲回 **PathTooLongException** 例外狀況。有個解決方法是直接呼叫 kernel32 API，例如 **CopyFile**。


## 幕後：備份與還原的詳細資料

### 備份
可靠的狀態管理員能夠建立一致的備份，而不會封鎖任何讀取或寫入作業。為了達到這個目的，它會利用檢查點和記錄持續性機制。可靠的狀態管理員會在特定時間點採用模糊 (輕量型) 檢查點，來減輕交易記錄檔的壓力和改善復原時間。呼叫 **IReliableStateManager.BackupAsync** 時，可靠的狀態管理員會指示所有可靠的物件將其最新的檢查點檔案複製到本機備份資料夾。然後，可靠的狀態管理員會從「開始指標」開始，將所有記錄檔記錄複製到備份資料夾中的最新記錄檔記錄。因為記錄到最新記錄檔記錄的所有記錄檔記錄都會包含於備份中，而且可靠的狀態管理員會保留預寫記錄，所以，可靠的狀態管理員保證所有已認可的交易 (**CommitAsync** 已順利傳回) 都會包含於備份中。

呼叫 **BackupAsync** 之後認可的任何交易，不一定會在備份中。一旦由平台填入本機備份資料夾 (亦即執行階段完成的本機備份) 之後，即會叫用服務的備份回呼。此回呼會負責將備份資料夾移到外部位置，例如 Azure 儲存體。

### 還原

可靠的狀態管理員能夠利用 **IReliableStateManager.RestoreAsync** API，從備份還原。**RestoreAsync** 方法只能在 **OnDataLossAsync** 方法內呼叫。**OnDataLossAsync** 傳回的 Bool 表示服務是否從外部來源還原其狀態。如果 **OnDataLossAsync** 傳回 true，Service Fabric 將會從這個主要複本重建所有其他複本。Service Fabric 可確保將接收 **OnDataLossAsync** 的複本會先轉換成主要角色，但不會被授與讀取狀態或寫入狀態。這暗示對於 StatefulService 實施者而言，將不會呼叫 **RunAsync**，直到 **OnDataLossAsync** 成功完成為止。然後，會在新的主要複本上叫用 **OnDataLossAsync**。在服務成功完成此 API (藉由傳回 true 或 false) 並完成相關重新設定之前，將會一次一個地繼續呼叫 API。


**RestoreAsync** 會先卸除過去呼叫的主要複本中的所有現有狀態。然後，可靠的狀態管理員會建立存在於備份資料夾中所有可靠的物件。接下來，可靠的物件會獲得指示從其備份資料夾中的檢查點還原。最後，可靠的狀態管理員會從備份資料夾中的記錄檔記錄復原自己的狀態，並執行復原。做為復原程序的一部分，作業是從「開始點」開始，在備份資料夾中認可記錄檔記錄，並對可靠的物件重新執行。這個步驟可確保復原的狀態一致。

<!---HONumber=AcomDC_0107_2016-->