<properties
   pageTitle="Azure 自動化混合式 Runbook 背景工作"
   description="本文提供有關安裝和使用混合式 Runbook 背景工作的相關資訊，它是 Azure 自動化的一項功能，可讓您在本機資料中心內的機器上執行 Runbook。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/17/2015"
   ms.author="bwren" />

# Azure 自動化混合式 Runbook 背景工作

Azure 自動化中的 Runbook 無法存取您的本機資料中心中的資源，因為其在 Azure 雲端中執行。Azure 自動化的混合式 Runbook 背景工作功能可讓您在位於資料中心的機器上執行 Runbook，以管理本機資源。Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至執行所在的一或多個內部部署機器。

下圖說明這項功能。

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-overview.png)

您可以指定您的資料中心中的一或多部電腦，以做為混合式 Runbook 背景工作，然後從 Azure 自動化執行 Runbook。每一個背景工作角色都需要 Microsoft Management Agent 能夠連接 Microsoft Operations Management Suite 和 Azure 自動化 Runbook 環境。Operations Management Suite 僅用來安裝及維護管理代理程式，以及監視背景工作角色的功能。Runbook 的傳遞和執行的指示都是透過 Azure 自動化執行。

![混合式 Runbook 背景工作元件](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-components.png)

>[AZURE.NOTE]Operational Insights 目前正在整合到 Operations Management Suite 中，您可能會看到入口網站和文件中使用任一名稱。

