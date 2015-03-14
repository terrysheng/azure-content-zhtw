<properties 
	pageTitle="什麼是 Azure Active Directory？" 
	description="使用 Azure Active Directory 將現有的內部部署身分識別擴充到雲端，以期改善管理和使用者經驗，同時 Microsoft 利用高延展性、高可用性及整合式災害復原等能力維持 Active Directory 在雲端中運作。或者，為貴組織或其他組織開發 Azure AD 整合式應用程式。" 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="justinha"/>





<h1 id="whatisaad">什麼是 Azure Active Directory？</h1>

Azure Active Directory 是一項可在雲端中提供身分識別與存取管理功能的服務。就如同 Active Directory 是透過 Windows Server 作業系統提供給客戶的內部部署身分識別管理服務，Azure Active Directory (Azure AD) 是透過 Azure 提供的雲端型身分識別管理服務。[詳細資訊](http://msdn.microsoft.com/library/hh967611.aspx)

因為這是貴組織的雲端目錄，所以您可以決定誰是您的使用者、哪些資訊保留在雲端中、誰可以使用或管理這些資訊，以及哪些應用程式或服務可以存取這些資訊。 

當您使用 Azure AD 時，Microsoft 會負責讓雲端中執行的 Active Directory 保有高延展性、高可用性和整合式災難復原能力，同時完全尊重貴組織對資訊的隱私與安全性需求。

<h3>與內部部署 Active Directory 整合</h3>

Azure AD 可以用作貴組織的獨立式雲端目錄，但是您也可以將 Azure AD 與現有的內部部署 Active Directory 整合。部分整合功能包括目錄同步及單一登入，可將您現有內部部署身分識別的範圍進一步延伸至雲端，以改善系統管理員與一般使用者的體驗。 
 [詳細資訊](http://msdn.microsoft.com/library/jj573653)

<h3>與應用程式整合</h3>

應用程式開發人員可以將 Azure AD 與應用程式整合，以提供單一登入功能給其使用者。如此便可將企業應用程式裝載在雲端中，輕鬆利用公司認證來驗證使用者。如此也可讓 Azure AD 組織中的使用者透過軟體即服務 (SaaS) 提供者，更輕鬆地向服務驗明己身。開發人員也可以使用圖形 API 來查詢目錄資料，以管理使用者或群組等實體。[詳細資訊](http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409)

**其他資源**

* [以組織方式註冊 Azure](/zh-tw/manage/services/identity/organizational-account/)
* [Azure 身分識別](/zh-tw/manage/windows/fundamentals/identity/)
* [MSDN 上的 Azure AD 程式庫](http://go.microsoft.com/fwlink/?LinkId=293425)

<!--HONumber=46--> 
