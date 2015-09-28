<properties
   pageTitle="Azure 資料目錄必要條件"
   description="我需要什麼才能開始使用 Azure 資料目錄？"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/11/2015"
   ms.author="maroche"/>

# 我需要什麼才能開始使用 Azure 資料目錄？

## Azure 資料目錄必要條件

您必須注意幾件事，才能設定 **Azure 資料目錄**。別擔心 – 不會很久！

## Azure Active Directory

Azure Active Directory (Azure AD) 為您的企業提供在雲端和內部部署中，輕鬆管理身分識別和存取的方式。使用者可以使用單一公司帳戶或學校帳戶，以單一登入方法登入任何雲端和內部部署 Web 應用程式。Azure 資料目錄採用 Azure AD 來驗證登入。若要深入了解，請參閱[如何開始使用 Azure AD](active-directory-get-started.md)。

## Active Directory 原則組態

使用者可能會遇到一種情況，他們可以登入「Azure 資料目錄」入口網站，但在嘗試登入資料來源註冊工具時卻遇到錯誤訊息，導致無法登入。僅在使用者處於公司網路或是從公司網路外部連接時，才會發生此問題行為。

資料來源註冊工具使用「表單驗證」，以根據 Active Directory 驗證使用者登入。Active Directory 系統管理員必須在全域驗證原則中啟用表單驗證，登入才會成功。

「全域驗證」原則允許分別為內部網路和外部網路連接啟用驗證方法，如下圖所示。如果使用者連線的網路未啟用表單驗證，可能會發生登入錯誤。

 ![Active Directory 全域驗證原則](./media/data-catalog-prerequisites/global-auth-policy.png)

如需詳細資訊，請參閱「[設定驗證原則](https://technet.microsoft.com/zh-TW/library/dn486781.aspx)」。


## Azure 訂閱
Azure 訂用帳戶可協助您組織雲端服務資源的存取權，例如 Azure 資料目錄。它們也可協助您控制如何根據資源使用量產生報告、計費及付費。每一個訂用帳戶可以有不同的計費和付款設定，因此，依照部門、專案、區域辦事處等，您可以有不同的訂用帳戶和不同的計劃。每一個雲端服務都屬於某個訂用帳戶，在設定 Azure 資料目錄之前，您必須先有訂用帳戶。若要深入了解，請參閱[管理帳戶、訂用帳戶及管理角色](https://msdn.microsoft.com/library/azure/hh531793.aspx)。

<!---HONumber=Sept15_HO3-->