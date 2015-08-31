<properties
   pageTitle="在 Docker 中裝載 Web Apps | Microsoft Azure"
   description="了解如何使用 Visual Studio 在 Docker 容器中裝載 Web 應用程式。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/17/2015"
   ms.author="kempb" />

# 在 Docker 中裝載 Web Apps

[Docker](https://www.docker.com/whatisdocker/) 是輕量級容器引擎，與虛擬機器在某些方面類似，您可以用它來裝載應用程式和服務。Visual Studio 在 Ubuntu、CoreOS 和 Windows 上支援 Docker。

此範例將示範如何使用 Visual Studio 2015 Tools for Docker 延伸模組，在已安裝 Docker 延伸模組與 ASP.NET 5 Web 應用程式的 Azure 上將 ASP.NET 5 應用程式發佈到 Ubuntu Linux 虛擬機器 (在此稱為 Docker 主機)。同樣的使用體驗可以用來發佈至 Windows 容器。

您可以使用 [自訂主機] 設定，將應用程式發佈到裝載於 Azure 上的新 Docker 主機，或是到內部部署伺服器、Hyper-V 或 Boot2Docker 主機。發佈您的應用程式到 Docker 主機之後，您可以使用 Docker 命令列工具來與發佈您的應用程式的目標容器互動。

## 建立及發佈新的 Docker 容器

在這些程序中，您將建立新的 ASP.NET 5 Web 應用程式專案、建立容器主機，然後在 Docker 容器中建置以及執行 Web 應用程式專案。若要開始，請下載並安裝 [Visual Studio 2015 Tools for Docker](aka.ms/DockerToolsForVS)。

### 加入 ASP.NET 5 Web 應用程式專案

1. 建立新的 ASP.NET Web 應用程式專案。在主功能表上依序選擇 [檔案] 與 [新增專案]。在 **C#**、**Web** 下，選擇 [ASP.NET Web 應用程式]。

1. 在 **ASP.NET 5 Preview 範本**的清單中，選擇 [網站]。

1. 由於 Web 應用程式將裝載/執行於 Docker 中，請清除 [雲端中的主機] 核取方塊 (若已選取)，然後選擇 [確定] 按鈕。

  ![][0]

  這是您一般會加入程式碼至 Web 應用程式，以便進行一些實用動作的時間點，但在此範例中，我們只保留其預設值。(請注意，您也可以選擇使用現有的 ASP.NET 5 Web 應用程式。)

### 發佈專案

1. 在 ASP.NET 專案的內容功能表中，選擇 [發佈]。

1. 在 [發佈 Web] 對話方塊中的 [選取發佈目標] 區段，選擇 [Docker 容器] 按鈕。

    如果您沒有看到 [Docker 容器] 選項，請確定您已安裝 Visual Studio 2015 Tools for Docker，並且您在上一個程序中選取 ASP.NET 5 網站範本。

    ![][1]

    [選取 Docker 虛擬機器] 對話方塊隨即出現。這可讓您指定發佈專案所在的 Docker 主機。您可以建立新的 Docker 主機或選擇裝載在 Azure 上或其他位置的現有的 VM。稍後在這個範例中，我們要建立新的 Azure Docker 主機。

1. 如果您已登入 Azure 帳戶，請跳至步驟 5。如果您未登入系統帳戶，請選擇 [新增帳戶] 按鈕。

    ![][2]

1. 在 [登入 Visual Studio] 對話方塊中，輸入您的 Azure 訂用帳戶的電子郵件帳戶，然後選擇 [繼續] 按鈕。

1. 選擇 [新增] 按鈕以建立新的 Azure Docker VM，然後選擇 [確定] 按鈕。

    ![][3]

    請注意，您也可以選擇使用現有的 Docker 主機。若要這樣做，請在 [現有 Azure Docker 虛擬機器] 下拉式清單 (而不是選擇 [新增] 按鈕) 選擇它。這份清單不會只顯示容器主機，它會列出所有 Azure 租用戶中的所有 VM。

    或者，您可以選擇發佈到自訂的 Docker 主機。如需詳細資訊，請參閱本主題稍後的[提供自訂 Docker 主機](#BKMK_CustomHost)。

1. 於 [在 Microsoft Azure 上建立虛擬機器] 對話方塊中輸入下列資訊。完成時，選擇 [確定] 按鈕。這會建立設定了 Docker 延伸模組的 Linux 虛擬機器。

    ![][4]

    請注意，您現在也可以選擇使用 Windows Server 2016 Technical Preview 3 (TP3) 建立Windows 容器主機。

    ![][8]

    |屬性名稱|設定|
    |---|---|
    |位置|將此設定變更為最接近您的地區設定的區域。|
    |DNS 名稱|輸入虛擬機器的唯一名稱。如果 Azure 接受此名稱，右邊會出現綠色圓圈，帶有白色核取記號。如果名稱不被接受，會出現紅色圓圈，帶有白色 x。在此情況下，請輸入新的唯一名稱。|
    |映像|選擇要在 Docker 主機中使用的 OS 映像 (如果有)。對於此範例，選擇 Ubuntu Server 映像。(請注意，Windows Server 映像現在於可用映像的清單中提供。)|
    |使用者名稱|輸入虛擬機器的獨特使用者名稱。|
    |密碼|輸入使用者的密碼並加以確認。|
    |憑證目錄 |這會指定儲存 Docker 憑證的資料夾。雖然您可以建立新資料夾或指向現有的資料夾，建議您使用預設的憑證資料夾 (C:\\Users\\[*username*]\\.docker)。否則，如果您在另一個專案或系統上重複使用相同的主機，將無法自動擷取 [驗證] 選項。|

1. 選擇 [憑證目錄] 項目旁的省略符號 (...) 按鈕，然後為 Docker 憑證建立新資料夾，或瀏覽至現有的 Docker 憑證資料夾。

    如果找不到 VM 所需的 Docker 憑證，在項目旁會出現驚嘆號圖示，讓您知道找不到所需的憑證，並且繼續將會刪除然後重新建立任何現有的憑證。

1. 選擇 [確定] 按鈕來開始建立 VM。您會收到訊息，說明正在 Azure 中建立虛擬機器。

    Visual Studio 會建立 Azure 資源管理員 (ARM) 範本檔案、參數檔案和 PowerShell 指令碼，讓您在未來可以再次執行命令。

    ![][7]

    Visual Studio 會輸出作業的進度到 [輸出] 視窗。Visual Studio 呼叫 PowerShell 指令碼來部署 VM。指令碼會使用 Azure PowerShell 指令程式來部署 Azure 資源群組。之後，另一個 PowerShell 指令碼會使用發出 Docker 命令來發佈，就好像您是手動建立 Docker 主機。

    佈建 Docker 主機可能需要一些時間，因此請檢查 [輸出] 視窗中的狀態，以得知作業何時完成。

1. Docker 主機完全在 Azure 中佈建之後，您可以在 Azure 入口網站上檢查您的帳戶。您應該可以在 Azure 入口網站上的 [虛擬機器] 類別目錄下看到新的虛擬機器。

1. 一旦 Docker 主機準備就緒時，請返回並發佈 Web 應用程式專案。在 [方案總管] 中 Web 應用程式專案節點的內容功能表中上，選擇 [發佈]。Visual Studio 會根據您所建立的 VM 建立發佈檔案。

1. 在 [發佈 Web] 對話方塊 的[連接] 索引標籤中，選擇 [驗證連接] 方塊以確定 Docker 主機已經備妥。如果連接良好，請選擇 [發佈] 按鈕以發佈 Web 應用程式。

    第一次將應用程式發佈至 Docker 主機時，需要時間才能下載 Docker 檔案中所參考的任何基礎映像 (例如 **FROM** *imagename*)。

    請注意，Docker 檔案是作業系統特定的檔案。如果您選擇重新發佈至不同的作業系統，您必須重新命名 Docker 檔案，使得 Visual Studio 能夠根據目標作業系統建立新預設值。例如，如果您先發佈至 Linux 容器並稍後決定發佈到 Windows，您應該將 Docker 檔案重新命名為唯一的名稱，例如 DockerLinux。然後，當您重新發佈至 Windows 時，Visual Studio 將會為 Windows 重新建立預設的 Docker 檔案。稍後，當您重新發佈其中任一個時，只需選取作業系統適用的適當 Docker 檔案。

## 提供自訂 Docker 主機

先前的程序讓您建立在 Azure 上裝載的 Docker 虛擬機器。不過，如果您在其他地方已經有現有的 Docker 主機，可以選擇發佈到它，而不是 Azure。

### 如何提供自訂的 Docker 主機

1. 在 [選取 Docker 虛擬機器] 對話方塊中，選取[自訂 Docker 主機] 核取方塊。

    ![][5]

1. 選擇 [確定] 按鈕。

1. 在 [發佈 Web] 對話方塊方塊中，將值加入到 **CustomDockerHost** 區段中的設定，例如：伺服器 URL、映像名稱、Dockerfile 位置和主機與容器連接埠號碼。

    在 [Docker 進階選項] 區段中，您可以檢視或變更 [驗證] 和 [執行] 選項，以及 Docker 命令列。

    ![][6]

1. 輸入所有必要的值之後，選擇 [驗證連接] 按鈕以確保對 Docker 主機的連接正常運作。

1. 如果連接能夠正常運作，您可以選擇 [下一步] 按鈕來查看將發佈的元件的清單，或您可以選擇 [發佈] 按鈕以立即發佈專案。

## 測試 Docker 主機

既然專案已經發佈到 Azure 上的 Docker 主機，您可以藉由檢查它的設定來進行測試。因為 Docker 命令列工具會隨著 Visual Studio 延伸模組安裝，您可以直接從 Windows 命令提示字元發出命令至 Docker。

下列程序用於與已部署至 Azure 的 Docker 主機通訊。

### 如何測試 Docker 主機

1. 開啟 Windows 命令提示字元。

1. 指派 Docker 主機並對環境變數驗證。若要這麼做，請在命令提示字元中輸入下列命令。(將 *NameofAzureVM* 替代為您的 Docker 主機名稱。)

    ```
    Set DOCKER_HOST=tcp://<NameofAzureVM>.cloudapp.net:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    叫用這些命令可讓您不必加入 `–H (Host) tcp://<NameofAzureVM>.cloudapp.net:2376` 和 `--TLSVERIFY` 到您發出的每個命令。

1. 現在您可以發出這類命令來測試 Docker 主機是否已存在且正常運作。

    |命令列|說明|
    |---|---|
    |docker info|取得 Docker 版本資訊。|
    |docker ps|取得執行中容器的清單。|
    |docker ps –a|取得清單的容器，包括停止的容器。|
    |docker logs <Docker container name>|取得指定的容器的記錄檔。|
    |docker images|取得映像的清單。|

    如需 Docker 命令的完整清單，只要在命令提示字元中輸入命令 `docker`。如需詳細資訊，請參閱 [Docker 命令列](https://docs.docker.com/reference/commandline/cli/)。

## 後續步驟

既然已具備 Docker 主機，您可以發出 Docker 命令給它。若要深入了解 Docker，請參閱 [Docker 文件](https://docs.docker.com/) 和 [Docker 線上教學課程](https://www.docker.com/tryit/)。

如需在 Visual Studio 中使用 Docker 的問題，請參閱[疑難排解 Docker 錯誤](vs-docker-troubleshooting-docker-errors.md)。

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=August15_HO8-->