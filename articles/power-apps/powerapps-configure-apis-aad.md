<properties
	pageTitle="在 PowerApps 中設定 API 連接到 Azure Active Directory 網域中的後端系統 | Microsoft Azure"
	description="在 PowerApps 中設定 API 連接到受 AAD 保護的後端系統"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# 設定 API 連接到 Azure Active Directory 網域上的後端資源
隨著愈來愈多的使用者在 Azure Active Directory (AAD) 上建立網域，後端資源也被加入這些 AAD 網域中。您可以建立和設定 API 連接到這些後端資源。

#### 開始使用的必要條件

- 註冊 [PowerApps Enterprise](powerapps-get-started-azure-portal.md)。
- 建立 [App Service 環境](powerapps-get-started-azure-portal.md)。
- 安裝 [Azure PowerShell][11] 1.0 Preview 或更新版本。
- 在 [App Service 環境](powerapps-register-api-hosted-in-app-service.md)中註冊 API。

## 步驟 1：建立 Active Directory 應用程式並授與權限

若要存取 AAD 網域中的後端系統，請建立 AAD 應用程式並授與其現有後端 (也是 AAD 應用程式) 的適當權限。步驟：

1. 在 [Azure 傳統入口網站][13]中，移至 Azure Active Directory，開啟租用戶 (或目錄)，然後選取 [應用程式] 索引標籤：![][14]
2. 選取底部的 [加入] 按鈕。然後：  

	a) 選擇 [加入我的組織正在開發的應用程式]。b) 輸入應用程式名稱，並選取 [Web 應用程式和/或 Web API]。c) 在 [登入 URL] 和 [應用程式識別碼 URI] 中，輸入 AAD 內的唯一 URL 和對貴組織有意義的 URL。例如，您可以輸入 http://powerappssignon.contoso.com 或 http://powerappsappid.contoso.com。我們建議您使用貴組織 AAD 網域內的 URL。URL 用做識別碼，沒有任何需求。沒有人會瀏覽您所輸入的 URL。您可以輸入 HTTP 或 HTTPS。

3. 在新建立的 AAD 應用程式頁面，移至 [設定] 索引標籤：![][15]
4. 在 [金鑰] 區段中，使用下拉式清單選取持續時間。請注意，金鑰會在您選取 [儲存] 之後出現：![][16]
5. 在 [單一登入] 中加入 ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` 為**回覆 URL**。
6. 在 [其他應用程式的權限] 中：  

	1. 選取 [加入應用程式]。在快顯視窗中，選擇保護您現有後端的 AAD 應用程式：![][17]  

	2. 使用下拉式清單加入權限：![][18]

7. 選取底部的 [儲存]。
8. 複製並儲存**用戶端識別碼**和**金鑰**。關閉 Azure 入口網站之後，金鑰就不再出現。 

如需 AAD 應用程式的詳細資訊，請參閱[整合應用程式與 Azure Active Directory](../active-directory/active-directory-integrating-applications.md)。

## 步驟 2：使用 Azure PowerShell 設定 API

此時，沒有任何 Azure 入口網站支援可初始化 API 需要的設定。若要在 Azure 入口網站中設定 API，請使用下列 Azure PowerShell 指令碼：

> [AZURE.TIP] 若要了解如何安裝、設定和執行 Azure PowerShell，請參閱[如何安裝和設定 Azure PowerShell][11]。下列指令碼可搭配 Azure PowerShell 1.0 Preview 或更新版本使用。

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**請注意**，**權杖**連接參數名稱很重要。您可以自行選擇名稱，但必須是駝峰式命名法。稍後在後端程式碼或 API 原則中會使用這個名稱。

接下來，請移至 [Azure 入口網站][19]，再到 API 的 [一般] 設定刀鋒視窗。您應該會看到其他的設定選項：![][21]


## 立即試用

在 PowerApps 中開啟應用程式。新的 API 會列在 [可用連線] 中。當您選取 [連接] 就會顯示 AAD 登入視窗。輸入貴組織的 AAD 帳戶詳細資訊後，隨即建立連接。

現在，當使用這個連接從您的應用程式向 API 進行執行階段呼叫時，後端會在 **x-ms-apim-tokens** HTTP 標頭中收到下列 [Base64 編碼][20]格式的使用者 AAD 權杖：

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**請注意，**屬性名稱**權杖**要符合您設定此設定時所用的連接參數名稱。

然後，如有需要，後端程式碼才能從 **AccessToken** 屬性取得 AAD 權杖並使用它。App Service 環境會自動重新整理權杖。

## 設定 API 原則

或者，您也可以使用 API 原則，在標準的 HTTP **授權**標頭中設定 AAD 權杖。如此一來，如果後端程式碼需要使用 AAD 權杖，您就可以標準方式取得它，而不是研究自訂的 HTTP 標頭並進行 Base64 解碼。若要執行此作業，請移至 Azure 入口網站，再到 API 的 [原則] 刀鋒視窗中設定下列原則：

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

請查看這個原則，基本上，它讓您使用**權杖**變數將 **x-ms-apim-tokens** 標頭中的值當做解碼的 JObject 參考。然後您就可以使用 **set-header** 原則取得實際的 AAD 權杖，並將它設定到**授權**標頭。這和 [Azure API 管理](https://azure.microsoft.com/services/api-management/)使用的原則一樣。如需詳細資訊，請參閱 [Azure API 管理的原則](../api-management/api-management-howto-policies.md)。

**請注意，**屬性名稱**權杖**要符合您設定此設定時所用的連接參數名稱。

## 摘要和後續步驟

在本主題中，您已了解如何設定 API 連接 (和驗證) Azure Active Directory 網域中的後端資源。以下列出一些相關主題及資源，可以協助您深入了解 PowerApps：

- [開發 PowerApps 的 API](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_0309_2016-->