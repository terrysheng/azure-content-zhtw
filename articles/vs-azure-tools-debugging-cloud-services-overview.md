<properties 
   pageTitle="偵錯 Azure 雲端服務 | Microsoft Azure"
   description="偵錯 Azure 雲端服務"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/14/2015"
   ms.author="patshea" />

# 偵錯雲端服務

您可以使用 Azure Tools for Microsoft Visual Studio 和 Azure SDK，透過不同的方法來進行 Azure 應用程式偵錯：

- 您可以在開發時從 Visual Studio 進行 Azure 應用程式偵錯，就如同任何 Visual C# 或 Visual Basic 應用程式一樣。如需詳細資訊，請參閱[在您的本機電腦上偵錯您的雲端服務](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)。

- 不論角色是在開發環境中或在 Azure 中執行，您都可以使用 Azure 診斷來記錄在角色內執行的程式碼中的詳細資訊。如需詳細資訊，請參閱[使用 Azure 診斷來收集記錄資料](http://go.microsoft.com/fwlink/p/?LinkId=400450)。

- 如果您使用 Visual Studio Enterprise 來撰寫適用於 .NET Framework 4 或 .NET Framework 4.5 的角色，您可以在從 Visual Studio 部署 Azure 雲端服務時啟用 IntelliTrace。IntelliTrace 提供您可搭配 Visual Studio 使用於偵錯應用程式 (如同在 Azure 中執行) 的記錄檔。如需詳細資訊，請參閱[使用 IntelliTrace 和 Visual Studio 進行已發佈的雲端服務偵錯](http://go.microsoft.com/fwlink/p/?LinkId=623016)。

- 您可以在從 Visual Studio 部署雲端服務時，啟用雲端服務的遠端偵錯。如果您選擇對部署啟用遠端偵錯，則會在執行每個角色執行個體的虛擬機器上安裝遠端偵錯服務。這些服務 (例如 msvsmon.exe) 不會影響效能或造成額外成本。如需詳細資訊，請參閱[在 Azure 中偵錯雲端服務](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)。

<!---HONumber=Oct15_HO3-->