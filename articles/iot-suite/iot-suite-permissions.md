<properties
  pageTitle="Azure IoT Suite 和 Azure Active Directory | Microsoft Azure"
  description="描述 Azure IoT Suite 如何使用 Azure Active Directory 來管理權限。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
  
# azureiotsuite.com 網站的權限

## 當您登入時會發生什麼事？

當您第一次在 [azureiotsuite.com][lnk-azureiotsuite] 登入時，此網站會根據目前選取的 Azure Active Directory (AAD) 租用戶和 Azure 訂用帳戶決定您擁有的權限等級。

1.  此網站會先從 Azure 找出您所屬的 AAD 租用戶，以便填入您已登入的使用者名稱旁邊顯示的租用戶清單。此時，網站一次只能取得一個租用戶的使用者權杖。如此一來，當您使用右上角下拉式清單切換至不同的租用戶時，此網站會讓您重新登入該租用戶，以取得該租用戶的權杖。

2.  接下來，網站會從 Azure 找出與所選租用戶有關聯的訂用帳戶。當您建立預先設定的新方案時，您會看到可用的訂用帳戶。

3.  最後，網站會擷取訂用帳戶中的所有資源以及標記為預先設定之方案的資源群組，並在首頁上填入動態磚。

下列各節說明的角色可控制預先設定之方案的存取。

## AAD 角色

AAD 角色控制在預先設定的方案中佈建預先設定的方案以及管理使用者的能力。

您可於[在 Azure AD 中指派管理員角色][lnk-aad-admin]中找到有關 AAD 中管理員角色的詳細資訊，但本文主要著重於預先設定的方案所使用的 [全域管理員] 和 [網域使用者/成員] 角色。

**全域管理員：**每一個 AAD 租用戶可以有許多全域管理員。當您建立 AAD 租用戶時，您會預設為該租用戶的全域管理員。全域管理員可以佈建預先設定的方案，並獲得其 AAD 租用戶的應用程式內部的 [管理員] 角色。不過，如果相同的 AAD 租用戶中有其他使用者建立應用程式，則全域管理員的預設角色會是 [隱含唯讀]。全域管理員可以使用 [Azure 傳統入口網站][lnk-classic-portal]來指派應用程式的角色。

**網域使用者/成員：**每一個 AAD 租用戶可以有許多網域使用者/成員。網域使用者可以透過 [azureiotsuite.com][lnk-azureiotsuite] 網站佈建預先設定的方案。他們針對其佈建的應用程式所獲得的預設角色為 [管理員]。他們可以使用 [azure-iot-solution][lnk-github-repo] 儲存機制中的 build.cmd 指令碼建立應用程式，但他們所獲得的預設角色為 [隱含唯讀]，因為他們沒有指派角色的權限。如果 AAD 租用戶中有其他使用者建立應用程式，他們預設會獲得該應用程式的 [隱含唯讀] 角色。他們無法指派應用程式的角色；因此無法新增應用程式 (即使是由他們所佈建) 的使用者或使用者角色。

**來賓使用者/來賓：**每一個 AAD 租用戶可以有許多來賓使用者/來賓。來賓使用者在 AAD 租用戶中有一組受限的權限。因此，來賓使用者無法在 AAD 租用戶中佈建預先設定的方案。

如需詳細資訊，請參閱下列資源：

- [在 Azure AD 中建立或編輯使用者][lnk-create-edit-users]
- [在 AAD 中指派應用程式角色][lnk-assign-app-roles]

## Azure 訂用帳戶管理員角色

Azure 管理員角色可控制將 Azure 訂用帳戶對應至 AD 租用戶的能力。

您可以在[如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員][lnk-admin-roles]一文中，深入了解 Azure 共同管理員、服務管理員和帳戶管理員角色。

## 應用程式角色

應用程式角色可控制您預先設定的方案中的裝置存取權。

當您佈建預先設定的方案時所建立的應用程式中，已定義兩個角色和一個隱含角色。

-   **管理員：**擁有完整控制權可新增、管理及移除裝置

-   **唯讀：**具備檢視裝置的能力

