<properties
	pageTitle="如何新增或變更 Azure 管理員角色 | Microsoft Azure"
	description="說明如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="genli"/>

# 如何新增或變更 Azure 管理員角色

Microsoft Azure 中的管理員角色有三種：

| 管理角色 | 限制 | 說明
| ------------- | ------------- |---------------|
|帳戶管理員 (AA) | 每個 Azure 帳戶 1 名 |這就是註冊或購買 Azure 訂用帳戶，且經過授權可存取[帳戶中心](https://account.windowsazure.com/Home/Index)及執行各種管理工作的那個人。包括能夠建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式以及變更服務管理員。
| 服務管理員 (SA) | 每個 Azure 訂用帳戶 1 名 |此角色經過授權，可管理 [Azure 入口網站](https://portal.azure.com)上的服務。根據預設，新訂用帳戶的帳戶管理員也是服務管理員。|
|[Azure 傳統入口網站](https://manage.windowsazure.com)中的共同管理員 (CA)|每個訂用帳戶 200 名| 此角色的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。 |

> [AZURE.NOTE] Azure Active Directory 角色型存取控制 (RBAC) 能讓使用者擁有多個角色。如需詳細資訊，請參閱 [Azure Active Directory 角色型存取控制](./active-directory/role-based-access-control-configure.md)。

## 如何新增訂用帳戶的管理員

**Azure 入口網站**

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 [中樞] 功能表中，選取 [訂用帳戶] > 您想要讓管理員存取的訂用帳戶。

	![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. 在 [訂用帳戶] 刀鋒視窗中，選取 [設定]> [使用者]。

	![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. 在 [使用者] 刀鋒視窗中，選取 [新增] > [選取角色] > [擁有者]。

	![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

	**注意**
	- 擁有者角色具有與共同管理員相同的存取權限。此角色沒有 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)的存取權限。
	- 您透過 [Azure 入口網站](https://portal.azure.com)新增的擁有者無法管理 [Azure 傳統入口網站](https://manage.windowsazure.com)中的服務。  

5. 輸入您想要新增為擁有者的使用者的電子郵件地址，按一下使用者，然後按一下 [選取]。

	![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Azure 傳統入口網站**

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。

2. 在導覽窗格中，選取 [設定]> [管理員]> [新增]。</br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. 輸入您想新增為共同管理員之人員的電子郵件地址，然後選取您想讓共同管理員存取的訂用帳戶。</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

下列電子郵件地址可以新增為共同管理員：

* **Microsoft 帳戶** (先前稱為 Windows Live ID) </br> 您可以使用 Microsoft 帳戶登入所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、Skype (MSN)、OneDrive、Windows Phone 和 Xbox LIVE。
* **組織帳戶**</br> 組織帳戶是建立在 Azure Active Directory 之下的帳戶。組織帳戶地址的樣式如下：user@&lt;yourdomain&gt;.onmicrosoft.com

### 限制和約束

 * 每個訂用帳戶都與一個 Azure AD 目錄 (也就是預設目錄) 相關聯。若要尋找與訂用帳戶相關聯的預設目錄，請前往 [Azure 傳統入口網站](https://manage.windowsazure.com/)，然後選取 [設定] > [訂用帳戶]。請查看訂用帳戶識別碼來尋找預設目錄。

 * 如果您以 Microsoft 帳戶登入，就只能將其他 Microsoft 帳戶或預設目錄中的使用者新增為共同管理員。

 * 如果您以組織帳戶登入，就可以將組織中的其他組織帳戶新增為共同管理員。舉例來說，abby@contoso.com 可以將 bob@contoso.com 新增為服務管理員或共同管理員，但無法新增 john@notcontoso.com，除非 john@noncontoso.com 在預設目錄中。以組織帳戶登入的使用者，可以繼續將 Microsoft 帳戶使用者新增為服務管理員或共同管理員。

 * 現在可以使用組織帳戶登入至 Azure，以下是服務管理員和共同管理員帳戶需求的變更：

	登入方法| 將 Microsoft 帳戶或預設目錄中的使用者新增為 CA 或 SA？ |將相同組織中的組織帳戶新增為 CA 或 SA？ |將不同組織中的組織帳戶新增為 CA 或 SA？
	------------- | ------------- |---------------|---------------
	Microsoft 帳戶 |是|否|否
	組織帳戶|是|是|否

## 如何變更訂用帳戶的服務管理員

只有帳戶管理員可以變更訂用帳戶的服務管理員。

1. 使用帳戶管理員登入 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)。

2. 選取您想變更的訂用帳戶。

3. 按一下右側的 [編輯訂用帳戶] 詳細資料。</br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. 在 [服務管理員] 方塊中，輸入新的服務管理員的電子郵件地址。</br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## 如何變更帳戶管理員

若要將 Azure 帳戶的擁有權移轉到另一個帳戶，請參閱[移轉 Azure 訂用帳戶](billing-subscription-transfer.md)。

## 後續步驟

* 若要深入了解如何在 Microsoft Azure 中控制資源存取，請參閱[了解 Azure 中的資源存取](./active-directory/active-directory-understanding-resource-access.md)

* 如需有關 Azure Active Directory 如何與您的 Azure 訂用帳戶關聯的詳細資訊，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](./active-directory/active-directory-how-subscriptions-associated directory.md)

* 如需有關 Azure Active Directory 如何與您的 Azure 訂用帳戶關聯的詳細資訊，請參閱[在 Azure Active Directory (Azure AD) 中指派系統管理員角色](./active-directory/active-directory-assign-admin-roles.md)

<!---HONumber=AcomDC_0330_2016-->