<properties
	pageTitle="RBAC：內建角色 | Microsoft Azure"
	description="本主題說明角色型存取控制 (RBAC) 的內建角色。"
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="12/04/2015"
	ms.author="inhenk"/>

#RBAC：內建角色

## 內建角色

Azure 角色型存取控制會隨附三個內建的角色，供您指派給使用者、群組與服務。您無法修改內建角色定義。在即將發佈的 Azure RBAC 版本中，您將能從可在 Azure 資源上執行的可用動作清單中撰寫一系列動作，藉此定義自訂角色。

按一下對應的連結，請參閱角色定義的 [動作] 和 [非動作] 屬性。[動作] 屬性會指定 Azure 資源上允許的動作。動作字串可以使用萬用字元。角色定義的 [非動作] 屬性可指定必須從允許的動作中排除的動作。


| 角色名稱 | 說明 |
| --------- | ----------- |
| [API 管理服務參與者](#api-management-service-contributor) | 可以管理 API 管理服務 |
| [Application Insights 元件參與者](#application-insights-component-contributor) | 可以管理 Application Insights 元件 |
| [自動化運算子](#automation-operator) | 能夠啟動、停止、暫停和繼續工作 |
| [BizTalk 參與者](#biztalk-contributor) | 可以管理 BizTalk 服務 |
| [ClearDB MySQL DB 參與者](#cleardb-mysql-db-contributor) | 可以管理 ClearDB MySQL 資料庫 |
| [參與者](#contributor) | 可以管理存取以外的所有內容。 |
| [Data Factory 參與者](#data-factory-contributor) | 可以管理資料處理站 |
| [研發/測試實驗室使用者](#devtest-lab-user) | 可以檢視所有項目，並連接、啟動、重新啟動和關閉虛擬機器。 |
| [Document DB 帳戶參與者](#document-db-account-contributor) | 可以管理 Document DB 帳戶 |
| [Intelligent Systems 帳戶參與者](#intelligent-systems-account-contributor) | 可以管理 Intelligent Systems 帳戶 |
| [網路參與者](#network-contributor) | 可以管理網路資源 |
| [NewRelic APM 帳戶參與者](#newrelic-apm-account-contributor) | 可以管理 NewRelic 應用程式效能管理帳戶和應用程式 |
| [擁有者](#owner) | 擁有者可以管理所有項目，包括存取。 |
| [讀取者](#reader) | 讀取者可以檢視所有項目，但是無法進行變更。 |
| [Redis 快取參與者](#redis-cache-contributor]) | 可以管理 Redis 快取 |
| [排程器工作集合參與者](#scheduler-job-collections-contributor) | 可以管理排程器工作集合 |
| [搜尋服務參與者](#search-service-contributor) | 可以管理搜尋服務 |
| [安全性管理員](#security-manager) | 可以管理安全性元件、安全性原則及虛擬機器 |
| [SQL DB 參與者](#sql-db-contributor) | 可以管理 SQL 資料庫，但是無法管理它們的安全性相關原則 |
| [SQL 安全性管理員](#sql-security-manager) | 可以管理 SQL Server 和資料庫的安全性相關原則 |
| [SQL Server 參與者](#sql-server-contributor) | 可以管理 SQL Server 和資料庫，但是無法管理它們的安全性相關原則 |
| [傳統儲存體帳戶參與者](#classic-storage-account-contributor) | 可以管理傳統儲存體帳戶 |
| [儲存體帳戶參與者](#storage-account-contributor) | 可以管理儲存體帳戶 |
| [使用者存取系統管理員](#user-access-administrator) | 可以管理 Azure 資源的使用者存取 |
| [傳統虛擬機器參與者](#classic-virtual-machine-contributor) | 可以管理傳統虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶 |
| [虛擬機器參與者](#virtual-machine-contributor) | 可以管理虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶 |
| [傳統網路參與者](#classic-network-contributor) | 可以管理傳統虛擬網路和保留 IP |
| [Web 方案參與者](#web-plan-contributor) | 可以管理 Web 方案 |
| [網站參與者](#website-contributor) | 可以管理網站，但是不能管理它們連接的 Web 方案 |

### API 管理服務參與者
可以管理 API 管理服務

| **動作** | |
| ------- | ------ |
| Microsoft.ApiManagement/Services/* | 建立和管理 API 管理服務 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### Application Insights 元件參與者
可以管理 Application Insights 元件

| **動作** | |
| ------- | ------ |
| Microsoft.Insights/components/* | 建立和管理 Insights 元件 |
| Microsoft.Insights/webtests/* | 建立和管理 Web 測試 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 自動化運算子
自動化運算子能夠啟動、停止、暫停和繼續工作

| **動作** ||
| ------- | ------ |
| Microsoft.Automation/automationAccounts/read | 讀取自動化帳戶 |
| Microsoft.Automation/automationAccounts/runbooks/read | 讀取自動化 Runbook |
| Microsoft.Automation/automationAccounts/schedules/read | 讀取自動化帳戶排程 |
| Microsoft.Automation/automationAccounts/schedules/write | 撰寫自動化帳戶排程 |
| Microsoft.Automation/automationAccounts/jobs/read | 讀取自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/write | 撰寫自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/stop/action | 停止自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | 暫停自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobs/resume/action | 繼續自動化帳戶工作 |
| Microsoft.Automation/automationAccounts/jobSchedules/read | 讀取自動化帳戶工作排程 |
| Microsoft.Automation/automationAccounts/jobSchedules/write | 讀取自動化帳戶工作排程 |

### BizTalk 參與者
可以管理 BizTalk 服務

| **動作** ||
| ------- | ------ |
| Microsoft.BizTalkServices/BizTalk/* | 建立和管理 BizTalk 服務 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### ClearDB MySQL DB 參與者
可以管理 ClearDB MySQL 資料庫

| **動作** ||
| ------- | ------ |
| successbricks.cleardb/databases/* | 建立和管理 ClearDB MySQL 資料庫 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 參與者
參與者可以管理存取以外的所有內容

| **動作** ||
| ------- | ------ |
| * | 建立和管理所有類型的資源 |
| ****無動作 | | | Microsoft.Authorization/*/Write | 無法建立角色和角色指派 | | Microsoft.Authorization/*/Delete | 無法刪除角色和角色指派 |

### Data Factory 參與者
可以管理資料處理站

| **動作** ||
| ------- | ------ |
| Microsoft.DataFactory/dataFactories/* | 建立和管理資料處理站 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 研發/測試實驗室使用者
可以檢視所有項目，並連接、啟動、重新啟動和關閉虛擬機器

| **動作** ||
| ------- | ------ |
| */read | 讀取所有類型的資源 | | Microsoft.DevTestLab/labs/labStats/action | 讀取實驗室狀態 | | Microsoft.DevTestLab/Environments/* | 建立和管理環境 |
| Microsoft.DevTestLab/labs/createEnvironment/action | 建立實驗室環境 |
| Microsoft.Compute/virtualMachines/start/action | 啟動虛擬機器 |
| Microsoft.Compute/virtualMachines/restart/action | 重新啟動虛擬機器 |
| Microsoft.Compute/virtualMachines/deallocate/action | 取消配置虛擬機器 |
| Microsoft.Storage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |
| Microsoft.Network/virtualNetworks/join/action | 加入虛擬網路 |
| Microsoft.Network/loadBalancers/join/action | 加入負載平衡器 |
| Microsoft.Network/publicIPAddresses/link/action | 連結至公開 IP 位址 |
| Microsoft.Network/networkInterfaces/link/action | 連結至網路介面 |
| Microsoft.Network/networkInterfaces/write | 撰寫網路介面 |

### Document DB 帳戶參與者
可以管理 Document DB 帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.DocumentDb/databaseAccounts/* | 建立及管理 DocumentDB 帳戶 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### Intelligent Systems 帳戶參與者
可以管理 Intelligent Systems 帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.IntelligentSystems/accounts/* | 建立及管理 Intelligent Systems 帳戶 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 網路參與者
可以管理網路資源

| **動作** ||
| ------- | ------ |
| Microsoft.Network/* | 建立和管理網路 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### NewRelic APM 帳戶參與者
可以管理 NewRelic 應用程式效能管理帳戶和應用程式

| **動作** ||
| ------- | ------ |
| NewRelic.APM/accounts/* | 建立和管理 NewRelic 應用程式效能管理帳戶 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 擁有者
可以管理所有項目，包括存取

| **動作** ||
| ------- | ------ |
| * | 建立和管理所有類型的資源 |

### 讀取者
可以檢視所有項目，但是無法進行變更

| **動作** ||
| ------- | ------ |
| **/read | 讀取密碼以外的所有類型資源。 |

### Redis 快取參與者
可以管理 Redis 快取

| **動作** ||
| ------- | ------ |
| Microsoft.Cache/redis/* | 建立和管理 Redis 快取 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 排程器工作集合參與者
可以管理排程器工作集合

| **動作** ||
| ------- | ------ |
| Microsoft.Scheduler/jobcollections/* | 建立和管理工作集合 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 搜尋服務參與者
可以管理搜尋服務

| **動作** ||
| ------- | ------ |
| Microsoft.Search/searchServices/* | 建立和管理搜尋服務 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 安全性管理員
可以管理安全性元件、安全性原則及虛擬機器

| **動作** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/*/read | 讀取傳統網路的組態資訊 |
| Microsoft.ClassicCompute/*/read | 讀取傳統運算虛擬機器的組態資訊 |
| Microsoft.ClassicCompute/virtualMachines/*/write | 撰寫虛擬機器的組態 |
| Microsoft.Security/* | 建立和管理安全性元件和原則 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### SQL DB 參與者
可以管理 SQL 資料庫，但是無法管理它們的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | 讀取 SQL Server |
| Microsoft.Sql/servers/databases/* | 建立和管理 SQL 資料庫 |
| Microsoft.Authorization/*/read | 讀取角色和角色指派 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立及管理警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |
| **非動作** | |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯稽核原則 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯資料遮罩原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯安全性度量 |

### SQL 安全性管理員
可以管理 SQL Server 和資料庫的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Sql/servers/read | 讀取 SQL Server |
| Microsoft.Sql/servers/auditingPolicies/* | 建立和管理 SQL Server 稽核原則 |
| Microsoft.Sql/servers/databases/read | 讀取 SQL 資料庫 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 建立和管理 SQL Server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 建立和管理 SQL Server 資料庫連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 建立和管理 SQL Server 資料庫資料遮罩原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 建立和管理 SQL Server 資料庫安全性度量 |
| Microsoft.Authorization/*/read | 讀取 Microsoft 授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |
| Microsoft.Sql/servers/databases/schemas/read | 讀取 SQL Server 資料庫結構描述 |
| Microsoft.Sql/servers/databases/schemas/tables/read | 讀取 SQL Server 資料庫資料表 |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | 讀取 SQL Server 資料庫資料表資料行 |

### SQL Server 參與者
可以管理 SQL Server 和資料庫，但是無法管理它們的安全性相關原則

| **動作** ||
| ------- | ------ |
| Microsoft.Sql/servers/* | 建立和管理 SQL Server |
| Microsoft.Authorization/*/read | 讀取授權|
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |
| **非動作** | |
| Microsoft.Sql/servers/auditingPolicies/* | 無法編輯 SQL Server 稽核原則 |
| Microsoft.Sql/servers/databases/auditingPolicies/* | 無法編輯 SQL Server 資料庫稽核原則 |
| Microsoft.Sql/servers/databases/connectionPolicies/* | 無法編輯 SQL Server 資料庫連接原則 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | 無法編輯 SQL Server 資料庫資料遮罩原則 |
| Microsoft.Sql/servers/databases/securityMetrics/* | 無法編輯 SQL Server 資料庫安全性度量 |

### 傳統儲存體帳戶參與者
可以管理傳統儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/* | 建立及管理儲存體帳戶 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resources/read | 讀取訂閱資源 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 儲存體帳戶參與者
可以管理儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/* | 建立及管理儲存體帳戶 |
| Microsoft.Authorization/*/read | 讀取所有授權 |
| Microsoft.Resources/subscriptions/resources/read | 讀取訂閱資源 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 使用者存取系統管理員
可以管理 Azure 資源的使用者存取

| **動作** ||
| ------- | ------ |
| */read | 讀取密碼以外的所有類型資源。 | | Microsoft.Authorization/* | 讀取授權 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 傳統虛擬機器參與者
可以管理傳統虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.ClassicStorage/storageAccounts/read | 讀取傳統儲存體帳戶 |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |
| Microsoft.ClassicStorage/storageAccounts/disks/read | 讀取儲存體帳戶磁碟 |
| Microsoft.ClassicStorage/storageAccounts/images/read | 讀取儲存體帳戶映像 |
| Microsoft.ClassicNetwork/virtualNetworks/read | 讀取虛擬網路 |
| Microsoft.ClassicNetwork/reservedIps/read | 讀取保留的 IP 位址 |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | 加入虛擬網路 |
| Microsoft.ClassicNetwork/reservedIps/link/action | 連結保留 IP |
| Microsoft.ClassicCompute/domainNames/* | 建立和管理傳統運算網域名稱 |
| Microsoft.ClassicCompute/virtualMachines/* | 建立和管理虛擬機器 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 虛擬機器參與者
可以管理虛擬機器，但是無法管理它們連接的虛擬網路或儲存體帳戶

| **動作** ||
| ------- | ------ |
| Microsoft.Storage/storageAccounts/read | 讀取儲存體帳戶 |
| Microsoft.Storage/storageAccounts/listKeys/action | 列出儲存體帳戶金鑰 |
| Microsoft.Network/virtualNetworks/read | 讀取虛擬網路 |
| Microsoft.Network/virtualNetworks/subnets/join/action | 加入虛擬網路子網路 |
| Microsoft.Network/loadBalancers/read | 讀取負載平衡器 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | 加入負載平衡器後端位址集區 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | 加入負載平衡器輸入 NAT 規則 |
| Microsoft.Network/publicIPAddresses/read | 讀取網路公用 IP 位址 |
| Microsoft.Network/publicIPAddresses/join/action | 加入網路公用 IP 位址 |
| Microsoft.Network/networkSecurityGroups/read | 讀取網路安全性群組 |
| Microsoft.Network/networkSecurityGroups/join/action | 加入網路安全性群組 |
| Microsoft.Network/networkInterfaces/* | 建立和管理網路介面 |
| Microsoft.Network/locations/* | 建立和管理網路位置 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | 加入網路應用程式閘道器後端位址集區 |
| Microsoft.Compute/virtualMachines/* | 建立和管理虛擬機器 |
| Microsoft.Compute/availabilitySets/* | 建立和管理運算可用性集合 |
| Microsoft.Compute/locations/* | 建立和管理運算位置 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 傳統網路參與者
可以管理傳統虛擬網路和保留 IP

| **動作** ||
| ------- | ------ |
| Microsoft.ClassicNetwork/* | 建立和管理傳統網路 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### Web 方案參與者
可以管理 Web 方案

| **動作** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/* | 建立和管理伺服器陣列 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |

### 網站參與者
可以管理網站，但是不能管理它們連接的 Web 方案

| **動作** ||
| ------- | ------ |
| Microsoft.Web/serverFarms/read | 讀取伺服器陣列 |
| Microsoft.Web/serverFarms/join/action | 加入伺服器陣列 |
| Microsoft.Web/sites/* | 建立和管理網站 |
| Microsoft.Web/certificates/* | 建立和管理網站憑證 |
| Microsoft.Web/listSitesAssignedToHostName/read | 讀取指派給主機名稱的網站 |
| Microsoft.Authorization/*/read | 讀取授權 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 讀取訂用帳戶資源群組 |
| Microsoft.Resources/subscriptions/resourceGroups/resources/read | 讀取訂用帳戶資源群組資源 |
| Microsoft.Resources/subscriptions/resourceGroups/deployments/* | 建立和管理訂用帳戶資源群組部署 |
| Microsoft.Insights/alertRules/* | 建立和管理 Insights 警示規則 |
| Microsoft.Support/* | 建立和管理支援票證 |
| Microsoft.Insights/components/* | 建立和管理 Insights 元件 |

## RBAC 主題
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!-------HONumber=AcomDC_1210_2015--->