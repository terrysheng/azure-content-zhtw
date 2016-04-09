<properties
   pageTitle="在 Docker 中裝載 Web Apps | Microsoft Azure"
   description="了解如何使用 Visual Studio 在 Docker 容器中裝載 Web 應用程式。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="02/10/2016"
   ms.author="tarcher" />

# 在 Docker 中裝載 Web Apps

[Docker](https://www.docker.com/whatisdocker/) 是輕量級容器引擎，與虛擬機器在某些方面類似，您可以用它來裝載應用程式和服務。Visual Studio 在 Ubuntu、CoreOS 和 Windows 上支援 Docker。

此範例將示範如何使用 **Visual Studio 2015 Tools for Docker** 延伸模組將 ASP.NET 5 應用程式發佈到 Azure 上的 Ubuntu Linux 虛擬機器 (在本文為 Docker 主機)。同樣的使用體驗可以用來發佈至 Windows 容器。

發佈您的應用程式到 Docker 主機之後，您可以使用 Docker 命令列工具來與發佈您的應用程式的目標容器互動。

## 建立及發佈新的 Docker 容器

在下節中，您將建立新的 ASP.NET 5 Web 應用程式專案、建立容器主機，然後建置和執行 Docker 容器中的 Web 應用程式專案。若要開始，請下載並安裝 [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)。

### 加入 ASP.NET 5 Web 應用程式專案

1. 從 Visual Studio 功能表中，選取 [檔案] > [新增] > [專案]。 

1. 在 [新增專案] 對話方塊的 [範本] 區段下，選取 [Visual C#] > [Web]。

1. 選取 [ASP.NET Web 應用程式]，並命名您的新專案。(本文章中的螢幕擷取畫面使用預設名稱 **WebApplication1**)。

1. 點選 [確定]。

1. 由於 Web 應用程式會在 Docker 中裝載/執行，如果 [雲端中的主機] check 核取方塊已勾選，請將它清除並點選 [確定]。

  ![][0]

  這時候您通常會將程式碼新增至 Web 應用程式，讓它進行一些有助益的事。例如，我們想把事情簡單化以便專注於 Docker。(請注意，您也可以選擇使用現有的 ASP.NET 5 Web 應用程式。)

### 發佈專案
一旦專案建立 - 或者您開啟現有的專案 - 下列步驟將引導您完成將專案發佈至 Azure 中的 Docker 容器。

1. 從 [Visual Studio 方案總管] 中，以滑鼠右鍵按一下專案，然後從專案的內容功能表選取 [發行]。

1. 在 [發行 Web] 對話方塊的 [選取發行目標] 區段中，點選 [Docker 容器]。

    如果您沒有看到 [Docker 容器] 選項，請確定您已安裝 Visual Studio 2015 Tools for Docker，並且您在上一節中已選取 ASP.NET 5 網站範本。

    ![][1]

    [選取 Docker 虛擬機器] 對話方塊可讓您指定您要發行專案的 Docker 主機。您可以建立新的 Docker 主機或選擇裝載在 Azure 上或其他位置的現有的 VM。稍後在這個範例中，我們要建立新的 Azure Docker 主機。

1. 如果您已登入 Azure 帳戶，請跳至步驟 5。如果您未登入帳戶，請點選 [新增帳戶]。

    ![][2]

1. 在 [登入 Visual Studio] 對話方塊中，輸入您的 Azure 訂用帳戶的電子郵件帳戶，然後點選 [繼續]。

1. 點選 [新增] 來建立新的 Azure Docker VM，然後點選 [確定]。

    ![][3]

    請注意，您也可以選擇使用現有的 Docker 主機。若要這樣做，請在 [現有 Azure Docker 虛擬機器] 下拉式清單選取它，而不是點選 [新增]。請注意，清單並不只限於容器主機；會列出所有 Azure 租用戶中的 VM。

    或者，您可以選擇發佈到自訂的 Docker 主機。如需詳細資訊，請參閱本主題稍後的[提供自訂 Docker 主機](#provide-a-custom-docker-host)一節。

1. 於 [在 Microsoft Azure 上建立虛擬機器] 對話方塊中輸入下列資訊。當您完成時，請點選 [確定]。這會建立設定了 Docker 延伸模組的 Linux 虛擬機器。

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
	|憑證目錄 |這會指定儲存 Docker 憑證的目錄。雖然您可以建立新的目錄，或指向現有的目錄，但建議您使用預設憑證目錄 (C:\\Users\\[*username*]\\.docker)。否則，如果您在另一個專案或系統上重複使用相同的主機，將無法自動擷取 [驗證] 選項。|

1. 點選 [憑證目錄] 項目旁邊的省略符號 (...)，然後針對 Docker 憑證建立新目錄，或瀏覽至現有的 Docker 憑證目錄。

    如果找不到 VM 所需的 Docker 憑證，在項目旁會出現驚嘆號圖示，讓您知道找不到所需的憑證，並且繼續將會刪除然後重新建立任何現有的憑證。

1. 點選 [確定]，開始建立 VM。您會收到訊息，說明正在 Azure 中建立虛擬機器。

    Visual Studio 會建立 Azure 資源管理員 (ARM) 範本檔案、參數檔案和 PowerShell 指令碼，讓您在未來可以再次執行命令。

    ![][7]

    Visual Studio 會將作業的進度輸出到 [輸出] 視窗。Visual Studio 呼叫 PowerShell 指令碼來部署 VM。指令碼會使用 Azure PowerShell 指令程式來部署 Azure 資源群組。之後，另一個 PowerShell 指令碼會使用發出 Docker 命令來發佈，就好像您是手動建立 Docker 主機。

    佈建 Docker 主機可能需要一些時間，因此請檢查 [Visual Studio 輸出] 視窗中的狀態，以得知作業何時完成。

1. Docker 主機完全在 Azure 中佈建之後，您可以在 Azure 入口網站上檢查您的帳戶。您應該可以在 Azure 入口網站上的 [虛擬機器] 類別目錄下看到新的虛擬機器。

1. 一旦 Docker 主機準備就緒時，請返回並發佈 Web 應用程式專案。在 [方案總管] 中 Web 應用程式專案節點的內容功能表中上，點選 [發行]。Visual Studio 會根據您所建立的 VM 建立發佈檔案。

1. 在 [發行 Web] 對話方塊的 [連線] 索引標籤上，點選 [驗證連線] 確定 Docker 主機已就緒。如果連線狀況良好，請點選 [發行] 來發行 Web 應用程式。

    第一次將應用程式發佈至 Docker 主機時，需要一些時間才能下載 Docker 檔案中所參考的任何基礎映像 (例如 **FROM** *imagename*)。

    請注意，Docker 檔案是作業系統特定的檔案。如果您選擇重新發佈至不同的作業系統，您必須重新命名 Docker 檔案，使得 Visual Studio 能夠根據目標作業系統建立新預設值。例如，如果您先發佈至 Linux 容器並稍後決定發佈到 Windows，您應該將 Docker 檔案重新命名有意義但唯一的名稱，例如 DockerLinux。然後，當您重新發佈至 Windows 時，Visual Studio 將會為 Windows 重新建立預設的 Docker 檔案。稍後，當您重新發佈其中任一個時，只需選取作業系統適用的適當 Docker 檔案。

## 提供自訂 Docker 主機

在上一節中，您會看到如何建立裝載於 Azure 的 Docker 虛擬機器。不過，如果您在其他地方已經有現有的 Docker 主機，可以選擇發佈到該主機，而不是 Azure。

### 如何提供自訂的 Docker 主機

1. 在 [選取 Docker 虛擬機器] 對話方塊中，選取[自訂 Docker 主機] 核取方塊。

    ![][5]

1. 點選 [確定]。

1. 在 [發行 Web] 對話方塊方塊中，將值加入到 **CustomDockerHost** 區段中的設定，例如：伺服器 URL、映像名稱、Docker 檔案位置和主機與容器連接埠號碼。

    在 [Docker 進階選項] 區段中，您可以檢視或變更 [驗證] 和 [執行] 選項，以及 Docker 命令列。

    ![][6]

1. 輸入所有必要的值之後，點選 [驗證連接] 以確保與 Docker 主機的連接會正常運作。

1. 如果連線運作正常，您可以點選 [下一步] 查看將會發行的元件清單，或者您可以點選 [發行] 立即發行專案。

## 測試 Docker 主機

一旦專案已經發佈到 Azure 上的 Docker 主機，您可以藉由檢查它的設定來進行測試。因為 Docker 命令列工具會隨著 Visual Studio 延伸模組安裝，您可以直接從 Windows 命令提示字元發出命令至 Docker。

下列程序用於與已部署至 Azure 的 Docker 主機通訊。

### 如何測試 Docker 主機

1. 開啟 Windows 命令提示字元。

1. 指派 Docker 主機並對環境變數驗證。若要這麼做，請在命令提示字元中輸入下列命令。(將 *NameofAzureVM* 替代為您的 Docker 主機名稱，並將 *Region* 替代為建立它的區域。)

    ```
    Set DOCKER_HOST=tcp://[NameOfAzureVM].[Region].cloudapp.azure.com:2376
    Set DOCKER_TLS_VERIFY=1
    ```

    設定這些環境變數，可讓您不必手動針對每個您所發出的命令指定此資訊。

1. 現在您可以發出類似下列命令，來測試 Docker 主機是否已存在且正常運作。

	|命令列|說明|
	|---|---|
	|`docker info`|取得 Docker 版本資訊。|
	|`docker ps`|取得執行中容器的清單。|
	|`docker ps –a`|取得清單的容器，包括停止的容器。|
	|`docker logs <Docker container name>`|取得指定的容器的記錄檔。|
	|`docker images`|取得映像的清單。|

    如需 Docker 命令的完整清單，請在命令提示字元中輸入 `docker` 命令，並按 **&lt;Enter>** 鍵。如需詳細資訊，請參閱 [Docker 命令列](https://docs.docker.com/reference/commandline/cli/)文件。

## 後續步驟

既然已具備 Docker 主機，您可以發出 Docker 命令給它。若要深入了解 Docker，請參閱 [Docker 文件](https://docs.docker.com/)和 [Docker 線上教學課程](https://www.docker.com/tryit/)。

若要了解如何在 Azure 上使用 Linux 的 Docker VM 擴充程式，請參閱 [Azure 上 Linux 的 Docker 虛擬機器擴充程式](virtual-machines/virtual-machines-linux-dockerextension.md)。

若是與在 Visual Studio 中使用 Docker 的相關問題，請參閱[使用 Visual Studio 疑難排解 Windows 上的 Docker 用戶端錯誤](vs-azure-tools-docker-troubleshooting-docker-errors.md)。

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796678.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796679.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796680.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796681.png
[4]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796682.png
[5]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796683.png
[6]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796684.png
[7]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796685.png
[8]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/IC796686.png

<!---HONumber=AcomDC_0323_2016-->