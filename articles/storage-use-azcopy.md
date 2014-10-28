<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram"></tags>

# 開始使用 AzCopy 命令列公用程式

AzCopy 是個命令列公用程式，專為高效能上傳、下載，以及將資料複製到和複製出 Microsoft Azure Blob 和檔案儲存體所設計。本指南提供使用 AzCopy 的概觀。

> [WACOM.NOTE] 本指南假設您已安裝 AzCopy 2.5 或更新版本。請注意，AzCopy 目前為發行前版本，未來版本中的命令列選項及其功能有可能會有更動。

## 目錄

-   [下載並安裝 AzCopy][下載並安裝 AzCopy]
-   [了解 AzCopy 命令列語法][了解 AzCopy 命令列語法]
-   [限制複製資料時的並行寫入][限制複製資料時的並行寫入]
-   [使用 AzCopy 複製 Azure Blob][使用 AzCopy 複製 Azure Blob]
-   [使用 AzCopy 複製在 Azure 檔案共用中的檔案][使用 AzCopy 複製在 Azure 檔案共用中的檔案]
-   [AzCopy 版本][AzCopy 版本]
-   [後續步驟][後續步驟]

## <span id="install"></span></a> 下載並安裝 AzCopy

1.  下載[最新版本的 AzCopy][最新版本的 AzCopy]。
2.  執行安裝。依預設，AzCopy 安裝會在 `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (在執行 64 位元 Windows 的機器上) 或 `%ProgramFiles%\Microsoft SDKs\Azure\` (在執行 32 位元 Windows 的機器上) 下建立一個名為 `AzCopy` 的資料夾。不過，您可以在安裝精靈中變更安裝路徑。
3.  若有需要，您可以在您的系統路徑中加入 AzCopy 安裝位置。

## <span id="syntax"></span></a> 了解 AzCopy 命令列語法

接下來，開啟命令視窗，並瀏覽至電腦上的 AzCopy 安裝目錄，也就是 `AzCopy.exe` 可執行檔的位置。AzCopy 命令的基本語法是：

    AzCopy <source> <destination> [filepattern] [options]

-   `<source>` 參數可指定要複製的來源資料。來源可以是檔案系統目錄、Blob 容器、Blob 虛擬目錄或儲存體檔案共用。

-   `<destination>` 參數可指定複製的目的地。目的地可以是檔案系統目錄、Blob 容器、Blob 虛擬目錄或儲存體檔案共用。

-   選擇性 `filepattern` 參數的行為取決於來源資料的位置以及是否有遞迴模式選項。遞迴模式可透過 `/S` 選項進行指定。

    如果指定來源是檔案系統中的目錄，則標準萬用字元便會立即生效，且所提供的檔案模式會與目錄中的檔案做比對。如果已指定 `/S` 選項，則 AzCopy 也會將指定模式與該目錄下任何子資料夾中的所有檔案做比對。

    如果指定來源是 Blob 容器或虛擬目錄，則萬用字元並不適用。如果已指定 `/S` 選項，則 AzCopy 會將指定的檔案模式解譯為 Blob 首碼。如果未指定 `/S` 選項，則 AzCopy 會將檔案模型與確切 Blob 名稱做比對。

    如果指定來源是 Azure 檔案共用，則您必須指定確切檔案名稱 (*例如*`abc.txt`) 以複製單一檔案，或指定 `/S` 選項以遞迴方式複製共用中的所有檔案。嘗試一起指定檔案模式和 `/S` 選項將會造成錯誤。

    未指定檔案模式時所使用的預設檔案模式如下：若是檔案系統目錄是 `*.*`，若是 Azure Blob 或檔案儲存體資源則是空白首碼。

    > [WACOM.NOTE] 基於效能考量，AzCopy 2.5 版不再支援單一命令中的多個檔案模式。您現在必須發佈多個命令，每個命令均具有單一檔案模式，以解決多個檔案模式的案例。

-   下表說明 `options` 參數的可用選項。您也可以在命令列中輸入 `AzCopy /?` 以取得選項的說明。

<table>
  <tr>
<th>選項名稱</th>
<th>說明</th>
<th>適用於 Blob 儲存體 (Y/N)</th>
<th>適用於檔案儲存體 (Y/N)</th>
  </tr>
  <tr>
<td><b>/DestKey:&lt;storage-key&gt;</b></td>
<td>指定目的地資源的儲存體帳戶金鑰。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/DestSAS:&lt;container-SAS&gt;</b></td>
<td>指定目的地容器的共用存取簽章 (SAS) (如果適用的話)。為 SAS 加上雙引號，因為它可能包含特殊命令列字元。<br />
如果目的地資源是 Blob，您可以指定此選項後面接著 SAS 權杖，或者您可以不使用此選項，直接將 SAS 指定為目的地 Blob URI 的一部分。<br />
此選項僅適用於 Blob 儲存體，而且只有在相同儲存體帳戶內上傳或複製 Blob 時才有效。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/SourceKey:&lt;storage-key&gt;</b></td>
<td>指定來源資源的儲存體帳戶金鑰。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/SourceSAS:&lt;container-SAS&gt;</b></td>
<td>指定來源容器的共用存取簽章 (如果適用的話)。為 SAS 加上雙引號，因為它可能包含特殊命令列字元。 
        <br />
如果未提供金鑰及 SAS，則可透過匿名存取讀取容器。 
        <br />
此選項僅適用於 Blob 儲存體。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/S</b></td>
<td>指定複製作業的遞迴模式。在遞迴模式中，AzCopy 將複製所有符合指定檔案模式的 Blob 或檔案，包括子資料夾中的 Blob 或檔案。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/BlobType:&lt;block | page&gt;</b></td>
<td>指定目的地是區塊 Blob 或是分頁 Blob。此選項僅在目的地是 Blob 時才適用；否則便會產生錯誤。如果目的地是 Blob 且未指定此選項，則 AzCopy 會依預設假設需要區塊 Blob。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/CheckMd5</b></td>
<td>計算下載資料的 MD5 雜湊，並驗證 MD5 雜湊是否儲存在符合計算之雜湊的 Blob 或檔案的 Content-MD5 屬性中。MD5 檢查依預設為關閉，因此您必須指定此選項，才能在下載資料時執行 MD5 檢查。
    <br />
請注意，Azure 儲存體並不保證儲存供 Blob 或檔案使用的 MD5 雜湊是最新的版本。每次修改 Blob 或檔案時將 MD5 更新是用戶端的責任。
    <br />
在上傳至服務之後，AzCopy 一定會為 Azure Blob 或檔案設定 Content-MD5 屬性。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Snapshot</b></td>
<td>指出是否傳輸快照。此選項僅在來源是 Blob 才有效。 
        <br />
已傳輸的 Blob 快照會以下列格式重新命名：[blob-name] (snapshot-time)[extension]。 
        <br />
依預設，不會複製快照。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/V:[verbose log-file]</b></td>
<td>將詳細資訊狀態訊息輸出至記錄檔。依預設，在 <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code> 中詳細資訊記錄檔會被命名為 <code data-inline="1">AzCopyVerbose.log</code>。如果您在此選項中指定現有檔案位置，則詳細資訊記錄將會被附加到該檔案。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Z:[journal-file-folder]</b></td>
<td>指定用於繼續作業的日誌檔案資料夾。<br />
如果作業遭到中斷，AzCopy 絕對支援繼續作業。<br />
如果未指定此選項，或指定此選項但沒有指定資料夾路徑，則 AzCopy 將在預設位置上建立日誌檔案，預設位置是 <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code>。<br />
每次發佈命令至 AzCopy 時，它會檢查預設資料夾或透過此選項指定的資料夾中是否有日誌檔案存在。如果在這兩個地方都找不到日誌檔案，AzCopy 會將此作業視為新的作業，並產生新的日誌檔案。
        <br />
如果找到日誌檔案，則 AzCopy 將檢查所輸入的命令列是否符合日誌檔案中的命令列。如果這兩個命令列相符，AzCopy 便會繼續未完成的作業。如果這兩個命令列不符，系統將提示您覆寫日誌檔案並開始新的作業，或取消目前作業。 
        <br />
成功完成作業時，系統便會將日誌檔案刪除。
        <br />
請注意，不支援根據舊版的 AzCopy 所建立的日誌檔案繼續作業。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/@:response-file</b></td>
<td>指定包含參數的檔案。AzCopy 處理檔案中的參數，就好像在命令列上指定這些參數一様。<br /> 
在回應檔案中，您可以在單行中指定多個參數，或每一行各自指定一個參數。請注意，各個參數無法橫跨多行。 
        <br />
回應檔案可包含開頭為 <code data-inline="1">#</code> 符號的命令列。 
        <br />
您可以指定多個回應檔案。不過，請記住 AzCopy 不支援巢狀回應檔案。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Y</b></td>
<td>隱藏所有 AzCopy 確認提示。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/L</b></td>
<td>僅指定清單作業，不會複製資料。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/MT</b></td>
<td>將下載檔案的最後修改時間設定為與來源 Blob 或檔案相同的最後修改時間。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XN</b></td>
<td>排除較新的來源資源。如果來源比目的地還要新，則不會複製資源。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XO</b></td>
<td>排除較舊的來源資源。如果來源資源比目的地還要舊，則不會複製資源。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/A</b></td>
<td>僅上傳已設定 [封存] 屬性的檔案。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/IA:[RASHCNETOI]</b></td>
<td>僅上傳已設定任何指定屬性的檔案。<br />
可用屬性包括：  
        <br />
R&nbsp;&nbsp;&nbsp;唯讀檔案
        <br />
A&nbsp;&nbsp;&nbsp;準備封存的檔案
        <br />
S&nbsp;&nbsp;&nbsp;系統檔案
        <br />
H&nbsp;&nbsp;&nbsp;隱藏檔案
        <br />
C&nbsp;&nbsp;&nbsp;壓縮檔案
        <br />
N&nbsp;&nbsp;&nbsp;正常檔案
        <br />
E&nbsp;&nbsp;&nbsp;加密檔案
        <br />
T&nbsp;&nbsp;&nbsp;暫存檔案
        <br />
O&nbsp;&nbsp;&nbsp;離線檔案
        <br />
I&nbsp;&nbsp;&nbsp;未編製索引的檔案</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XA:[RASHCNETOI]</b></td>
<td>排除已設定任何指定屬性的檔案。<br />
可用屬性包括：  
        <br />
R&nbsp;&nbsp;&nbsp;唯讀檔案  
        <br />
A&nbsp;&nbsp;&nbsp;準備封存的檔案  
        <br />
S&nbsp;&nbsp;&nbsp;系統檔案  
        <br />
H&nbsp;&nbsp;&nbsp;隱藏檔案  
        <br />
C&nbsp;&nbsp;&nbsp;壓縮檔案  
        <br />
N&nbsp;&nbsp;&nbsp;正常檔案  
        <br />
E&nbsp;&nbsp;&nbsp;加密檔案  
        <br />
T&nbsp;&nbsp;&nbsp;暫存檔案  
        <br />
O&nbsp;&nbsp;&nbsp;離線檔案  
        <br />
I&nbsp;&nbsp;&nbsp;未編製索引的檔案</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Delimiter:&lt;delimiter&gt;</b></td>
<td>指出在 Blob 名稱中，用來分隔虛擬目錄的分隔符號字元。<br />
依預設，AzCopy 會使用 / 作為分隔符號字元。不過，AzCopy 支援使用任何常見字元 (例如 @、# 或 %) 作為分隔符號。如果您必須在命令列中包含其中一個特殊字元，請為檔案名稱加上雙引號。 
        <br />
此選項僅適用於下載 Blob。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/NC</b></td>
<td>指定並行作業的數目。 
        <br />
預設數目是執行中核心處理器數目的 8 倍。因此如果您的電腦擁有 8 個核心，則依預設 AzCopy 會啟動 64 個並行作業。<br />
請注意，在低頻寬環境中的大量並行作業有可能會拖垮網路連線，並阻止完全完成作業。根據實際可用網路頻寬來節流處理並行作業。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/SourceType:Blob</b></td>
<td>指定 <code data-inline="1">source</code> 資源是可在本機開發環境中使用，並在儲存體模擬器中執行的 Blob。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/DestType:Blob</b></td>
<td>指定 <code data-inline="1">destination</code> 資源是可在本機開發環境中使用，並在儲存體模擬器中執行的 Blob。</td>
<td>Y</td>
<td>N</td>
  </tr>
</table>


## <span id="limit-writes"></span></a> 限制複製資料時的並行寫入

使用 AzCopy 複製 Blob 或檔案時，請留意當您在複製資料時，另一個應用程式可能正在修改該資料。請儘可能地確定在複製作業過程中，您正要複製的資料並不在修改中。例如，當複製與 Azure 虛擬機器相關聯的 VHD 時，請確定目前沒有其他應用程式正在寫入此 VHD。此外，您可以首先建立 VHD 的快照，然後複製此快照。

如果您無法在複製時防止其他應用程式寫入 Blob 或檔案，請記住，工作完成時，複製的資源可能不再與來源資源完全相同。

## <span id="copy-blobs"></span></a> 使用 AzCopy 複製 Azure Blob

下列範例說明使用 AzCopy 複製 Blob 的各種案例。

### 複製單一 Blob

**將檔案從檔案系統上傳至 Blob 儲存體：**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**將 Blob 從 Blob 儲存體下載到檔案系統：**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### 透過伺服器端複製來複製 Blob

當您在儲存體帳戶內或跨儲存體帳戶複製 Blob 時，系統便會執行伺服器端複製作業。如需伺服器端複製作業的詳細資訊，請參閱[非同步跨帳戶複製 Blob 簡介][非同步跨帳戶複製 Blob 簡介] (英文)。

**在儲存體帳戶內複製 Blob：**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**在不同儲存體帳戶內複製 Blob：**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### 從次要區域複製 Blob

如果您的儲存體帳戶已啟用讀取存取地理區域備援儲存體，則您可以複製次要區域的資料。

**將 Blob 從次要帳戶複製到主要帳戶：**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**將次要區域中的 Blob 下載到檔案系統中的某個檔案：**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### 將檔案上傳至新的 Blob 容器或虛擬目錄

**將檔案上傳至新的 Blob 容器**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

請注意，如果指定的目的地容器不存在，則 AzCopy 將建立此容器並將檔案上傳至該容器中。

**將檔案上傳至新的 Blob 虛擬目錄**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

請注意，如果指定的虛擬目錄不存在，則 AzCopy 將上傳檔案並在其名稱中加上此虛擬目錄 (*例如*，上述範例中的 `vd/abc.txt`)。

### 將 Blob 下載到新的資料夾

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

如果資料夾 `C:\myfolder` 尚未存在，AzCopy 將在檔案系統中建立此資料夾，並將 `abc.txt` 下載到新的資料夾。

### 將目錄中的檔案和子資料夾以遞迴方式上傳至容器

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

指定 `/S` 選項以遞迴方式複製指定目錄的內容到 Blob 儲存體，這表示也會複製所有的子資料夾及其檔案。例如，假設下列檔案位於 `C:\myfolder` 資料夾內：

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 將檔案從目錄以非遞迴方式上傳至容器

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

如果您未在命令列上指定 `/S` 選項，則 AzCopy 將不會以遞迴方式複製。只有指定目錄中的檔案會被複製；任何子資料夾及其檔案「不會」被複製。例如，假設下列檔案位於 `C:\myfolder` 資料夾內：

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

    abc.txt
    abc1.txt
    abc2.txt

### 將容器中的所有 Blob 以遞迴方式下載到檔案系統中的目錄

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

假設下列 Blob 位於指定容器中：

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

複製作業之後，目錄 `C:\myfolder` 將包含下列檔案：

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### 將虛擬 Blob 目錄中的 Blob 以遞迴方式下載到檔案系統中的目錄

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

假設下列 Blob 位於指定容器中：

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

複製作業之後，目錄 `C:\myfolder` 將包含下列檔案。請注意，只有虛擬目錄中的 Blob 會被複製：

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### 將符合指定檔案模式的檔案以遞迴方式上傳至容器

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

假設下列檔案位於 `C:\myfolder` 資料夾內：

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

複製作業之後，容器將包含下列檔案：

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 將具有指定首碼的 Blob 以遞迴方式下載到檔案系統

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

假設下列 Blob 位於指定容器中。所有以首碼 `a` 開頭的 Blob 將會被複製：

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

複製作業之後，資料夾 `C:\myfolder` 將包含下列檔案：

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

請注意，首碼會套用到虛擬目錄，虛擬目錄會構成第一部分的 Blob 名稱。在上述範例中，虛擬目錄不符合指定的首碼，所以不會被複製。

### 將 Blob 及其快照複製到另一個儲存體帳戶

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

複製作業之後，目標容器將包含 Blob 及其快照。假設上述範例中的 Blob 有兩份快照，則容器將包含下列 Blob 及快照：

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### 使用回應檔案指定命令列參數

    AzCopy /@:"C:\myfolder\abc.txt"

您可以在回應檔案中包含任何 AzCopy 命令列參數。AzCopy 處理檔案中的參數，就好像在命令列上指定這些參數一様，執行使用檔案內容的直接取代。

**指定一或多個單行回應檔案**

假設名為 `source.txt` 且指定來源容器的回應檔案：

    http://myaccount.blob.core.windows.net/mycontainer

及名為 `dest.txt` 且在檔案系統中指定目的地資料夾的回應檔案：

    C:\myfolder

及名為 `options.txt` 且指定 AzCopy 選項的回應檔案：

    /S /Y

若要使用這些回應檔案 (所有這些檔案皆位於 `C:\responsefiles` 目錄內) 呼叫 AzCopy，請使用此命令：

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy 處理此命令，就好像您在命令列上包含所有個別參數一樣：

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**指定多行回應檔案**

假設名為 `copyoperation.txt` 且包含下列資料行的回應檔案。每一行會各自指定一個 AzCopy 參數：

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

若要使用這個回應檔案呼叫 AzCopy，請使用此命令：

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy 處理此命令，就好像您在命令列上包含所有個別參數一樣：

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

請注意，必須在一行內指定完一個 AzCopy 參數。例如，如果您將參數分割成兩行，如以下的 `/sourcekey` 參數所示，則 AzCopy 將會失敗：

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### 指定共用存取簽章 (SAS)

**使用 /sourceSAS 選項為來源容器指定 SAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**為來源容器 URI 上的來源容器指定 SAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**使用 /destSAS 選項為目的地容器指定 SAS**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**為來源和目的地容器指定 SAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### 指定日誌檔案資料夾

每次發佈命令至 AzCopy 時，它會檢查預設資料夾或透過此選項指定的資料夾中是否有日誌檔案存在。如果在這兩個地方都找不到日誌檔案，AzCopy 會將此作業視為新的作業，並產生新的日誌檔案。

如果找到日誌檔案，則 AzCopy 將檢查所輸入的命令列是否符合日誌檔案中的命令列。如果這兩個命令列相符，AzCopy 便會繼續未完成的作業。如果這兩個命令列不符，系統將提示您覆寫日誌檔案並開始新的作業，或取消目前作業。

**使用日誌檔案的預設位置**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

如果省略 `/Z` 選項，或指定 `/Z` 選項但沒有指定資料夾路徑 (如上所示)，則 AzCopy 會在預設位置上建立日誌檔案，預設位置是 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`。如果日誌檔案已存在，則 AzCopy 會根據此日誌檔案繼續作業。

