<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="在 HDInsight 上對 Hadoop 進行偵錯：錯誤訊息 | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="了解您使用 PowerShell 來管理 HDInsight 時可能收到的錯誤訊息，以及可採取來回復的步驟。" services="hdinsight" title="在 HDInsight 上對 Hadoop 進行偵錯：錯誤訊息" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# 在 HDInsight 上對 Hadoop 進行偵錯：錯誤訊息

## 簡介

本主題列舉的錯誤訊息可協助 Azure HDInsight 的 Hadoop 使用者了解，他們在使用 Azure PowerShell 來管理服務時可能遭遇的錯誤狀況，並建議一些步驟供他們從錯誤中回復。

使用 Azure 入口網站管理 HDinsight 叢集時也可能會看到其中某些錯誤訊息。但由於此情況下可能採取的補救動作有其限制，您可能看到的其他錯誤訊息比較不詳細。在很顯然有緩和措施的情況下，則會提供其他錯誤訊息。舉例來說，如果違反參數的限制，則輸入值的方塊右上角會彈出訊息。以下是要求太多資料節點的例子。補救方法是將數量減少到 22 以下的允許值。

![HDI.Debugging.ErrorMessages.Portal][HDI.Debugging.ErrorMessages.Portal]

[HDInsight 錯誤][HDInsight 錯誤]一節會依名稱的字母順序，列出使用者在 Azure PowerShell 或 Azure 入口網站中可能遭遇的錯誤，您可以從中連結至[錯誤的說明和緩和][錯誤的說明和緩和]一節來查看下列錯誤資訊：

-   **說明**：使用者看見的錯誤訊息
-   **緩和**：可採取以便從錯誤中回復的步驟。

### HDInsight 錯誤

[AtleastOneSqlMetastoreMustBeProvided][AtleastOneSqlMetastoreMustBeProvided]
[AzureRegionNotSupported][AzureRegionNotSupported]
[ClusterContainerRecordNotFound][ClusterContainerRecordNotFound]
[ClusterDnsNameInvalidReservedWord][ClusterDnsNameInvalidReservedWord]
[ClusterNameUnavailable][ClusterNameUnavailable]
[ClusterUserNameInvalid][ClusterUserNameInvalid]
[ClusterUserNameInvalidReservedWord][ClusterUserNameInvalidReservedWord]
[ContainerNameMisMatchWithDnsName][ContainerNameMisMatchWithDnsName]
[DataNodeDefinitionNotFound][DataNodeDefinitionNotFound]
[DeploymentDeletionFailure][DeploymentDeletionFailure]
[DnsMappingNotFound][DnsMappingNotFound]
[DuplicateClusterContainerRequest][DuplicateClusterContainerRequest]
[DuplicateClusterInHostedService][DuplicateClusterInHostedService]
[FailureToUpdateDeploymentStatus][FailureToUpdateDeploymentStatus]
[HdiRestoreClusterAltered][HdiRestoreClusterAltered]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HeadNodeConfigNotFound][HeadNodeConfigNotFound]
[HostedServiceCreationFailure][HostedServiceCreationFailure]
[HostedServiceHasProductionDeployment][HostedServiceHasProductionDeployment]
[HostedServiceNotFound][HostedServiceNotFound]
[HostedServiceWithNoDeployment][HostedServiceWithNoDeployment]
[InsufficientResourcesCores][InsufficientResourcesCores]
[InsufficientResourcesHostedServices][InsufficientResourcesHostedServices]
[InternalErrorRetryRequest][InternalErrorRetryRequest]
[InvalidAzureStorageLocation][InvalidAzureStorageLocation]
[InvalidNodeSizeForDataNode][InvalidNodeSizeForDataNode]
[InvalidNodeSizeForHeadNode][InvalidNodeSizeForHeadNode]
[InvalidRightsForDeploymentDeletion][InvalidRightsForDeploymentDeletion]
[InvalidStorageAccountBlobContainerName][InvalidStorageAccountBlobContainerName]
[InvalidStorageAccountConfigurationSecretKey][InvalidStorageAccountConfigurationSecretKey]
[InvalidVersionHeaderFormat][InvalidVersionHeaderFormat]
[MoreThanOneHeadNode][MoreThanOneHeadNode]
[OperationTimedOutRetryRequest][OperationTimedOutRetryRequest]
[ParameterNullOrEmpty][ParameterNullOrEmpty]
[PreClusterCreationValidationFailure][PreClusterCreationValidationFailure]
[RegionCapabilityNotAvailable][RegionCapabilityNotAvailable]
[StorageAccountNotColocated][StorageAccountNotColocated]
[SubscriptionIdNotActive][SubscriptionIdNotActive]
[SubscriptionIdNotFound][SubscriptionIdNotFound]
[UnableToResolveDNS][UnableToResolveDNS]
[UnableToVerifyLocationOfResource][UnableToVerifyLocationOfResource]
[VersionCapabilityNotAvailable][VersionCapabilityNotAvailable]
[VersionNotSupported][VersionNotSupported]
[VersionNotSupportedInRegion][VersionNotSupportedInRegion]
[WasbAccountConfigNotFound][WasbAccountConfigNotFound]

