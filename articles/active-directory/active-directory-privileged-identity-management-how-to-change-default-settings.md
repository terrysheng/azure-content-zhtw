<properties
   pageTitle="Azure AD Privileged Identity Management：如何變更或檢視角色的預設設定"
   description="了解如何利用 Azure AD Privileged Identity Management 擴充功能，變更特殊權限身分識別的預設設定。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/10/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management：如何變更或檢視角色的預設啟用設定

## 變更及檢視預設的角色啟用
1. 從儀表板按一下要從角色資料表設定的角色。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入時數，以設定預設啟用持續時間 (小時)。
4. 如果您想要將啟用通知傳送給管理員，請按一下 [啟用]，如果不想要傳送，請按一下 [停用]。
5. 按一下 [啟用] 或 [停用]，即可允許或不允許管理員將票證資訊輸入啟用要求中。
6. 按一下 [啟用] 或 [停用]，即可設定啟用要求需要或不需要多重要素驗證。

    您無法針對 Azure AD 和 Office365 中具有極高權限的角色停用 MFA，包括︰

    - 全域管理員
    - 使用者帳戶管理員
    - 目錄寫入器
    - 合作夥伴第 1 層支援
    - 合作夥伴第 2 層支援
    - 計費管理員
    - 安全性系統管理員
    - Exchange 系統管理員
    - 信箱管理員
    - 商務用 Skype 的管理員
    - SharePoint 管理員
    - 規範管理員

7. 按一下 [啟用] 或 [停用]，可讓全域管理員為暫時性。
8. 按一下 [儲存]。

如需關於搭配 PIM 使用 MFA 的詳細資訊，請參閱[如何要求 MFA](active-directory-privileged-identity-management-how-to-require-mfa.md)。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->