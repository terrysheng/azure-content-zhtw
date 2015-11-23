<properties
	pageTitle="如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員 | Microsoft Azure"
	description="說明如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="genli"/>

# 如何新增或變更 Azure 共同管理員、 服務管理員和帳戶管理員
## 管理員的角色

Microsoft Azure 中的管理員角色有三種：

| 管理角色 | 限制 | 說明
| ------------- | ------------- |---------------|
|帳戶管理員 | 每個 Azure 帳戶 1 名 |這是註冊或購買 Azure 訂用帳戶的人員，經授權可存取帳戶中心和執行各種管理工作。包括能夠建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式以及變更服務管理員。
| 服務管理員 | 每個 Azure 訂用帳戶 1 名 |此人員經授權可存取所指派帳戶中所有訂用帳戶的 Azure 管理入口網站。根據預設，新訂用帳戶的帳戶管理員即服務管理員。|
|共同管理員|每個訂用帳戶 200 名 (服務管理員除外)|此人員的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。|

## 加入訂用帳戶的共同管理員
1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2. 在導覽窗格中，選取 [**設定**] > [**管理員**] > [**加入**]。 </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 輸入您想加入為共同管理員之人員的電子郵件地址，然後選取您想讓共同管理員存取的訂用帳戶。</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

下列電子郵件地址可以新增為共同管理員：

* **Microsoft 帳戶** (先前稱為 Windows Live ID) </br> 您可以使用 Microsoft 帳戶登入所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone 和 Xbox LIVE。
* **組織帳戶**</br> 組織帳戶是建立在 Azure Active Directory 之下的帳戶。組織帳戶地址的樣式如下：user@<your domain>.onmicrosoft.com

**注意**

 * 如果您是以 Microsoft 帳戶登入，就只能將其他 Microsoft 帳戶加入為共同管理員。這是基於安全的考量，以防止非組織帳戶探索某些帳戶 (例如 janedoe@contoso.com) 是否為有效帳戶。
 * 如果您是以組織帳戶登入，您可以在組織中加入其他組織帳戶做為共同管理員。例如，abby@contoso.com 可以將 bob@contoso.com 加入為服務管理員或共同管理員，但無法將 john@notcontoso.com 加入。使用組織帳戶登入的使用者可以繼續新增 Microsoft 帳戶使用者做為服務管理員或共同管理員。
 * 現在可以使用組織帳戶登入至 Azure，以下是服務管理員和共同管理員帳戶需求的變更：

| 登入方法| 是否可將 Microsoft 帳戶加入為共同管理員或服務管理員？ |是否可將相同組織的組織帳戶加入為共同管理員或服務管理員？ |是否可將不同組織的組織帳戶加入為共同管理員或服務管理員？
| ------------- | ------------- |---------------|---------------|
|Microsoft 帳戶 |是|否|否|
|組織帳戶|是|是|否|

## 變更訂用帳戶的服務管理員
只有帳戶管理員可以變更訂用帳戶的服務管理員。

1. 使用帳戶管理員的身分登入[帳戶管理入口網站](https://account.windowsazure.com/subscriptions)。
2. 選取您想變更的訂用帳戶。
3. 按一下右側的 [編輯訂用帳戶] 詳細資料。</br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. 在 [服務管理員] 方塊中，輸入新的服務管理員的電子郵件位址。![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## 變更帳戶管理員

若要將 Azure 帳戶的擁有權移轉到另一個帳戶，請參閱[移轉 Azure 訂用帳戶](../billing-subscription-transfer.md)。

<!---HONumber=Nov15_HO3-->