## <span id="discription-mitigation-errors"></span></a>錯誤的診斷和緩和

### <span id="AtleastOneSqlMetastoreMustBeProvided"></span></a>AtleastOneSqlMetastoreMustBeProvided

-   **說明**：請至少提供一個元件的 Azure SQL Database 詳細資料，以便使用 Hive 和 Oozie metastore 的自訂設定。
-   **緩和**：使用者必須提供有效的 SQL Azure metastore，然後重試要求。

### <span id="AzureRegionNotSupported"></span></a>AzureRegionNotSupported

-   **說明**：無法在區域 *nameOfYourRegion* 中建立叢集。請使用有效的 HDInsight 區域，然後重試要求。
-   **緩和**：客戶應該建立目前支援他們的叢集區域：東南亞、西歐、北歐和美國西部。

### <span id="ClusterContainerRecordNotFound"></span></a>ClusterContainerRecordNotFound

-   **說明**：伺服器找不到所要求的叢集記錄。
-   **緩和**：重試作業。

### <span id="ClusterDnsNameInvalidReservedWord"></span></a>ClusterDnsNameInvalidReservedWord

-   **說明**：叢集 DNS 名稱 *yourDnsName* 無效。請確定名稱以英數字元開始和結束，且只能包含 '-' 特殊字元
-   **緩和**：請確定叢集的 DNS 名稱有效，開頭和結束是英數字元，且不含虛線 '-' 以外的特殊字元，然後重試作業。

### <span id="ClusterNameUnavailable"></span></a>ClusterNameUnavailable

-   **說明**：叢集名稱 *yourClusterName* 無法使用。請挑選其他名稱。
-   **緩和**：使用者應該指定唯一且尚不存在的叢集名稱，然後重試。如果使用者在使用入口網站，當建立步驟中已使用叢集名稱時，UI 會通知使用者。

### <span id="ClusterPasswordInvalid"></span></a>ClusterPasswordInvalid

-   **說明**：叢集密碼無效。密碼長度至少必須為 10 個字元，且至少必須包含一個數字、大寫字母、小寫字母和特殊字元，不可有空格，而且不得包含使用者名稱。
-   **緩和**：請提供有效的叢集密碼，然後重試作業。

### <span id="ClusterUserNameInvalid"></span></a>ClusterUserNameInvalid

-   **說明**：叢集使用者名稱無效。請確定使用者名稱不含特殊字元或空格。
-   **緩和**：請提供有效的叢集使用者名稱，然後重試作業。

### <span id="ClusterUserNameInvalidReservedWord"></span></a>ClusterUserNameInvalidReservedWord

