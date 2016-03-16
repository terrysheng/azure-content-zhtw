<properties
   pageTitle="使用 Application Insights 疑難排解雲端服務 | Microsoft Azure"
   description="了解如何使用 Application Insights 疑難排解雲端服務問題，以處理 Azure 診斷的資料。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# 使用 Application Insights 疑難排解雲端服務

使用 [Azure SDK 2.8](https://azure.microsoft.com/downloads/) 和 Azure 診斷延伸模組 1.5，您現在可以將雲端服務的 Azure 診斷資料直接傳送至 Application Insights。Azure 診斷收集的各種記錄檔 (包括應用程式記錄檔、Windows 事件記錄檔、ETW 記錄檔和效能計數器) 現在可以傳送至 Application Insights，並在 Application Insights 入口網站 UI 中視覺化。搭配 Application Insights SDK 使用時，您現在可以深入了解來自您的應用程式和系統的計量和記錄檔，以及來自 Azure 診斷的基礎結構層級資料。
  
## 設定 Azure 診斷將資料傳送至 Application Insights

請遵循下列步驟來設定雲端服務專案將 Azure 診斷資料傳送至 Application Insights。

1) 在 Visual Studio [方案總管] 中，以滑鼠右鍵按一下角色，然後選取 [屬性] 以開啟角色設計工具。
	
![方案總管角色屬性][1]

2) 在角色設計工具的 [診斷] 區段下方，選取 [傳送診斷資料至 Application Insights] 核取方塊。

![角色設計工具會將診斷資料傳送至 Application Insights][2]

3) 在快顯的對話方塊中選取您想要傳送 Azure 診斷資料給它的 Application Insights 資源。對話方塊可讓您從您的訂用帳戶中選取現有的 Application Insights 資源，或是為 Application Insights 資源手動指定檢測金鑰。如果您沒有現有的 Application Insights 資源，可以透過按一下 [建立新的資源] 連結建立一個，這樣會開啟連結至 Azure 傳統入口網站的瀏覽器視窗，在該處可以建立 Application Insights 資源。如需建立 Application Insights 資源的詳細資訊，請參閱[建立新的 Application Insights 資源](app-insights-create-new-resource.md)。

![選取 Application Insights 資源][3]

4) 加入 Application Insights 資源後，該資源的檢測金鑰會儲存為名稱是 **APPINSIGHTS\_INSTRUMENTATIONKEY** 的服務組態設定。您可以藉由從服務組態下拉式清單中選取不同的組態，並為該組態指定一個新的檢測金鑰，以變更每個服務組態或環境的這項組態設定。

![選取服務組態][4]
	
在發佈期間，Visual studio 使用 **APPINSIGHTS\_INSTRUMENTATIONKEY** 組態設定來設定診斷延伸模組的適當 Application Insights 資源資訊。組態設定是為不同的服務組態定義不同檢測金鑰的便利方式。發佈時，Visual Studio 會轉譯該設定，並將它插入診斷延伸模組公用組態。為簡化使用 PowerShell 設定診斷延伸模組的程序，Visual Studio 中的封裝輸出也包含了公用組態 XML，並且內含適當的 Application Insights 檢測金鑰。公用組態檔會建立在延伸模組資料夾中，並遵循 PaaSDiagnostics.<RoleName>.PubConfig.xml 的模式。任何以 PowerShell 為基礎的部署都可以使用此模式將每個組態對應至角色。

5) 啟用 [傳送診斷資料至 Application Insights] 會自動設定 Azure 診斷將 Azure 診斷代理程式收集的所有效能計數器和錯誤層級記錄檔傳送至 Application Insights。如果您想進一步設定哪些資料要傳送至 Application Insights，您必須手動編輯每個角色的 *diagnostics.wadcfgx* 檔案。若要深入了解手動更新組，請參閱[設定 Azure 診斷以將資料傳送至 Application Insights](azure-diagnostics-configure-applicationinsights.md)。

雲端服務設定為傳送 Azure 診斷資料到 Application Insights 後，您就可以像平常一樣將它部署至 Azure，確定 Azure 診斷延伸模組已啟用。請參閱[使用 Visual Studio 發佈雲端服務](vs-azure-tools-publishing-a-cloud-service.md)。

## 在 Application Insights 中檢視 Azure 診斷資料
Azure 診斷遙測會顯示在為您的雲端服務設定的 Application Insights 資源中。

以下說明各種 Azure 診斷記錄檔類型如何對應 Application Insights 概念：

-  效能計數器在 Application Insights 中會顯示為自訂計量
-  Windows 事件記錄檔在 Application Insights 中會顯示為追蹤和自訂事件
-  應用程式記錄檔、ETW 記錄檔和任何診斷基礎結構記錄檔在 Application Insights 中會顯示為追蹤。

若要在 Application Insights 中檢視 Azure 診斷資料：

- 使用[計量瀏覽器](../application-insights/app-insights-metrics-explorer.md)以視覺化方式檢視任何自訂的效能計數器，或是不同類型的 Windows 事件記錄檔事件的計數。

![[計量瀏覽器] 中的自訂計量][5]

- 使用[搜尋](../application-insights/app-insights-diagnostic-search.md)在各種 Azure 診斷傳送的追蹤記錄檔中搜尋。例如，如果您的角色中有未處理的例外狀況造成該角色當機和回收，該資訊會顯示在 [*Windows 事件記錄檔*] 的 [*應用程式*] 通道。您可以使用搜尋功能來查看 Windows 事件記錄檔錯誤並取得例外狀況的完整堆疊追蹤，讓您尋找問題的根本原因。 

![搜尋追蹤][6]

## 後續步驟

- [將 Application Insights SDK 加入您的雲端服務](../application-insights/app-insights-cloudservices.md)，從您的應用程式傳送有關要求、例外狀況、相依性及任何自訂遙測的資料。與 Azure 診斷資料結合，您可以在相同的 Application Insight 資源中取得應用程式和系統的完整檢視。  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_1217_2015-->