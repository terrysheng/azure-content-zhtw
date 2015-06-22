<properties 
   pageTitle="connect-scom" 
   description="將電腦直接連線到 Operational Insights" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# 將電腦直接連線到 Operational Insights 

您可以在要連線的每一部電腦上安裝 Operational Insights 代理程式，將電腦直接連線到 Operational Insights。 


## 下載、安裝及設定代理程式
### 下載代理程式安裝檔案
1. 在 [**Operations Insights 入口網站**] 的 [**概觀**] 頁面中，按一下 [**伺服器和使用方式**]。
1. 在 [**伺服器直接連線**] 下，按一下 [**設定**]。
1. 在 [**加入代理程式**] 旁，按一下 [代理程式] 連結以下載安裝檔案。
1. 在 [**工作區的主金鑰**] 方塊中，選取並複製金鑰 (CTRL+ C)。


### 使用安裝程式安裝代理程式
1. 在您想要管理的電腦上執行安裝程式以安裝代理程式。
1. 選取 [**將代理程式連線至 Microsoft Azure Operational Insights**]，然後按 [**下一步**]。
1. 出現提示時，輸入您在步驟 4 中複製的資訊。
1. 完成時，[**Microsoft 管理代理程式**] 會出現在 [**控制台**] 中。

### 使用命令列安裝代理程式
修改，然後搭配使用下列範例與命令列來安裝代理程式。

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## 重新設定 Microsoft Monitoring Agent (選擇性)
您可以使用下列資訊，讓代理程式直接與 Microsoft Azure Operational Insights 服務通訊。設定代理程式之後，它會註冊代理程式服務，並將獲得所需的組態資訊及包含智慧組件資訊的管理組件。

從代理程式所監視的電腦收集資料之後，受監視的電腦數目會出現在 [**直接連線的代理程式**] 下 [使用方式] 頁面上的 Operational Insights 入口網站。對於傳送資料的任何電腦，您可以在 Operational Insights 入口網站中檢視其資料和評估資訊。

您也可以停用代理程式 (如有需要)，或使用命令列或指令碼加以啟用。

### 設定代理程式
- 安裝 **Microsoft Monitoring Agent** 後，開啟 [**控制台**]。
- 開啟 Microsoft Monitoring Agent，然後按一下 [Azure Operational Insights] 索引標籤。
- 選取 [**連線至 Azure Operational Insights**]。
- 在 [**工作區識別碼**] 方塊中，輸入 Operational Insights 入口網站中提供的工作區識別碼。
- 在 [帳戶金鑰] 方塊中，貼上安裝代理程式時所複製的 [**工作區的主金鑰**]，然後按一下 [**確定**]。

### 停用代理程式
- 安裝代理程式之後，開啟 [**控制台**]。
- 開啟 Microsoft Monitoring Agent，然後按一下 **[Azure Operational Insights]** 索引標籤。
- 清除至 **Azure Operational Insights** 的連線。

### 使用命令列或指令碼啟用代理程式
您可以參考下列範例來使用 Windows PowerShell 或 VB 指令碼。
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### 設定 Proxy 和防火牆設定 (選擇性)
如果您的環境中有限制存取網際網路的 Proxy 伺服器或防火牆，您可能需要依照下列程序，讓 Operations Manager 和/或代理程式與 Operational Insights 服務進行通訊 



- [設定 Proxy 和防火牆設定 (選擇性)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## 相關內容

- [部落格文章：將伺服器直接連線到 Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [部落格文章：啟用 Azure 虛擬機器的 Operational Insights](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52--> 