<properties 
   pageTitle="監視負載平衡器的作業、事件和計數器 | Microsoft Azure"
   description="了解如何啟用 Azure 負載平衡器的警示事件和探查健全狀況狀態記錄"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="joaoma" />

# Azure 負載平衡器的記錄檔分析 (預覽)
您可以在 Azure 中使用不同類型的記錄檔來管理和疑難排解負載平衡器。透過入口網站可以存取其中一些記錄檔，而從 Azure Blob 儲存體可以擷取所有記錄檔並且在不同的工具中進行檢視，例如 Excel 和 PowerBI。您可以從下列清單進一步了解不同類型的記錄檔。


- **稽核記錄檔︰**您可以使用 [Azure 稽核記錄檔](insights-debugging-with-events.md) (之前稱為「作業記錄檔」) 來檢視提交至您的 Azure 訂用帳戶的所有作業及其狀態。預設會啟用稽核記錄檔，並可在 Azure 入口網站中進行檢視。
- **警示事件記錄檔：**您可以使用此記錄檔來檢視已引發哪些負載平衡器警示。系統每五分鐘會收集一次負載平衡器的狀態。只有在引發負載平衡器警示事件時，才會寫入此記錄檔。  
- **健全狀況探查記錄檔：**您可以使用此記錄檔檢查探查的健全狀況檢查狀態、負載平衡器後端中有多少個執行個體在線上，以及從負載平衡器接收網路流量之虛擬機器的百分比。探查狀態事件發生變更時，便會寫入此記錄檔。

>[AZURE.WARNING] 記錄檔僅適用於在資源管理員部署模型中部署的資源。您無法將記錄檔使用於傳統部署模型中的資源。若要深入了解這兩個模型，請參閱[了解資源管理員部署和傳統部署](resource-manager-deployment-model.md)一文。<BR> 記錄檔分析目前僅適用於網際網路面向的負載平衡器。此限制是暫時的，隨時可能變更。請務必再次瀏覽此頁面，以確認未來變更。

## 啟用記錄
每個資源管理員資源都會隨時自動啟用稽核記錄。您需要啟用事件和健全狀況探查記錄，才能開始收集可透過這些記錄檔取得的資料。若要啟用記錄，請遵循下列步驟。

登入 [Azure 入口網站](http://portal.azure.com)。如果您還沒有負載平衡器，請先[建立負載平衡器](load-balancer-internet-arm-ps.md)再繼續。

在入口網站中，按一下 [瀏覽] >> [負載平衡器]。

![入口網站 - 負載平衡器](./media/load-balancer-monitor-log/load-balancer-browse.png)

選取現有的負載平衡器 >> [所有設定]。

![入口網站 - 負載平衡器 - 設定](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

在 [設定] 刀鋒視窗中，按一下 [診斷]，然後在 [診斷] 窗格中的 [狀態] 旁，按一下 [開啟]。在 [設定] 刀鋒視窗中，按一下 [儲存體帳戶]，然後選取現有儲存體帳戶或建立新帳戶。

在下拉式清單的 [儲存體帳戶] 之下，選取您要記錄警示事件、探查健全狀況狀態還是同時執行這兩項，然後按一下 [儲存]。

![Preview 入口網站 - 診斷記錄檔](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

>[AZURE.INFORMATION] 稽核記錄檔不需要個別的儲存體帳戶。將儲存體用於事件和健全狀況探查記錄將會產生服務費用。

## 稽核記錄檔
此記錄檔 (之前稱為「作業記錄檔」) 預設是由 Azure 產生。記錄檔會在 Azure 的 [事件記錄檔] 存放區中保留 90 天。閱讀[檢視事件和稽核記錄檔](insights-debugging-with-events.md)一文，進一步了解這些記錄檔。

## 警示事件記錄檔
如果您已如上所述對每一個負載平衡器進行啟用，才會產生此記錄檔。資料會儲存在您啟用記錄時所指定的儲存體帳戶中。資訊會以 JSON 格式記錄下來，如下所示。

	
	{
    "time": "2016-01-26T10:37:46.6024215Z",
	"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
	

JSON 輸出中會顯示 *eventname* 屬性，此屬性會描述負載平衡器建立警示的原因。在此案例中，來源 IP NAT (SNAT) 限制導致了 TCP 連接埠耗盡，因此產生此警示。

## 健全狀況探查記錄檔
如果您已如上所述對每一個負載平衡器進行啟用，才會產生此記錄檔。資料會儲存在您啟用記錄時所指定的儲存體帳戶中。系統會建立名為 'insights-logs-loadbalancerprobehealthstatus' 的容器，並記錄下列資料：

		{
	    "records":

	    {
	   		"time": "2016-01-26T10:37:46.6024215Z",
	        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 1,
	            "healthPercentage": 50.000000
	        }
	    },
	    {
	        "time": "2016-01-26T10:37:46.6024215Z",
			"systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
	        "category": "LoadBalancerProbeHealthStatus",
	        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
	        "operationName": "LoadBalancerProbeHealthStatus",
	        "properties": {
	            "publicIpAddress": "40.83.190.158",
	            "port": "81",
	            "totalDipCount": 2,
	            "dipDownCount": 0,
	            "healthPercentage": 100.000000
	        }
	    }

	]
	}

JSON 輸出在屬性欄位中顯示了探查健全狀況狀態的基本資訊。*dipDownCount* 屬性會顯示後端上因為探查回應失敗而不會接收網路流量的執行個體總數。

## 檢視和分析稽核記錄檔
您可以使用下列任何方法，檢視和分析稽核記錄檔資料：

- **Azure 工具︰**透過 Azure PowerShell、Azure 命令列介面 (CLI)、Azure REST API 或 Azure Preview 入口網站，從稽核記錄擷取資訊。[稽核作業與資源管理員](resource-group-audit.md)一文會詳述每個方法的逐步指示。
- **Power BI︰**如果還沒有 [Power BI](https://powerbi.microsoft.com/pricing) 帳戶，您可以免費試用。使用 [Power BI 的 Azure 稽核記錄檔內容套件](https://support.powerbi.com/knowledgebase/articles/742695)，您可以使用預先設定的儀表板 (可按原樣使用或加以自訂) 來分析資料。

## 檢視和分析健全狀況探查與事件記錄檔 
您需要連接到儲存體帳戶並擷取事件和健全狀況探查記錄檔的 JSON 記錄項目。下載 JSON 檔案後，您可以將它們轉換成 CSV 並在 Excel、PowerBI 或任何其他資料視覺化工具中檢視。

>[AZURE.TIP] 如果您熟悉 Visual Studio 以及在 C# 中變更常數和變數值的基本概念，您可以使用 Github 所提供的[記錄檔轉換器工具](https://github.com/Azure-Samples/networking-dotnet-log-converter)。

## 其他資源

- [使用 Power BI 視覺化您的 Azure 稽核記錄檔](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)部落格文章。
- [在 Power BI 和其他工具中檢視和分析 Azure 稽核記錄](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)部落格文章。

<!---HONumber=AcomDC_0204_2016-->