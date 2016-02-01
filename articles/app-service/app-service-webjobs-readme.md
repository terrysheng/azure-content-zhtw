<properties
	pageTitle="Azure App Service 中的 WebJobs"
	description="了解如何建置 WebJobs 以執行背景測試、與儲存體和服務匯流排之類的服務進行互動，以及建立排定的工作。"
	services="app-service"
	documentationCenter=""
	authors="christopheranderson"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="chrande"/>

# 在 Azure App Service 中使用 WebJobs

本主題連結至有關如何使用 Azure WebJobs 和 Azure WebJobs SDK 的文件資源。Azure WebJob 可讓您輕鬆地在 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 上以背景處理序的方式執行指令碼或程式。您可以上傳並執行可執行檔，例如 cmd、bat、exe (.NET)、ps1、sh、php、py、js 和 jar。這些程式會依照排程 (cron) 或持續當作 WebJobs 執行。

WebJobs SDK 可讓您更輕鬆地使用 Azure 儲存體。WebJobs SDK 具有繫結和觸發系統，適用於 Microsoft Azure 儲存體 Blob、佇列和資料表以及服務匯流排佇列。

使用 Visual Studio 中的整合工具可完美地建立、部署和管理 WebJobs。您可以從範本建立 WebJobs、發佈及管理 (執行/停止/監視/偵錯) 它們。

Azure 入口網站中的 WebJob 儀表板提供強大的管理功能，讓您能夠完全掌控 WebJob 的執行，包括叫用 WebJob 內個別函數的功能。儀表板也會顯示函數執行階段和記錄輸出。

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]

<!---HONumber=AcomDC_0121_2016-->