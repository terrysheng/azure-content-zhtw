<properties pageTitle="How to implement disaster recovery using service backup and restore in Azure API Management" metaKeywords="" description="Learn how to use backup and restore to perform disaster recovery in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to implement disaster recovery using service backup and restore in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# 如何在 Azure API 管理中使用服務備份和還原實作災害復原

選擇透過 Azure API 管理來發佈及管理 API，您將能夠利用許多非 Azure API 管理使用者須另行設計、實作及管理的容錯和基礎結構功能。Azure 平台可緩和絕大部分可能的失敗後果，且成本低廉。

若要從影響範圍涵蓋 API 管理服務託管所在之區域的可用性問題復原，您應該要做好準備能隨時在其他區域重新建構服務。由於可用性目標和復原時間目標不盡相同，您也許會想要在一或多個區域預留備份服務，並嘗試與作用中的服務維持組態和內容同步。服務備份和還原功能可提供實作災害復原策略時所不可或缺的建置組塊。

您可以透過服務管理 REST API 取得服務備份和還原功能。如需如何取得 API 之存取權限的指示，請參閱[驗證 Azure 資源管理員要求][驗證 Azure 資源管理員要求] (英文)。

## 本主題內容

-   [備份 API 管理服務][備份 API 管理服務]
-   [還原 API 管理服務][還原 API 管理服務]

## <a name="step1"> </a>備份 API 管理服務

若要備份 API 管理服務，請發出以下 HTTP 要求：

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

其中：

-   `subscriptionId` - 訂閱的識別碼，此訂閱含有您嘗試備份的 API 管理服務
-   `resourceGroupName` - 'Api-Default-{service-region}' 形式的字串；其中，`service-region` 可識別您嘗試備份之 API 管理服務託管所在的 Azure 區域 (例如 `North-Central-US`)
-   `serviceName` - 欲備份之 API 管理服務的名稱，該名稱乃是在服務建立時指定的
-   `api-version` - 取代為 `2014-02-14`

在要求的本文中指定目標 Azure 儲存體帳戶、存取金鑰、Blob 容器名稱及備份名稱：

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

將 `Content-Type` 要求標頭的值設定為 `application\json`。

備份作業的執行時間較長，因此可能需要幾分鐘的時間才能完成。如果要求成功且備份程序已起始，您會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。請向 `Location` 標頭中的 URL 中發出 'GET' 要求，以查明作業的狀態。當備份進行時，您會持續收到「202 已接受」狀態碼。回應碼 `200 OK` 代表備份作業已成功完成。

**注意**：

-   於要求本文中指定的**容器必須存在**。
-   備份進行時，請**避免嘗試任何服務管理作業**，如提升或降低階層、變更網域名稱等。
-   備份還原的**保證僅限建立後的 7 天內**。
-   備份**不包括**用來建立分析報表的**流量資料**。請使用 [Azure API 管理 REST API][Azure API 管理 REST API] 來定期擷取分析報告，以利妥善保存。
-   執行服務備份的頻率會影響您的復原點目標。為了盡可能縮小，建議您實施定期備份，以及在針對 API 管理服務做出重要變更後執行隨選備份。
-   在備份作業進行時針對服務組態 (如 API、原則、開發人員入口網站外觀) 所做的**變更可能不會納入備份中，因此可能會遺失**。

## <a name="step2"> </a>還原 API 管理服務

若要從先前建立的備份還原 API 管理服務，請發出以下 HTTP 要求：

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

其中：

-   `subscriptionId` - 訂閱的識別碼，此訂閱含有您要還原備份的目標 API 管理服務
-   `resourceGroupName` - 'Api-Default-{service-region}' 形式的字串；其中，`service-region` 可識別您要還原備份之目標 API 管理服務託管所在的 Azure 區域 (例如 `North-Central-US`)
-   `serviceName` - 用於還原之目標 API 管理服務的名稱，該名稱乃是在服務建立時指定的
-   `api-version` - 取代為 `2014-02-14`

在要求的本文中指定備份檔案位置，即 Azure 儲存體帳戶、存取金鑰、Blob 容器名稱及備份名稱：

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

將 `Content-Type` 要求標頭的值設定為 `application\json`。

還原作業的執行時間較長，因此可能需要 30 分鐘以上的時間才能完成。如果要求成功且還原程序已起始，您會收到含有 `Location` 標頭的 `202 Accepted` 回應狀態碼。請向 `Location` 標頭中的 URL 中發出 'GET' 要求，以查明作業的狀態。當還原進行時，您會持續收到「202 已接受」狀態碼。回應碼 `200 OK` 代表還原作業已成功完成。

**注意**：

-   用於還原之目標服務的**階層必須符合**即將還原之已備份服務的階層。
-   在還原作業進行時針對服務組態 (如 API、原則、開發人員入口網站外觀) 所做的**變更可能會遭到覆寫**。

  [驗證 Azure 資源管理員要求]: http://msdn.microsoft.com/zh-tw/library/dn790557.aspx
  [備份 API 管理服務]: #step1
  [還原 API 管理服務]: #step2
  [Azure API 管理 REST API]: http://msdn.microsoft.com/zh-tw/library/azure/dn781421.aspx