沒有輸入防火牆需求可支援混合式 Runbook 背景工作。本機電腦上的代理程式會起始與雲端中 Azure 自動化的所有通訊。啟動 Runbook 時，Azure 自動化會建立代理程式會擷取的指示。代理程式接著會在執行之前取得 Runbook 和任何參數。它也會從 Azure 自動化擷取 Runbook 使用的任何[資產](http://msdn.microsoft.com/library/dn939988.aspx)。

## 混合式 Runbook 背景工作群組

每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。

在混合式 Runbook 背景工作上啟動 Runbook 時，您會指定將執行的群組。群組的成員將會決定哪一個背景工作將會服務要求。您無法指定特定的背景工作。

## Hybrid Runbook Worker 的需求

您必須指定至少一台內部部署電腦，才能執行混合式 Runbook 工作。這台電腦必須安裝下列軟體：

- Windows Server 2012 或更新版本
- Windows PowerShell 4.0 或更新版本

針對混合式背景工作角色，請考慮下列建議：

- 在每個群組中指定多個混合式背景工作角色，以保留較高的可用性。  
- 混合式背景工作角色可以與 Service Management Automation 或 System Center Orchestrator Runbook 伺服器並存。
- 請考慮使用實際位於或接近自動化帳戶所在區域的電腦，因為當工作完成時，工作資料會送回到 Azure 自動化。

## 安裝混合式 Runbook 背景工作
下列程序描述如何安裝和設定 Hybrid Runbook Worker。對您的自動化環境執行一次前兩個步驟，再對每一台背景工作角色電腦重複其餘步驟。

### 1\.建立 Operations Management Suite 工作區
如果尚無 Operations Management Suite 工作區，請使用[設定Operational Insights 工作區](../operational-insights/operational-insights-onboard-in-minutes.md)中的指示建立此工作區。如果您已經有工作區，可以使用現有的工作區。

### 2\.將自動化解決方案加入至 Operations Management Suite 工作區
解決方案會將功能加入至 Operations Management Suite。自動化解決方案會增加 Azure 自動化的功能，包括支援 Hybrid Runbook Worker。將解決方案加入至工作區時，它會自動將背景工作角色元件往下推送給您在下一步將安裝的代理程式電腦。

請依照[使用 [解決方案資源庫] 新增解決方案](../operational-insights/operational-insights-setup-workspace.md#1-add-solutions)中的指示，將**自動化**解決方案加入至 Operations Management Suite 工作區。

### 3\.安裝 Microsoft Management Agent
Microsoft Management Agent 可將電腦連線至 Operations Management Suite。將代理程式安裝在內部部署電腦，並連接到您的工作區時，它會自動下載 Hybrid Runbook Worker 所需的元件。

請依照[將電腦直接連線到 Operational Insights](../operational-insights/operational-insights-direct-agent.md) 中的指示，將代理程式安裝在內部部署電腦上。您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式成功連接到 Operations Management Suite 時，它會列在 Operations Management Suite [設定] 窗格的 [已連接的來源] 索引標籤上。當 C:\\Program Files\\Microsoft Monitoring Agent\\Agent 下出現 **AzureAutomationFiles** 資料夾時，就可確認代理程式已正確下載自動化解決方案。

### 4\.安裝 Runbook 環境並連接到 Azure 自動化
將代理程式加入至 Operations Management Suite 時，自動化解決方案會往下推送包含 **Add-HybridRunbookWorker** Cmdlet 的 **HybridRegistration** PowerShell 模組。您可以使用這個 Cmdlet 在電腦上安裝 Runbook 環境並向 Azure 自動化進行註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

	cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\5.2.20826.0\HybridRegistration"
	Import-Module HybridRegistration.psd1


然後使用下列語法執行 **Add-HybridRunbookWorker** Cmdlet：

	Add-HybridRunbookWorker –Name <String> -EndPoint <Url> -Token <String>

你可以從 Azure Preview 入口網站的 [管理金鑰] 刀鋒視窗取得這個 Cmdlet 所需的資訊。在自動化帳戶的 [元素] 面板按一下金鑰圖示，可開啟此刀鋒視窗。

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

- **名稱**是混合式 Runbook 背景工作群組的名稱。如果自動化帳戶中已有這個群組，那麼會將目前的電腦加入。如果尚不存在，則會加入。
- **EndPoint** 是 [管理金鑰] 刀鋒視窗中的 [URL] 欄位。
- **權杖**是 [**管理金鑰**] 刀鋒視窗中的**主要存取金鑰**。  

在 **Add-HybridRunbookWorker** 中使用 **-Verbose** 參數可接收安裝的詳細資訊。

### 5\.安裝 PowerShell 模組
Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。不過，這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。例外狀況是預設安裝的 Azure 模組，可提供 Azure 自動化所有 Azure 服務和活動的 Cmdlet 存取權。

由於「混合式 Runbook 背景工作」功能的主要目的是要管理本機資源，您很可能必須安裝支援這些資源的模組。您可以參考[安裝模組](http://msdn.microsoft.com/library/dd878350.aspx)來取得安裝 Windows PowerShell 模組的詳細資訊。

## 移除 Hybrid Runbook Worker

您可以在內部部署電腦上執行 **Remove-HybridRunbookWorker** Cmdlet，即可從該電腦上移除 Hybrid Runbook Worker。使用 **-Verbose** 參數可取得移除程序的詳細記錄。

## 在混合式 Runbook 背景工作上啟動 Runbook

[在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md) 描述啟動 Runbook 的不同方法。混合式 Runbook 背景工作加入了 **RunOn** 選項，您可以在其中指定混合式 Runbook 背景工作群組的名稱。如果未指定群組，則會擷取 Runbook，且由該群組中的背景工作執行。如果未指定此選項，則會正常在 Azure 自動化中執行。

在 Azure Preview 入口網站中啟動 Runbook 時，您會看到**執行於**選項，您可以在此選取 [**Azure**] 或 [**混合式背景工作**]。如果您選取 [**混合式背景工作**]，則您可以從下拉式清單中選取群組。

使用 **RunOn** 參數。您可以使用下列命令使用 Windows PowerShell 在混合式 Runbook 背景工作群組上啟動名為 Test-Runbook 的 Runbook。

	Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

>[AZURE.NOTE]**RunOn** 參數已加入至 0.9.1 版的 Microsoft Azure PowerShell 的 **Start-AzureAutomationRunbook** Cmdlet。如果您安裝較早的版本，您應該[下載最新版本](http://azure.microsoft.com/downloads)。您只需要在將會從 Windows PowerShell 啟動 Runbook 的工作站上安裝此版本。您不需要將它安裝在背景工作電腦上，除非您想要從該電腦啟動 Runbook。您目前無法在混合式 Runbook 背景工作上從另一個 Runbook 啟動 Runbook，因為這項作業需要在您的自動化帳戶中安裝最新版本的 Azure PowerShell。Azure 自動化中將自動更新為最新版本，並且很快將其自動往下推送到背景工作。

>[AZURE.NOTE]Hybrid Runbook Worker 只能在[圖形化和 PowerShell 工作流程 Runbook](automation-runbook-types.md) 上執行。您目前無法在 Hybrid Runbook Worker 上啟動 [PowerShell Runbook](automation-runbook-types.md)。

## 在 Hybrid Runbook Worker 上進行 Runbook 疑難排解

[Runbook 輸出和訊息](automation-runbook-output-and-messages.md)會從混合式背景工作角色傳送到 Azure 自動化，就像雲端中執行的 Runbook 工作一樣。您也可以啟用詳細資訊和進度資料流，就像您在其他 Runbook 中的作法一樣。

記錄檔儲存每一個混合式背景工作角色本機的 C:\\ProgramData\\Microsoft\\System Center\\Orchestrator\\7.2\\SMA\\Sandboxes 中。


## 為混合式 Runbook 背景工作建立 Runbook

在 Azure 自動化和混合式 Runbook 背景工作上執行的 Runbook 結構中沒有任何差異。您對每個使用的 Runbook 可能會有顯著差異，不過由於混合式 Runbook 背景工作的 Runbook 通常會管理資料中心內的本機資源，而 Azure 自動化中的 Runbook 通常管理 Azure 雲端中的資源。

### Runbook 權限

Runbook 將在混合式 Runbook 背景工作上的本機系統帳戶內容中執行，因此，它們必須對將會存取的資源提供自己的驗證。它們不能使用相同的[通常用於 Runbook 對 Azure 資源驗證的方法](automation-configuring.md#configuring-authentication-to-azure-resources)，因為它們將存取 Azure 外部的資源。

您可以在您的 Runbook 中使用[認證](http://msdn.microsoft.com/library/dn940015.aspx)和[憑證](http://msdn.microsoft.com/library/dn940013.aspx)資產，搭配可讓您指定的認證的 Cmdlet，使得您可以向不同的資源驗證。下列範例顯示會重新啟動電腦的 Runbook 的一部分。它會從認證資產擷取認證和從變數資產擷取電腦的名稱，然後使用這些值搭配 Restart-Computer Cmdlet。

	$Cred = Get-AutomationCredential "MyCredential"
	$Computer = Get-AutomationVariable "ComputerName"

	Restart-Computer -ComputerName $Computer  -Credential $Cred

您也可以運用 [InlineScript](automation-powershell-workflow.md#inline-script)，它可讓您利用 [PSCredential 一般參數](http://technet.microsoft.com/library/jj129719.aspx)指定的認證在另一部電腦上執行程式碼區塊。


### 編寫和測試 Runbook

您可以在 Azure 自動化中編輯混合式 Runbook 背景工作的 Runbook，但如果您嘗試在編輯器中測試 Runbook，可能會遇到困難。存取本機資源的 PowerShell 模組可能未安裝在 Azure 自動化環境中，在此情況下，測試就會失敗。如果您已安裝所需的模組，就會執行 Runbook，但它不能存取本機資源以進行完整測試。

## 與 Service Management Automation 的關聯性

[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) 是 Windows Azure Pack (WAP) 的一項元件，可讓您在本機資料中心內執行 Azure 自動化支援的相同 Runbook。不同於 Azure 自動化，SMA 需要本機安裝，其中包含 Windows Azure Pack 管理入口網站和資料庫，用以保存 Runbook 和 SMA 組態。Azure 自動化在雲端中提供這些服務，並只要求您在本機環境中維護混合式 Runbook 背景工作。

如果您是現有 SMA 使用者，可以將您的 Runbook 移至 Azure 自動化，以搭配混合式 Runbook 背景工作使用，而不必變更，假設他們已依[為混合式 Runbook 背景工作建立 Runbook](#creating-runbooks-for-hybrid-runbook-worker) 中所述，向資源進行驗證 。SMA 中的 Runbook 會在背景工作伺服器上服務帳戶的內容中執行，其可能為 Runbook 提供該驗證。

您可以使用下列準則來判斷 Azure 自動化搭配混合式 Runbook 背景工作或 Service Management Automation 更適合您的需求。

- SMA 需要本機安裝 Windows Azure Pack，其較 Azure 自動化具有更高的本機資源和維護成本，後者只需要在本機 Runbook 背景工作上安裝代理程式。代理程式由 Operations Management Suite 管理，進一步減少維護成本。
- Azure 自動化會在雲端中儲存其 Runbook，並提供它們至內部部署混合式 Runbook 背景工作。如果您的安全性原則不允許這種行為，您應該使用 SMA。
- Windows Azure Pack 可免費下載，而 Azure 自動化可能需支付訂用帳戶費用。Azure。必須為 SMA 維護多個資料庫。
- Azure 自動化搭配混合式 Runbook 背景工作，可讓您在一個位置管理雲端資源的 Runbook 和本機資源，與個別管理 Azure 自動化和 SMA 相對。
- Azure 自動化具備的進階功能包括 SMA 中並未提供的圖形編寫。


## 相關文章

- [在 Azure 自動化中啟動 Runbook](automation-starting-a-runbook.md)
- [在 Azure 自動化中編輯 Runbook](https://msdn.microsoft.com/library/dn879137.aspx)
 

<!---HONumber=Oct15_HO3-->