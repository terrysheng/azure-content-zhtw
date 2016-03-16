<properties
   pageTitle="Azure 特殊權限身分識別管理：如何開始將角色新增到使用者"
   description="了解如何將角色新增到具備 Azure 特殊權限身分識別管理擴充功能的特殊權限身分識別。"
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
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management：如何新增或移除使用者角色

## 新增或移除使用者角色
有數種方式可以瀏覽到 PIM 介面的 [新增受管理的使用者] 刀鋒視窗。以下列出每個項目的點選順序：

- 儀表板 > 管理員角色中的使用者 > 新增或移除
- 儀表板 > 角色摘要 > 所有使用者清單 > 新增或移除
- 儀表板 > 按一下角色表格中的使用者角色 (例如全域管理員) > 新增或移除

## 將使用者新增至角色
一旦瀏覽到 [新增受管理的使用者] 刀鋒視窗：

1. 按一下 [選取角色]。如果您是按一下角色表格中的使用者角色而瀏覽到此處，則該角色將是已經選取的。
2. 從角色清單中選取角色。例如，[密碼管理員]，隨即會開啟 [選取使用者] 刀鋒視窗。
3. 在搜尋欄位中輸入使用者名稱。如果使用者位於目錄中，則他們的帳戶顯示方式就像您輸入其他具有類似名稱的使用者。
4. 選取清單中的使用者，然後按一下 [完成]。
5. 按一下 [確定] 以儲存您的選取項目。您選取的使用者將會出現在清單中，而角色將是暫時性的。
6. 如果想要讓角色變成永久，請按一下清單中的使用者。該使用者的資訊即會出現在新的刀鋒視窗中。在使用者資訊功能表中選取 [變成永久]。
7. 按一下 [啟用]，以開始提出為使用者啟用此角色的要求。在 [要求理由] 文字欄位中輸入啟用的原因。此時將會為此使用者自動啟用角色，並傳送通知給全域管理員。

## 從角色移除使用者
1. 使用上述說明的其中一個路徑，瀏覽到使用者角色清單中的使用者。
2. 按一下使用者清單中的使用者。
3. 按一下 [移除]。隨即會看到確認訊息。
4. 按一下 [是]，即會從使用者中移除角色。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->