**指定日誌檔案的自訂位置**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

如果日誌檔案不存在，本範例將建立日誌檔案。如果日誌檔案已存在，則 AzCopy 會根據此日誌檔案繼續作業。

**繼續 AzCopy 作業**

    AzCopy /Z:C:\journalfolder\

本範例會繼續最後一個無法完成的作業。

### 產生記錄檔

**寫入預設位置上的詳細資訊記錄檔**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

如果指定 `/V` 選項，但沒有提供詳細資訊記錄的檔案路徑，則 AzCopy 會在預設位置上建立記錄檔，預設位置是 `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`。

**寫入自訂位置上的詳細資訊記錄檔**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

請注意，如果您在 `/V` 選項後面指定相對路徑 (例如 `/V:test/azcopy1.log`)，則系統會在目前工作目錄中、名為 `test` 的子資料夾內建立詳細資訊記錄。

### 將下載檔案的最後修改時間設定為與來源 Blob 相同的最後修改時間

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### 根據其最後修改時間，將 Blob 從複製作業中排除

指定 `/MT` 選項可比較來源 Blob 和目的地檔案的最後修改時間。

**將比目的地檔案還要新的 Blob 排除**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**將比目的地檔案還要舊的 Blob 排除**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### 指定要啟動的並行作業數目

