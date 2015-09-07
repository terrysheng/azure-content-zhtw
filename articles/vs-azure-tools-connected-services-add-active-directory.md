<properties 
   pageTitle="使用 Visual Studio 的已連接服務加入 Azure Active Directory | Microsoft Azure"
	description="使用 Visual Studio 的 [加入已連接服務] 對話方塊加入 Azure Active Directory"
	services="visual-studio-online"
	documentationCenter="na"
	authors="patshea123"
	manager="douge"
	editor="tlee"/>
<tags  ms.service="visual-studio-online"" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="visual-studio-online" ms.date="08/12/2015" ms.author="patshea"/>

# 在 Visual Studio 中使用已連接服務加入 Azure Active Directory 

##概觀
使用 Azure Active Directory (Azure AD)，您可以對 ASP.NET MVC Web 應用程式或 Web API 服務中的 AD 驗證支援單一登入 (SSO)。透過 Azure AD 驗證，您的使用者可以使用其帳戶從 Azure AD 連接到您的 Web 應用程式。透過 Web API 的 Azure AD 驗證優點包括從 Web 應用程式公開 API 時的資料安全性會增強。有了 Azure AD，您不必以各自的帳戶和使用者管理作業來管理個別的驗證系統。

## 支援的專案類型

您可以使用 [已連接服務] 對話方塊來連接到 Azure AD 中的下列專案類型。

- ASP.NET MVC 專案

- ASP.NET Web API 專案


### 使用已連接服務對話方塊連接到 Azure AD

1. 確定您具有 Azure 帳戶。如果您沒有 Azure 帳戶，您可以註冊[免費試用](http://go.microsoft.com/fwlink/?LinkId=518146)。

1. 在 Visual Studio 中，開啟您專案中 [參考] 節點的快顯功能表，然後選擇 [加入已連接服務]。
1. 選取 [Azure AD 驗證]，然後選擇 [設定]。

    ![選擇加入 Azure AD 驗證](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. 在 [設定 Azure AD 驗證] 的第一頁上，核取 [使用 Azure AD 設定單一登入]。

    如果您的專案是使用另一份驗證組態所設定，則精靈會警告您繼續執行將會停用先前的組態。

    ![在精靈中設定 Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1.  第二頁上，選取 [網域] 下拉式清單中的網域。網域清單包含 [帳戶設定] 對話方塊中所列的帳戶可存取的所有網域。或者，如果找不到您所尋找的網域 (例如 mydomain.onmicrosoft.com)，您可以輸入網域名稱。您可以選擇用以建立新 Azure AD 應用程式的選項，或使用現有 Azure AD 應用程式的設定。

    ![在精靈中設定 Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)


1. 在精靈的第三頁上，確定已核取 [讀取目錄資料]。精靈將會填入 [用戶端密碼]。

    ![在精靈中設定 Azure AD](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. 選擇 [完成] 按鈕。對話方塊會加入必要的組態程式碼和參考，讓您的專案啟用 Azure AD 驗證。您可以在 Azure 入口網站上看到 AD 網域。

1. 檢閱瀏覽器中顯示的 [開始使用] 頁面以取得後續步驟的概念，並檢閱 [發生什麼情形] 頁面來查看專案的修改內容。如果您想要檢查是否一切正常，請開啟其中一個已修改的組態檔，並確認其中包含 [發生什麼情形] 中所提及的設定。例如，ASP.NET MVC 專案中的主要 web.config 必須加入這些設定：

        <appSettings> 
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:AADInstance" value="https://login.windows.net/" />
            <add key="ida:Domain" value="Your selected domain" />
            <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
            <add key="ida:PostLogoutRedirectUri" value="The default redirect URI from the project" />
        </appSettings>

## 您的專案修改方式

當您執行精靈時，Visual Studio 會將 Azure AD 和相關聯的參考加入至您的專案。您專案中的組態檔和程式碼也會進行修改，以加入 Azure AD 支援。Visual Studio 所做的特定修改視專案類型而定。如需如何修改 ASP.NET MVC 專案的詳細資訊，請參閱[發生什麼情形 – MVC 專案](http://go.microsoft.com/fwlink/p/?LinkID=513809)。若為 Web API 專案，請參閱[發生什麼情形 – Web API 專案](http://go.microsoft.com/fwlink/p/?LinkId=513810)。

##後續步驟

提出問題並取得協助。

 - [MSDN 論壇︰Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)

 - [Azure AD 文件](http://azure.microsoft.com/documentation/services/active-directory/)

 - [部落格文章：Azure AD 簡介](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

<!---HONumber=August15_HO9-->