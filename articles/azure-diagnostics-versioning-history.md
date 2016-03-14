<properties
	pageTitle="Azure 診斷版本歷程記錄"
	description="說明不同 Microsoft Azure SDK 版本隨附的各 Azure 診斷版本變更。"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="robb"/>


# Microsoft Azure 診斷版本歷程記錄

Azure 診斷的新手嗎？ 請參閱 [Azure 診斷概觀](azure-diagnostics.md)。

每個 Azure SDK 版本通常會隨附新版的 Azure 診斷。下表說明與 SDK 版本相關的 Azure SDK 和 Azure 診斷版本。



Azure SDK 版本 | Azure 診斷版本 | Model
--- | --- | ---
1\.x | 1\.0 | 外掛程式
2\.0 至 2.4| 1\.0 | "
2\.5 | 1\.2 | 擴充功能
2\.6 | 1\.3 | "
2\.7 | 1\.4 | "
2\.8 | 1\.5 | "


最新版本 1.5 隨附於 Azure SDK 2.8。SDK 隨附的 Azure 診斷擴充功能版本僅用於本機模擬器案例。任何已部署的應用程式在 Azure 中執行時，都會自動使用最新的版本，無論應用程式建置的 SDK 版本為何。不過，除非您安裝了最新的 Azure SDK，否則仍無法擁有所有協助您使用新功能的工具。

各種功能和變更如下所述。

## Azure SDK 2.8
Azure SDK 2.8 加入傳送診斷資料至 [Application Insights](./application-insights/app-insights-cloudservices.md) 的功能，因此可更容易在應用程式以及系統和基礎結構層級中診斷問題。

## Azure 2.6 診斷變更

對 Visual Studio 中的 Azure SDK 2.6 雲端服務專案進行下列變更。(這些變更也套用至更新版的 Azure SDK)。

- 本機模擬器現在支援診斷。這表示您可以收集診斷資料並確保您在 Visual Studio 中進行開發及測試時，您的應用程式在建立正確的追蹤。當您在 Visual Studio 中使用 Azure 儲存體模擬器來執行您的雲端服務專案時，連接字串 `UseDevelopmentStorage=true` 會啟用診斷資料收集。所有的診斷資料都會收集到 (開發儲存體) 儲存體帳戶中。

- 診斷儲存體帳戶連接字串 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) 會再一次儲存在服務組態 (.cscfg) 檔中。在 Azure SDK 2.5 中，診斷儲存體帳戶會在 diagnostics.wadcfgx 檔案中指定。

連接字串在 Azure SDK 2.4 及更舊版本和 Azure SDK 2.6 及更新版本中的運作方式有一些顯著的差異。

- 在 Azure SDK 2.4 及更舊版本中，階段診斷外掛程式使用連接字串做為執行階段，以取得用於傳輸診斷記錄檔的儲存體帳戶資訊。

- 在 Azure SDK 2.6 及更新版本中，Visual studio 會使用診斷連接字串，在發佈期間設定內含適當儲存體帳戶資訊的診斷延伸模組。連接字串可讓您為 Visual Studio 在發佈時將使用的不同服務組態定義不同的儲存體帳戶。不過，因為診斷外掛程式 (在 Azure SDK 2.5 之後) 不再提供使用，所以 .cscfg 檔案本身無法啟用診斷延伸模組。您必須個別透過 Visual Studio 或 PowerShell 等工具啟用延伸模組。

- 為了使用 PowerShell 簡化診斷延伸模組的設定程序，從 Visual Studio 的封裝輸出也包含每個角色之診斷延伸模組的公用組態 XML。Visual Studio 使用診斷連接字串填入出現在公用組態的儲存體帳戶資訊。公用設定檔會在延伸模組資料夾中建立並遵循模式 PaaSDiagnostics.<RoleName>.PubConfig.xml。任何以 PowerShell 為基礎的部署都可以使用此模式將每個組態對應至角色。

- Azure 入口網站也會使用 .cscfg 檔案中的連接字串來存取診斷資料，所以它也可以出現在 [監視] 索引標籤中。若要在入口網站中顯示詳細監視資料，必須要有連接字串。

### 將專案移轉至 Azure SDK 2.6 及、更新版本

從 Azure SDK 2.5 移轉至 Azure SDK 2.6 或更新版本時，如果您在 .wadcfgx 檔案中指定診斷儲存體帳戶，它就會留在那裡。若要針對不同儲存體組態充分利用不同儲存體帳戶的靈活性，您必須手動將連接字串加入專案。如果您將專案從 Azure SDK 2.4 或更早版本移轉至 Azure SDK 2.6，系統會保留診斷連接字串。不過，請注意上一節中指定之 Azure SDK 2.6 中連接字串處理方式的變更。

### Visual Studio 如何決定診斷儲存體帳戶

- 如果在 .cscfg 檔案中指定診斷連接字串，Visual Studio 會在發佈時，以及在封裝期間產生公用組態 xml 檔案時使用它來設定診斷延伸模組。

- 如果未在 .cscfg 檔案中指定診斷連接字串，Visual Studio 會回復到在發佈時，以及在封裝期間產生公用組態 xml 檔案時使用在 .wadcfgx 檔案中指定的儲存體帳戶來設定診斷延伸模組。

- 在 .cscfg 檔案中的診斷連接字串的優先順序高於 .wadcfgx 檔案中的儲存體帳戶。如果在 .cscfg 檔案中指定診斷連接字串，Visual Studio 會使用它並忽略 .wadcfgx 中的儲存體帳戶。

### 「更新開發儲存體連接字串...」核取方塊的作用為何？

[在發佈至 Microsoft Azure 時使用 Microsoft Azure 儲存體帳戶認證更新診斷和快取的開發儲存體連接字串] 核取方塊提供便利的方式，使用發佈期間指定的 Azure 儲存體帳戶更新任何開發儲存體帳戶連接字串。

例如，假設您選取此核取方塊，診斷連接字串就會指定 `UseDevelopmentStorage=true`。當您將專案發佈至 Azure 時，Visual Studio 會自動使用您在 [發佈] 精靈中指定的儲存體帳戶更新診斷連接字串。不過，如果將實際的儲存體帳戶指定為診斷連接字串，則會改用該帳戶。

## Azure SDK 2.4 及更舊版本和 Azure SDK 2.5 及更新版本之間的診斷功能差異

如果您要將專案從 Azure SDK 2.4 更新為 Azure SDK 2.5 或更新版本，您應該謹記下列診斷功能差異。

- **組態 API 已被取代** – 診斷的程式設計組態可在 Azure SDK 2.4 或更舊版本中使用，但在 Azure SDK 2.5 及更新版本中已被取代。如果診斷組態目前以程式碼定義，您將需要在移轉專案中從頭開始進行這些設定才能讓診斷保持運作。Azure SDK 2.4 的診斷組態檔是 diagnostics.wadcfg，而 diagnostics.wadcfgx 是 Azure SDK 2.5 及更新版本的診斷組態檔。

- **雲端服務應用程式的診斷只能在角色層級設定，而不是在執行個體層級。**

- **每次部署您的應用程式時，都會更新診斷組態** – 如果您從 [伺服器總管] 變更診斷組態並重新部署您的應用程式，會導致同位檢查的問題。

- **在 Azure SDK 2.5 及更新版本中，損毀傾印不會以診斷組態檔設定** – 如果您以程式碼設定損毀傾印，您必須手動將組態從程式碼傳輸至組態檔中，因為損毀傾印不會在移轉至 Azure SDK 2.6 期間傳輸。

<!---HONumber=AcomDC_0302_2016-->