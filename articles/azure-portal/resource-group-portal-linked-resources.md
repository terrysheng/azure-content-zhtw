<properties 
	pageTitle="磚庫中相關的資源與連結的資源" 
	description="了解 Azure Preview 入口網站磚庫中的相關資源和連結資源。" 
	services="azure-portal" 
	documentationCenter="" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="adamab"/>

# 磚庫中相關的資源與連結的資源

磚庫可讓您尋找磚的特定資源，並將其拖曳到您目前的刀鋒視窗。您可以使用磚庫建立跨越資源的管理檢視。對於任何指定的資源，相關的資源會包括共用與資源相同資源群組的所有資源，以及連結到資源或從資源連結而來的任何資源。

## Azure 資源管理員中連結的資源

連結是 Azure 資源管理員的一項功能。它可讓您宣告資源之間的關聯性，即使它們不是存放在相同的資源群組。連結不會影響資源的執行階段、計費，以及以角色為基礎的存取。它只是一個可以用來表示關聯性的機制，以便如磚庫等工具可以提供豐富的管理體驗。您的工具可以使用連結 API 檢查連結，同時提供自訂的關聯性管理體驗。

## 如何連結我的資源？

當您透過入口網站或透過 Azure PowerShell 或 Azure CLI 部署範本建立資源時，就會自動為某些相依的資源建立連結。您也可以使用[連結資源 REST API](https://msdn.microsoft.com/library/azure/mt238499.aspx) 或在範本中宣告關聯性，以程式設計方式連結資源。如需使用連結資源的完整討論，請參閱[在 Azure 資源管理員中連結資源](../resource-group-link-resources.md)。

## 後續步驟

- 如果您需要撰寫 Azure 資源管理員範本的簡介，請參閱[撰寫範本](../resource-group-authoring-templates.md)。
- 若要深入了解在資源之間建立連結的詳細資料，請參閱[在 Azure 資源管理員中連結資源](../resource-group-link-resources.md)。
- 若要深入了解透過 Preview 入口網站使用資源群組，請參閱[使用 Azure Preview 入口網站管理 Azure 資源](resource-group-portal.md)。

<!---HONumber=August15_HO6-->