`/NC` 選項可指定並行複製作業的數目。AzCopy 預設的開始並行作業數目是 8 倍的所擁有核心處理器數目。如果您在低頻寬的網路上執行 AzCopy，則您可以在此選項中指定較低的數字，以避免因為資源競爭所導致的失敗。

### 在儲存體模擬器中針對 Blob 資源執行 AzCopy

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> 使用 AzCopy 複製在 Azure 檔案共用中的檔案

下列範例說明使用 AzCopy 複製 Azure 檔案的各種案例。

### 將檔案從 Azure 檔案共用下載到檔案系統

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

請注意，如果指定來源是 Azure 檔案共用，則您必須指定確切檔案名稱 (*例如*`abc.txt`) 以複製單一檔案，或指定 `/S` 選項以遞迴方式複製共用中的所有檔案。嘗試一起指定檔案模式和 `/S` 選項將會造成錯誤。

### 將 Azure 檔案共用中的檔案和資料夾以遞迴方式下載到檔案系統

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

請注意，將不會複製任何空白資料夾。

### 將檔案和資料夾從檔案系統以遞迴方式上傳至 Azure 檔案共用

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

請注意，將不會複製任何空白資料夾。

### 將符合指定檔案模式的檔案以遞迴方式上傳至 Azure 檔案共用

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> AzCopy 版本

