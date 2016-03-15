<properties 
	pageTitle="使用 .NET SDK 存取 Azure Mobile Engagement 服務 API" 
	description="描述如何使用 Mobile Engagement .NET SDK 存取 Azure Mobile Engagement 服務 API"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="erikre" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="piyushjo" />

#使用 .NET SDK 存取 Azure Mobile Engagement 服務 API

Azure Mobile Engagement 公開一組 API，讓您可以管理裝置、觸達/推送活動等。若要與這些 API 互動，我們也提供您 [Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)，可以與工具搭配使用來針對您慣用的語言產生 SDK。我們建議使用 [AutoRest](https://github.com/Azure/AutoRest) 工具從我們的 Swagger 檔案產生 SDK。

我們已經以類似的方式建立 .NET SDK，可讓您使用 C# 包裝函式與這些 API 互動，且您不需要自行執行驗證權杖交涉和重新整理。

這個範例逐步說明使用 .NET SDK 所遵循的一組步驟：

1. 首先，您必須使用 Azure Active Directory 為您的 API 設定驗證，如[這裡](mobile-engagement-api-authentication.md#authentication)所述。在這些步驟的結尾，您應該會有有效的 **SubscriptionId**、**TenantId**、**ApplicationId** 和 **Secret**。 

2. 我們將使用簡單的 Windows 主控台應用程式，示範使用 .NET SDK 建立公告行銷活動的案例。開啟 Visual Studio，建立 [主控台應用程式]。

3. 接下來您必須下載 .NET SDK，它在 Nuget 資源庫 ([這裡](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/)) 中以 **Microsoft Azure Engagement Management Library (Microsoft Azure Engagement 管理資源庫)** 提供。如果您要從 Visual Studio 安裝 Nuget，搜尋套件時必須確定已選取標示 [包括發行前版本] 的選項：

	![][1]

4. 在 `Program.cs` 檔案中，加入下列命名空間：

		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.Engagement;
		using Microsoft.Azure.Management.Engagement.Models;

5. 接著您必須定義以下常數，我們將用來驗證您正在建立公告行銷活動之 Mobile Engagement 應用程式並與其互動：

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/zh-TW/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. 定義我們將用來呼叫 Mobile Engagement SDK 方法的 `EngagementManagementClient` 變數：

		static EngagementManagementClient engagementClient; 

7. 在您的 `Main` 方法加入以下內容：

		try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. 定義下列處理初始化 `EngagementManagementClient` 的方法，方法是先進行驗證，然後將其本身與您打算建立公告行銷活動的 Mobile Engagement 應用程式關聯：

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

	> [AZURE.IMPORTANT] 請注意，您必須使用 Azure 管理入口網站中針對 AppName 參數定義的**應用程式資源名稱**。

9. 最後，定義 CreateCampaign 方法，它會負責使用先前初始化的 EngagementClient 來建立含有標題和訊息的簡單**隨時**及**僅通知**活動：

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/zh-TW/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/zh-TW/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. 執行主控台應用程式，在成功建立活動後您將會看到以下訊息：

	**已成功建立活動識別碼 '159'，並且處於「草稿」狀態**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png

<!---HONumber=AcomDC_0302_2016-------->