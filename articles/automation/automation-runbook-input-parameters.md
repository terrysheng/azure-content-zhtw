<properties 
   pageTitle="Runbook 輸入參數"
   description="Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。本文說明在 Runbook 中使用輸入參數的不同案例。"
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
   ms.date="12/16/2015"
   ms.author="sngun"/>

# Runbook 輸入參數

Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。這些參數可讓Runbook 動作以特定案例和環境做為目標。在本文中，我們將逐步引導您了解在 Runbook 中使用輸入參數的不同案例。

## 設定輸入參數

輸入參數可以在 PowerShell、PowerShell 工作流程和圖形化 Runbook 中設定。一個 Runbook 可以使用具有不同資料類型的多個參數，或完全不使用參數。輸入參數可以是強制性或選擇性的，而且您可以為選擇性參數指派預設值。為 Runbook 設定的輸入參數可以在您透過任何可用的方法啟動 Runbook 時獲得指派的值，這些方法包括透過 UI、Webhook、Web 服務來啟動 Runbook，或在另一個 Runbook 中以內嵌方式呼叫子 Runbook。

## 在 PowerShell 和 PowerShell 工作流程 Runbook 中設定輸入參數

Azure 自動化中的 PowerShell 和 [PowerShell 工作流程 Runbook](automation-first-runbook-textual.md) 支援使用下列屬性定義的輸入參數：

| **屬性** | **說明** |
|:--- |:---|
| 類型 | 必要。對參數值預期的資料類型。任何 .Net 類型皆有效。 |
| 名稱 | 必要。參數名稱。這在 Runbook 中必須是唯一的，並且只能包含字母、數字或底線字元，且必須以字母開頭。 |
| 強制 | 選用。指定是否必須提供參數的值。如果您將此項目設定為 **$true**，則在 Runbook 啟動時必須提供其值。如果您將此項目設定為 **$false**，則可以選擇提供值。 |
| 預設值 | 選用。指定在 Runbook 啟動時未傳遞值的情況下所將用於參數的值。任何參數皆可設定為預設值，此值將會使參數自動成為選擇性，無論 [強制] 設定為何。 |

Windows PowerShell 所支援的輸入參數屬性比此處所列的多，例如驗證、別名和參數集；但 Azure 自動化目前僅支援上述清單。

PowerShell 工作流程 Runbook 中的參數定義具有下列一般形式，其中，多個參數會以逗號分隔。

```
     Param
     (
         [Parameter (Mandatory= $true/$false)]
         [Type] Name1 = <Default value>,

         [Parameter (Mandatory= $true/$false)]
         [Type] Name2 = <Default value>
     ) 
```

>[AZURE.NOTE]定義參數時，如果您未指定 [強制] 屬性，依預設會將參數視為選擇性。此外，如果您在 PowerShell 工作流程 Runbook 中設定某個參數的預設值，則 Powershell 會將其視為選擇性參數，無論 [強制] 屬性值為何。

舉例來說，我們為輸出虛擬機器 (可以是單一 VM 或服務內的所有 VM) 相關詳細資料的 PowerShell 工作流程 Runbook 設定輸入參數。此 Runbook 有兩個參數：虛擬機器的名稱和服務的名稱，如以下螢幕擷取畫面所示。

![自動化 PowerShell 工作流程](media/automation-runbook-input-parameters/automation_01_PowerShellWorkflow.png)

在此參數定義中，參數 **$VMName** 和 **$ServiceName** 是字串類型的簡單參數；不過，PowerShell 和 PowerShell 工作流程 Runbook 支援所有簡單類型和複雜類型，例如輸入參數的 **object** 或 **PSCredential**。

如果您的 Runbook 有 [object] 類型輸入參數，因而要傳入值，請使用具有 (name,value) 配對的 PowerShell 雜湊表。例如，如果您在 Runbook 中有下列參數，

     [Parameter (Mandatory = $true)]
     [object] $FullName

