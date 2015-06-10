<properties 
	pageTitle="Azure AD Connect 運作方式" 
	description="深入了解 Azure AD Connect 運作方式。" 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="billmath"/>

# Azure AD Connect 運作方式


<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/active-directory-aadconnect/" title="它是什麼" class="current">它是什麼</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-how-it-works/" title="運作方式">運作方式</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-get-started/" title="開始使用">開始使用</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-whats-next/" title="下一步">下一步</a> <a href="/zh-tw/documentation/articles/active-directory-aadconnect-learn-more/" title="深入了解">深入了解</a>
</div>

Azure Active Directory Connect 是由構成 Azure Active Directory Connect 的三個主要組件所組成。它們是同步處理服務、選用性 Active Directory Federation Services 部分，以及使用 Azure AD Connect Health 所執行的監控部分。


<center>![Azure AD Connect 堆疊](./media/active-directory-aadconnect-how-it-works/AADConnectStack.png) </center>

- 同步處理 - 此組件是由先前發行為 Dirsync 和 AAD Sync 的元件和功能所組成。這是負責建立使用者和群組的組件。它也會負責確保您內部部署環境中的使用者和群組資訊符合雲端中的資訊。
- AD FS - 這是 Azure AD Connect 的選用性組件，而且可以用來使用內部部署 AD FS 基礎結構來設定混合環境。組織可以使用此組件來處理複雜部署，而複雜部署包括網域加入 SSO、AD 登入原則的強制執行以及智慧卡或協力廠商 MFA 這類項目。如需設定 SSO 的詳細資訊，請參閱[搭配單一登入的 DirSync](https://msdn.microsoft.com/library/azure/dn441213.aspx)。
- 狀況監控 - 對於使用 AD FS 的複雜部署，Azure AD Connect Health 可以提供同盟伺服器的健全監控，並在 Azure 入口網站中提供提供此活動的中央位置。如需詳細資訊，請參閱 [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx)。






**其他資源**

* [在雲端中使用內部部署身分識別基礎結構](active-directory-aadconnect.md)
* [開始使用 Azure AD Connect](active-directory-aadconnect-get-started.md)
* [使用 Azure AD Connect 的下一步](active-directory-aadconnect-whats-next.md)
* [深入了解](active-directory-aadconnect-learn-more.md)
* [MSDN 上的 Azure AD Connect](https://msdn.microsoft.com/library/azure/dn832695.aspx)

<!---HONumber=58-->