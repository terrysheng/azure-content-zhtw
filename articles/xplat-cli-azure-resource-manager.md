<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure Cross-Platform Command-Line Interface" pageTitle="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" title="Using Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaKeywords="windows azure cross-platform command-line interface Resource Manager, windows azure command-line resource manager, azure command-line resource manager, azure cli resource manager" description="Use the Microsoft Azure Cross-Platform Command-Line Interface with the Resource Manager" metaCanonical="http://www.windowsazure.com/zh-tw/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

搭配使用 Azure 跨平台命令列介面與資源管理員
===========================================

我們最近引進資源管理員預覽，這是一種管理 Microsoft Azure 的新方法。在本文中，您將學習如何使用 Azure 跨平台命令列介面 (xplat-cli) 來使用資源管理員。

> [WACOM.NOTE] 「資源管理員」目前為預覽版，且無法提供與 Azure 服務管理相同等級的管理功能。

> [WACOM.NOTE] 若您尚未安裝及設定 xplat-cli，請參閱[安裝及設定 Microsoft Azure 跨平台命令列介面](/zh-tw/documentation/articles/xplat-cli/)以取得如何安裝、設定和使用 xplat-cli 的詳細步驟。

資源管理員
----------

資源管理員可讓您將一組*資源* (使用者管理的實體，例如資料庫伺服器、資料庫或網站) 作為單一邏輯單元管理，或作為*資源群組*管理。例如，一個資源群組可能包含一個網站和 SQL Database 資源。

為了支援更具宣告性的方式來說明資源群組內的資源變更，資源管理員使用*範本* (為 JSON 文件)。範本語言也能讓您說明可在執行命令時內嵌的參數，或儲存於個別 JSON 檔案中的參數。如此一來，您只要提供不同的參數，就能使用相同的範本輕易建立新資源。例如，建立網站的範本將具有網站名稱的參數、網站將放置的地區，以及其他共同參數。

> [WACOM.NOTE] 目前並未記錄範本語言的特殊資訊。一旦有可用文件後，將更新本主題來提供參照文件的連結。
>
> 不過，您可以使用 `azure group template download` 命令，從範本庫下載和修改 Microsoft 和合作夥伴提供的範本。

使用範本修改或建立群組時，會建立*部署*，接著將其套用至群組。

驗證
----

目前若要透過 xplat-cli 使用資源管理員，您需要使用組織帳戶通過 Microsoft Azure 驗證。使用 Microsoft 帳戶驗證，或透過 .publishsettings 檔案安裝憑證都沒有作用。

如需使用組織帳戶進行驗證的詳細資訊，請參閱[安裝及設定 Microsoft Azure 跨平台命令列介面](/zh-tw/documentation/articles/xplat-cli/)。

使用群組和範本
--------------

1.  資源管理員目前正在預覽中，因此預設不會啟用使用它的 xplat-cli 命令。請使用下列命令來啟用命令。

         azure config mode arm

    > [WACOM.NOTE] 資源管理員模式與 Azure 服務管理模式是互斥的。亦即，任一模式所建立的資源，將無法由另一種模式來管理。

2.  使用範本時，您可以建立您自己的範本，或使用範本庫的範本。若要列出範本庫的可用範本，請使用下列命令。

         azure group template list

    回應將列出發行者和範本名稱，並將出現以下類似的結果。

         data:Publisher               Name
        data:    ----------------------------------------------------------------------------
        data:Microsoft               Microsoft.WebSite.0.1.0-preview1
        data:Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
        data:Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
        data:Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
        data:Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3.  若要檢視將建立 Azure 網站之範本的預設值，請使用下列命令。

         azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    如此會傳回範本的描述資訊。

4.  選取範本後，可使用下列命令下載範本。

         azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

    下載範本可讓您自訂該範本，如此能更貼近您的需求。例如，將其他資源新增至範本。

    > [WACOM.NOTE] 如果您修改範本，請先使用 `azure group template validate` 命令來驗證範本，再使用它來建立或修改現有的資源群組。

