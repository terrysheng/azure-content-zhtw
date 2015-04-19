<properties 
	pageTitle="" 
	description="開始使用 Azure Active Directory (Web API 專案) 精靈的相關資訊。" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
  
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [開始使用](vs-active-directory-webapi-getting-started.md)
> - [發生什麼情形](vs-active-directory-webapi-what-happened.md)


##開始使用 Azure Active Directory (Web API 專案)

#####存取控制器之前需要驗證
 
專案中的所有控制器都加上 **Authorize** 屬性做裝飾。此屬性要求使用者必須經過驗證，才能存取這些控制器所定義的 API。若要允許以匿名方式存取控制器，請從控制器中移除此屬性。如果您要以更精確地設定權限，請將此屬性套用至每一個需要授權的方法，而非套用至控制器類別。

[深入了解 Azure Active Directory](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=46--> 