-   **說明**：叢集 DNS 名稱 *yourDnsClusterName* 無效。請確定名稱以英數字元開始和結束，且只能包含 '-' 特殊字元
-   **緩和**：請提供有效的 DNS 叢集使用者名稱，然後重試作業。

### <span id="ContainerNameMisMatchWithDnsName"></span></a>ContainerNameMisMatchWithDnsName

-   **說明**：URI *yourcontainerURI* 中的容器名稱和要求內文中的 DNS 名稱 *yourDnsName* 必須相同。
-   **緩和**：請確定容器名稱和 DNS 名稱相同，然後重試作業。

### <span id="DataNodeDefinitionNotFound"></span></a>DataNodeDefinitionNotFound

-   **說明**：叢集組態無效。在節點大小中找不到任何資料節點定義。
-   **緩和**：重試作業。

### <span id="DeploymentDeletionFailure"></span></a>DeploymentDeletionFailure

-   **說明**：刪除叢集的部署失敗。
-   **緩和**：重試刪除作業。

### <span id="DnsMappingNotFound"></span></a>DnsMappingNotFound

-   **說明**：服務組態錯誤。找不到必要的 DNS 對應資訊。
-   **緩和**：刪除叢集，然後建立新的叢集。

### <span id="DuplicateClusterContainerRequest"></span></a>DuplicateClusterContainerRequest

-   **說明**：嘗試建立重複的叢集容器。已有 *nameOfYourContainer* 的記錄存在，但 Etag 不符。
-   **緩和**：請提供容器的唯一名稱，然後重試建立作業。

### <span id="DuplicateClusterInHostedService"></span></a>DuplicateClusterInHostedService

-   **說明**：代管服務 *nameOfYourHostedService* 已包含叢集。代管服務不可包含多個叢集
-   **緩和**：將叢集裝載於其他代管服務中。

### <span id="FailureToUpdateDeploymentStatus"></span></a>FailureToUpdateDeploymentStatus

-   **說明**：伺服器無法更新叢集部署的狀態。
-   **緩和**：重試作業。如果此情況發生多次，請連絡 CSS。

### <span id="HdiRestoreClusterAltered"></span></a>HdiRestoreClusterAltered

-   **說明**：維護時已刪除叢集 *yourClusterName*。請重新建立叢集。
-   **緩和**：重新建立叢集。

### <span id="HeadNodeConfigNotFound"></span></a>HeadNodeConfigNotFound

-   **說明**：叢集組態無效。在節點大小中找不到必要的前端節點組態。
-   **緩和**：重試作業。

### <span id="HostedServiceCreationFailure"></span></a>HostedServiceCreationFailure

-   **說明**：無法建立代管服務 *nameOfYourHostedService*。請重試要求。
-   **緩和**：重試要求。

### <span id="HostedServiceHasProductionDeployment"></span></a>HostedServiceHasProductionDeployment

-   **說明**：代管服務 *nameOfYourHostedService* 已有生產部署。代管服務不可包含多個生產部署請使用不同的叢集名稱來重試要求。
-   **緩和**：請使用不同的叢集名稱，然後重試要求。

### <span id="HostedServiceNotFound"></span></a>HostedServiceNotFound

-   **說明**：找不到叢集的代管服務 *nameOfYourHostedService*。
-   **緩和**：如果叢集處於錯誤狀態，請刪除叢集，然後再試一次。

### <span id="HostedServiceWithNoDeployment"></span></a>HostedServiceWithNoDeployment

-   **說明**：代管服務 *nameOfYourHostedService* 沒有相關聯的部署。
-   **緩和**：如果叢集處於錯誤狀態，請刪除叢集，然後再試一次。

### <span id="InsufficientResourcesCores"></span></a>InsufficientResourcesCores

