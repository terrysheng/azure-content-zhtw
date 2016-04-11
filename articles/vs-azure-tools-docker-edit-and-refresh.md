<properties
   pageTitle="使用編輯及重新整理來偵錯本機 Docker 容器中的應用程式 | Microsoft Azure"
   description="了解如何透過編輯及重新整理功能，來修改在本機 Docker 容器中執行的應用程式，以及重新整理容器"
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
   ms.date="03/25/2016"
   ms.author="tarcher" />

# 使用編輯及重新整理來偵錯本機 Docker 容器中的應用程式

## 概觀
Visual Studio Tools for Docker 提供便利的方式來讓您在本機 Docker 容器中開發及測試自己的應用程式，且不需要在每次變更程式碼之後重新啟動容器。本文章將說明如何使用「編輯及重新整理」功能在本機 Docker 容器中啟動 ASP.NET 5 Web 應用程式、進行任何必要的變更，然後重新整理瀏覽器來查看這些變更。

## 先決條件
您的電腦必須安裝下列工具。

- [Visual Studio 2015 Update 1](https://go.microsoft.com/fwlink/?LinkId=691979)
- [Microsoft ASP .NET 及 Web 工具 2015 RC](https://go.microsoft.com/fwlink/?LinkId=627627)
- [Docker 工具箱](https://www.docker.com/products/overview#/docker_toolbox)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)
- [設定 Docker 用戶端](./vs-azure-tools-docker-setup.md)

> [AZURE.NOTE] 如果電腦已經安裝 Visual Studio 2015 Tools for Docker 之前的版本，您必須先從控制台將之前的版本解除安裝，再安裝最新的版本。

## 編輯在本機 Docker 容器中執行的應用程式
Visual Studio 2015 Tools for Docker 可讓 ASP.NET 5 Web 應用程式開發人員在 Docker 容器中測試及執行自己的應用程式、在 Visual Studio 應用程式中變更應用程式，以及重新整理瀏覽器來查看已套用至容器中執行之應用程式的變更。

1. 從 Visual Studio 功能表中，選取 [檔案] > [新增] > [專案]。 

1. 在 [新增專案] 對話方塊的 [範本] 區段下，選取 [Visual C#] > [Web]。

1. 選取 [ASP.NET Web 應用程式]。

1. 指定新應用程式的名稱 (或使用預設值)。

1. 點選 [確定]。

1. 在 [ASP.NET 5 範本] 下，選取 [ASP.NET Web 應用程式]。

1. 點選 [確定]。

1. 在 [Visual Studio 方案總管] 中，用滑鼠右鍵按一下專案，然後選取 [新增] > [Docker 支援]。

	![][0]
 
1. 專案節點下方隨即會建立下列檔案：

	![][1]

1. 將方案組態設定為 `Debug`，然後按下 &lt;F5> 鍵來開始在本機測試應用程式。

1. 當容器映像已建置，且正在 Docker 容器中執行時，PowerShell 主控台會嘗試在您的預設瀏覽器中啟動 Web 應用程式。如果您是使用 Microsoft Edge 瀏覽器，請參閱[疑難排解](#troubleshooting)一節。

1. 返回 Visual Studio，然後開啟 `Views\Home\Index.cshtml`。

1. 把下列 HTML 內容附加至檔案尾端，然後儲存變更

		<div>
			<h1>Hello from Docker Container!</h1>
		</div>

1.	切換回瀏覽器，然後重新整理瀏覽器。

1.	捲動到首頁底部，您應該會看到變更已經套用！ 請注意，網站可能需要幾秒鐘的時間來重新編譯，因此如果您沒有立刻看到變更，只要再次重新整理瀏覽器即可。

##疑難排解 

- **執行應用程式會導致 PowerShell 開啟、顯示錯誤訊息，然後關閉。瀏覽器頁面沒有開啟。**

	這可能是在 `docker-compose-up` 期間發生的錯誤。如要檢視該錯誤，請執行下列步驟：

	1. 開啟 `Properties\launchSettings.json` 檔案
	
	1. 找出 Docker 項目。
	
	1. 找出開頭為下列內容的那一行：

			"commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
	1. 新增 `-noexit` 參數，讓該行看起來類似下列內容。這會使 PowerShell 保持開啟，讓您能檢視錯誤訊息。

			"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

- **建置：無法建置映像、查看 TLS 連線時發生錯誤：主機並未執行**

	請確認預設的 Docker 主機正在執行。請參閱[設定 Docker 用戶端](./vs-azure-tools-docker-setup.md)一文。

- **找不到磁碟區對應**

	根據預設，VirtualBox 會將 `C:\Users` 當做 `c:/Users` 來共用。如果專案不在 `c:\Users` 下，請將專案手動新增到 VirtualBox 的[共用資料夾](https://www.virtualbox.org/manual/ch04.html#sharedfolders)中。

- **把 Microsoft Edge 當做預設瀏覽器**

	如果您在使用 Microsoft Edge 瀏覽器，網站可能不會開啟，原因是 Edge 認為該 IP 位址不安全。如要修正這個問題，請執行下列步驟：
	1. 從 Windows [執行] 方塊中，輸入 `Internet Options`。
	2. 當 [網際網路選項] 出現時，點選該選項。 
	2. 點選 [安全性] 索引標籤。
	3. 選取 [近端內部網路] 區域。
	4. 點選 [網站]。 
	5. 將您虛擬機器 (在這個案例中為 Docker 主機) 的 IP 位址新增到清單中。 
	6. 在 Edge 中重新整理網頁，此時您應該會看到網站已正常運作。 
	7. 如需有關這個問題的詳細資訊，請造訪 Scott Hanselman 的部落格文章：[Microsoft Edge can't see or open VirtualBox-hosted local web sites (Microsoft Edge 無法看到或開啟裝載在 VirtualBox 上的本機網站)](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx)。

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png

<!---HONumber=AcomDC_0330_2016-->