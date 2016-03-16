<properties
	pageTitle="在 Visual Studio 中部署虛擬機器調整集範本 | Microsoft Azure"
	description="如何使用 Visual Studio 資源群組部署，部署虛擬機器調整集。"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="guybo"/>

# 在 Visual Studio 中部署虛擬機器調整集範本
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md)。

本文說明如何使用 Visual Studio 資源群組部署，部署 Azure 虛擬機器調整集。


[Azure 虛擬機器調整集](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)是 Azure 計算資源，可以使用針對自動調整和負載平衡輕易整合的選項，部署和管理類似虛擬機器的集合。您可以使用 [Azure 資源管理員 (ARM) 範本](https://github.com/Azure/azure-quickstart-templates)佈建和部署 VM 調整集。可以使用 Azure CLI、PowerShell、REST 部署 ARM 範本，也可以直接從 Visual Studio 部署。Visual Studio 會提供一組範例範本，可以部署為 Azure 資源群組部署專案的一部分。

Azure 資源群組部署是一種方式，可以將相關 Azure 資源集群組在一起，並且在單一部署作業中發佈。您可以在以下位置深入了解：[透過 Visual Studio 建立與部署 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/)。

## 必要條件

若要開始在 Visual Studio 中部署 VM 調整集，您需要下列項目：

- Visual Studio 2013 或 2015
- Azure SDK 2.7 或 2.8

注意：這些指示假設您使用 Visual Studio 2015 與 [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)。

## 建立專案

1. 選擇 [檔案 | 新增 | 專案]，在 Visual Studio 2015 中建立新專案。

	![新增檔案][file_new]

2. 在 [Visual C# | 雲端] 底下，選擇 [Azure 資源管理員]，建立專案以部署 ARM 範本。

	![建立專案][create_project]

3.  從範本清單中，選取 Linux 或 Windows 虛擬機器調整集範本。

	![選取範本][select_Template]

4. 您的專案建立之後，您會看到 PowerShell 部署指令碼、Azure 資源管理員範本和虛擬機器調整集的參數檔案。

	![Solution Explorer][solution_explorer]

## 自訂您的專案

現在您可以編輯範本，以針對您的應用程式需求自訂，例如新增 VM 延伸模組屬性或編輯負載平衡規則。根據預設，會設定 VM 調整集範本以部署 AzureDiagnostics 延伸模組，讓新增自動調整規則更容易。它也會使用公用 IP 位址部署負載平衡器，使用輸入 NAT 規則設定，讓您以 SSH (Linux) 或 RDP (Windows) 連接至 VM 執行個體 – 前端連接埠範圍從 50000 開始，這表示在 Linux 的情況下，如果您的 SSH 連接至公用 IP 位址的連接埠 50000 (或網域名稱)，您會被路由至調整集中第一個 VM 的連接埠 22。連接至通訊埠 50001 將會路由至第二個 VM 的連接埠 22，依此類推。

 使用 Visual Studio 編輯您的範本的好方法是使用 JSON 大綱來組織參數、變數和資源。了解結構描述 Visual Studio 可以在部署範本之前在其中指出錯誤。

![JSON 總管][json_explorer]

## 部署專案

6. 將 ARM 範本部署至 Azure 以建立 VM 調整集資源。以滑鼠右鍵按一下專案節點，然後選擇 [部署 | 新增部署]。

	![部署範本][5deploy_Template]

7. 在 [部署到資源群組] 對話方塊中選取您的訂用帳戶。

	![部署範本][6deploy_Template]

8. 您也可以從這裡建立新的 Azure 資源群組以部署您的範本。

	![新增資源群組][new_resource]

9. 接下來，選取 [編輯參數] 按鈕以輸入參數，這些參數會傳遞至您的範本。需要特定值 (例如，OS 的使用者名稱和密碼) 才能建立部署。

	![編輯參數][edit_parameters]

10. 現在，按一下 [部署]。[輸出] 視窗會顯示部署進度。請注意，動作會執行 **Deploy-AzureResourceGroup.ps1** 指令碼。

	![輸出視窗][output_window]

## 探索 VM 調整集

部署完成之後，您可以在 Visual Studio **雲端總管** 中檢視 VM 調整集 (重新整理清單)。雲端總管可讓您在開發應用程式的同時，於 Visual Studio 中管理 Azure 資源。您也可以在 Azure 入口網站和 Azure 資源總管中檢視 VM 調整集。

![雲端總管][cloud_explorer]

 入口網站提供最佳方式，使用網頁瀏覽器以視覺化方式管理 Azure 基礎結構，而 Azure 資源總管則提供簡單的方法以探索和偵錯 Azure 資源，讓視窗成為「執行個體檢視」，並且也會顯示您要尋找的資源的 PowerShell 命令。VM 調整集在預覽時，資源總管會顯示您的 VM 調整集最詳細的資料。

## 後續步驟

一旦您透過 Visual Studio 成功部署 VM 調整集，您可以進一步自訂您的專案，以符合應用程式的需求。例如，設定自動調整，方法是新增 Insights 資源、將基礎結構新增至您的範本 (例如獨立 VM)，或是使用自訂指令碼延伸模組部署應用程式。良好的範例範本來源可以在 [Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates) GitHub 儲存機制中找到 (搜尋 "vmss")。

[file_new]: ./media/virtual-machines-vmss-vstemplates/1-FileNew.png
[create_project]: ./media/virtual-machines-vmss-vstemplates/2-CreateProject.png
[select_Template]: ./media/virtual-machines-vmss-vstemplates/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-vmss-vstemplates/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-vmss-vstemplates/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-vmss-vstemplates/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-vmss-vstemplates/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-vmss-vstemplates/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-vmss-vstemplates/8-EditParameter.png
[output_window]: ./media/virtual-machines-vmss-vstemplates/9-Output.png
[cloud_explorer]: ./media/virtual-machines-vmss-vstemplates/12-CloudExplorer.png

<!---HONumber=AcomDC_0128_2016-->