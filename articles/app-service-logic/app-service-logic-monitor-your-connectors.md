<properties 
	pageTitle="在 App Service 中管理和監視連接器和 API Apps | Microsoft Azure" 
	description="在 Azure App Service 中檢視連接器和 API 應用程式的效能；microservices 架構" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="mandia"/>

# 管理和監視內建 API 應用程式和連接器

您已建立一個內建 API 應用程式。接下來呢？

在 Azure 中，每個 API 應用程式都是裝載在 Azure 上的個別網站。因此，您可以輕鬆地查看提出多少要求，以及查看連接器使用多少資料。您也可以備份 API 應用程式、建立警示、啟用 Tinfoil 安全性，以及加入使用者和角色。

本主題說明一些管理 API 應用程式的不同選項。

若要查看這些內建功能，請在 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)中開啟 API 應用程式。如果 API 應用程式是在儀表板上，則請選取它以開啟屬性。您也可以依序選取 [瀏覽]、[API Apps] 和您的 API 應用程式：

![][browse]

## 查看您所輸入的內容

當您開啟 API 應用程式時，有幾個功能和工作可用：

![][settings]

您可以：

- **設定**：顯示 API 應用程式的特定資訊 (包括您的訂用帳戶詳細資料)，並列出可存取您 API 應用程式的使用者。您也可以使用其他功能中的 [調整規模] 功能來增加或減少 API 應用程式執行個體數目。
- 使用 [開始] 和 [停止] 按鈕控制 API 應用程式。
- 對 API 應用程式所使用的基礎檔案進行產品更新時，您可以按一下 [更新] 取得最新版本。例如，如果有 Microsoft 所發行的修正程式或安全性更新，則按一下 [更新] 會自動更新 API 應用程式以納入此修正程式。 
- 根據 API 應用程式的資料使用狀況，選取 [變更方案] 進行升級或降級。您也可以使用這項功能查看資料使用狀況。
- 當您建立具有資料表的連接器 (例如 SQL 連接器) 時，可以選擇性地輸入要連接的資料表名稱。會自動建立以資料表為基礎的結構描述，按一下 [下載結構描述] 即可使用。您之後可以使用這個下載的結構描述來建立轉換或對應。 

## 變更連接器或您所輸入的 API 組態值

設定或建立您所建置的連接器之後，即可變更您輸入的值。例如，如果您已設定 SQL 連接器，並且想要變更 SQL Server 名稱或資料表名稱，則可以在您連接器的 API 應用程式刀鋒視窗中進行。

步驟包括：

1. 開啟連接器或 API 應用程式。這麼做之後，[API 應用程式] 刀鋒視窗隨即開啟。
2. 在 [程式集] 中，按一下主機屬性下的超連結。超連結的命名類似 *slackconnector* 或 *microsoftsqlconnector123*：

	![][apiapphost]

3. 在 [API 應用程式主機] 刀鋒視窗中，選取 [設定]。在 [設定] 刀鋒視窗中，選取 [應用程式設定]。您的組態值會列在 [應用程式設定] 下：
	
	![][hostsettings]

4. 按一下您要變更的設定，並輸入新值，然後 [儲存] 您的變更。


## 安裝混合式連線管理員 - 選擇性

![][hcsetup]

Hybrid Connection Manager 可讓您連線至內部部署系統 (例如 SQL Server 或 SAP)。此混合式連線使用 Azure 服務匯流排連線，並控制 Azure 資源與內部部署資源之間的安全性。

請參閱[在 Azure App Service 中使用混合連線管理員](app-service-logic-hybrid-connection-manager.md)。

> [AZURE.NOTE]只有當您連接到防火牆後方的內部部署資源時，才需要混合式連線管理員。如果您未連接至內部部署系統，則混合連線管理員可能不會列在連接器刀鋒視窗中。

## 監視效能
效能度量是內建功能，並隨附於您所建立的每個 API 應用程式。這些度量專屬於 Azure 中所裝載的 API 應用程式。範例度量：

