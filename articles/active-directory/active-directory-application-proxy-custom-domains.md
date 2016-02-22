<properties
	pageTitle="使用 Azure AD 應用程式 Proxy 中的自訂網域 | Microsoft Azure"
	description="說明如何使用 Azure AD 應用程式 Proxy 中的自訂網域。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# 使用 Azure AD 應用程式 Proxy 中的自訂網域
> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時才能使用的功能。如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

使用預設網域，可讓您將相同的 URL 設為可供存取應用程式的內部或外部 URL，而您的使用者在存取應用程式時只需記住一個 URL (不論他們是從哪裡進行存取)。這也讓您可以在應用程式的 [存取面板] 中建立單一捷徑。如果您使用 Azure AD 應用程式 Proxy 所提供的預設網域，則無需進行其他設定來啟用您的網域。在使用自訂網域的情況下，您需要執行一些動作，以確保應用程式 Proxy 能辨識您的網域並驗證其憑證。

## 選取自訂網域

1. 根據[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)中的指示，發佈您的應用程式。
2. 在應用程式出現在應用程式清單後，加以選取並按一下 [設定]。
3. 在 [外部 URL] 之下，輸入您的自訂網域。
4. 如果您的外部 URL 是 https，系統將提示您上傳憑證，以便 Azure 驗證應用程式的 URL。您也可以上傳符合應用程式外部 URL 的萬用字元憑證。這個網域必須在您的 [Azure 已驗證網域](https://msdn.microsoft.com/library/azure/jj151788.aspx)清單內。Azure 必須有應用程式之網域 URL 的憑證或符合應用程式之 [外部 URL] 的萬用字元憑證。
5. 請務必新增可將內部 URL 路由傳送至應用程式的 DNS 記錄，讓您有相同的 URL 以供內部和外部存取，以及在使用者的應用程式清單中有應用程式的單一捷徑。

## 有關使用自訂網域的常見問題

問：是否可以選取已上傳的憑證，而不再重新上載？ 答︰先前已上傳的憑證會自動繫結至應用程式，而只會有一個符合應用程式主機名稱的憑證。 …問：如何加入憑證，而已匯出的憑證應以何種格式上傳？ 答：憑證應從應用程式組態頁面進行上傳。此憑證應該是 PFX 檔案。 …問：是否可以使用 ECC 憑證？ 答：簽章方法沒有明確的限制。 …問：是否可以使用 SAN 憑證？ 答：可以。 ...問：是否可以使用萬用字元憑證？ 答：可以。 ...問：是否可以在每個應用程式上使用不同的憑證？ 答：可以，除非兩個應用程式共用相同的外部主機。 …問：如果我註冊新的網域，是否可以使用該網域？ 答：可以，網域清單是來自租用戶的已驗證網域清單。…問：憑證過期會發生什麼狀況？ 答：您會在應用程式組態頁面的 [憑證] 區段中收到警告。當使用者嘗試存取應用程式時，將會顯示安全性警告。 …問：我該如何取代指定應用程式的憑證？ 答：從應用程式組態頁面上傳新的憑證。 ...問：我是否可以刪除憑證並取代它？ 答：當您上傳新的憑證時，如果舊的憑證並未由另一個應用程式所使用，將會自動予以刪除。 ...問：憑證被撤銷時會發生什麼狀況？ 答：不會對憑證執行撤銷檢查。當使用者嘗試存取應用程式時，視瀏覽器而定，可能會出現安全性警告。 …問：是否可以使用自我簽署憑證？ 答：可以，可以使用自我簽署憑證。請注意，如果您使用私人憑證授權單位，則憑證的 CDP (憑證撤銷點散發點) 應是公開的。 ...問：是否有地方可以查看我的租用戶的所有憑證？ 答︰目前的版本不支援此功能。


## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)- [使用應用程式 Proxy 疑難排解您遇到的問題](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源
- [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
- [以組織方式註冊 Azure](sign-up-organization.md)
- [Azure 身分識別](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->