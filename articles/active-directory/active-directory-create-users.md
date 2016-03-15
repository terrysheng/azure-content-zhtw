<properties
	pageTitle="在 Azure Active Directory 中建立或編輯使用者 | Microsoft Azure"
	description="說明如何在 Azure Active Directory 中建立或編輯使用者帳戶的主題。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/03/2016"
	ms.author="curtand;viviali"/>

# 在 Azure AD 中建立或編輯使用者

您必須為每一個會存取 Microsoft Cloud 的使用者在租用戶目錄中建立帳戶。您也可以變更使用者帳戶，或在不需要時將它們刪除。根據預設，使用者沒有系統管理員權限，但是您可以指派給他們。

## 建立使用者

1. 按一下 [Active Directory]，然後選取貴組織的目錄名稱。
2. 在 [**使用者**] 頁面上，按一下 [**加入使用者**]。
3. 在 [**告訴我們這位使用者**] 頁面上，針對 [**使用者類型**]，選取下列其中一項：

	- **您組織中的新使用者** – 在您的目錄中建立新的使用者帳戶
	- **現有 Microsoft 帳戶的使用者** – 將現有的 Microsoft 取用者帳戶加入至您的目錄 (例如，Outlook 帳戶)
	- **另一個 Azure AD 目錄中的使用者** – 將源自另一個 Azure AD 目錄的使用者帳戶加入至您的目錄 (注意：您必須是其他目錄的成員才能選取其中的使用者)
	- **合作夥伴公司中的使用者** - 邀請並授權合作夥伴公司使用者使用您的目錄 ([請參閱 Azure Active Directory B2B 共同作業](active-directory-b2b-what-is-azure-ad-b2b.md))


4. 視您選取的選項而定，輸入使用者名稱、電子郵件地址，或上傳 CSV 檔案，其中包括登入使用者的電子郵件地址。
5. 在 [**使用者設定檔**] 頁面上，提供使用者的姓氏和名字、使用者易記名稱，並從 [角色] 下拉式選單中選擇使用者角色。如需有關使用者和系統管理員角色的詳細資訊，請參閱[在 Azure AD 中指派系統管理員角色](active-directory-assign-admin-roles.md)。指定是否**啟用 Multi-Factor Authentication**。
6. 在 [**取得暫時密碼**] 頁面上，按一下 [**建立**]。

如果您的組織使用多個網域，當您建立使用者帳戶時，請注意下列問題：

- 如果您先建立 geoffgrisso@contoso.onmicrosoft.com再建立 geoffgrisso@contoso.com，則可以跨網域建立具有相同使用者主體名稱 (UPN) 的使用者帳戶。
- 您無法先建立 geoffgrisso@contoso.com 再建立 geoffgrisso@contoso.onmicrosoft.com。

## 編輯使用者

在 Azure 傳統入口網站中編輯使用者：

1. 按一下 [**Active Directory**]，然後按一下貴組織的目錄名稱。
2. 在 [**使用者**] 頁面上，按一下您想要編輯之使用者的顯示名稱。
3. 完成您的變更，然後按一下 [**儲存**]。

如果您嘗試編輯的使用者已經與內部部署 Active Directory 服務同步處理，則會出現錯誤訊息，您將無法使用此程序來編輯使用者。若要編輯此使用者，使用您的本機 Active Directory 管理工具。

## 重設使用者的密碼

1. 按一下 [**Active Directory**]，然後按一下貴組織的目錄名稱。
2. 在 [**使用者**] 頁面上，按一下您想要編輯之使用者的顯示名稱。
3. 在入口網站底部，按一下 [**重設密碼**]。
4. 在 [重設密碼] 對話方塊中，按一下 [**重設**]。
5. 按一下核取記號，以確認已重設密碼。

## 建立外部使用者

在 Azure AD 中，您也可以藉由上傳 CSV 檔案，將來自您所屬的另一個 Azure AD 目錄且具有 Microsoft 帳戶的使用者，或來自合作夥伴公司的使用者，加入 Azure AD 目錄。若要建立外部使用者，請在入口網站中新增使用者，針對 [使用者類型] 選取 [另一個 Azure AD 目錄中的使用者] 或 [合作夥伴公司中的使用者]。

