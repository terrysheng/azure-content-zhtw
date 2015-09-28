<properties
   pageTitle="在使用 Visual Studio 的 Azure 雲端服務專案中管理角色 | Microsoft Azure"
   description="了解如何使用 Visual Studio，將角色新增至 Azure 雲端服務專案，或如何從 Azure 雲端服務專案將現有角色移除。"
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
   ms.date="09/08/2015"
   ms.author="kempb" />

# 在使用 Visual Studio 的 Azure 雲端服務專案中管理角色

建立您的 Azure 雲端服務專案之後，您可以在專案中新增角色或移除現有角色。您也可以匯入現有的專案，並將它轉換成角色。例如，您可以匯入 ASP.NET Web 應用程式，並將它指定為 Web 角色。

## 新增或移除角色

**新增角色**

在 [方案總管] 中，開啟雲端服務專案中 [角色] 節點的捷徑功能表，並選擇 [新增]。您可以從目前的方案中選取現有的 Web 角色或背景工作角色，或建立新的 Web 或背景工作角色專案。或者您可以選取適當的專案 (例如 ASP.NET Web 應用程式專案)，並將它與角色專案產生關聯。

**移除角色關聯**

在 [方案總管] 的雲端服務專案的 [角色] 節點中，開啟要移除之角色的捷徑功能表，並選擇 [移除]。

## 在您的雲端服務中移除和新增角色

如果您從雲端服務專案中移除角色，但稍後決定將該角色重新加入至專案，則只有角色宣告和基本屬性 (例如端點和診斷資訊) 會被加入專案。ServiceDefinition.csdef 檔案或 ServiceConfiguration.cscfg 檔案不會加入額外的資源或參考。如果您想要新增這項資訊，您必須手動將它重新加回這些檔案。

例如，您可能移除了 Web 服務角色，但稍後決定將這個角色重新加回方案。如果您這樣做的話將會發生錯誤。若要避免這個錯誤，您必須將下列 XML 顯示的 `<LocalResources>` 元素重新加回 ServiceDefinition.csdef 檔案。使用您重新加回專案的 Web 服務角色名稱做為 **<LocalStorage>** 項目的部分名稱屬性。在此範例中，此 Web 服務角色的名稱是 **WCFServiceWebRole1**。

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## 後續步驟

若要了解如何在 Visual Studio 中設定角色，請參閱[使用 Visual Studio 設定 Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。

<!---HONumber=Sept15_HO3-->