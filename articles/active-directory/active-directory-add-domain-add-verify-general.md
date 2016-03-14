<properties
	pageTitle="新增及驗證 Azure Active Directory 中的自訂網域名稱 | Microsoft Azure"
	description="如何將現有的網域新增至 Azure Active Directory 以開始使用 Azure AD。設定您的自訂網域，與您的內部部署身分識別基礎結構同步處理使用者帳戶資訊。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>

# Add and verify a custom domain name in Azure Active Directory (新增及驗證 Azure Active Directory 中的自訂網域名稱)

若要新增自訂網域名稱，並加以驗證以搭配您的 Azure Active Directory 使用，您必須完成下列步驟。

1.  執行下列其中一項：

    -   [新增將會與內部部署 Active Directory 同盟的自訂網域名稱](#add-a-custom-domain-name-that-will-be-federated)

    -   [新增將不會與內部部署 Active Directory 同盟的自訂網域名稱](#add-a-custom-domain-name-that-will-not-be-federated)

2.  驗證自訂網域名稱。

    -   新增 Azure AD 將用來在您網域的網域名稱註冊機構驗證您的組織是否擁有自訂網域名稱的 DNS 項目。

    -   驗證 Azure AD 中的網域。

## 新增將會同盟的自訂網域名稱

如需有關使用 Azure AD Connect 進行內部部署目錄整合的詳細資訊，請參閱

**將自訂網域名稱新增至您的 Azure AD 目錄**

1.  在 Azure AD 中，使用具備全域管理員權限的帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2.  選取 Active Directory。

3.  開啟您的目錄。

4.  選取 [網域] 索引標籤。

5.  在命令列上選取 [新增]。

6.  輸入您的自訂網域名稱。請務必包含.com 副檔名。

7.  選取 [我計劃將這個網域設定為可使用我的本機 Active Directory 進行單一登入] 核取方塊。

8.  按一下 [新增]。

## 新增將不會同盟的自訂網域名稱

大部分的自訂網域名稱為次層網域 "contoso.com"

**將自訂網域名稱新增至您的 Azure AD 目錄**

1.  在 Azure AD 中，使用具備全域管理員權限的帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2.  選取 Active Directory。

3.  開啟您的目錄。

4.  選取 [網域] 索引標籤。

5.  在命令列上選取 [新增]。

6.  輸入您的自訂網域名稱。請務必包含.com 副檔名。

7.  請確認已清除 [我計劃將這個網域設定為可使用我的本機 Active Directory 進行單一登入] 核取方塊。

8.  選取 [新增]。

9.  查看您的網域是否已新增至目錄。

## 驗證任何網域名稱註冊機構上的網域

若要驗證網域，請在網域名稱註冊機構或裝載 DNS 的任何位置上建立 DNS 記錄，Azure AD 接著會使用該記錄確認您擁有該網域。[在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

如果您已經向網域名稱註冊機構註冊網域，則必要的 DNS 記錄已經存在。

當您已新增自訂網域但尚未驗證網域時，狀態會顯示為 [未驗證]。

## 驗證將不會與內部部署目錄同盟的自訂網域名稱
於網域註冊機構成功透過 DNS 系統新增您針對網域建立的任何記錄之後，請執行下列動作：

1.  在 [Azure 傳統入口網站](https://manage.windowsazure.com/)的 Azure Active Directory 中，按一下 [網域]。

2.  在 [**網域**] 清單中，尋找您要驗證的網域，然後根據您使用的入口網站，按一下 [**按一下以驗證網域**] 或 [**驗證**]。

3.  遵循提供的指示來完成驗證程序。

    -   如果網域驗證成功，將會通知您：已將網域新增至帳戶。

    -   如果網域驗證失敗，則您在網域註冊機構所做的任何變更可能需要更多時間進行傳播。取消驗證，稍後再試一次。

如果變更網域後已超過 72 個小時，請登入網域註冊機構的網站，並確認您已正確地輸入別名資訊。如果您輸入的資訊不正確，您必須移除不正確的 DNS 記錄，並使用正確資訊建立一個新的記錄。

## 驗證您的自訂網域是否已和您的內部部署目錄同盟

1.  下載並執行 Azure AD Connect。Azure AD Connect 工具將[提示您新增提供給您的 DNS 項目](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

## 第三層網域名稱

您可以使用第三層網域 (例如 "europe.contoso.com") 搭配 Azure AD。新增與使用第三層網域：

1.  新增並驗證第二層網域 "contoso.com"

2.  將任何子網域 (例如 "europe.contoso.com") 新增至 Azure AD。當您新增已驗證之第二層網域的子網域時，第三層網域會自動由 Azure AD 驗證。不需要再加入任何其他的 DNS 項目。

這些步驟也可以使用 PowerShell 和 Graph 來完成。

驗證網域之後，即可設定網域來使用帳戶。

## 後續步驟

- [使用自訂網域名稱，以簡化您的使用者的登入體驗](active-directory-add-domain.md)
- [在登入和存取面板頁面加上公司商標](active-directory-add-company-branding.md)
- [將使用者指派至自訂網域](active-directory-add-domain-add-users.md)
- [變更您的自訂網域名稱的 DNS 註冊機構](active-directory-add-domain-change-registrar.md)
- [刪除 Azure Active Directory 中的自訂網域](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0302_2016-->