5.  在文字編輯器中開啟範本檔案。請注意，**[參數]** 集合靠近頂端。這包含此範本預期的參數清單，目的是建立範本所說明的資源。部分參數 (如 **sku**) 有預設值，而其他參數只要指定值的類型 (如 **siteName**)。使用範本時，您可以將參數做為命令列參數的一部分提供，或藉由指定包含參數值的檔案提供。不論是哪一種方式，參數必須是 JSON 格式。

    若要建立包含 Microsoft.WebSiteSQLDatabase.0.1.0-preview1 範本之參數的檔案，請使用下列資料及建立名為 **params.json** 的檔案。以您自己的值取代開頭為 **My** 的值，例如 **MyWebSite**。**siteLocation** 應指定您附近的 Azure 地區，例如 **North Europe** 或 **South Central US**。

         {
        "siteName": {
        "value":"MyWebSite"
           },
        "hostingPlanName": {
        "value":"MyHostingPlan"
           },
        "siteLocation": {
        "value":"North Europe"
           },
        "serverName": {
        "value":"MySQLServer"
           },
        "serverLocation": {
        "value":"North Europe"
           },
        "administratorLogin": {
        "value":"MySQLAdmin"
           },
        "administratorLoginPassword": {
        "value":"MySQLAdminPassword"
           },
        "databaseName": {
        "value":"MySQLDB"
           }
         }

6.  儲存 **params.json** 檔案後，請使用下列命令以根據範本建立新資源群組。`-e` 參數指定前一個步驟所建立的 **params.json** 檔案。

         azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

    以您要使用的群組名稱取代 **MyGroupName**，並以範本中指定的 **siteLocation** 值取代 **MyDataCenter**。

    > [WACOM.NOTE] 上傳部署後，但在將部署套用至群組中資源前，此命令將傳回 [OK]。若要檢查部署的狀態，請使用下列命令。
    >
    > `azure group deployment show MyGroupName MyDeployment`
    >
    > **ProvisioningState** 會顯示部署的狀態。
    >
    > 如果您認為您的設定不正確，並需要停止長期執行的部署，請使用下列命令。
    >
    > `azure group deployment stop MyGroupName MyDeployment`
    >
    > 若未提供部署名稱，將根據範本檔案的名稱自動建立一個名稱。`azure group create` 命令的輸出將傳回該名稱。

7.  若要檢視群組，請使用下列命令。

         azure group show MyGroupName

    此命令會傳回群組中資源的相關資訊。如果您有多個群組，可使用 `azure group list` 命令來擷取群組名稱清單，然後使用 `azure group show` 來檢視特定群組的詳細資料。

使用資源
--------

雖然範本可讓您宣告設定中整個群組的變更，但有時您只需要使用特定的資源。您可以使用 `azure resource` 命令執行此工作。

> [WACOM.NOTE] 使用 `list` 命令以外的 `azure resource` 命令時，必須指定 `-o` 參數正在搭配使用之資源的 API 版本。如果不確定要使用的 API 版本，請咨詢範本檔案並尋找資源的 **apiVersion** 欄位。

1.  若要列出群組中的所有資源，請使用下列命令。

         azure resource list MyGroupName

2.  若要檢視個別資源，例如網站，請在群組中使用下列命令。

         azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

    請注意 **Microsoft.Web/sites** 參數。這表示您正在要求哪類資源的資訊。如果您查看稍早下載的範本檔案，您會發現此相同的值可用來定義範本中說明的網站資源類型。

    此命令會傳回網站的相關資訊。例如，**hostNames** 欄位應包含網站的 URL。請搭配使用此欄位與瀏覽器來確認網站正在執行。

3.  檢視資源的詳細資料時，使用 `--json` 參數通常很有實用，因為此參數可製作更具可讀性的輸出，其中部分值是巢狀結構或集合。下列示範會將 show 命令的結果作為 JSON 文件傳回。

         azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

    > [WACOM.NOTE] 您可以使用 \> 字元將輸出導向檔案，藉此將 JSON 資料儲存至檔案。例如：
    >
    > `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

4.  若要刪除現有的資源，請使用下列命令。

         azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

記錄
----

若要檢視群組上執行之作業的記錄資訊，請使用 `azure group log show` 命令。依預設，這會列出群組上執行的最後一個作業。若要檢視所有作業，請使用選用的 `--all` 參數。針對前次部署，使用 `--last-deployment`。針對特定部署，使用 `--deployment` 並指定部署名稱。下列範例傳回針對群組 'MyGroup' 執行的所有作業記錄。

    azure group log show mygroup --all

後續步驟
--------

-   如需使用 Azure 跨平台命令列介面的詳細資訊，請參閱[安裝及設定 Microsoft Azure 跨平台命令列介面](/zh-tw/documentation/articles/xplat-cli/)。
-   如需使用 Windows Azure PowerShell 來使用資源管理員的詳細資訊，請參閱[開始搭配使用 Windows PowerShell 與資源管理員](http://go.microsoft.com/fwlink/?LinkId=394760) (英文)。

