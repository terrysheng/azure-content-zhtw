<properties
   pageTitle="開始使用 Azure AD 報告 API"
   description="如何開始使用 Azure Active Directory 報告 API"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# 開始使用 Azure AD 報告 API

*這份文件是 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)的一部分。*

Azure Active Directory 提供各種活動、安全性與稽核報告。此資料可以透過 Azure 入口網站取用，但是在其他許多應用程式 (例如 SIEM 系統、稽核和商業智慧工具) 中也可能非常有用。

Azure AD 報告 API 透過一組以 REST 為基礎的 API (可從各種程式設計語言和工具呼叫)，提供這些資料的程式設計方式存取。

本文將逐步引導您使用 PowerShell 完成呼叫 Azure AD 報告 API 的程序。您可以依照案例需求修改範例 PowerShell 指令碼，從任何可用的報告 (JSON、XML 或文字格式) 存取資料。

若要使用此範例，您需要 [Azure Active Directory](active-directory-whatis.md)

## 建立 Azure AD 應用程式以存取 API

報告 API 會使用 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 授權存取 Web API。若要存您的目錄存取資訊，您必須在 Active Directory 中建立應用程式，並授與其存取 AAD 資料的適當權限。


### 建立應用程式
- 瀏覽至 [Azure 管理入口網站](https://manage.windowsazure.com/)。
- 瀏覽至您的目錄。
- 瀏覽至應用程式。
- 按一下底列上的 [新增]。
	- 按一下 [加入我的組織正在開發的應用程式]。
	- **名稱**：任何名稱均可。建議類似「報告 API 應用程式」。
	- **類型**：選取 [Web 應用程式和/或 Web API]。
	- 按一下箭號以移至下一頁。
	- **登入 URL**：```http://localhost```。
	- **應用程式識別碼 URI**：```http://localhost```。
	- 按一下核取記號以完成應用程式新增。

### 授與您的應用程式使用 API 的權限
- 瀏覽至 [應用程式] 索引標籤。
- 瀏覽至新建立的應用程式。
- 按一下 [設定] 索引標籤。
- 在 [其他應用程式的權限] 區段中：
	- 在 [Microsoft Azure Active Directory] > [應用程式權限] 中選取 [**讀取目錄資料**]。
- 按一下底列上的 [**儲存**]。


### 取得目錄識別碼、用戶端識別碼和用戶端密鑰

下列步驟將引導您取得應用程式用戶端識別碼和用戶端密鑰。您也需要知道您的租用戶名稱，它可以是您的 *.onmicrosoft.com 或自訂網域名稱。將它們複製到不同的位置；您將使用它們來修改指令碼。

#### 應用程式用戶端識別碼
- 瀏覽至 [應用程式] 索引標籤。
- 瀏覽至新建立的應用程式。
- 瀏覽至 [**設定**] 索引標籤。
- 您的應用程式用戶端識別碼會列在 [**用戶端識別碼**] 欄位中。

#### 應用程式用戶端密鑰
- 瀏覽至 [應用程式] 索引標籤。
- 瀏覽至新建立的應用程式。
- 瀏覽至 [設定] 索引標籤。
- 在 [金鑰] 區段中選取持續時間，為您的應用程式產生新的秘鑰。
- 此金鑰將會在儲存時顯示。務必將它複製並貼到安全的位置，因為之後便無法予以擷取。


## 修改指令碼
編輯下列其中一個指令碼以搭配您的目錄使用：使用 [在 Azure AD 中委派存取權] 中的正確值來取代 $ClientID、$ClientSecret 和 $tenantdomain。

### PowerShell 指令碼

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

### Bash 指令碼

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"





## 執行指令碼
完成指令碼編輯後，加以執行並確認從 AuditEvents 報告傳回預期的資料。

指令碼會列出所有可用的報告，並在 PowerShell 視窗中從 AccountProvisioningEvents 報告傳回 JSON 格式的輸出。它也會建立具有相同輸出的檔案 (JSON、文字和 XML 格式)。您可透過修改指令碼以從其他報告傳回資料來註解實驗，以及註解排除您不需要的輸出格式。

## 注意事項

- Azure AD Reporting API (使用 OData 分頁) 傳回的事件數目沒有任何限制。
	- 如需報告資料的保留限制，請參閱[報告保留原則](active-directory-reporting-retention.md)。


## 後續步驟
- 想知道有哪些安全性、稽核及活動報告可用嗎？ 請查看 [Azure AD 安全性、稽核及活動報告](active-directory-view-access-usage-reports.md)
- 如需稽核報告的詳細資訊，請參閱 [Azure AD 稽核報告事件](active-directory-reporting-audit-events.md)
- 如需圖形 API REST 服務的詳細資訊，請參閱 [Azure AD 報告和事件 (預覽)](https://msdn.microsoft.com/library/azure/mt126081.aspx)。

<!---HONumber=AcomDC_1217_2015-->