-   **說明**：SubscriptionId *yourSubscriptionId* 沒有剩餘的核心可建立叢集 *yourClusterName*。需要：*resourcesRequired*，可用：*resourcesAvailable*。
-   **緩和**：釋出訂用帳戶中的資源，或增加可供訂用帳戶的資源，然後重試建立叢集。

### <span id="InsufficientResourcesHostedServices"></span></a>InsufficientResourcesHostedServices

-   **說明**：訂用帳戶識別碼 *yourSubscriptionId* 沒有配額可供新的 HostedService 建立叢集 *yourClusterName*。
-   **緩和**：釋出訂用帳戶中的資源，或增加可供訂用帳戶的資源，然後重試建立叢集。

### <span id="InternalErrorRetryRequest"></span></a>InternalErrorRetryRequest

-   **說明**：伺服器發生內部錯誤。請重試要求。
-   **緩和**：重試要求。

### <span id="InvalidAzureStorageLocation"></span></a>InvalidAzureStorageLocation

-   **說明**：Azure 儲存體位置 *dataRegionName* 不是有效的位置。請確定區域正確，然後重試要求。
-   **緩和**：請選取支援 HDInsight 的儲存體位置，檢查您的叢集已並存，然後重試作業。

### <span id="InvalidNodeSizeForDataNode"></span></a>InvalidNodeSizeForDataNode

-   **說明**：資源節點的 VM 大小無效。所有資料節點僅支援 'Large VM' 大小。
-   **緩和**：請為資料節點指定支援的節點大小，然後重試作業。

### <span id="InvalidNodeSizeForHeadNode"></span></a>InvalidNodeSizeForHeadNode

-   **說明**：前端節點的 VM 大小無效。前端節點僅支援 'ExtraLarge VM' 大小。
-   **緩和**：請為前端節點指定支援的節點大小，然後重試作業。

### <span id="InvalidRightsForDeploymentDeletion"></span></a>InvalidRightsForDeploymentDeletion

-   **說明**：使用的訂用帳戶識別碼 *yourSubscriptionId* 權限不足，無法對叢集 *yourClusterName* 執行刪除作業。
-   **緩和**：如果叢集處於錯誤狀態，請卸除叢集，然後再試一次。

### <span id="InvalidStorageAccountBlobContainerName"></span></a>InvalidStorageAccountBlobContainerName

-   **說明**：外部儲存帳戶 Blob 容器名稱 *yourContainerName* 無效。請確定名稱以字母開頭，且只包含小寫字母、數字和虛線。
-   **緩和**：請指定有效的儲存帳戶 Blob 容器名稱，然後重試作業。

### <span id="InvalidStorageAccountConfigurationSecretKey"></span></a>InvalidStorageAccountConfigurationSecretKey

-   **說明**：需要有密碼詳細資料，才能設定外部儲存帳戶 *yourStorageAccountName* 的組態。
-   **緩和**：請為儲存帳戶指定有效的密碼，然後重試作業。

### <span id="InvalidVersionHeaderFormat"></span></a>InvalidVersionHeaderFormat

-   **說明**：版本標頭 *yourVersionHeader* 不是有效格式 yyyy-mm-dd。
-   **緩和**：請為版本標頭指定有效格式，然後重試要求。

### <span id="MoreThanOneHeadNode"></span></a>MoreThanOneHeadNode

-   **說明**：叢集組態無效。發現多個前端節點組態。
-   **緩和**：請編輯組態，僅指定一個前端節點。

### <span id="OperationTimedOutRetryRequest"></span></a>OperationTimedOutRetryRequest

-   **說明**：無法於允許的時間內完成作業，或已達到重試次數上限。請重試要求。
-   **緩和**：重試要求。

### <span id="ParameterNullOrEmpty"></span></a>ParameterNullOrEmpty

-   **說明**：參數 *yourParameterName* 不可為 Null 或空白。
-   **緩和**：請為參數指定有效值。

### <span id="PreClusterCreationValidationFailure"></span></a>PreClusterCreationValidationFailure

