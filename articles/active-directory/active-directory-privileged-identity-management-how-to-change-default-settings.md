<properties
   pageTitle="Azure 特殊權限身分識別管理：如何變更或檢視角色的預設設定"
   description="了解如何利用 Azure 特殊權限身分識別管理擴充功能，變更特殊權限身分的預設設定。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management：如何變更或檢視角色的預設啟用設定

## 變更及檢視預設的角色啟用
1. 從儀表板按一下要從角色資料表設定的角色。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入時數，以設定預設啟用持續時間 (小時)。
4. 如果您想要將啟用通知傳送給管理員，請按一下 [啟用]，如果不想要傳送，請按一下 [停用]。
5. 按一下 [啟用] 或 [停用]，即可允許或不允許管理員將票證資訊輸入啟用要求中。
6. 按一下 [啟用] 或 [停用]，即可設定啟用要求需要或不需要多重要素驗證。如需關於搭配 PIM 使用 MFA 的詳細資訊，請參閱[如何要求 MFA](active-directory-privileged-identity-management-how-to-require-mfa.md)。
7. 按一下 [啟用] 或 [停用]，可讓全域管理員為暫時性。 
8. 按一下 [儲存]。

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->