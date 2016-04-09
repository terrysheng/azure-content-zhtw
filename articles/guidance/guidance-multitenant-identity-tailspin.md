<properties
   pageTitle="關於 Tailspin Surveys 應用程式 | Microsoft Azure"
   description="Tailspin Surveys 應用程式概觀"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# 有關 Tailspin Surveys 應用程式

本文是[系列文章的其中一篇]。這一系列另外還附有完整的[範例應用程式]。

Tailspin 是虛構的公司，他們正在開發名為 Surveys 的 SaaS 應用程式。此應用程式可讓組織建立並發佈線上問卷。

- 組織可以註冊該應用程式。
- 組織註冊之後，使用者可以使用他們組織的認證登入應用程式。
- 使用者可以建立、編輯和發佈問卷。

> [AZURE.NOTE] 若要開始使用應用程式，請參閱 [Running the Surveys application (執行 Surveys 應用程式)]。

此螢幕擷取畫面顯示 [Edit Survey] \(編輯問卷) 頁面：

![編輯問卷](media/guidance-multitenant-identity/edit-survey.png)

請注意，該使用者是使用他的組織身分識別 `bob@contoso.com` 登入。

使用者可以檢視相同租用戶中的其他使用者所建立的問卷。

![租用戶問卷](media/guidance-multitenant-identity/tenant-surveys.png)

當使用者建立問卷時，他或她可以邀請其他人參與該問卷。參與者可以編輯問卷，但不能刪除或發佈問卷。

![加入參與者](media/guidance-multitenant-identity/add-contributor.png)

使用者可以從其他租用戶加入參與者，因此能夠跨租用戶共用資源。在此螢幕擷取畫面中，Bob (`bob@contoso.com`) 正在將 Alice (`alice@fabrikam.com`) 加入以成為 Bob 所建立之問卷的參與者。

當 Alice 登入時，她會看見該問卷列在 [Surveys I can contribute to] \(我可以參與的問卷) 之下。

![問卷參與者](media/guidance-multitenant-identity/contributor.png)

請留意，Alice 是用自己的租用戶登入，而不是以 Contoso 租用戶之來賓的身分登入。Alice 只對該問卷有參與者權限 &mdash; 她不能檢視其他來自 Contoso 租用戶的問卷。

## 架構

Surveys 應用程式由 Web 前端和 Web API 後端組成。兩者都使用 [ASP.NET 核心 1.0] 實作。

該 Web 應用程式使用 Azure Active Directory (Azure AD) 來驗證使用者。該 Web 應用程式也會呼叫 Azure AD 來取得 Web API 的 OAuth 2 存取權杖。取存取權杖快取在 Azure Redis Cache 中。快取可讓多個執行個體共用相同的權杖快取 (例如，在伺服器陣列中)。

![架構](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[系列文章的其中一篇]: guidance-multitenant-identity.md
[Running the Surveys application (執行 Surveys 應用程式)]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET 核心 1.0]: https://docs.asp.net/en/latest/
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!------HONumber=AcomDC_0302_2016-------->