-   **說明**：一或多個叢集建立要求輸入無效。請確定輸入值正確，然後重試要求。
-   **緩和**：請確定輸入值正確，然後重試要求。

### <span id="RegionCapabilityNotAvailable"></span></a>RegionCapabilityNotAvailable

-   **說明**：區域 *yourRegionName* 和訂用帳戶識別碼 *yourSubscriptionId* 無法使用區域功能。
-   **緩和**：請指定支援 HDInsight 叢集的區域。公開支援的區域如下：東南亞、西歐、北歐和美國西部。

### <span id="StorageAccountNotColocated"></span></a>StorageAccountNotColocated

-   **說明**：儲存帳戶 *yourStorageAccountName* 位於區域 *currentRegionName* 中。此值必須與叢集區域 *yourClusterRegionName* 相同。
-   **緩和**：請指定您的叢集所在相同區域中的儲存帳戶，或者，如果您的資料已在儲存帳戶中，請在現有儲存帳戶所在的相同區域中建立新的叢集。如果您在使用入口網站，UI 會事先通知此問題。

### <span id="SubscriptionIdNotActive"></span></a>SubscriptionIdNotActive

-   **說明**：指定的訂用帳戶識別碼 *yourSubscriptionId* 並未使用。
-   **緩和**：請重新啟動訂用帳戶，或取得新的有效訂用帳戶。

### <span id="SubscriptionIdNotFound"></span></a>SubscriptionIdNotFound

-   **說明**：找不到訂用帳戶識別碼 *yourSubscriptionId*。
-   **緩和**：請檢查訂用帳戶識別碼有效，然後重試作業。

### <span id="UnableToResolveDNS"></span></a>UnableToResolveDNS

-   **說明**：無法解析 DNS *yourDnsUrl*。請確定您提供 Blob 端點的完整 URL。
-   **緩和**：提供有效的 Blob URL。URL「必須」完全有效，包括以 *http://* 開頭，以 *.com* 結尾。通常可在 manage.windowsazure.com 入口網站的儲存體索引標籤中找到完整 URL。

### <span id="UnableToVerifyLocationOfResource"></span></a>UnableToVerifyLocationOfResource

-   **說明**：無法驗證資源的 *yourDnsUrl* 的位置。請確定您提供 Blob 端點的完整 URL。
-   **緩和**：提供有效的 Blob URL。URL「必須」完全有效，包括以 *http://* 開頭，以 *.com* 結尾。通常可在 manage.windowsazure.com 入口網站的儲存體索引標籤中找到完整 URL。

### <span id="VersionCapabilityNotAvailable"></span></a>VersionCapabilityNotAvailable

-   **說明**：版本 *specifiedVersion* 和訂用帳戶識別碼 *yourSubscriptionId* 無法使用版本功能。
-   **緩和**：請選擇可用的版本，然後重試作業。

### <span id="VersionNotSupported"></span></a>VersionNotSupported

-   **說明**：不支援版本 *specifiedVersion*。
-   **緩和**：請選擇支援的版本，然後重試作業。

### <span id="VersionNotSupportedInRegion"></span></a>VersionNotSupportedInRegion

-   **說明**：Azure 區域 *specifiedRegion* 中無法使用版本 *specifiedVersion*。
-   **緩和**：請選擇指定區域中支援的版本，然後重試作業。

### <span id="WasbAccountConfigNotFound"></span></a>WasbAccountConfigNotFound

-   **說明**：叢集組態無效。在外部帳戶中找不到必要的 WASB 帳戶組態。
-   **緩和**：請驗證帳戶已存在且於組態中指定正確，然後重試作業。

## <span id="resources"></span></a>其他偵錯資源

