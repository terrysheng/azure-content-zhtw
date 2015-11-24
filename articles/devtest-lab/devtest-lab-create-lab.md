    <properties
	pageTitle="Create a DevTest Lab | Microsoft Azure"
	description="Create a new DevTest Lab lab for virtual machines"
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
	ms.topic="get-started-article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# 建立 Azure DevTest 實驗室

## 必要條件

若要建立 DevTest 實驗室，您必須：

- Azure 訂用帳戶。若要深入了解 Azure 購買選項，請參閱[如何購買 Azure](http://azure.microsoft.com/pricing/purchase-options/) 或[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。您必須是訂用帳戶的擁有者才能建立實驗室。
- 實驗室的 Azure 資源群組。請參閱 [Azure 資源管理員概觀](resource-group-overview.md)和[管理和稽核資源存取權](./azure-portal/resource-group-rbac.md)。


## 建立實驗室

1. 登入 [Azure Preview 入口網站](http://portal.azure.com)。

1. 點選 [瀏覽]。

1. 從清單點選 [DevTest 實驗室]。

1. 在 [DevTest 實驗室] 刀鋒視窗上，點選 [加入]。

    ![加入 DevTest 實驗室](./media/devtest-lab-create-lab/add-lab-button.png)

1. 在 [建立 DevTest 實驗室] 刀鋒視窗上：

    1. 輸入新實驗室的**實驗室名稱**。
    1. 選取要與實驗室關聯的**訂用帳戶**。
    1. 選取用來儲存實驗室的 [位置]。
    1. 點選 [建立]。

    ![建立 DevTest 實驗室刀鋒視窗](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## 後續步驟

建立您的實驗室之後，以下是要考慮的一些後續步驟：

- [安全存取 DevTest 實驗室](devtest-lab-add-devtest-user.md)。

- [設定實驗室原則](devtest-lab-set-lab-policy.md)。

- [建立實驗室範本](devtest-lab-create-template.md)。

- [為您的 VM 建立自訂成品](devtest-lab-artifact-author.md)。

- [將具有構件的 VM 加入至 Azure DevTest 實驗室](devtest-lab-add-vm-with-artifacts.md)。

<!---HONumber=Nov15_HO4-->