-   **隱含唯讀：**這與 [唯讀] 角色相同，但是會授與給 AAD 租用戶的所有使用者。這麼做是為了開發期間的方便性。修改 [RolePermissions.cs][lnk-resource-cs] 原始程式檔，即可移除此角色。

### 變更應用程式角色

您必須是 AAD 全域管理員才能變更使用者的角色：

1. 前往 [Azure 傳統入口網站][lnk-classic-portal]。

2. 選取 [**Active Directory**]

3. 按一下 AAD 租用戶的名稱。

4. 按一下 [應用程式]。

5. 如果清單中看不到您的應用程式，請將 [顯示] 下拉式功能表切換成 [我公司所擁有的應用程式]，然後按一下核取記號。

6. 按一下符合預先設定之方案名稱的應用程式名稱。

7. 按一下 [使用者]。

8. 選取您想要轉換角色的使用者。

9. 按一下 [指派] 按鈕和您想要指派的角色，再按一下核取記號。

## 常見問題集

### 我是服務管理員，我想要變更我的訂用帳戶與特定 AAD 租用戶之間的目錄對應。我要怎麼做？

1. 前往 [Azure 傳統入口網站][lnk-classic-portal]，然後按一下左側服務清單中的 [設定]。

2. 選取您要變更目錄對應的訂用帳戶。

3. 按一下 [編輯目錄]。

4. 選取下拉式清單中您要使用的 [目錄]。按一下向前箭號。

5. 確認目錄對應和受影響的共同管理員。請注意，如果您要從另一個目錄移動，則會移除原始目錄中的所有共同管理員。

### 我是 AAD 租用戶的網域使用者/成員，我已建立預先設定的方案。如何獲得我的應用程式的角色？

要求全域管理員將您指派為 AAD 租用戶的全域管理員，以取得自行指派角色給使用者的權限，或要求全域管理員指派角色給您。如果您想要變更預先設定的方案已部署至的 AAD 租用戶，請參閱下一個問題。

### 如何切換已指派給我的遠端監視預先設定之方案和應用程式的 AAD 租用戶？

您可以從 <https://github.com/Azure/azure-iot-remote-monitoring> 執行雲端部署，並透過新建立的 AAD 租用戶重新部署。因為當您建立新的 AAD 租用戶時，您會預設為全域管理員，所以您有新增使用者及指派角色給這些使用者的權限。

1. 在 [Azure 管理入口網站][lnk-classic-portal]中建立新的 AAD 目錄。

2. 移至 <https://github.com/Azure/azure-iot-remote-monitoring>。如需有關雲端部署的詳細資訊，請參閱[雲端部署][lnk-wiki-clouddeployment]。

3. 執行 `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (例如，`build.cmd cloud debug myRMSolution`)

4. 出現提示時，將 **tenantid** 設定為您新建立的租用戶，而不是您先前的租用戶。


### 我想要在使用組織帳戶登入時變更服務管理員或共同管理員

請參閱支援文章[在使用組織帳戶登入時變更服務管理員和共同管理員][lnk-service-admins]。

### 為什麼出現以下錯誤？ 「您的帳戶沒有適當的權限可建立方案。請洽詢您的帳戶管理員或嘗試使用不同的帳戶。」

看看下圖：

![][img-flowchart]

**當我有 Azure 訂用帳戶時為何會出現此錯誤？** *需要有 Azure 訂用帳戶，才能建立預先設定的方案。只需要幾分鐘的時間，您就可以建立免費試用帳戶。*

如果您確定有 Azure 訂用帳戶，請驗證您的訂用帳戶的租用戶對應，並確保已在下拉式清單中選取正確的租用戶。如果您已驗證所需的租用戶是否正確，請遵循上圖並驗證您的訂用帳戶與此 AAD 租用戶的對應。

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-github-repo]: https://github.com/Azure/azure-iot-solution
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Manually-setting-up-roles-and-assigning-permissions-in-Azure-Active-Directory-(AAD)#assigning-users-to-the-roles
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-wiki-clouddeployment]: https://github.com/Azure/azure-iot-remote-monitoring/wiki/Cloud-deployment

<!---HONumber=AcomDC_0218_2016-->