-   [Azure HDInsight SDK 文件][Azure HDInsight SDK 文件]

  [HDI.Debugging.ErrorMessages.Portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png
  [HDInsight 錯誤]: #hdinsight-error-messages
  [錯誤的說明和緩和]: #discription-mitigation-errors
  [AtleastOneSqlMetastoreMustBeProvided]: #AtleastOneSqlMetastoreMustBeProvided
  [AzureRegionNotSupported]: #AzureRegionNotSupported
  [ClusterContainerRecordNotFound]: #ClusterContainerRecordNotFound
  [ClusterDnsNameInvalidReservedWord]: #ClusterDnsNameInvalidReservedWord
  [ClusterNameUnavailable]: #ClusterNameUnavailable
  [ClusterUserNameInvalid]: #ClusterUserNameInvalid
  [ClusterUserNameInvalidReservedWord]: #ClusterUserNameInvalidReservedWord
  [ContainerNameMisMatchWithDnsName]: #ContainerNameMisMatchWithDnsName
  [DataNodeDefinitionNotFound]: #DataNodeDefinitionNotFound
  [DeploymentDeletionFailure]: #DeploymentDeletionFailure
  [DnsMappingNotFound]: #DnsMappingNotFound
  [DuplicateClusterContainerRequest]: #DuplicateClusterContainerRequest
  [DuplicateClusterInHostedService]: #DuplicateClusterInHostedService
  [FailureToUpdateDeploymentStatus]: #FailureToUpdateDeploymentStatus
  [HdiRestoreClusterAltered]: #HdiRestoreClusterAltered
  [HeadNodeConfigNotFound]: #HeadNodeConfigNotFound
  [HostedServiceCreationFailure]: #HostedServiceCreationFailure
  [HostedServiceHasProductionDeployment]: #HostedServiceHasProductionDeployment
  [HostedServiceNotFound]: #HostedServiceNotFound
  [HostedServiceWithNoDeployment]: #HostedServiceWithNoDeployment
  [InsufficientResourcesCores]: #InsufficientResourcesCores
  [InsufficientResourcesHostedServices]: #InsufficientResourcesHostedServices
  [InternalErrorRetryRequest]: #InternalErrorRetryRequest
  [InvalidAzureStorageLocation]: #InvalidAzureStorageLocation
  [InvalidNodeSizeForDataNode]: #InvalidNodeSizeForDataNode
  [InvalidNodeSizeForHeadNode]: #InvalidNodeSizeForHeadNode
  [InvalidRightsForDeploymentDeletion]: #InvalidRightsForDeploymentDeletion
  [InvalidStorageAccountBlobContainerName]: #InvalidStorageAccountBlobContainerName
  [InvalidStorageAccountConfigurationSecretKey]: #InvalidStorageAccountConfigurationSecretKey
  [InvalidVersionHeaderFormat]: #InvalidVersionHeaderFormat
  [MoreThanOneHeadNode]: #MoreThanOneHeadNode
  [OperationTimedOutRetryRequest]: #OperationTimedOutRetryRequest
  [ParameterNullOrEmpty]: #ParameterNullOrEmpty
  [PreClusterCreationValidationFailure]: #PreClusterCreationValidationFailure
  [RegionCapabilityNotAvailable]: #RegionCapabilityNotAvailable
  [StorageAccountNotColocated]: #StorageAccountNotColocated
  [SubscriptionIdNotActive]: #SubscriptionIdNotActive
  [SubscriptionIdNotFound]: #SubscriptionIdNotFound
  [UnableToResolveDNS]: #UnableToResolveDNS
  [UnableToVerifyLocationOfResource]: #UnableToVerifyLocationOfResource
  [VersionCapabilityNotAvailable]: #VersionCapabilityNotAvailable
  [VersionNotSupported]: #VersionNotSupported
  [VersionNotSupportedInRegion]: #VersionNotSupportedInRegion
  [WasbAccountConfigNotFound]: #WasbAccountConfigNotFound
  [Azure HDInsight SDK 文件]: http://msdnstage.redmond.corp.microsoft.com/zh-tw/library/dn479185.aspx
