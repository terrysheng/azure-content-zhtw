<properties
	pageTitle="使用 Azure 資源群組專案在 Visual Studio Team Services中進行連續整合 | Microsoft Azure"
	description="使用 Azure 資源群組部署專案在 Visual Studio Team Services 中進行連續整合"
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="tarcher" />

# 使用 Azure 資源群組部署專案在 Visual Studio Team Services 中進行連續整合

要部署 Azure 範本，您需要執行各種階段的工作，包括組建、測試、複製到 Azure (也稱為「暫存」) 及部署範本。在 Visual Studio Team Services (VS Team Services) 中兩種不同的方式進行此作業。兩種方法所產生的結果都相同，因此請選擇最符合您工作流程的方法。

-	在執行 PowerShell 指令碼的組建定義 (包含在 Azure 資源群組部署專案，Deploy-AzureResourceGroup.ps1) 中新增一個步驟。指令碼會複製構件，接著部署範本。
-	新增多個 VS Team Services 建置步驟，每個都執行一個階段工作。

本文示範如何使用第一個選項 (使用組建定義來執行 PowerShell 指令碼)。此選項的其中一個優點是開發人員在 Visual Studio 中使用的指令碼，與 VS Team Services 使用的指令碼相同。此程序假設您已經在 VS Team Services 中簽入 Visual Studio 部署專案。

## 將構件複製到 Azure 

無論何種情況，如果有任何需要進行範本部署的構件，您需要提供 Azure 資源管理員存取權。這些構件包括下列檔案：

-	巢狀範本
-	組態指令碼及 DSC 指令碼
-	應用程式二進位檔

