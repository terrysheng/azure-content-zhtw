<properties
   pageTitle="Microsoft Azure 多租用戶應用程式的身分識別管理 | Microsoft Azure"
   description="在多租用戶應用程式中進行驗證、授權和身分識別管理的最佳作法。"
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

# Microsoft Azure 多租用戶應用程式的身分識別管理

此系列說明多租用戶使用 Azure AD 進行驗證和身分識別管理時的最佳作法。

建置多租用戶應用程式時，最初會遇到管理使用者身分識別的難題，因為現在每位使用者各屬於一名租用戶。例如：

- 使用者透過其組織認證登入。
- 使用者應該可以存取其組織的資料，但不能存取屬於其他租用戶的資料。
- 組織可以註冊應用程式，並再將該應用程式角色指派給其成員。

Azure Active Directory (Azure AD) 有一些絕佳功能，能夠支援這些所有案例。

為了使此系列文章更加完整，我們還建立了多租用戶應用程式的完整[端對端實作][tailspin]。文章反映出我們在建置應用程式過程中學到的知識。若要開始使用應用程式，請參閱[執行調查應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) (英文)。


## 目錄

- [簡介](guidance-multitenant-identity-intro.md)
- [關於 Tailspin 調查應用程式](guidance-multitenant-identity-tailspin.md)
- [使用 Azure AD 驗證](guidance-multitenant-identity-authenticate.md)
- [使用宣告式身分識別](guidance-multitenant-identity-claims.md)
- [註冊和租用戶上架](guidance-multitenant-identity-signup.md)
- [定義和管理應用程式角色](guidance-multitenant-identity-app-roles.md)
- [Authorization](guidance-multitenant-identity-authorize.md)
- [保護後端 Web API](guidance-multitenant-identity-web-api.md)
- [快取 OAuth2 存取權杖](guidance-multitenant-identity-token-cache.md)
- [與客戶的 AD FS 同盟](guidance-multitenant-identity-adfs.md)
- [透過用戶端聲明從 Azure AD 取得存取權杖](guidance-multitenant-identity-client-assertion.md)
- [使用金鑰保存庫來保護應用程式的機密資訊](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->