![][monitoring]

您可以：

- 選取 [**要求和錯誤**]，加入包括已知 HTTP 錯誤代碼的不同效能度量 (例如 200、400 或 500 HTTP 狀態碼)。您也可以查看回應時間、對 API 應用程式提出多少要求，以及有多少輸入資料和輸出資料。根據效能度量，如果度量超出您選擇的臨界值，則可以建立電子郵件警示。 
- 在 [使用情況] 中，您可以查看 API 應用程式使用多少 [CPU]、檢閱目前 [使用量配額] (MB)，以及查看根據成本層而定的最大資料使用量。[預估的花費] 可協助您判斷執行 API 應用程式的潛在成本。
- 選取 [**程序**] 開啟程序總管。這會顯示 Web 執行個體和其屬性 (包括執行緒計數和記憶體使用量)。

使用這些工具，您可以根據業務需求來判斷應該調升或調降 App Service 方案的規模。這些都是入口網站的內建功能，並不需要額外工具。

## 控制安全性

API 應用程式使用以角色為基礎的安全性。這些角色會套用至整個 Azure 經驗 (包括 API 應用程式和其他 Azure 資源)。角色包括：

角色 | 說明
--- | ---
擁有者 | 可以完整存取管理經驗，也可以將存取權授與其他使用者或群組。
參與者 | 可以完整存取管理經驗。無法將存取權授與其他使用者或群組。
讀取者 | 可以檢視密碼以外的所有資源。
使用者存取系統管理員 | 可以檢視所有資源、建立/管理角色，以及建立/管理支援票證。

請參閱 [Microsoft Azure 入口網站中以角色為基礎的存取控制](role-based-access-control-configure.md)。

您可以輕鬆地加入使用者，並將 API 應用程式的特定角色指派給他們。入口網站會顯示具有存取權的使用者和其獲指派角色：

![][access]

- 選取 [使用者] 加入使用者、指派角色，以及移除使用者。
- 選取 [角色] 以查看特定角色的所有使用者、將使用者加入角色，以及從角色中移除使用者。 


## 其他不錯的工具
- 選取 [API 定義]，開啟針對特定 API 應用程式自動建立的 Swagger 檔案。
- 選取 [相依性] 以檢視您 API 應用程式所需的檔案。例如，如果您使用 SAP 連接器，則會在內部部署混合式連線管理員上安裝一些額外檔案。這些相依性顯示於 [API 應用程式] 刀鋒視窗中。 

> [AZURE.IMPORTANT]如果您開啟 API 應用程式屬性，並查看 [程式集] 下方，則會有可開啟新刀鋒視窗的 [主機] 和 [閘道] 連結：
> 
> ![][host]
> 
> 這些屬性專屬於裝載您 API 應用程式的網站。使用內建 API 應用程式或連接器時，這些屬性大部分並未實際套用，因此建議您不要更新這些屬性。如果您已在 Visual Studio 中建立專屬 API 應用程式，並將其部署至 Azure 訂用帳戶，則可以使用 [主機] 和 [閘道] 刀鋒視窗。[管理 API 應用程式](../app-service-api/app-service-api-manage-in-portal.md)提供的詳細資訊，讓您了解可利用自訂建立之 API 應用程式，進而在這些刀鋒視窗中進行何種作業。


>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure Logic Apps，請移至[試用 Logic App](https://tryappservice.azure.com/?appservice=logic)，即可在 App Service 中立即建立短期入門邏輯應用程式。不需要信用卡，無需承諾。

## 閱讀更多資訊

[監視邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)<br/> [App Service 中的連接器和 API 應用程式清單](app-service-logic-connectors-list.md)<br/> [Microsoft Azure 入口網站中以角色為基礎的存取控制](role-based-access-control-configure.md)<br/>[在 Azure App Service 中使用混合連線管理員](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!-------HONumber=AcomDC_1210_2015--->