| 版本       | 新功能                                                                   |
|------------|--------------------------------------------------------------------------|
| **V2.5.0** | **目前版本。針對大規模複製案例最佳化效能，並介紹數個重要的可用性改良。** |
| V2.4.1     | 支援在安裝精靈中指定目的地資料夾。                                       |
| V2.4.0     | 支援上傳與下載 Azure 檔案儲存體的檔案。                                  |
| V2.3.0     | 支援讀取存取地理區域備援儲存體帳戶。                                     |
| V2.2.2     | 已升級並使用 Azure 儲存體用戶端程式庫 3.0.3 版。                         |
| V2.2.1     | 已修正在相同儲存體帳戶中複製大量檔案時的效能問題。                       |
| V2.2       | 支援設定 Blob 名稱的虛擬目錄分隔符號。支援指定日誌檔案路徑。             |
| V2.1       | 提供超過 20 個選項，以有效的方式支援 Blob 上傳、下載及複製作業。         |

## <span id="next-steps"></span></a>後續步驟

如需關於 Azure 儲存體和 AzCopy 的詳細資訊，請參閱下列資源。

### Azure 儲存體文件：

-   [Azure 儲存體簡介][Azure 儲存體簡介]
-   [在 Blob 儲存體中儲存檔案 (英文)][在 Blob 儲存體中儲存檔案 (英文)]
-   [使用檔案儲存體在 Azure 中建立 SMB 檔案共用 (英文)][使用檔案儲存體在 Azure 中建立 SMB 檔案共用 (英文)]

