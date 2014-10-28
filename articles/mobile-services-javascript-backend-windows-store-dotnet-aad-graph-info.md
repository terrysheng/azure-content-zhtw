<properties linkid="develop-mobile-tutorials-javascript-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# 存取 Azure Active Directory 圖形資訊

<div class="dev-center-tutorial-selector sublanding">
<a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/" title="Windows 市集 C#" class="current">Windows 市集 C#</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title=".NET 後端">.NET 後端</a> |
<a href="/zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="JavaScript 後端" class="current">JavaScript 後端</a>
</div>

如同其他隨行動服務提供的身分識別提供者，Azure Active Directory (AAD) 提供者也支援豐富的[圖形 API][圖形 API]，可用於對目錄的程式設計存取。在本教學課程中，您會根據使用[圖形 API][圖形 API] 從目錄中擷取的其他使用者資訊來更新 ToDoList 應用程式，以個人化已驗證的使用者應用程式體驗。

> [WACOM.NOTE] 本教學課程的目的是要擴充您使用 Azure Active Directory 進行驗證的知識。您應已使用 Azure Active Directory 驗證提供者完成[開始使用驗證][開始使用驗證]教學課程。本教學課程接著將更新[開始使用驗證][開始使用驗證]教學課程中使用的 TodoItem 應用程式。

本教學課程將逐步引導您完成下列步驟：

1.  [在 AAD 中產生應用程式註冊的存取金鑰][在 AAD 中產生應用程式註冊的存取金鑰]
2.  [建立 GetUserInfo 自訂 API][建立 GetUserInfo 自訂 API]
3.  [更新應用程式以使用自訂 API][更新應用程式以使用自訂 API]
4.  [測試應用程式][測試應用程式]

## 必要條件

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

-   [開始使用驗證][開始使用驗證]
    將登入需求新增至 TodoList 範例應用程式。

-   [自訂 API 教學課程][自訂 API 教學課程]
    說明如何呼叫自訂 API。

## <a name="generate-key"></a>在 AAD 中產生應用程式註冊的存取金鑰

在進行[開始使用驗證][開始使用驗證]教學課程期間，您已在完成[註冊使用 Azure Active Directory 登入][註冊使用 Azure Active Directory 登入]步驟時，為整合的應用程式建立註冊。在本節中，您將產生在使用該整合的應用程式用戶端識別碼讀取目錄資訊時所將使用的金鑰。

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]

## <a name="create-api"></a>建立 GetUserInfo 自訂 API

在本節中，您將建立 GetUserInfo 自訂 API，以使用[圖形 REST API][圖形 REST API] 從 AAD 擷取使用者的其他相關資訊。

如果您未曾搭配使用自訂 API 與行動服務，您應考慮先參閱[自訂 API 教學課程][自訂 API 教學課程]，再開始執行本節步驟。

1.  在 [Azure 管理入口網站][Azure 管理入口網站]中，為您的行動服務建立新的 GetUserInfo 自訂 API，然後將 get 方法的參數設為 [僅限通過驗證的使用者]。

    ![][]

2.  為新的 GetUserInfo API 開啟指令碼編輯器，並將下列變數移至指令碼的最上方。

        var appSettings = require('mobileservice-config').appSettings;
        var tenant_domain = appSettings.AAD_TENANT_DOMAIN;
        var clientID = appSettings.AAD_CLIENT_ID;
        var key = appSettings.AAD_CLIENT_KEY;

3.  為 `getAADToken` 函數新增下列定義。

        function getAADToken(callback) {
            var req = require("request");
            var options = {
                url: "https://login.windows.net/" + tenant_domain + "/oauth2/token?api-version=1.0",
                method: 'POST',
                form: {
                    grant_type: "client_credentials",
                    resource: "https://graph.windows.net",
                    client_id: clientID,
                    client_secret: key
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body).access_token);
            });
        }

    在給定 *tenant\_domain*、整合的應用程式*用戶端識別碼*和應用程式*金鑰*的情況下，此函數會提供用來讀取目錄資訊的圖形存取權杖。

4.  新增下列會使用圖形 API 傳回使用者資訊的 `getUser` 函數。

        function getUser(access_token, objectId, callback) {
            var req = require("request");
            var options = {
                url: "https://graph.windows.net/" + tenant_domain + "/users/" + objectId + "?api-version=1.0",
                method: 'GET',
                headers: {
                    "Authorization": "Bearer " + access_token
                }
            };
            req(options, function (err, resp, body) {
                if (err || resp.statusCode !== 200) callback(err, null);
                else callback(null, JSON.parse(body));
            });
        };

    此函數是圖形 REST API 的[取得使用者][取得使用者]端點附近的精簡型包裝函式。它會使用圖形存取權杖和使用者的物件識別碼，來取得使用者資訊。

5.  依照下列方式使用其他函數更新匯出的 `get` 方法，以傳回使用者資訊。

        exports.get = function (request, response) {
            if (request.user.level == 'anonymous') {
                response.send(statusCodes.UNAUTHORIZED, null);
                return;
            }
            var errorHandler = function (err) {
                console.log(err);
                response.send(statusCodes.INTERNAL_SERVER_ERROR, err);
            };
            request.user.getIdentities({
                success: function (identities) {
                    var objectId = identities.aad.oid;
                    getAADToken(function (err, access_token) {
                        if (err) errorHandler(err);
                        else getUser(access_token, objectId, function (err, user_info) {
                            if (err) errorHandler(err);
                            else {
                                console.log('GetUserInfo: ' + JSON.stringify(user_info, null, 4));
                                response.send(statusCodes.OK, user_info);
                              }
                            });
                    });
                },
                error: errorHandler
            });
        };

## <a name="update-app"></a>更新應用程式以使用 GetUserInfo

在本節中，您會更新您在[開始使用驗證][開始使用驗證]教學課程中實作的 `AuthenticateAsync` 方法，以呼叫自訂 API 並從 AAD 傳回使用者的其他相關資訊。

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]

## <a name="test-app"></a>測試應用程式

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]

## <a name="next-steps"></a>後續步驟

在下一個教學課程[行動服務中使用 AAD 的角色型存取控制][行動服務中使用 AAD 的角色型存取控制]中，您將會搭配使用角色型存取控制與 Azure Active Directory (AAD) 來檢查群組成員資格，然後允許存取。

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [Windows 市集 C#]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-aad-graph-info/ "Windows 市集 C#"
  [.NET 後端]: /zh-tw/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ ".NET 後端"
  [JavaScript 後端]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "JavaScript 後端"
  [圖形 API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
  [開始使用驗證]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [在 AAD 中產生應用程式註冊的存取金鑰]: #generate-key
  [建立 GetUserInfo 自訂 API]: #create-api
  [更新應用程式以使用自訂 API]: #update-app
  [測試應用程式]: #test-app
  [自訂 API 教學課程]: /zh-tw/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api/
  [註冊使用 Azure Active Directory 登入]: /zh-tw/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [圖形 REST API]: http://msdn.microsoft.com/zh-tw/library/azure/hh974478.aspx
  [Azure 管理入口網站]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/create-getuserinfo.png
  [取得使用者]: http://msdn.microsoft.com/zh-tw/library/azure/dn151678.aspx
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [行動服務中使用 AAD 的角色型存取控制]: /zh-tw/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/
