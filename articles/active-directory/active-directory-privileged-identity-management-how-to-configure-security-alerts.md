<properties
   pageTitle="Azure 特殊權限身分識別管理：如何設定安全性警示"
   description="了解如何為 Azure Privileged Identity Management 擴充功能設定安全性警示。"
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
   ms.author="inhenk"/>

# Azure Privileged Identity Management：如何設定安全性警示

## 安全性警示概觀
Azure Privileged Identity Management (PIM) 提供下列可設定的警示。您可以在 PIM 儀表板的 [警示] 區段中檢視安全性警示。

| 警示 | 觸發程序 |
| ------------- | ------------- |
| **懷疑遭受永久性啟用攻擊** | 系統管理員已在 PIM 外部啟用其暫時性角色。 |
| **特殊權限角色的可疑啟用更新** | 在設定所允許的時間內，重複啟用相同角色的次數過多。 |
| **蜂蜜權杖全域管理員使用者的可疑用法** | 偵測到「蜂蜜罐」使用者的用法。|
| **針對角色啟用設定弱式驗證** | 設定中有一些不含 MFA 的角色。 |
| **多餘的系統管理員會增加您的受攻擊面** | 有一些暫時性系統管理員未在設定的天數內啟用它們的角色。 |
| **太多全域管理員會增加您的受攻擊面** | 全域管理員的數目超過設定中所允許的數目。 |

## 設定安全性警示

### 設定「特殊權限角色的可疑啟用更新」警示
1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入分鐘數，來設定 [啟用更新時間範圍]。允許的數目上限為 100。
4. 在啟用更新時間範圍內，藉由調整滑桿或在文字欄位中輸入更新數目，來設定 [啟用更新數目]。更新數目上限為 100。
5. 按一下 [儲存]。

### 設定「多餘的系統管理員會增加您的受攻擊面」警示
1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入天數，來選取允許未啟用角色的天數。
4. 按一下 [儲存]。

### 設定「太多全域管理員會增加您的受攻擊面」警示

這個警示含有兩個可能觸發警示的設定。如果全域管理員的數目超過允許的系統管理員數目，則前者的數目下限將會觸發警示。如果系統管理員類型數目總和中的全域管理員百分比高過設定中的百分比，也會觸發警示。

1. 從儀表板的 [活動] 區段，選取 [安全性警示]。隨即會出現 [作用中的安全性警示] 刀鋒視窗。
2. 按一下 [設定]。
3. 調整滑桿或在文字欄位中輸入數目，來設定 [全域管理員數目下限]。
4. 調整滑桿或在文字欄位中輸入數目，來設定 [全域管理員的百分比]。
5. 按一下 [儲存]。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0128_2016-->