### 巢狀範本和組態指令碼
當您使用 Visual Studio (或以 Visual Studio 程式碼片段建置的) 提供的範本時，PowerShell 指令碼不但會暫存構件，也會參數化資源 URI 以進行不同的部署。接著，指令碼會將構件複製到 Azure 中的安全容器，並為該容器建立 SaS 權杖，再將該資訊傳遞至範本部署。請參閱[建立範本部署](https://msdn.microsoft.com/library/azure/dn790564.aspx)以深入了解巢狀範本。

## 在 VS Team Services 中設定連續部署

要在 VS Team Services 中呼叫 PowerShell 指令碼，請更新您的組建定義。簡單來說，請執行下列步驟：

1.	編輯組建定義。
1.	在 VS Team Services 中設定 Azure 授權。
1.	新增參考 Azure 資源群組部署專案中 PowerShell 指令碼的 Azure PowerShell 組建步驟。
1.	設定 *-ArtifactsStagingDirectory* 參數值，以與 VS Team Services 中建置的專案搭配使用。

### 詳細的逐步解說

下列步驟會逐步引導您進行在 VS Team Services 設定連續部署所需的步驟。

1.	編輯您的 VS Team Services 組建定義並新增 Azure PowerShell 建置步驟。在 [組建定義] 類別下選擇組建定義，再選擇 [編輯] 連結。

    ![][0]

1.	在組建定義中新增 [Azure PowerShell] 組建步驟，再選擇 [新增組建步驟...] 按鈕。

    ![][1]

1.	選擇 [部署工作] 類別，選取 [Azure PowerShell] 工作，再選擇其 [新增] 按鈕。

    ![][2]

1.	選擇 [Azure PowerShell] 組建步驟，再填上其值。

    1.	如果您已經將 Azure 服務端點加入 VS Team Services，請在 [Azure 訂用帳戶] 下拉式清單方塊中選擇訂用帳戶，並跳至下一節。 

        如果您的 VS Team Services 中沒有 Azure 服務端點，請新增一個。此訂用帳戶會帶您完成此程序。如果您的 Azure 帳戶使用 Microsoft 帳戶 (例如 Hotmail)，您需要進行下列步驟以取得服務主體驗證。

    1.	選擇 [Azure 訂用帳戶] 下拉式清單方塊旁的 [管理] 連結。

        ![][3]

    1. 在 [新服務端點] 下拉式清單方塊中選擇 [Azure]。

        ![][4]

    1.	在[新增 Azure 訂用帳戶] 對話方塊中，選取 [服務主體] 選項。

        ![][5]

    1.	在 [新增 Azure 訂用帳戶] 對話方塊中新增 Azure 訂用帳戶資訊。您必須先提供下列項目：
        -	訂用帳戶識別碼
        -	訂用帳戶名稱
        -	服務主體識別碼
        -	服務主體金鑰
        -	租用戶識別碼

    1.	在 [訂用帳戶] 名稱方塊中新增您選擇的名稱。此值稍後會出現在 VS Team Services 中的 [Azure 訂用帳戶] 下拉式清單中。

    1.	如果您不知道 Azure 訂用帳戶識別碼，可以使用以下其中一個命令取得。
        
        對於 Azure PowerShell 指令碼，請使用：

        `Get-AzureRmSubscription`

        對於 Azure CLI，請使用：

        `azure account show`
    

    1.	要取得服務主體識別碼、服務主體金鑰及租用戶識別碼，請依照[使用入口網站建立 Active Directory 應用程式和服務主體](resource-group-create-service-principal-portal.md)或[以 Azure 資源管理員驗證服務主體](resource-group-authenticate-service-principal.md)中的程序。

    1.	在 [新增 Azure 訂用帳戶] 對話方塊中新增服務主體識別碼、服務主體金鑰及租用戶識別碼值，再選擇 [確定] 按鈕。

        現在，您擁有可執行 Azure PowerShell 指令碼的有效服務主體。

1.	編輯組建定義並選擇 **Azure PowerShell** 建置步驟。在 [Azue 訂用帳戶] 下拉式清單方塊中選取訂用帳戶。(如果訂用帳戶未出現，請選擇 [管理] 連結旁的 [重新整理] 按鈕。)

    ![][8]

1.	提供 Deploy-AzureResourceGroup.ps1 PowerShell 指令碼的路徑。要這樣做，請選擇 [指令碼路徑] 方塊旁的省略符號 (…) 按鈕，瀏覽到您專案的 [指令碼] 資料夾中的 Deploy-AzureResourceGroup.ps1 PowerShell 指令碼，選取並選擇 [確定] 按鈕。

    ![][9]

1. 選取指令碼之後，將路徑更新到該指令碼，以便從 Build.StagingDirectory 執行 (與 *ArtifactsLocation* 設定的目錄相同)。您可以在指令碼路徑的開頭加入 “$(Build.StagingDirectory)/” 以執行此項作業。

    ![][10]

1.	在 [指令碼引數] 方塊中，輸入下列參數 (請輸入在同一行)。當您在 Visual Studio 中執行指令碼時，可以在 [輸出] 視窗中看到 VS 如何使用參數。您可以從這裡開始，在建置步驟中設定參數值。

    | 參數 | 說明|
    |---|---|
    | -ResourceGroupLocation | 資源群組所在的地理位置，例如 **eastus** 或**'美國東部'**。(如果名稱中有空間，請加入單引號。) 如需詳細資訊，請參閱 [Azure 區域](https://azure.microsoft.com/zh-TW/regions/)。| |
    | -ResourceGroupName | 此部署使用的資源群組名稱。| |
    | -UploadArtifacts | 出現此參數時，表示必須將構件從本機系統上傳到 Azure。您只需要在範本部署需要您希望暫存的其他構件時，使用 PowerShell 指令碼設定此轉換 (例如組態指令碼或巢狀範本) 即可。 |
    | -StorageAccountName | 用來暫存此部署之構件的儲存體帳戶名稱。只有在您將構件複製到 Azure 時才需要此參數。部署時不會自動建立此儲存體帳戶，請在部署前先自行建立此儲存體帳戶。| |
    | -StorageAccountResourceGroupName | 與儲存體帳戶相關聯的資源群組名稱。只有在您將構件複製到 Azure 時才需要此參數。| |
    | -TemplateFile | Azure 資源群組部署專案中的範本檔案路徑。為了提高彈性，請使用與 PowerShell 指令碼相關的參數路徑，不要使用絕對路徑。|
    | -TemplateParametersFile | Azure 資源群組部署專案中的參數檔案路徑。為了提高彈性，請使用與 PowerShell 指令碼相關的參數路徑，不要使用絕對路徑。|
    | -ArtifactStagingDirectory | 此參數可讓 PowerShell 指令碼從應該複製專案二進位檔的位置取得資料夾。這個值會覆寫 PowerShell 指令碼使用的預設值。若要供 VS Team Services 使用，請將值設為：-ArtifactStagingDirectory $(Build.StagingDirectory) |

    以下是指令碼引數的範例 (斷行以方便閱讀)：

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    完成之後，[指令碼引數] 方塊應該如下所示。

    ![][11]

1.	將所有必要的項目都加入 Azure PowerShell 建置步驟之後，請選擇 [佇列] 建置按鈕以建置專案。[建置] 畫面會顯示 PowerShell 指令碼的輸出。

## 後續步驟

如需 Azure 資源管理員和 Azure 資源群組的詳細資訊的詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0128_2016-->