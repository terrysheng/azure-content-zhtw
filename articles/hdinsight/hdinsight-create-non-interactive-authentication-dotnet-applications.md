<properties
	pageTitle="建立非互動式驗證 .NET HDInsight 應用程式 | Microsoft Azure"
	description="了解如何建立非互動式驗證 .NET HDInsight 應用程式。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="jgao"/>

# 建立非互動式驗證 .NET HDInsight 應用程式

您可以使用應用程式本身的身分識別 (非互動式) 或使用應用程式的登入使用者的身分識別 (互動式)，執行 .NET Azure HDInsight 應用程式。如需互動式應用程式的範例，請參閱[使用 HDInsight .NET SDK 提交 Hive/Pig/Sqoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)。本文將說明如何建立非互動式驗證 .NET 應用程式，來連接到 Azure HDInsight，並提交 Hive 作業。

從 .NET 應用程式，您將需要︰

- 您的 Azure 訂用帳戶租用戶識別碼
- Azure Directory 應用程式用戶端識別碼
- Azure Directory 應用程式秘密金鑰  

此主要程序包含下列步驟：

2. 建立 Azure Directory 應用程式
2. 將角色指派至 AD 應用程式
3. 開發您的用戶端應用程式


##必要條件

- HDInsight 叢集。您可以使用[開始使用教學課程](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)中的指示建立。 




## 建立 Azure Directory 應用程式 
當您建立 Active Directory 應用程式時，將會同時建立應用程式和服務主體。您可以使用應用程式的身分識別執行應用程式。

目前，您必須使用 Azure 傳統入口網站來建立新的 Active Directory 應用程式。Azure 入口網站會在稍後的版本中新增這項功能 。您也可以透過 Azure PowerShell 或 Azure CLI 執行下列步驟。如需了解搭配服務主體來使用 PowerShell 或 CLI 的詳細資訊，請參閱[使用 Azure Resource Manager 驗證服務主體](../resource-group-authenticate-service-principal.md)。

**建立 Azure Directory 應用程式**

1.	登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
2.	從左窗格中，選取 [**Active Directory**]。

    ![Azure 傳統入口網站 Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	選取您想要用來建立新應用程式的目錄。應該為現有的項目。
4.	按一下頂端的 [應用程式] 以列出現有的應用程式。
5.	按一下底端的 [新增] 以新增新的應用程式。
6.	輸入 [名稱]，選取 [Web 應用程式和/或 Web API]，然後按 [下一步]。

    ![新 Azure Active Directory 應用程式](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	輸入 [登入 URL] 和 [應用程式識別碼 URI]。針對 [**登入 URL**]，提供描述您應用程式之網站的 URI。不會驗證網站是否存在。針對 [應用程式識別碼 URI]，提供識別您應用程式的 URI。然後，按一下 [完成]。需要一點時間才會建立應用程式。應用程式建立之後，入口網站會顯示新的應用程式的 [快速瀏覽] 頁面。請不要關閉入口網站。

    ![新 Azure Active Directory 應用程式屬性](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**若要取得應用程式用戶端識別碼和秘密金鑰**

1.	從新建立的 AD 應用程式頁面的頂端功能表，按一下 [設定]。
2.	複製 [用戶端識別碼]。您在 .NET 應用程式中需要它。
3.	在 [金鑰] 底下按一下 [選取持續時間] 下拉式清單，然後選取 [1 年] 或 [2 年]。在您儲存組態之前不會顯示金鑰值。
4.	按一下頁面底部的 [儲存]。秘密金鑰出現時，複製金鑰。您在 .NET 應用程式中需要它。

##將 AD 應用程式指派給角色

您必須將應用程式指派給某個[角色](../active-directory/role-based-access-built-in-roles.md)，以便授與它執行動作的權限。您可以針對訂用帳戶、資源群組或資源的層級設定範圍。較低的範圍層級會繼承較高層級的權限 (舉例來說，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源)。在本教學課程中，您將在資源群組層級設定範圍。因為 Azure 傳統入口網站不支援資源群組，這個部分必須從 Azure 入口網站執行。

**若要將擁有者角色新增至 AD 應用程式**

1.	登入 [Azure 入口網站](https://portal.azure.com)。
2.	按一下左側面板上的 [資源群組]。
3.	按一下資源群組，它包含您稍後在本教學課程中要在其中執行 Hive 查詢的 HDInsight 叢集。如果有太多的資源群組，您可以使用篩選器。
4.	從叢集刀鋒視窗中，按一下 [存取]。

    ![雲和雷電圖示 = 快速入門](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	從 [使用者] 刀鋒視窗中，按一下 [新增]。
6.	依照指示以將 [擁有者] 角色新增至您在上一個程序中建立的 AD 應用程式。當您成功完成此作業時，您會看到 [使用者] 刀鋒視窗中列出的應用程式具有 [擁有者] 角色。


##開發 HDInsight 用戶端應用程式

遵循[在 HDInsight 中提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically/#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk)中的指示，建立 C# .net 主控台應用程式。以下列項目取代 GetTokenCloudCredentials 方法：

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

若要透過 PowerShell 擷取租用戶識別碼︰

    Get-AzureRmSubscription

或者，Azure CLI：

    azure account show --json

      
## 另請參閱

- [在 HDInsight 上提交 Hadoop 工作](hdinsight-submit-hadoop-jobs-programmatically.md)
- [使用入口網站建立 Active Directory 應用程式和服務主體](../resource-group-create-service-principal-portal.md)
- [使用 Azure Resource Manager 驗證服務主體](../resource-group-authenticate-service-principal.md)
- [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0330_2016-->