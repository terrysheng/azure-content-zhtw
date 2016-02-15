<properties 
   pageTitle="Azure 自動化中常見錯誤的疑難排解秘訣 | Microsoft Azure"
   description="本文提供您在使用 Azure 自動化時可能會遇到之常見錯誤的基本疑難排解步驟。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" 
   tags="top-support-issue"/>
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="sngun; v-reagie"/>

# Azure 自動化中常見錯誤的疑難排解秘訣

當您在使用自動化資源 (例如 Runbook、模組和自動化資產) 時遇到問題，您需要找出哪裡出錯。本文將說明一些您在使用 Azure 自動化時可能會遇到的一些常見錯誤，並且建議可能的補救步驟。

## 對使用 Azure 自動化 Runbook 時的驗證錯誤進行疑難排解  


**案例：登入 Azure 帳戶失敗**

**錯誤：**使用 Add-AzureAccount 或 Login-AzureRmAccount Cmdlet 時，收到錯誤「Unknown\_user\_type：未知的使用者類型」。

**疑難排解秘訣：**為了判斷錯誤原因，請使用下列步驟：

1. 請確定您沒有任何特殊字元，包括用來連接到 Azure 的自動化認證資產名稱中的 **@** 符號。  

2. 請檢查您可以使用儲存在本機 PowerShell ISE 編輯器中的 Azure 自動化認證的使用者名稱和密碼。您可以藉由在 PowerShell ISE 中執行下列 Cmdlet 來完成這項操作：

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount – Credential $Cred

3. 如果您的驗證在本機失敗，這表示您尚未正確設定 Azure Active Directory 認證。請參閱[使用 Azure Active Directory 對 Azure 進行驗證](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)部落格文章，以正確設定 Active Directory 帳戶。


**案例：找不到 Azure 訂用帳戶**

**錯誤：**使用 Select-AzureSubscription 或 Select-AzureRmSubscription Cmdlet 時，收到錯誤「找不到名為 ``<subscription name>`` 的訂用帳戶」。
 
**疑難排解秘訣：**為了判斷您是否已正確地驗證至 Azure，並且具有您嘗試選取之訂用帳戶的存取權，請執行下列步驟：

1. 請確定您在執行 **Select-AzureSubscription** Cmdlet 之前執行 **Add-AzureAccount**。  

2. 如果您仍然看到此錯誤訊息，修改您的程式碼，方法是在 **Add-AzureAccount** Cmdlet 之後加上 **Get-AzureSubscription** Cmdlet，然後執行程式碼。現在，請確認 Get-AzureSubscription 的輸出是否包含您的訂用帳戶詳細資料。
    * 如果您在輸出中未看到任何訂用帳戶詳細資料，這表示訂用帳戶尚未初始化。  
    * 如果您在輸出中看到訂用帳戶詳細資料，請使用 **Select-AzureSubscription** Cmdlet 確認您使用正確的訂用帳戶名稱或 ID。   



**案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證**

**錯誤：**使用您的 Azure 使用者名稱和密碼對 Azure 進行驗證時，收到錯誤「Add-AzureAccount：AADSTS50079：需要強式驗證註冊 (proof-up)」。

**錯誤的原因：**如果在您的 Azure 帳戶有多重要素驗證，您無法使用 Active Directory 使用者來向 Azure 驗證。相反地，您必須使用憑證或服務主體來向 Azure 驗證。

