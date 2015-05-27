<properties 
	pageTitle="註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證" 
	description="了解如何在 Azure Mobile Services 應用程式中註冊 Windows 市集應用程式進行 Microsoft 驗證。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# 註冊 Windows 市集應用程式封裝以進行 Microsoft 驗證

Azure 行動服務對於用戶端導向和伺服器導向的驗證方法均可支援。伺服器導向驗證會使用身分識別提供者，包括 Microsoft 帳戶在內。如果您未向行動服務註冊您的應用程式，即使用 Microsoft 帳戶進行伺服器導向驗證，則在每次要求驗證時，都會提示使用者提供認證。當您註冊應用程式時，Microsoft 帳戶登入認證會被快取，而此快取可用於後續的驗證，讓使用者不會再看見提供認證的提示。此主題將說明如何註冊 Windows 市集應用程式封裝，以提升使用 Azure 行動服務進行驗證時的 Microsoft 帳戶登入效能。

>[AZURE.NOTE]Visual Studio 2013 可讓您輕鬆地向行動服務註冊您的 Windows 市集應用程式封裝。如需詳細資訊，請參閱 Windows 開發人員中心的<a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">快速入門：新增行動服務的推播通知</a>。

用戶端管理的驗證可讓您透過 Live Connect，在 Windows 裝置上執行單一登入。如果您使用 Live Connect API，您即無須完成此主題中的步驟。如需詳細資訊，請參閱[使用 Live Connect 單一登入驗證 Windows 市集應用程式]。

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

註冊應用程式套件後，當您呼叫 <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> 方法時，請記得為  <em>useSingleSignOn</em> 提供 <strong>true</strong> 值。這可讓您的使用者在使用 Microsoft 帳戶時有更理想的登入體驗。

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[使用 Live Connect 單一登入驗證 Windows 市集應用程式]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with users C#]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with users JavaScript]: /develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=54-->