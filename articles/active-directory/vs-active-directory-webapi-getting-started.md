<properties 
	pageTitle="開始使用 Azure Active Directory 和 Visual Studio 已連接服務 (WebApi 專案) | Microsoft Azure" 
	description="使用 Visual Studio 已連接服務連接 Azure AD 或建立 Azure AD 後，如何在 WebApi 專案中開始使用 Azure Active Directory" 
  services="active-directory"
	documentationCenter="" 
	authors="TomArcher" 
	manager="douge" 
	editor=""/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/18/2015" 
	ms.author="tarcher"/>

# 開始使用 Azure Active Directory 和 Visual Studio 已連接服務 (WebApi 專案)

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##存取控制器之前需要驗證
 
專案中的所有控制器都加上 **Authorize** 屬性做裝飾。此屬性要求使用者必須經過驗證，才能存取這些控制器所定義的 API。若要允許以匿名方式存取控制器，請從控制器中移除此屬性。如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

[深入了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
 

<!---HONumber=AcomDC_0128_2016-->