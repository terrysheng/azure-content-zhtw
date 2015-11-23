<properties 
    pageTitle="Azure 自動化中的原始檔控制整合 | Microsoft Azure"
    description="本文說明在 Azure 自動化中與 GitHub 的原始檔控制整合。"
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="11/04/2015"
    ms.author="sngun" />

# Azure 自動化中的原始檔控制整合

原始檔控制整合可讓您將自動化帳戶中的 Runbook 和 PowerShell 指令碼關聯至 GitHub 原始檔控制儲存機制。它提供的環境可讓您輕鬆地與小組共同作業、追蹤變更，以及回復至舊版的 Runbook。例如，原始檔控制可讓您將不同的分支同步處理至您的開發、測試或生產自動化帳戶，有助於將程式碼提升至已在開發環境中測試過的生產環境。您可以將程式碼從 Azure 自動化推送至原始檔控制，或將 Runbook 從原始檔控制提取至 Azure 自動化。本文說明如何在 Azure 自動化環境中設定原始檔控制。我們一開始會建立 GitHub 儲存機制並逐步設定 Azure 自動化，以存取您的 GitHub 儲存機制。


>[AZURE.NOTE]原始檔控制支援提取和推送 [PowerShell 工作流程 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 以及 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)，但尚未支援[圖形化 Runbook](automation-runbook-types.md#graphical-runbooks)。<br><br>


為您的自動化帳戶設定原始檔控制時，必須執行兩個簡單的步驟，而如果您已經有 GitHub 帳戶，則只需要執行一個步驟。如下：
## 步驟 1：建立 GitHub 儲存機制

如果您已經有想要連結到 Azure 自動化的 GitHub 帳戶和儲存機制，則登入您現有的帳戶並從下面的步驟 2 開始執行。否則，瀏覽至 [GitHub](https://github.com/)，註冊新的帳戶以及[建立新的儲存機制](https://help.github.com/articles/create-a-repo/)。


## 步驟 2：設定原始檔控制

* 從 Azure Preview 入口網站中的 [自動化帳戶] 刀鋒視窗，按一下 [設定原始檔控制]。<br> ![設定原始檔控制](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
* [原始檔控制] 刀鋒視窗隨即開啟，您可以在其中設定您的 GitHub 帳戶詳細資料。以下是要設定的參數清單：<br>

|參數 |說明 |
|:---|:---| 
|選擇原始檔 | 選取原始檔。目前只支援**GitHub**。 |
|Authorization | 按一下 [授權] 按鈕，授與 GitHub 儲存機制的 Azure 自動化存取權。如果您已在不同的視窗中登入您的 GitHub 帳戶，則會使用該帳戶的認證。成功授權之後，刀鋒視窗會在 [授權屬性] 之下顯示您的 GitHub 使用者名稱。 |
|選擇儲存機制 | 從可用的儲存機制清單中選取 GitHub 儲存機制。 |
|選擇分支 | 從可用的分支清單中選取分支。如果您尚未建立任何分支，只會顯示 **master** 分支。 |
|Runbook 資料夾路徑 | Runbook 資料夾路徑可指定 GitHub 儲存機制中的路徑，以便您從中推送或提取程式碼。它必須以 **/foldername/subfoldername** 格式輸入。只有 Runbook 資料夾路徑中的指令碼會同步處理至您的自動化帳戶。Runbook 資料夾路徑的子資料夾中的指令碼**不會**進行同步處理。使用 **/** 來同步處理儲存機制下的所有 PowerShell 指令碼。 |


例如，如果您有名為 **PowerShellScripts** 的儲存機制，其中包含名為 **RootFolder** 的資料夾，而該資料夾內含名為 **SubFolder** 的資料夾。如果所有資料夾與儲存機制包含您要同步處理的 Runbook，則：

  1. 若要從 **repository** 同步處理 Runbook，則 Runbook 資料夾路徑為 */*
  2. 若要從 **RootFolder** 同步處理 Runbook，則 Runbook 資料夾路徑為 */RootFolder*
  3. 若要從 **SubFolder** 同步處理 Runbook，則 Runbook 資料夾路徑為 */RootFolder/SubFolder*。<br>

* 設定參數之後，它們會顯示在 [設定原始檔控制] 刀鋒視窗上。

![設定刀鋒視窗](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


* 原始檔控制整合現已針對您的自動化帳戶設定，而且應以您的 GitHub 資訊進行更新，您現在可按一下此部分來檢視所有原始檔控制同步處理工作歷程記錄。<br> ![儲存機制的值](media/automation-source-control-integration/automation_03_RepoValues.png)

* 設定原始檔控制之後，將會在您的自動化帳戶中建立下列自動化資源：

建立兩個 [變數資產](automation-variables.md)。
      

  * **Microsoft.Azure.Automation.SourceControl.Connection** 變數包含連接字串的值，如下所示。<br>

|參數 |值 |
|:---|:---|
| 名稱 | Microsoft.Azure.Automation.SourceControl.Connection |
| 類型 | String |
| 值 | {"Branch":<*您的分支名稱*>,"RunbookFolderPath":<*Runbook 資料夾路徑*>,"ProviderType":<*GitHub 的值為 1*>,"Repository":<*您的儲存機制名稱*>,"Username":<*您的 GitHub 使用者名稱*>} |


  * **Microsoft.Azure.Automation.SourceControl.OauthToken** 變數包含 OAuthToken 的安全加密值。<br>

|參數 |值 |
|:---|:---|
| 名稱 | Microsoft.Azure.Automation.SourceControl.OauthToken |
| 類型 | Unknown(Encrypted) |
| 值 | <*已加密的 OAuthToken*> |

![變數](media/automation-source-control-integration/automation_04_Variables.png)

**自動化原始檔控制**已當作已授權的應用程式加入至您的 GitHub 帳戶。若要檢視應用程式：從 GitHub 首頁，瀏覽至您的 [設定檔] > [設定] > [應用程式]。此應用程式可讓 Azure 自動化將 GitHub 儲存機制同步至自動化帳戶。

![Git 應用程式](media/automation-source-control-integration/automation_05_GitApplication.png)


## 在自動化中使用原始檔控制


### 從 Azure 自動化將 Runbook 簽入至原始檔控制

Runbook 簽入可讓您將對 Runbook 所做的變更推送至原始檔控制儲存機制。以下是簽入 Runbook 的步驟：

* 從您的自動化帳戶，[建立新的文字 Runbook](automation-first-runbook-textual.md)，或[編輯現有的文字 Runbook](automation-edit-textual-runbook.md)。此 Runbook 可以是 PowerShell 工作流程或 PowerShell 指令碼 Runbook。  
* 編輯 Runbook 之後，加以儲存並按一下 [編輯] 刀鋒視窗中的 [簽入]。![簽入按鈕](media/automation-source-control-integration/automation_06_CheckinButton.png)


 >[AZURE.NOTE]從 Azure 自動化簽入會覆寫原始檔控制中目前存在的程式碼。要簽入的 Git 對等命令列指示為 **git add + git commit + git push**

* 當您按一下 [簽入] 時，將會出現一個訊息，按一下 [是] 繼續進行。![簽入訊息](media/automation-source-control-integration/automation_07_CheckinMessage.png)
* 簽入會啟動 Runbook：**Sync-MicrosoftAzureAutomationAccountToGitHubV1**。此 Runbook 會連接到 GitHub 並將 Azure 自動化中的變更推送至您的儲存機制。若要檢視簽入工作歷程記錄，請回到 [原始檔控制整合] 索引標籤，按一下以開啟 [儲存機制同步處理] 刀鋒視窗。此刀鋒視窗會顯示所有的原始檔控制工作。選取您要檢視的工作並按一下以檢視詳細資料。![簽入 Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
* 簽入工作如有任何錯誤，工作狀態應為 [暫止] ，而您可以在工作刀鋒視窗中檢視更多錯誤詳細資訊。[所有記錄檔] 部分會顯示與該工作相關聯的所有 PowerShell 串流。這會提供可協助您修正問題的詳細資料。  
* 修改過的 Runbook 名稱會當作輸入參數傳送至簽入 Runbook。在 [儲存機制同步處理] 刀鋒視窗中展開 Runbook，即可[檢視工作詳細資料](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal)。![簽入輸入](media/automation-source-control-integration/automation_09_CheckinInput.png)
* 在工作完成時重新整理您的 GitHub 儲存機制，即可檢視變更。您的儲存機制中應有一項認可，其認可訊息為：**已在 Azure 自動化中更新*Runbook 名稱*。**  



### 將原始檔控制中的 Runbook 同步處理至 Azure 自動化 

[Runbook 同步處理] 刀鋒視窗上的 [同步處理] 按鈕可讓您將儲存機制的 Runbook 資料夾路徑中的所有 Runbook 提取至您的自動化帳戶。相同的儲存機制可以同步處理至多個自動化帳戶。以下是同步處理 Runbook 的步驟：

* 從您設定原始檔控制的自動化帳戶，開啟 [原始檔控制整合/儲存機制同步處理] 刀鋒視窗並按一下 [同步處理]，然後會顯示一則訊息時，請按一下 [是] 繼續進行。![同步處理按鈕](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
* 同步處理會啟動 Runbook：**Sync-MicrosoftAzureAutomationAccountFromGitHubV1**。此 Runbook 會連接到 GitHub 並將儲存機制中的變更提取至 Azure 自動化。若要檢視 Runbook，請回到 [原始檔控制整合/儲存機制同步處理] 刀鋒視窗並加以展開。![同步處理 Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
>[AZURE.NOTE]從原始檔控制進行的同步處理會針對目前在原始檔控制中的**所有** Runbook，覆寫目前存在於您的自動化帳戶中的 Runbook 草稿版本。要同步處理的 Git 對等命令列指示為 **git pull**


## 中斷原始檔控制的連線

若要中斷與 GitHub 帳戶的連線，請開啟 [儲存機制同步處理] 刀鋒視窗，然後按一下 [中斷連線]。一旦中斷與原始檔控制的連線，先前同步處理的 Runbook 仍會保留在您的自動化帳戶中，但不會啟用 [儲存機制同步處理] 刀鋒視窗。![中斷連線按鈕](media/automation-source-control-integration/automation_12_Disconnect.png)



## 後續步驟

如需原始檔控制整合的詳細資訊，請參閱下列資源：- [Azure 自動化：Azure 自動化中的原始檔控制整合](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [選出您最愛的原始檔控制系統](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Azure 自動化：使用 Visual Studio Online 整合 Runbook 原始檔控制](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=Nov15_HO3-->