則您可以將下列值傳遞至參數：

    @{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}


## 在圖形化 Runbook 中設定輸入參數

為了使用輸入參數設定圖形化 Runbook，我們將建立會輸出虛擬機器 (可以是單一 VM 或服務內的所有 VM) 相關詳細資料的[圖形化 Runbook](automation-first-runbook-graphical.md)。此 Runbook 包含兩個主要活動：

* [**Add-AzureAccount**](https://msdn.microsoft.com/library/dn495128.aspx) 用來對 Azure 進行驗證。
* [**Get-AzureVM**](https://msdn.microsoft.com/library/azure/dn495236.aspx) 用來取得所有虛擬機器。

您可以使用 [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) 活動輸出虛擬機器的名稱。活動 **Get-AzureVM** 會接受兩個參數：**虛擬機器名稱**和**服務帳戶名稱**。由於這些參數在您每次啟動 Runbook 時可能需要不同的值，因此您可以將輸入參數新增至您的 Runbook。以下是新增輸入參數的步驟：

1. 從 [Runbook] 刀鋒視窗中選取圖形化 Runbook，並加以[編輯](automation-graphical-authoring-intro.md)。 

2. 在 [編輯] 刀鋒視窗中按一下 [輸入和輸出]，以開啟 [輸入和輸出] 刀鋒視窗。

    ![自動化圖形化 Runbook](media/automation-runbook-input-parameters/automation_02_GraphicalRunbook.png)

 
3. [輸入和輸出] 刀鋒視窗會顯示為 Runbook 定義的輸入參數清單。從此刀鋒視窗中，您可以新增輸入參數，或編輯現有輸入參數的組態。若要為 Runbook 新增參數，請按一下 [新增輸入] 以開啟 [Runbook 輸入參數] 刀鋒視窗，在其中您可以設定下列參數：

    | **屬性** | **說明** |
    |:--- |:---|
    | 名稱 | 必要。參數名稱。這在 Runbook 中必須是唯一的，並且只能包含字母、數字或底線字元，且必須以字母開頭。 |
    | 說明 | 選用。關於輸入參數用途的說明。 |
    | 類型 | 選用。對參數值預期的資料類型。支援的參數類型包括字串、Int32、Int64、十進位數、布林值、日期時間和物件。若未選取資料類型，將預設為字串。 |
    | 強制 | 選用。指定是否必須提供參數的值。如果您選擇 [是]，則在 Runbook 啟動時必須提供其值。如果您選擇 [否]，則在 Runbook 啟動時不一定需要值，並且可設定預設值。 |
    | 預設值 | 選用。指定在 Runbook 啟動時未傳遞值的情況下所將用於參數的值。不是強制性的參數可以設定預設值。您可以選擇 [自訂] 以設定預設值。除非在 Runbook 啟動時提供了其他值，否則將會使用此值。若不想提供任何預設值，請選擇 [無]。 |  

    ![AddNewInput](media/automation-runbook-input-parameters/automation_03_AddNewInput.png)

4. 使用下列屬性，建立將由 **Get-AzureVM** 活動使用的兩個參數：

    * **Parameter1：**名稱 - VMName、類型 - 字串、強制 - 否
	
    * **Parameter2：**名稱 - ServiceName、類型 - 字串、強制 - 否、預設值 - 自訂、自訂預設值 - <包含虛擬機器之預設服務名稱>

5. 新增參數之後，請按一下 [確定]。現在，您可以在 [輸入和輸出] 刀鋒視窗中加以檢視。再按一下 [確定]，然後**儲存**並**發佈**您的 Runbook。

## 將值指派給 Runbook 中的輸入參數

在下列情況下，您可以將值傳遞至 Runbook 中的輸入參數：

### 啟動 Runbook 並指派參數

Runbook 有多種啟動方式：透過 Azure 入口網站 UI、透過 Webhook、透過 PowerShell Cmdlet、REST API 和 SDK。以下我們將討論啟動 Runbook 並指派參數的不同方法。

* **透過 Azure 入口網站啟動已發佈的 Runbook，並指派參數**

當您 [啟動 Runbook](automation-starting-a-runbook#starting-a-runbook-with-the-azure-portal.md) 時，將會開啟 [啟動 Runbook] 刀鋒視窗，在其中您可以為剛建立的參數設定值。

![開始使用入口網站](media/automation-runbook-input-parameters/automation_04_StartRunbookUsingPortal.png)

在輸入文字方塊下的標籤中，您可以檢視為參數設定的屬性：強制或選擇性、類型和任何預設值。在參數名稱旁的球形文字說明中，您可以檢視您在進行參數輸入值相關決策時所需的所有金鑰資訊。這包括參數是強制性或選擇性、參數的類型、任何預設值，以及其他有用的資訊。

![球形文字說明](media/automation-runbook-input-parameters/automation_05_HelpBaloon.png)


>[AZURE.NOTE]字串類型參數支援**空**字串值。在輸入參數文字方塊中，輸入 **[EmptyString]**，將會傳遞空字串給參數。此外，字串類型參數不支援傳遞 **Null** 值。若未傳遞任何值給字串參數，PowerShell 會將其解譯為 Null。

* **使用 PowerShell Cmdlet 啟動已發佈的 Runbook，並指派參數**

    - **Azure 服務管理 Cmdlet：**您可以使用 [Start-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690259.aspx) 啟動在預設資源群組中建立的自動化 Runbook

    **範例：**

      ``` 
		$params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}

        Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
      ```

    - **Azure 資源管理員 Cmdlet：**您可以使用 [Start-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) 啟動在資源群組中建立的自動化 Runbook

	
    **範例：**

      ``` 
		$params = @{“VMName”=”WSVMClassic”;”ServiceName”=”WSVMClassicSG”}

        Start-AzureRMAutomationRunbook -AutomationAccountName “TestAutomationRG” -Name “Get-AzureVMGraphical” –ResourceGroupName “RG1” -Parameters $params
      ```

>[AZURE.NOTE]當您使用 PowerShell Cmdlet 啟動 Runbook 時，將會隨著您所傳遞的輸入參數，建立值為 **PowerShell** 的預設參數 **MicrosoftApplicationManagementStartedBy**。您可以在 [作業詳細資料] 刀鋒視窗中檢視此參數。

- **使用 SDK 啟動 Runbook，並指派參數**

    - **Azure 服務管理方法：**您可以使用程式設計語言的 SDK 來啟動 Runbook。以下說明如何以 C# 程式碼片段在您的自動化帳戶中啟動 Runbook，您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。  

    ```      
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
            var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
            {
                Properties = new JobCreateProperties 
                {
                    Runbook = new RunbookAssociationProperty
                    {
                        Name = runbookName
                    },
                        Parameters = parameters
                }
            });
            return response.Job;
        }
    ```
      
    * **Azure 資源管理員方法：**您可以使用程式設計語言的 SDK 來啟動 Runbook。以下說明如何以 C# 程式碼片段在您的自動化帳戶中啟動 Runbook，您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。  

    ```
        public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
        {
           var response = AutomationClient.Jobs.Create(resourceGroup, automationAccount, new JobCreateParameters
           {
               Properties = new JobCreateProperties 
               {
                   Runbook = new RunbookAssociationProperty
                   {
                       Name = runbookName
                   },
                       Parameters = parameters
               }
           });
        return response.Job;
        }
    ```

若要啟動此方法，請建立字典來儲存 Runbook 參數、VMName 和 ServiceName 及其值，然後啟動 Runbook。以下 C# 程式碼片段會呼叫上述定義的方法。

```
    IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();

    // Add parameters to the dictionary.
    RunbookParameters.Add("VMName", "WSVMClassic");
    RunbookParameters.Add("ServiceName", "WSVMClassicSG");

    //Call the StartRunbook method with parameters
    StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
```

* **使用 REST API 啟動 Runbook，並指派參數**

Runbook 作業可透過Azure 自動化 REST API，使用 **PUT** 方法和下列要求 URI 來建立及啟動。

    https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08

在 [要求 URI] 中，取代下列參數：
 
* **subscription-id：**您的 Azure 訂用帳戶識別碼。  
* **cloud-service-name：**要求所要傳送到的雲端服務的名稱。  
* **automation-account-name：**您裝載在指定雲端服務內的自動化帳戶的名稱。  
* **job-id：**作業的 GUID。PowerShell 中的 GUID 可使用 **[GUID]::NewGuid().ToString()** Cmdlet 來建立。
	
若要將參數傳遞至 Runbook 作業，請使用要求本文，它會採用兩個以 JSON 格式提供的屬性：

* **Runbook 名稱** – 必要。作業要啟動之 Runbook 的名稱。  
* **Runbook 參數** – 選擇性。參數清單的字典；清單必須採用 (名稱, 值) 格式，其中的名稱應為字串類型，而值可以是任何有效的 JSON 值。 

如果您想要啟動先前以 VMName 和 ServiceName 做為參數建立的 **Get-AzureVMTextual** Runbook，請使用下列 JSON 格式的要求本文。

```
        {
           "properties":{
           "runbook":{
               "name":"Get-AzureVMTextual"
           },
           "parameters":{
               "VMName":"WSVMClassic",
               "ServiceName":”WSCS1”
           }
          }
       }
```

如果成功建立作業，將會傳回 HTTP 狀態碼 201，如需關於回應標頭和回應本文的詳細資訊，請參閱[使用 REST API 建立 Runbook 作業](https://msdn.microsoft.com/library/azure/mt163849.aspx)。

### 測試 Runbook 並指派參數

當您使用測試選項[測試](automation-testing-runbook.md) Runbook 的草稿版本時，將會開啟 [測試] 刀鋒視窗，在其中您可以為剛建立的參數設定值。

![測試並指派參數](media/automation-runbook-input-parameters/automation_06_TestAndAssignParameters.png)

### 將排程連結至 Runbook，並指派參數

您可以[將排程連結](automation-scheduling-a-runbook.md)至您的 Runbook，以在特定時間啟動。在建立排程時會指派輸入參數，且 Runbook 經由排程啟動時，將會使用這些值。必須提供所有必要參數值，才能儲存排程。

![排程並指派參數](media/automation-runbook-input-parameters/automation_07_ScheduleAndAssignParameters.png)

### 建立 Runbook 的 Webhook，並指派參數

您可以為您的 Runbook 建立 [Webhook](automation-webhooks.md)，並設定 Runbook 輸入參數。必須提供所有必要參數值，才能儲存 Webhook。

![建立 Webhook 並指派參數](media/automation-runbook-input-parameters/automation_08_CreateWebhookAndAssignParameters.png)

當您使用 Webhook 執行 Runbook 時，會隨著您定義的輸入參數傳送預先定義的輸入參數 **[Webhookdata](automation-webhooks.md#details-of-a-webhook)**。您可以按一下 WebhookData 參數加以展開，以取得詳細資訊。

![WebhookData 參數](media/automation-runbook-input-parameters/automation_09_WebhookDataParameter.png)


## 後續步驟

- 如需關於 Runbook 輸入和輸出的詳細資訊，請參閱 [Azure 自動化：Runbook 輸入、輸出和巢狀 Runbook](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)
- 如需以不同方式啟動 Runbook 的詳細資訊，請參閱[啟動 Runbook](automation-starting-a-runbook.md)
- 若要編輯文字 Runbook，請參閱[編輯文字 Runbook](automation-edit-textual-runbook.md)
- 若要編輯圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)

<!---HONumber=AcomDC_1223_2015-->