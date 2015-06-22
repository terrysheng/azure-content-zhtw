<properties
	pageTitle="開始使用 Azure AD iOS | Microsoft Azure"
	description="如何建置 iOS 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# 將 Azure AD 整合至 iOS 應用程式

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

如果您正在開發桌面應用程式，Azure AD 讓使用 Active Directory 帳戶驗證您的使用者變得簡單明瞭。它也可讓您的應用程式安全地使用任何受 Azure AD 保護的 Web API，例如 Office 365 API 或 Azure API。

對於需要存取受保護資源的 iOS 用戶端，Azure AD 提供 Active Directory 驗證程式庫 (或簡稱 ADAL)。ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。為了示範它有多麼容易使用，我們會在此建置可達行下列作業的目標 C 待辦事項清單應用程式：

-	使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)取得呼叫 Azure AD Graph API 的存取權杖。
-	在目錄中搜尋具有指定別名的使用者。
-	將使用者登出。

若要建立完整可用的應用程式，您必須：

2. 向 Azure AD 註冊您的應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始使用，請[下載應用程式基本架構](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)或[下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。如果您還沒有租用戶，[了解如何取得租用戶](active-directory-howto-tenant.md)。

## 步驟 1：下載並執行 .Net 或 Node.js REST API TODO 範例伺服器

本範例是根據現有範例，為建置 Microsoft Azure Active Directory 的單一租用戶 ToDo REST API 特別撰寫而成。這是快速入門的必要條件。

如需有關如何設定此項目的相關資訊，請瀏覽我們的現有範例：

* [適用於 Node.js 的 Microsoft Azure Active Directory 範例 REST API 服務](active-directory-devquickstarts-webapi-nodejs.md)

## 步驟 2：向 Microsoft Azure AD 租用戶註冊 Web API

**我要做什麼？**

*Microsoft Active Directory 支援加入兩種類型的應用程式。Web API 提供服務給可存取這些 Web API 的使用者和應用程式 (無論是在 Web 或在裝置上執行的應用程式)。在此步驟中，您要註冊在本機執行且用來測試此範例的 Web API。這個 Web API 通常是 REST 服務，提供您想要應用程式存取的功能。Microsoft Azure Active Directory 可以保護任何端點！*

*在本範例中，我們假設您正要註冊上述的 TODO REST API，但這適用於任何您想要 Azure Active Directory 保護的 Web API。*

向 Microsoft Azure AD 註冊 Web API 的步驟

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中按一下 Active Directory。
3. 按一下您要註冊範例應用程式的目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在下拉式清單中，按一下 [新增]。
6. 按一下 [加入我的組織正在開發的應用程式]。
7. 輸入應用程式的易記名稱，例如 "TodoListService"，選取 [Web 應用程式和/或 Web API]，然後按 [下一步]。
8. 在 [登入 URL] 中，輸入範例的基礎 URL，預設為 `https://localhost:8080`。
9. 在 [應用程式識別碼 URI] 中，輸入 `https://<your_tenant_name>/TodoListService`，將 `<your_tenant_name>` 換成您的 Azure AD 租用戶名稱。按一下 [確定] 以完成註冊。
10. 在 Azure 入口網站中時，按一下您的應用程式的 [設定] 索引標籤。
11. **找出 [用戶端識別碼] 值，將它複製到別處**，稍後設定應用程式時需要此值。

## 步驟 3：註冊範例 iOS 原生用戶端應用程式

第一個步驟是註冊您的 Web 應用程式。接下來，您也需要讓 Azure Active Directory 知道您的應用程式。這可讓您的應用程式與剛註冊的 Web API 通訊

**我要做什麼？**

*如上所述，Microsoft Active Directory 支援加入兩種類型的應用程式。Web API 提供服務給可存取這些 Web API 的使用者和應用程式 (無論是在 Web 或在裝置上執行的應用程式)。在此步驟中，您要註冊此範例中的應用程式。您必須執行此動作，此應用程式才能要求存取您剛剛註冊的 Web API。除非註冊應用程式，否則 Azure Active Directory 甚至會拒絕讓您的應用程式要求登入！ 這是模型安全性的一部分。*

*在本範例中，我們假設您要註冊上述的範例應用程式，但這適用於任何您想要開發的應用程式。*

**為什麼我要將應用程式和 Web API 都放在一個租用戶？**

*您可能已經猜到，您可以建置應用程式來存取在另一個租用戶的 Azure Active Directory 中註冊的外部 API。如果您這麼做，系統會提示您的客戶同意您可以使用應用程式中的 API。幸好，Active Directory Authentication Library for iOS 會替您處理此同意舉動！ 隨著我們深入更進階的功能，您將了解這是從 Azure 和 Office 及任何其他服務提供者存取 Microsoft API 套件時所需的一件重要工作。現在，因為您在相同租用戶下註冊您的 Web API 和應用程式，因此您不會看到任何要求同意的提示。如果您開發的應用程式僅供自己公司使用，通常會是這種情況。*

1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com)。
2. 在左側導覽中按一下 Active Directory。
3. 按一下您要註冊範例應用程式的目錄租用戶。
4. 按一下 [應用程式] 索引標籤。
5. 在下拉式清單中，按一下 [新增]。
6. 按一下 [加入我的組織正在開發的應用程式]。
7. 為應用程式輸入一個好記的名稱，例如 "TodoListClient-iOS"，選取 [原生用戶端應用程式]，然後按 [下一步]。
8. 在 [重新導向 URI] 中，輸入 `http://TodoListClient`。按一下 [完成]。
9. 按一下應用程式的 [設定] 索引標籤。
10. 找出 [用戶端識別碼] 值，將它複製到別處，稍後設定應用程式時需要此值。
11. 在 [其他應用程式的權限] 中，按一下 [新增應用程式]。 在 [顯示] 下拉式清單中選取 [其他]，然後按一下上方的核取記號。找到並按一下 TodoListService，然後按一下底部的核取記號，以新增應用程式。從 [委派的權限] 下拉式清單中選取 [存取 TodoListService]，並儲存組態。


## 步驟 4：下載 iOS 原生用戶端範例程式碼

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## 步驟 5：下載 ADAL for iOS，並將它加入您的 XCode 工作區

#### 下載 ADAL for iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### 將程式庫匯入到工作區中

在 XCode 中，以滑鼠右鍵按一下您的專案目錄，然後選取 [將檔案加入 iOS 範例]...

當系統出現提示時，請選取您複製 ADAL for iOS 的目錄

#### 將 libADALiOS.a 加入至您的連結架構和程式庫

按一下 [連結架構和程式庫] 底下的 [新增] 按鈕，並加入匯入架構的程式庫檔案。

建置專案，以確定正確編譯所有項目。


## 步驟 6：使用您的 Web API 資訊來設定 settings.plist 檔案

您會在 [支援檔案] 中找到 settings.plist 檔案。其中包含下列資訊：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

使用您的 Web API 設定來取代 plist 檔案中的資訊。

##### 注意

[適用於 Node.js 的 Azure Active Directory 範例 REST API 服務](https://github.com/AzureADSamples/WebAPI-Nodejs)。不過，您必須指定您 Web API 的 clientID。如果您正在執行自己的 API，您必須視需要更新端點。

## 步驟 7：建置並執行應用程式

您應該能夠連接至 REST API 端點，並會收到 Azure Active Directory 帳戶的認證提示。

如需其他資源，請參閱：- [GitHub 上的 AzureADSamples >>](https://github.com/AzureAdSamples) - [Azure.com 上的 Azure AD 文件 >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58--> 