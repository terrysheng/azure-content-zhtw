<properties
	pageTitle="Azure Active Directory 混合式身分識別設計考量 - 判斷多重要素驗證需求"
	description="透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。一旦符合這些條件，就會驗證使用者並允許存取應用程式。"
	documentationCenter=""
	services="active-directory"
	authors="femila"
	manager="billmath"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="11/11/2015"
	ms.author="billmath"/>

# 判斷混合式身分識別解決方案的多重要素驗證需求

在這個具備行動力的世界中，使用者可在雲端中或從任何裝置存取資料和應用程式，因此，保護此資訊就成為最重要的項目。每天都會出現關於安全性漏洞的新標題。雖然不會針對這類漏洞提供保證，但多重要素驗證還是可以提供額外的安全性層級來協助防止這些漏洞。一開始，先評估組織對於多重要素驗證的需求。也就是，組織試圖保護哪些項目。當您定義如何設定和啟用組織使用者來進行多重要素驗證的技術需求時，這項評估非常重要。

>[AZURE.NOTE]如果您不熟悉 MFA 和它的功用，強烈建議您先閱讀[什麼是 Azure 多重要素驗證？](multi-factor-authentication.md)一文，然後再繼續閱讀本節。

請確定回答下列問題：

- 貴公司試圖保護 Microsoft app 嗎？ 
- 這些 app 是如何發佈的？
- 貴公司是否提供遠端存取，讓員工可以存取內部部署 app？

如果是，是哪種類型的遠端存取？您也需要評估存取這些應用程式的使用者所在位置。這項評估是另一個定義適當多重要素驗證策略的步驟。請確定回答下列問題：

- 使用者即將位於何處？
- 他們將無所不在嗎？
- 貴公司是否想要根據使用者的位置來建立限制？

一旦了解這些需求之後，請務必同時評估使用者對於多重因素驗證的需求。這項評估很重要是因為它將定義首度發行多重要素驗證的需求。請確定回答下列問題：

- 使用者是否熟悉多重要素驗證？
- 需要透過有一些用法才能提供額外的驗證嗎？  
 - 如果是，一直都是、是在來自外部網路時、存取特定應用程式時，或者是在其他情況下？
- 使用者是否要求訓練如何設定和實作多重要素驗證？
- 哪些是貴公司想要為使用者啟用多重因素驗證的主要案例？

回答上述問題之後，您將能夠了解是否已經在內部部署中實作多重要素驗證。當您定義如何設定和啟用組織使用者來進行多重要素驗證的技術需求時，這項評估非常重要。請確定回答下列問題：

- 貴公司需要使用 MFA 保護具備特殊權限的帳戶嗎？
- 貴公司需要基於相容性因素，針對某些應用程式啟用 MFA 嗎？
- 貴公司需要針對這些應用程式的合格使用者，或只針對系統管理員啟用 MFA？
- 您需要一律啟用 MFA，或者只有在使用者於公司網路外部登入時啟用？


## 後續步驟
[定義混合式身分識別採用策略](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## 另請參閱
[設計考量概觀](active-directory-hybrid-identity-design-considerations-directory-overview.md)

<!---HONumber=Nov15_HO3-->