1.  登入 [Azure 管理入口網站]、按一下 [行動服務]，然後按一下您的行動服務。

    ![][]

2.  按一下 [推播] 索引標籤、針對 [權限] 選取 [僅限驗證的使用者]，然後按一下 [編輯指令碼]。

    ![][1]

    這樣讓您能夠自訂推播通知註冊回呼函數。如果您使用 Git 來編輯原始程式碼，就可以在 `.\service\extensions\push.js` 中找到這個相同的註冊函數。

3.  使用下列程式碼來取代現有的 **register** 函數：

        exports.register = function (registration, registrationContext, done) {   
            // Get the ID of the logged-in user.
            var userId = registrationContext.user.userId;    

            // Perform a check here for any disallowed tags.
            if (!validateTags(registration))
            {
                // Return a service error when the client tries 
                // to set a user ID tag, which is not allowed.      
                done("You cannot supply a tag that is a user ID");      
            }
            else{
                // Add a new tag that is the user ID.
                registration.tags.push(userId);

                // Complete the callback as normal.
                done();
            }
        };

        function validateTags(registration){
            for(var i = 0; i < registration.tags.length; i++) { 
                console.log(registration.tags[i]);           
                if (registration.tags[i]
                .search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
                    return false;
                }
                return true;
            }
        }

    這樣便能將標籤新增到已登入使用者識別碼的註冊中。提供的標籤會通過驗證，以防止使用者註冊其他使用者的識別碼。在將通知傳送給此使用者時，會在這個裝置和該使用者已註冊的任何其他裝置上收到通知。

4.  依序按一下向後鍵、[資料] 索引標籤、[TodoItem] 及 [指令碼]，然後選取 [插入]。

  []: ./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png
