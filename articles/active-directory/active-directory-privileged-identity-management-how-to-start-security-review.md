<properties
   pageTitle="Azure 特殊權限身分識別管理：如何開始安全性檢閱"
   description="了解如何為具備 Azure 特殊權限身分識別管理擴充功能的特殊權限身分識別建立安全性檢閱。"
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

# Azure Privileged Identity Management：如何開始安全性檢閱

## 開始安全性檢閱
最後，您可以在 Azure 入口網站的其他位置中執行安全性檢閱。本文件涵蓋的步驟可用來在**特殊權限身分識別管理 (PIM)** 介面內開始安全性檢閱。

或許有您不認得的使用者，或者有某位使用者已變更作業或專案，且在他們的新職位中已不再需要特殊存取權限。為了降低與這些「過時」角色指派相關聯的風險，您和其他系統管理員可以藉由啟動安全性檢閱來檢閱已授與使用者的角色。

## 開始安全性檢閱的路徑
> [AZURE.NOTE] 如果您尚未在 Azure 入口網站中建立 PIM 儀表板，請參閱[開始使用 Azure 特殊權限身分識別管理](active-directory-privileged-identity-management-getting-started.md)。

您可以依循下列路徑，從 Azure PIM 儀表板啟動檢視：

- 儀表板 > 安全性檢閱 > 檢閱 > [檢閱] 按鈕
- 儀表板 > 角色 > [檢閱] 按鈕
- 儀表板 > 按一下角色清單中要檢閱的角色 > [檢閱] 按鈕

## 開始安全性檢閱

當您按一下 [檢閱] 按鈕時，隨即會出現 [開始檢閱角色] 和 [選取要檢閱的角色] 刀鋒視窗，而且將會選取 [選取要檢閱的角色] 項目。

### 選取要檢閱的角色

1. 在 [選取要檢閱的角色] 刀鋒視窗中，從角色清單中選取角色。您一次只能選擇一個角色。[選取要檢閱的角色] 刀鋒視窗將會以 [選取檢閱者] 刀鋒視窗來取代。您在選取檢閱者時有下列兩個選項：
  - 我 - 如果您想要檢閱安全性檢閱的運作方式，而不涉及其他系統管理員，請使用此功能。
  - 由角色成員自我檢閱 - 使用此功能，讓使用者檢閱自己的角色指派。
2. 選取這其中一個，開始著手檢閱詳細資料。隨即會出現 [變更預設值] 刀鋒視窗。

### 自我檢閱

1. 在 [名稱] 欄位中輸入檢閱名稱，來為檢閱命名。建議您為檢閱提供獨特的名稱，來說明該檢閱且可讓您輕鬆追蹤該檢閱。
2. 在 [開始日期] 欄位中，輸入檢閱的開始日期。
3. 在 [結束日期] 欄位中，輸入檢閱的結束日期。在設定檢閱的結束日期時，您應該考量下列一些事項：
  - 要檢閱多少位使用者？
  - 使用者能夠新增此擴充功能並完成檢閱的速度有多快速？
4. 按一下 [變更預設值] 刀鋒視窗中的 [確定] 按鈕。它將會關閉。
5. 按一下 [啟動角色的檢閱] 刀鋒視窗中的 [確定] 按鈕。它將會關閉。Azure 入口網站的主功能表中隨即出現一則通知。按一下 [重新整理] 按鈕來重新整理儀表板，而安全性檢閱將會出現在 [安全性檢閱] 區段中。
6. 通知角色中的每個人，他們將需要新增該擴充功能，然後[檢閱他們自己的系統管理存取權](active-directory-privileged-identity-management-how-to-perform-security-review.md)。  

### 透過 [我] 來檢閱

如果您選取了 [我] 選項做為檢閱者，則請繼續進行安全性檢閱。如需更多完成檢閱的詳細資訊，請參閱 [Azure 特殊權限身分識別管理：如何執行安全性檢閱](active-directory-privileged-identity-management-how-to-perform-security-review.md)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## PIM 目錄
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->