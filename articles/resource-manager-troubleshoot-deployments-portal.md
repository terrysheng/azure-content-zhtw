<properties
   pageTitle="使用入口網站疑難排解部署 | Microsoft Azure"
   description="描述如何使用 Azure 入口網站來偵測並修正源自資源管理員部署的問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# 使用 Azure 入口網站疑難排解資源群組部署

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

如果您在將資源部署至 Azure 時發生錯誤，您需要針對出錯的地方進行疑難排解。Azure 入口網站提供介面，可讓您輕鬆地找到錯誤並判斷可能的修正方法。

您可以藉由查看稽核記錄檔或部署作業來疑難排解您的部署。本主題說明這兩種方法。

部署之前先驗證您的範本和基礎結構，即可避免發生一些錯誤。如需詳細資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

## 使用稽核記錄檔進行疑難排解

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相關錯誤，請使用下列步驟 ︰

1. 透過入口網站，選取 [瀏覽] 和 [稽核記錄檔] 來檢視稽核記錄檔。

    ![選取稽核記錄檔](./media/resource-manager-troubleshoot-deployments-portal/select-audit-logs.png)

2. 在 [稽核記錄檔] 刀鋒視窗中，您會看到訂用帳戶中所有資源群組的最新作業摘要。它會包含表示作業的時間與狀態的圖形，以及作業的清單。

    ![顯示動作](./media/resource-manager-troubleshoot-deployments-portal/audit-summary.png)

3. 您可以在清單中選取任何一個作業。選取包含您想要調查之錯誤的作業。

    ![選取作業](./media/resource-manager-troubleshoot-deployments-portal/select-operation.png)
  
4. 您會看到該作業的所有事件。請注意摘要中的**相互關聯識別碼**。此識別碼可用來追蹤相關的事件。與技術支援人員合作來疑難排解問題時，這非常有用。您可以選取任何事件，以查看事件的相關詳細資料。

    ![選取事件](./media/resource-manager-troubleshoot-deployments-portal/select-event.png)

5. 您會看到事件的相關詳細資料。請特別注意**屬性**，以取得錯誤的相關資訊。

    ![顯示稽核記錄檔詳細資料](./media/resource-manager-troubleshoot-deployments-portal/audit-details.png)

您可以篩選稽核記錄檔的檢視，以著重於特定條件。自訂稽核記錄檔的檢視︰

1. 選取 [稽核記錄檔] 刀鋒視窗頂端的 [篩選]。

    ![篩選記錄檔](./media/resource-manager-troubleshoot-deployments-portal/filter-logs.png)

2. 從 [篩選] 刀鋒視窗中，選取條件，將稽核記錄檔的檢視限制為您要查看的作業。例如，您可以篩選作業，使其只顯示特定資源群組的錯誤。

    ![設定篩選選項](./media/resource-manager-troubleshoot-deployments-portal/set-filter.png)

3. 您可以設定時間範圍，進一步篩選作業。下圖會以特定的 20 分鐘時間範圍來檢視篩選。

    ![設定時間](./media/resource-manager-troubleshoot-deployments-portal/select-time.png)

更新稽核記錄檔的檢視之後，您只會看到符合指定條件的作業。這些設定會保留到您下次檢視稽核記錄檔時，因此您可能需要變更這些值，以擴大您的作業檢視。

希望您已能找出部署失敗的原因。您也可以查看部署作業以取得狀態資訊，如下一節中所示。

## 使用部署作業進行疑難排解

若要查看部署作業，請使用下列步驟 ︰

1. 針對部署所含的資源群組，請注意最後一個部署的狀態。您可以選取此狀態，以取得更多詳細資料。

    ![deployment status](./media/resource-manager-troubleshoot-deployments-portal/deployment-status.png)

2. 您會看到最近的部署歷程記錄。選取失敗的部署。

    ![deployment status](./media/resource-manager-troubleshoot-deployments-portal/select-deployment.png)

3. 檢視部署的相關資訊，並選取失敗的作業，以查看錯誤的詳細資訊。

    ![檢視失敗的部署](./media/resource-manager-troubleshoot-deployments-portal/view-failed-deployment.png)

4. 在 [作業詳細資料] 刀鋒視窗中，您會看到有關作業失敗的資訊。請特別注意狀態訊息。

    ![檢視狀態訊息](./media/resource-manager-troubleshoot-deployments-portal/operations-status.png)



## 後續步驟

- 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用資源管理員來稽核作業](resource-group-audit.md)。
- 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0323_2016-->