<properties
   pageTitle="準備從 Visual Studio 發佈或部署 Azure 應用程式"
   description="了解設定雲端和儲存體帳戶服務以及 Azure 應用程式的程序。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/28/2015"
   ms.author="tarcher" />

# 準備從 Visual Studio 發佈或部署 Azure 應用程式

## 概觀

在您可以發佈雲端服務專案之前，您必須設定下列服務：

- 要在 Azure 環境中執行角色的**雲端服務**

- 提供 Blob、佇列和表格服務等存取權的**儲存體帳戶**。

使用下列程序來設定這些服務並設定您的應用程式


## 建立雲端服務

若要將雲端服務發佈至 Azure，您必須先建立在 Azure 環境中執行角色的雲端服務。您可以在 Azure 管理入口網站建立雲端服務，本主題稍後將在**使用 Azure 管理入口網站建立雲端服務**一節中說明。您也可以使用發佈精靈，在 Visual Studio 中建立雲端服務。

### 使用 Visual Studio 建立雲端服務

1. 開啟 Azure 專案的捷徑功能表，然後選擇 [發佈]。

    ![VST\_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. 如果您尚未登入，請以您的使用者名稱和密碼登入 Microsoft 帳戶或與您的 Azure 訂用帳戶相關聯的組織帳戶。

1. 選擇 [下一步] 按鈕，前進到 [設定] 頁面。

    ![發佈精靈一般設定](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. 在 [雲端服務] 清單中，選擇 [建立新的]。[建立 Azure 服務] 對話方塊隨即出現。

1. 輸入雲端服務的名稱。名稱是構成服務之 URL 的一部分，因此必須是全域唯一的。名稱不區分大小寫。

### 使用 Azure 管理入口網站建立雲端服務

1. 登入 Microsoft 網站上的 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkId=253103)。

1. (選擇性) 若要顯示您已經建立的雲端服務清單，請選擇頁面左邊的雲端服務連結。

1. 選擇左下角的 **+** 圖示，然後在出現的功能表上選擇 [雲端服務]。有 [快速建立] 和 [自訂建立] 兩個選項的另一個畫面會隨即出現。如果選擇 [快速建立]，便可藉由指定雲端服務的 URL 以及將實際裝載服務的區域，建立雲端服務。如果選擇 [自訂建立]，則可藉由指定封裝 (.cspkg 檔)、組態 (.cscfg) 檔和憑證，立即發佈雲端服務。如果您想要使用 Azure 專案中的**發佈**命令發佈您的雲端服務，就不需要選擇 [自訂建立]。您可在 Azure 專案的捷徑功能表上找到**發佈**命令。

1. 選擇 [快速建立]，以便稍後使用 Visual Studio 發佈雲端服務。

1. 指定您的雲端服務名稱。名稱旁會出現完整 URL。

1. 在清單中，選擇大部分使用者所在的區域。

1. 選擇視窗底部的 [建立雲端服務] 連結。

## 建立儲存體帳戶

儲存體帳戶會提供 Blob、佇列和表格服務等存取權。您可以使用 Visual Studio 或 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkId=253103)建立儲存體帳戶。

### 使用 Visual Studio 建立儲存體帳戶

1. 在 [方案總管] 中，開啟**儲存體**節點的捷徑功能表，然後選擇 [建立儲存體帳戶]。

    ![建立新的 Azure 儲存體帳戶](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. 在 [建立儲存體帳戶] 對話方塊中選取或輸入新儲存體帳戶的下列資訊。
    - 您要加入儲存體帳戶的 Azure 訂用帳戶。
    - 您想要用於新儲存體帳戶的名稱。
    - 區域或同質群組 (例如美國西部或東亞)。
    - 您要用於儲存體帳戶的複寫類型，例如異地備援。

1. 完成後，選擇 [建立]。新的儲存體帳戶會出現在 [伺服器總管] 的 [儲存體] 清單中。

### 使用 Azure 管理入口網站建立儲存體帳戶

1. 登入 Microsoft 網站上的 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkId=253103)。

1. (選擇性) 若要檢視您的儲存體帳戶，請選擇頁面左側面板中的 [儲存體] 連結。

1. 選擇頁面左下角的 **+** 圖示。

1. 在出現的功能表中，選擇 [儲存體]，然後選擇 [快速建立]。

1. 提供儲存體帳戶將會產生唯一 url 的名稱。

1. 提供名稱給您的雲端服務。完整的 URL 會出現在名稱旁邊。

1. 在區預清單中，選擇大部分使用者所在的區域。