使用者或這兩種類型的使用者是源自另一個目錄，並且建立為 [外部使用者]。外部使用者可以使用單一帳戶，與已存在於目錄中的使用者共同作業，而不需要使用新的帳戶和認證登入。外部使用者登入時由其主目錄驗證，驗證結果適用於已加入的其他所有目錄。

## 外部使用者管理和限制

當您將另一個目錄的使用者加入至您的目錄時，該使用者在您的目錄中就是外部使用者。最初會從使用者的「主目錄」複製顯示名稱和使用者名稱，並指定給您的目錄中的外部使用者。從那時起，外部使用者帳戶的這些和其他屬性就完全獨立：如果您在主目錄中對使用者做變更，例如變更使用者的名稱、新增職稱等，這些變更不會傳播至您的目錄中的外部使用者帳戶。

兩個帳戶之間的唯一連結是，使用者永遠針對主目錄或使用其 Microsoft 帳戶進行驗證。這就是為什麼您沒有看到選項可對外部使用者重設密碼或啟用多重要素驗證：目前，使用者登入時，只會評估主目錄或 Microsoft 帳戶的驗證原則。

> [AZURE.NOTE]
您仍然可以停用目錄中的外部使用者，這樣會阻止存取您的目錄。

如果使用者在其主目錄中被刪除，或取消其 Microsoft 帳戶，外部使用者仍存在您的目錄中。不過，該使用者無法存取您的目錄中的資源，因為使用者無法再向主目錄或 Microsoft 帳戶進行驗證。

以下是目前支援讓 Azure AD 外部使用者存取的服務：

- **Azure 傳統入口網站**：允許身為多個目錄的管理員的外部使用者，管理這些目錄
- **SharePoint Online**：如果啟用外部共用，允許外部使用者存取 SharePoint Online 授權資源
- **Dynamics CRM**：如果使用者透過 PowerShell 獲得授權，允許外部使用者存取 Dynamics CRM 中的授權資源

以下是 Azure AD 外部使用者的已知限制：

- 身為管理員的外部使用者無法將來自合作夥伴公司的使用者加入至其主目錄以外的目錄 (B2B 共同作業)
- 外部使用者無法同意在其主目錄以外的目錄中的多租用戶應用程式
- Visual Studio Online 目前不支援讓外部使用者存取*
- PowerBI 目前不支援讓外部使用者存取
- Office 入口網站不支援授權外部使用者

* Visual Studio Online 不允許使用 Microsoft 帳戶進行驗證的外部使用者，但使用工作或學校帳戶進行驗證的外部使用者不在此限。

## 來賓使用者管理和限制

來賓是指您的目錄中將 UserType 屬性設為 [來賓] 的使用者帳戶。一般使用者的 UserType 屬性為 [成員]，表示他們是您目錄中的成員。來賓表示來自其他目錄的使用者已受邀至您的目錄，可以存取特定資源，例如 SharePoint Online 文件、應用程式或 Azure 資源。

來賓在目錄中有一組受限的權限。這些權限禁止「來賓」探索目錄中其他使用者的相關資訊，但對於與他們使用的資源相關聯的使用者和群組，仍然能夠互動。來賓使用者可以：

- 查看與他們被指派的 Azure 訂用帳戶相關聯的使用者和群組
- 查看其所屬群組的群組成員
- 如果他們已經知道使用者的完整電子郵件地址，則可以查閱目錄中的其他使用者
- 僅能查看他們查閱的使用者的有限屬性集 - 僅限於顯示名稱、電子郵件地址、使用者主體名稱 (UPN) 和相片縮圖
- 取得租用戶中已驗證的網域清單
- 同意應用程式，授與它們與在您的目錄中相同的成員存取權

## 設定使用者存取原則

目錄內的 [設定] 索引標籤內含可控制外部使用者存取權限的選項。這些選項只可以由目錄全域管理員透過 Azure 傳統入口網站的 UI 進行變更，不提供使用 Windows PowerShell 或 API 變更的方法。若要在 Azure 傳統入口網站中開啟 [**設定**] 索引標籤，請按一下 [**Active Directory**]，然後按一下目錄的名稱。

![在 Azure Active Directory 中設定索引標籤][1]

接著您可以編輯選項來控制外部使用者的存取權限。

![][2]


## 接下來

- [管理 Azure AD](active-directory-administer.md)
- [在 Azure AD 中管理密碼](active-directory-manage-passwords.md)
- [在 Azure AD 中管理群組](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png

<!---HONumber=AcomDC_0309_2016-->