<properties
   pageTitle="Azure 特殊權限身分識別管理：如何使用稽核記錄"
   description="了解如何在 Azure 特殊權限身分識別管理擴充功能中使用稽核記錄。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure 特殊權限身分識別管理：如何使用稽核記錄

## 使用稽核記錄
您可以使用「特殊權限身分識別管理」稽核記錄，來查看指定期間內的所有使用者指派與啟用。

## 瀏覽到稽核記錄
您可以按一下 PIM 儀表板中的 [稽核記錄]，來存取稽核記錄。

## 稽核記錄圖表
您可以使用稽核記錄，在折線圖中檢視啟用總數、每天最大啟用數目，以及每天平均啟用數目。如果稽核記錄中有一個以上的角色，您也可以依角色篩選資料。

使用 [時間]、[動作] 或 [角色] 按鈕，依時間、動作或角色排序。

## 稽核記錄清單
稽核記錄清單中的欄如下：

- **要求者** - 要求角色啟用的人。
- **使用者** - 角色啟用的使用者。
- **角色** - 指派給使用者的角色。
- **動作** - 角色/使用者所採取的動作。
- **時間** - 動作發生的時間。
- **推理** - 如果在啟用過程中於 [原因] 欄位輸入任何文字，則它將會顯示於此處。
- **到期** - 如果到期年份是 9999，則表示使用者永久擁有該角色。

## 篩選稽核記錄

您也可以按一下 [篩選] 按鈕，來篩選要稽核記錄中顯示的資訊。隨即會出現 [更新圖表參數] 刀鋒視窗。

### 變更日期範圍
選取 [今天]、[過去一週]、[過去一個月] 或 [自訂] 等按鈕的其中一個，來變更稽核記錄的時間範圍。當您選擇 [自訂] 按鈕時，將會看見 [開始] 日期欄位和 [結束] 日期欄位，來為記錄指定所需的日期範圍。您可以使用 MM/DD/YYYY 格式輸入日期，或者按一下 [月曆] 圖示並從月曆中選擇日期。

### 變更記錄中包含的角色

選取或取消選取您想要從記錄中包含或排除之每個角色旁邊的 [角色] 核取方塊。

為稽核記錄設定所有篩選之後，按一下更新來篩選記錄中的資料。如果資料並未立即出現，可按一下 [重新整理] 按鈕。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->