1. 指定是否要啟用異地複寫。如果您啟用異地複寫，您的資料會儲存在多個實體位置，以降低遺失的可能性。這項功能會讓儲存體更昂貴，但您可以藉由建立儲存體帳戶時 (而不是稍後新增此功能時) 啟用地理位置來降低成本。如需詳細資訊，請參閱[異地複寫](http://go.microsoft.com/fwlink/?LinkId=253108)。

1. 選擇視窗底部的 [建立儲存體帳戶] 連結。

建立儲存體帳戶之後，您會看到可用來存取每個 Azure 儲存體服務中之資源的 URL，以及帳戶的主要和次要存取金鑰。您可以使用這些金鑰來驗證對儲存體服務提出的要求。

>[AZURE.NOTE]次要存取金鑰提供和主要存取金鑰相同的儲存體帳戶存取權，並會在主要存取金鑰遭到入侵時產生做為備份。此外，建議您定期重新產生存取金鑰。您可以修改連接字串設定以在您重新產生主要金鑰時使用次要金鑰，然後您可以修改它以在重新產生次要金鑰時使用重新產生的主要金鑰。

## 設定您的應用程式以使用儲存體帳戶所提供的服務

您必須設定任何存取儲存體服務的角色，以使用您已建立的 Azure 儲存體服務。若要這樣做，您可以使用 Azure 專案的多個服務組態。根據預設，兩者都會建立在您的 Azure 專案中。藉由使用多個服務組態，您可以利用您的程式碼使用相同的連接字串，但在每個服務組態中會有不同的連接字串值。例如，您可以使用服務組態在本機使用 Azure 儲存體模擬器和不同的服務組態來執行和偵錯您的應用程式，以將您的應用程式發佈到 Azure。如需服務組態的詳細資訊，請參閱[使用多個服務組態設定 Azure 專案](vs-azure-tools-multiple-services-project-configurations.md)。

### 設定您的應用程式以使用儲存體帳戶所提供的服務

1. 在 Visual Studio 中開啟您的 Azure 方案。在 [方案總管] 中，開啟 Azure 專案中存取儲存體服務之每個角色的捷徑功能表，並選擇 [屬性]。在 Visual Studio 編輯器中會顯示具有角色名稱的頁面。此頁面會顯示 [組態] 索引標籤的欄位。

1. 在角色的屬性頁面中，選擇 [設定]。

1. 在 [服務組態] 清單中，選擇您想要編輯的服務組態名稱。如果您想要變更此角色的所有服務組態，可以選擇 [所有組態]。如需關於如何更新服務組態的詳細資訊，請參閱[使用 Visual Studio 設定 Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)主題的**管理儲存體帳戶的連接字串**一節。

1. 若要修改任何連接字串設定，請選擇設定旁邊的 [...] 按鈕。[建立儲存體連接字串] 對話方塊會隨即出現。

1. 在 [連接方式] 下，選擇 [您的訂用帳戶] 選項。

1. 在 [訂用帳戶] 清單中，選擇您的訂用帳戶。如果訂用帳戶清單中沒有您想要的訂用帳戶，請選擇 [下載發佈設定] 連結。

1. 在 [帳戶名稱] 清單中，選擇您的儲存體帳戶名稱。Azure Tools 會自動使用.publishsettings 檔案取得儲存體帳戶認證。若要手動指定儲存體帳戶認證，請選擇 [手動輸入的認證] 選項，然後繼續此程序。您可以從 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)取得您的儲存體帳戶名稱和主要金鑰。如果您不想手動指定儲存體帳戶設定，請選擇 [確定] 按鈕以關閉對話方塊。

1. 選擇 [輸入儲存體帳戶] 認證連結。

1. 在 [帳戶名稱] 方塊中，輸入您的儲存體帳戶名稱。

    >[AZURE.NOTE]登入管理入口網站，然後選擇 [儲存體] 按鈕。入口網站會顯示儲存體帳戶的清單。如果您選擇帳戶，其頁面就會開啟。您可以從這個頁面複製儲存體帳戶的名稱。如果您使用舊版管理入口網站，儲存體帳戶的名稱會出現在管理入口網站的 [儲存體帳戶] 檢視中。若要複製這個名稱，請在該檢視的 [屬性] 視窗中將其反白顯示，然後選擇 Ctrl-C 鍵。若要將名稱貼到 Visual Studio，請選擇 [帳戶名稱] 文字方塊，然後選擇 Ctrl+V 鍵。

1. 在 [帳戶金鑰] 方塊中，輸入您的主要金鑰，或從[Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)將其複製並貼上。從管理入口網站複製這個金鑰：

    1. 在適當儲存體帳戶的頁面底部，選擇 [管理金鑰] 按鈕。

    1. 在 [管理存取金鑰] 頁面上，選取主要存取金鑰的文字，然後選擇 Ctrl+C 鍵。

    1. 在 Azure Tools 中，將金鑰貼到 [帳戶金鑰] 方塊中。

    1. 您必須選取下列選項之一來決定服務存取儲存體帳戶的方式：
        - **使用 HTTP**。這是標準選項。例如，`http://<account name>.blob.core.windows.net`。
        - **使用 HTTPS**，以確保連線安全無虞。例如，`https://<accountname>.blob.core.windows.net`。
        - 為三項服務的每一項**指定自訂端點**。然後您可以將這些端點輸入特定服務的欄位。

        >[AZURE.NOTE]如果您建立自訂端點，您可以建立更複雜的連接字串。當您使用這個字串格式，您可以指定儲存體服務端點，其包含您向 Blob 服務註冊儲存體帳戶的自訂網域名稱。您也可以透過共用的存取簽章，只在單一容器中授與 blob 資源的存取權。如需關於如何建立自訂端點的詳細資訊，請參閱[設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。

1. 若要儲存這些連接字串的變更，請選擇 [確定] 按鈕，然後選擇工具列上的 [儲存] 按鈕。儲存這些變更後，您可以使用 [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx)，在程式碼中取得這個連接字串的值。當您將應用程式發佈至 Azure 時，請選擇包含連接字串之 Azure 儲存體帳戶的服務組態。發佈您的應用程式之後，請確認應用程式如預期般對 Azure 儲存體服務運作

## 後續步驟

若要深入了解如何將應用程式從 Visual Studio 發佈至 Azure，請參閱[使用 Azure Tools 發佈雲端服務](vs-azure-tools-publishing-a-cloud-service.md)。

<!---HONumber=AcomDC_1217_2015-->