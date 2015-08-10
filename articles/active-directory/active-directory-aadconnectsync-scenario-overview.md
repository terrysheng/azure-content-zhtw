<properties
	pageTitle="Azure AD Connect 同步處理：多樹系案例概觀"
    description="本主題的目標是探討一些常見的案例和這些案例在 Azure AD Connect 同步處理的同步服務中呈現的方式。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 同步處理：多樹系案例概觀

許多組織都有包含多個內部部署 Active Directory 樹系的環境。部署多個內部部署 Active Directory 的原因有很多。常見的範例如包含帳戶資源樹系、合併及收購相關的樹系，或用於外包資料的樹系的設計。

Microsoft 提供您適用於單一樹系案例的解決方案 DirSync，以及適用於多樹系案例的解決方案 FIM。不過，設定 FIM 可能相當困難而且可能會耗用大量的時間。

利用 Azure AD Connect 同步處理，您可以明顯地簡化設定，使其更容易預測。

在由 Azure AD Connect 同步處理所提供的預設組態中，有以下假設：

1. 使用者只有一個啟用的帳戶，且此帳戶所在之樹系用於和使用者建立同盟關係。
2. 使用者只有一個信箱。
3. 裝載使用者信箱的樹系具有最佳品質，以供屬性在 Exchange 全域通訊清單 (GAL) 中顯示。如果使用者沒有信箱，則任何樹系皆可用於提供這些屬性值。


本主題的目標是探討一些常見的案例和這些案例在 Azure AD Connect 同步處理的同步服務中呈現的方式：

1. 分隔技術 
2. 使用選擇性 GALSync 的完整網狀 
3. 帳戶資源樹系 


## 分隔技術

在此環境中，所有內部部署的樹系被視為不同的實體，且使用者不會出現在任何其他的樹系中。<br> 每個樹系具有自己的 Exchange 組織，且在樹系間沒有 GALSync。這個情況可能是合併/收購之後，或是在一個每個業務單位各自獨立作業的組織中。

![分隔技術][1]

在此圖中，每個樹系中的個別物件在 Metaverse 中會顯示一次，並在目標 Azure AD 目錄中彙總。這會與讓一個 DirSync 伺服器連接至每個來源 AD 樹系的最終結果相同。
 




## 使用選擇性 GALSync 的完整網狀

完整網狀拓樸可以讓使用者與資源位於任何樹系中，且通常在樹系間會有雙向信任。

如果 Exchange 出現在一個以上的樹系中，有可能是選擇性的 GALSync 解決方案將位在單一樹系的使用者表示為樹系間的連絡人。

在此案例中，身分識別的物件通常會使用郵件屬性來聯結。因此，在一個樹系中擁有信箱的使用者會與其他樹系中的連絡人聯結。通訊群組和安全性群組可以在每個樹系中找到，並且可以包含使用者、連絡人和 FSP (外部安全性主體) 的組合。

下圖概述此案例。

![使用選擇性 GALSync 的完整網狀][2]


## 帳戶資源樹系
在帳戶資源樹系拓樸中，您會在一個以上的樹系中擁有作用中的使用者帳戶。<br> 此案例包含一個信任所有帳戶樹系的樹系。此樹系通常具有擴充的 AD 結構描述與 Exchange 和 Lync。所有的 Exchange 和 Lync 服務以及其他共用的服務都位於此樹系。使用者在此樹系中擁有停用的使用者帳戶，且信箱連結至帳戶樹系。

下圖僅使用一個帳戶來概述此案例。

![帳戶資源樹系][3]


## 其他資源

* [Azure AD Connect 同步處理：自訂同步處理選項](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

 
<!--Image references-->
[1]: ./media/active-directory-aadsync-scenario-overview/ic750599.png
[2]: ./media/active-directory-aadsync-scenario-overview/ic750600.png
[3]: ./media/active-directory-aadsync-scenario-overview/ic750601.png

<!---HONumber=July15_HO5-->