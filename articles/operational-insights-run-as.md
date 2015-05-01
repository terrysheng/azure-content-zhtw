<properties 
   pageTitle="Operational Insights 的 Operations Manager 執行身分帳戶"
   description="了解如何設定與 Operational Insights 搭配使用的 Operations Manager 執行身分帳戶"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Operational Insights 的 Operations Manager 執行身分帳戶

Microsoft Azure Operational Insights 會使用 Operations Manager 代理程式及管理群組，收集資料並將資料傳送給 Operational Insights 服務。Operational Insights 會建立工作負載的管理套件以提供加值服務。每個工作負載都需要具有特定的工作負載權限，才能在不同的安全性內容中執行管理套件，例如網域帳戶。您需要藉由設定 Operations Manager 執行身分帳戶來提供認證資訊。

下列章節描述如何為下列工作負載設定 Operations Manager 執行身分帳戶：

- SQL 評估
- Virtual Machine Manager
- Lync Server
- SharePoint

## 設定 SQL 評估的執行身分帳戶

 如果您已經在使用 SQL Server 管理套件，您應該使用該執行身分帳戶。

### 在 Operations 主控台中設定 SQL 執行身分帳戶

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights SQL 評估執行身分設定檔**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 SQL Server 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。
	>[AZURE.NOTE] 執行身分帳戶類型必須是 Windows。執行身分帳戶也必須屬於裝載 SQL Server 執行個體的所有 Windows 伺服器上的本機系統管理員群組。

5. 按一下 [儲存]。

6. 修改，然後在每個 SQL Server 執行個體上執行下列 T-SQL 範例，授與執行身分帳戶所需的最小權限授以執行 SQL 評估。不過，如果執行身分帳戶已是 SQL Server 執行個體上 sysadmin 伺服器角色的一部分，您就不需要這樣做。

---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master
    
    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS
    
    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]
    
    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

### 使用 Windows PowerShell 設定 SQL 執行身分帳戶

以您的資訊更新它之後，開啟 PowerShell 視窗並執行下列指令碼：

    
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account

## 設定 Virtual Machine Manager 的執行身分帳戶

請確定執行身分帳戶擁有下列動作的權限：

- 使用 VMM Windows PowerShell 模組

- 查詢 VMM 資料庫

- 從遠端管理虛擬化主機上執行的 VMM 代理程式

將 Operational Insights 連線到 Operations Manager 時，使用下列步驟來設定此帳戶。

### 設定 VMM 的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights VMM 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 VMM 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。
	>[AZURE.NOTE] 執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。


## 設定 Lync Server 的執行身分帳戶

 執行身分帳戶必須是本機系統管理員和 Lync RTCUniversalUserAdmins 安全性群組的成員。

### 設定 Lync 帳戶的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights Lync 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取同時為本機系統管理員和 Lync RTCUniversalUserAdmins 安全性群組成員的 Windows 執行身分帳戶。 
	>[AZURE.NOTE] 執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。


## 為 SharePoint 設定執行身分帳戶


### 設定 SharePoint 帳戶的認證

1. 在 Operations Manager 中開啟 Operations 主控台，然後按一下 [**管理**]。

2. 在 [**執行身分組態**] 下，按一下 [**設定檔**]，並開啟 [**Operational Insights SharePoint 執行身分帳戶**]。

3. 在 [**執行身分帳戶**] 頁面上，按一下 [**新增**]。

4. 選取包含 SharePoint 所需認證的 Windows 執行身分帳戶，或按一下 [**新增**] 建立一個。 
	>[AZURE.NOTE] 執行身分帳戶類型必須是 Windows。

5. 按一下 [儲存]。



<!--HONumber=52-->