**疑難排解秘訣：**若要搭配使用 Azure 服務管理 Cmdlet 與憑證，請參閱[建立和新增憑證以管理 Azure 服務](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要搭配使用服務主體與 Azure 資源管理員 Cmdlet，請參閱[使用 Azure 入口網站建立服務主體](./resource-group-create-service-principal-portal.md)和[使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)。



## 對使用 Runbook 時的常見錯誤進行疑難排解 

**案例：執行 Runbook 時無法繫結參數**

**錯誤：**您的 Runbook 失敗且具有錯誤「無法繫結參數 ``<ParameterName>``。無法將類型還原序列化 ``<ParameterType>`` 的 ``<ParameterType>`` 值轉換為類型 ``<ParameterType>``」。

**錯誤的原因：**如果您的 Runbook 是 PowerShell 工作流程，它會以還原序列化格式儲存複雜物件，以在暫止工作流程時保存 Runbook 狀態。

**疑難排解秘訣：**下列三個解決方案的任何一個可以修正此問題：

1. 如果您要將複雜物件從某個 Cmdlet 傳送到另一個 Cmdlet，請將這些 Cmdlet 包裝在 InlineScript 中。  
2. 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。  

3. 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。


**案例：Runbook 作業失敗，因為超過已配置的配額**

**錯誤：**您的 Runbook 作業失敗且有錯誤「這個訂用帳戶的每月總計作業執行時間的配額已觸達」。

**錯誤的原因：**當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。這個配額會套用至所有類型的作業執行工作，例如，測試作業、從入口網站中開始作業、使用 Webhook 執行作業，使用 Azure 入口網站或您的資料中心排程作業以執行。若要深入了解自動化的價格，請參閱[自動化價格](https://azure.microsoft.com/pricing/details/automation/)。

**疑難排解秘訣：**如果您每個月想要使用超過 500 分鐘的處理時間，您必須將您的訂用帳戶從免費層變更為基本層。您可以採取下列步驟，升級至基本層：

1. 登入您的 Azure 訂用帳戶：  
2. 選取您想要升級的自動化帳戶  
3. 按一下 [設定] > [定價層和使用方式] > [定價層]  
4. 在 [選擇定價層] 刀鋒視窗中，選取 [基本]    


**案例：執行 Runbook 時，無法辨識 Cmdlet**

**錯誤：**您的 Runbook 失敗且有錯誤「``<cmdlet name>``：詞彙 ``<cmdlet name>`` 無法辨識為 Cmdlet、函數、指令碼檔案或可執行程式的名稱」。

**錯誤的原因：**PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，會發生這個錯誤。這可能是因為包含 Cmdlet 的模組不在此帳戶、與 Runbook 名稱有名稱衝突，或 Cmdlet 也存在於另一個模組且自動化無法解析名稱。

**疑難排解秘訣：**下列解決方案的任何一個可以修正此問題：

- 檢查您所輸入的 Cmdlet 名稱正確，並確認 Cmdlet 的路徑正確。  

- 請確定 Cmdlet 存在於您的自動化帳戶中且沒有衝突。若要確認 Cmdlet 是否存在，請以編輯模式開啟 Runbook，並且搜尋您想要的 Cmdlet，以在程式庫中尋找，或執行 **Get-Command ``<CommandName>``**。一旦驗證 Cmdlet 可用於帳戶，且與其他 Cmdlet 或 Runbook 沒有名稱衝突，將其加入至畫布，並確保您在 Runbook 中使用有效的參數集。

- 如果有名稱衝突，而且 Cmdlet 可以在兩個不同的模組中使用，您可以使用 Cmdlet 的完整名稱來解決此問題。例如，您可以使用 **ModuleName\\CmdletName**。


## 對匯入模組時的常見錯誤進行疑難排解 

**案例：無法匯入模組，或無法在匯入之後執行 Cmdlet**

**錯誤：**模組無法匯入或匯入成功，但是未壓縮任何 Cmdlet。

**錯誤的原因：**模組可能未成功匯入 Azure 自動化的一些常見原因為：

- 結構不符合自動化所需要的結構。  

- 此模組是相依於尚未部署到您的自動化帳戶的另一個模組。

- 模組在資料夾中遺失其相依性。

- **New-AzureRmAutomationModule** Cmdlet 是用來上傳模組，且您尚未提供完整儲存體路徑，或尚未使用可公開存取的 URL 載入模組。

**疑難排解秘訣：**下列解決方案的任何一個可以修正此問題：

- 請確定模組遵循下列格式：ModuleName.Zip **->** ModuleName 或版本號碼 **->** (ModuleName.psm1, ModuleName.psd1)

- 開啟 .psd1 檔案，並且查看該模組是否有任何相依性。如果有，請將這些模組上傳至自動化帳戶。

- 請確定任何參考的 .dll 會出現在模組資料夾。



## 後續步驟

如果您已遵循上述的疑難排解步驟，並且需要本文中任何要點的額外協助，您可以：

- 取得 Azure 專家協助。提交您的問題至 [MSDN Azure 或堆疊溢位論壇](https://azure.microsoft.com/support/forums/)。

- 提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後在 [**技術及帳務支援**] 下方按一下 [**取得支援**]。

- 如果您要尋找 Azure 自動化 Runbook 解決方案或整合模組，請在[指令碼中心](https://azure.microsoft.com/documentation/scripts/)提出指令碼要求。

- 將關於 Azure 自動化的任何意見或功能要求，張貼在 [User Voice](https://feedback.azure.com/forums/34192--general-feedback) 上。

<!---HONumber=AcomDC_0204_2016-->