    <properties
	pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
	description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# 將擁有者和使用者加入研發/測試實驗室

## 概觀

研發/測試實驗室的存取權是由 Azure 角色型存取控制 (RBAC) 所控制。若要深入了解，可在 Azure Preview 入口網站中搜尋[角色型存取控制 (RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29)。

您可以透過下列兩個角色，將存取權授與您的研發/測試實驗室：

 - **擁有者**：已在 Azure 訂用帳戶層級指派給**擁有者**角色的使用者具備實驗室的完整存取權 (包括管理和監視功能)。

     >[AZURE.NOTE]研發/測試實驗室中不支援在訂用帳戶層級以外的 RBAC 層級指派的**擁有者**角色。不支援在研發/測試實驗室中將使用者指派給**擁有者**角色。

 -  **研發/測試實驗室使用者**：已指派給**研發/測試實驗室使用者**角色的使用者可以建立、更新和刪除指定實驗室中的 VM。使用者可以是*內部* (訂用帳戶的 Azure Active Directory 成員) 或*外部* (不屬於 Azure AD 成員的使用者，例如合作夥伴組織的成員)。
	-  **研發/測試實驗室使用者**角色必須透過實驗室的 [新增使用者] 磚來指派。
	-  **研發/測試實驗室使用者**角色中的使用者只能在指派他們的實驗室內部執行這些作業。例如， **研發/測試實驗室使用者**無法使用訂用帳戶的虛擬機器服務來建立虛擬機器。只允許從研發/測試實驗室帳戶建立虛擬機器。
	- *外部*使用者必須擁有其中一個 Microsoft 帳戶網域 (也就是 @hotmail.com、@live.com、@msn.com、@passport.com、@outlook.com，或者特定國家/地區的任何變體) 的帳戶。

## 將擁有者加入您的實驗室

研發/測試實驗室會考慮將包含實驗室的 Azure 訂用帳戶擁有者當成這些實驗室的擁有者。雖然您可以透過 Azure Preview 入口網站中實驗室的刀鋒視窗，將額外的擁有者加入研發/測試實驗室，但目前不支援此功能。

若要將擁有者加入您已在其中建立實驗室或將在其中建立新實驗室的 Azure 訂用帳戶，請遵循下列步驟：

1. 登入 [Azure Preview 入口網站](http://portal.azure.com)。

1. 在左側導覽中，點選 [訂用帳戶]。

	![訂用帳戶連結](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. 點選將包含實驗室的訂用帳戶。

1. 點選 [存取] 圖示。

	![存取使用者](./media/devtest-lab-add-devtest-user/access-users.png)

1. 在 [使用者] 刀鋒視窗中，點選 [新增]。

	![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在 [選取角色] 刀鋒視窗中，點選 [擁有者]。

1. 在 [使用者] 文字方塊中，輸入您想要新增為擁有者之使用者的電子郵件。如果找不到該使用者，您將會收到錯誤訊息來說明問題。如果找到使用者，該使用者將會列示於 [使用者] 文字方塊。

1. 點選找到的使用者名稱。

1. 點選 [選取]。

1. 點選 [確定] 以關閉 [新增存取] 刀鋒視窗。

1. 當您返回 [使用者] 刀鋒視窗時，您將會看到已將該使用者新增為擁有者。這位人員現在將是此訂用帳戶下方建立之所有實驗室的擁有者，因而能夠執行擁有者工作。

## 將研發/測試實驗室使用者加入您的實驗室

若要將研發/測試實驗室使用者加入您的實驗室，請遵循下列步驟：

1. 登入 [Azure Preview 入口網站](http://portal.azure.com)。

1. 點選 [瀏覽]。

1. 點選 [研發/測試實驗室]。

1. 從實驗室清單中，點選所需的實驗室。

1. 點選 [存取] 圖示。

	![使用者存取](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. 在 [使用者] 刀鋒視窗中，點選 [新增]。

	![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在 [選取角色] 刀鋒視窗中，點選 [研發/測試實驗室使用者]。

1. 在 [新增使用者] 刀鋒視窗中：

	1. [新增使用者] 刀鋒視窗將會顯示內建的使用者清單。如果所需的使用者已在清單中，您只需點選該使用者列來選取它。使用者左側出現核取記號表示已選取該使用者。若要選取多位使用者，請按住 **&lt;Ctrl>** 鍵，同時按一下每位使用者。若要取消選取使用者，請按住 **&lt;Ctrl>** 鍵，同時按一下該使用者。刀鋒視窗底部的計數器表示所選取的使用者數目。

	1. 如果所需的使用者不在清單中，請在 [使用者] 文字方塊中輸入有效的 Microsoft 電子郵件帳戶。如果電子郵件地址有效，使用者將會顯示在 [使用者] 文字方塊下方。只需點選它就能加以選取。

	1. 一旦您已選取想要新增到實驗室的使用者之後，請點選 [選取]。

	1. 點選 [確定] 以關閉 [新增存取] 刀鋒視窗。

	1. [使用者] 刀鋒視窗會顯示新增的角色及使用者。

<!---HONumber=Nov15_HO4-->