### Azure 儲存體部落格文章：

-   [Microsoft Azure 檔案服務簡介][Microsoft Azure 檔案服務簡介]
-   [AzCopy 2.5：針對大規模複製案例最佳化 (英文)][AzCopy 2.5：針對大規模複製案例最佳化 (英文)]
-   [AzCopy：支援讀取存取地理區域備援儲存體 (英文)][AzCopy：支援讀取存取地理區域備援儲存體 (英文)]
-   [AzCopy：使用可重新啟動模式和 SAS 權杖傳輸資料 (英文)][AzCopy：使用可重新啟動模式和 SAS 權杖傳輸資料 (英文)]
-   [AzCopy：使用跨帳戶複製 Blob (英文)][AzCopy：使用跨帳戶複製 Blob (英文)]
-   [AzCopy：上傳/下載 Windows Azure Blob 的檔案 (英文)][AzCopy：上傳/下載 Windows Azure Blob 的檔案 (英文)]

  [下載並安裝 AzCopy]: #install
  [了解 AzCopy 命令列語法]: #syntax
  [限制複製資料時的並行寫入]: #limit-writes
  [使用 AzCopy 複製 Azure Blob]: #copy-blobs
  [使用 AzCopy 複製在 Azure 檔案共用中的檔案]: #copy-files
  [AzCopy 版本]: #versions
  [後續步驟]: #next-steps
  [最新版本的 AzCopy]: http://aka.ms/downloadazcopy
  [非同步跨帳戶複製 Blob 簡介]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Azure 儲存體簡介]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-introduction/
  [在 Blob 儲存體中儲存檔案 (英文)]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-blobs/
  [使用檔案儲存體在 Azure 中建立 SMB 檔案共用 (英文)]: http://azure.microsoft.com/zh-tw/documentation/articles/storage-dotnet-how-to-use-files/
  [Microsoft Azure 檔案服務簡介]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5：針對大規模複製案例最佳化 (英文)]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy：支援讀取存取地理區域備援儲存體 (英文)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy：使用可重新啟動模式和 SAS 權杖傳輸資料 (英文)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy：使用跨帳戶複製 Blob (英文)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy：上傳/下載 Windows Azure Blob 的檔案 (英文)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
