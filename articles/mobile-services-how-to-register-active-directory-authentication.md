<properties pageTitle="註冊 Azure Active Directory 驗證 - 行動服務" description="了解如何在 Mobile Services 應用程式中註冊 Azure Active Directory 驗證。" authors="wesmc7777" services="mobile-services" documentationCenter="" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# 註冊應用程式以使用 Azure Active Directory 帳戶登入

本主題說明如何註冊應用程式以使用 Azure Active Directory 作為 Azure 行動服務的驗證提供者。 


>[AZURE.NOTE] 當您想要對應用程式使用[服務主導登入作業](http://msdn.microsoft.com/zh-tw/library/azure/dn283952.aspx)時，本主題中所述的步驟適用於[將驗證加入至行動服務應用程式](/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/)教學課程。或者，如果您的應用程式具有 Azure Active Directory 及 .NET 後端行動服務的[用戶端主導登入作業](http://msdn.microsoft.com/zh-tw/library/azure/jj710106.aspx)需求，則您應該從[使用 Active Directory Authentication Library 單一登入驗證您的應用程式](/zh-tw/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/)教學課程開始。


1. 登入 [Azure 管理入口網站]，按一下 [**行動服務**]，然後按一下您的行動服務。

    ![][1]

2. 按一下行動服務的 [**身分識別**] 索引標籤。 

    ![][2]

3. 向下捲動至 [**Azure Active 目錄**] 身分識別提供者區段，然後複製此處所列的 [**應用程式 URL**]。

    ![][3]

4. 導覽至管理入口網站中的 **Active Directory**，然後按一下您的目錄。

    ![][4] 	

5. 按一下頂端的 [**應用程式**] 索引標籤，然後按一下以 [**新增應用程式**] 。 

    ![][10]

6. 按一下 [**加入我的組織正在開發的應用程式**]。

7. 在 [新增應用程式精靈] 中，輸入應用程式的 [**名稱**]，然後按一下 [**Web 應用程式和/或 Web API**] 類型。接著，按一下以繼續。

    ![][5]

8. 在 [**登入 URL**] 方塊中，貼上您從行動服務的 Active Directory 身分識別提供者設定中複製的應用程式 ID。在 [**應用程式識別碼 URI**] 方塊中，輸入相同的唯一資源識別碼。接著，按一下以繼續。
 
    ![][6]


9. 新增應用程式之後，按一下 [**設定**] 索引標籤。按一下以複製應用程式的 [**用戶端識別碼**]。

    如果您建立行動服務是為了將 .Net 後端用於您的行動服務，請另外將 [**單一登入**] 底下的 [**回覆 URL**] 編輯為行動服務的 URL，後面加上路徑 _signin-aad_。例如， `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. 回到行動服務的 [**身分識別**] 索引標籤。在底部，貼上 Azure Active Directory 身分識別提供者的 [**用戶端識別碼**] 設定。

  
11. 在 [**允許的租用戶**] 清單中，需要新增您用來註冊應用程式之目錄的網域 (例如 contoso.onmicrosoft.com)。若要尋找您的預設網域名稱，可以按一下 Active Directory 上的 [**網域**] 索引標籤。

    ![][11]
 
    將您的網域名稱新增至[**允許的租用戶**] 清單，然後按一下 [**儲存**]。    


    ![][9]



現在，您已可在應用程式中使用 Azure Active Directory 進行驗證。 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Azure 管理入口網站]: https://manage.windowsazure.com/



<!--HONumber=42-->
