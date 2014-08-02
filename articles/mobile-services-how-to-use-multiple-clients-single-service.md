<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="krisragh" solutions="" manager="" editor="" />

從單一行動服務支援多個用戶端的注意事項
======================================

使用 Azure 行動服務來支援行動應用程式開發主要的好處之一，是能夠使用單一後端服務來支援某一在多個用戶端平台上執行的應用程式。行動服務提供了適用於所有主要裝置平台的原生用戶端程式庫。如需詳細資訊，請參閱[教學課程和資源](/en-us/develop/mobile/resources/)。

雖然行動服務可讓您透過單一後端服務輕鬆地在不同平台間移轉原生應用程式，但您仍需考量若干注意事項。本主題所提供的指引，將說明如何讓推播通知在您所有的用戶端平台上皆可順利運作。此外也會說明，如何解決在 Windows 市集和 Windows Phone 應用程式中使用 Microsoft 帳戶執行用戶端導向單一登入時所發生的問題。最後，本主題會討論在 Visual Studio 專案中重複使用程式碼的最佳作法。

推播通知
--------

本節所討論的兩種方法，可讓您從行動服務傳送推播通知至多個平台上的用戶端應用程式。

### 利用通知中心

Azure 通知中心是一項 Azure 服務和可擴充的解決方案，可讓您從行動服務 (或任何後端) 將推播通知傳送至位於所有主要裝置平台上的用戶端應用程式。如需詳細資訊，請參閱 [Azure 通知中心](/en-us/develop/net/how-to-guides/service-bus-notification-hubs/)。

通知中心提供了一致而統合的基礎結構，可用來建立及管理裝置的註冊，以及將推播通知傳送至執行於所有主要裝置平台上的裝置。如需詳細資訊，請參閱[開始使用通知中心](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)。通知中心支援平台特定的範本註冊，而讓您能夠使用單一 API 呼叫將通知傳送至執行於任何已註冊之平台上的應用程式。如需詳細資訊，請參閱[傳送跨平台通知給使用者](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)。

當您要從行動服務傳送通知至多個用戶端平台時，通知中心是我們建議使用的解決方案。

### 使用共同註冊資料表和平台識別碼

如果您選擇不使用通知中心，您仍可支援從行動服務傳送推播通知至多個用戶端的作業，只要定義共同裝置註冊機制即可。請使用單一資料表，儲存支援平台的推播通知服務所使用的裝置特定資訊。**開始使用推播通知**教學課程 ([Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/)) 會使用**「註冊」**資料表，並將通道 URI (Windows)、裝置權杖 (iOS) 或控制碼 (Android) 儲存在名為*「控制碼」*的資料行中。

**注意**

在 Visual Studio 2013 中使用「新增推播通知」精靈將推播通知新增至 Windows 市集應用程式時，精靈會自動建立名為**「通道」**的資料表來儲存通道 URI。教學課程**開始在 Visual Studio 2012 中使用推播通知** ([Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012)/[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012)) 會說明如何使用**「註冊」**資料表啟用推播通知。

若要在此單一註冊資料表中支援多個用戶端，請在資料表中加入*平台*資料行，而「平台」欄位應設為在註冊期間插入一個資料列的用戶端平台。例如，下列**「註冊」**類別定義，會從 Windows 市集 C\# 或 Windows Phone 應用程式將用戶端 *ChannelUri* 欄位對應至「註冊」資料表中的*「控制碼」*資料行。

     public class Registrations
        {
            [JsonProperty(PropertyName = "platform")]         
            public string Platform { 
                get
                {
                    return "windowsstore";
                    // return "windowsphone";
                }
                set { }
            }
            
            public string Id { get; set; }
        
            [JsonProperty(PropertyName = "handle")]
            public string ChannelUri { get; set; }
        }

請注意，在此 Windows 裝置上，*平台*欄位一律會傳回 `windowsstore` (或 `windowsphone`)。在啟用動態結構描述的情況下 (預設為啟用)，行動服務會在「註冊」資料表中新增一個平台資料行 (如果尚不存在)。如需詳細資訊，請參閱[動態結構描述](http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx)。

在您傳送通知的伺服器端指令碼中，使用平台欄位決定要呼叫 [push 物件](http://msdn.microsoft.com/en-us/library/windowsazure/jj554217.aspx)上的哪個平台特定函數。下列指令碼修正自**開始使用推播通知**教學課程 ([Windows 市集 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/)/[Windows 市集 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/)/[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8/)/[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios/)/[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android/))，可用來啟用多個用戶端平台：

     function insert(item, user, request) {
            request.execute({
                success:function(){
                    request.respond();
                    sendNotifications();
                }
            });
        
            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success:function(registrations) {
                        registrations.forEach(function(registration) {
                            if (registration.platform === 'winstore') {
                                push.wns.sendToastText04(registration.handle, {
                                    text1:item.text
                                }, {
                                    success:pushCompleted
                                });
                            } else if (registration.platform === 'winphone') {
                                push.mpns.sendFlipTile(registration.handle, {
                                    title:item.text
                                }, {
                                    success:pushCompleted
                                });
                            } else if (registration.platform === 'ios') {
                                push.apns.send(registration.handle, {
                                    alert:"Toast:" + item.text,
                                    payload: {
                                        inAppMessage:item.text
                                    }
                                });
                            } else if (registration.platform === 'android') {
                                push.gcm.send(registration.handle, item.text, {
                                    success:pushCompleted
                                });
                            } else {
                                console.error("Unknown push notification platform");
                            }
                        });
                    }
                });
            }
        
            function pushCompleted(pushResponse) {
                console.log("Sent push:", pushResponse);
            }
        }

Windows 應用程式註冊
--------------------

若要在 Windows 市集和 Windows Phone 應用程式中都使用 Microsoft 帳戶進行單一登入用戶端驗證，您必須先在 Windows 市集儀表板上註冊 Windows 市集應用程式。這是因為在您建立 Windows Phone 的 Live Connect 註冊後，您即無法建立 Windows 市集的註冊。如需如何執行此作業的詳細資訊，請閱讀**使用 Live Connect 單一登入驗證 Windows 市集應用程式** ([Windows 市集](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)) 主題。

在 Visual Studio 專案中重複使用程式碼的最佳作法
-----------------------------------------------

可攜式類別庫可讓您撰寫及建置能夠在多個平台 (例如 Windows 市集和 Windows Phone) 上運作的受管理組件。您可以建立適當類別，使其包含您要在許多專案間共用的程式碼，然後從不同類型的專案參考這些類別。

您可以使用 .NET Framework 可攜式類別庫實作 Model-View-View Model (MVVM) 模式，並在不同的平台間共用組件。您可以在 Visual Studio 2012 的可攜式類別庫專案中實作模型及檢視模型類別，然後建立為不同平台自訂的檢視。舉例來說，跨平台共用的模型程式碼可從 Azure 行動服務之類的來源，以平台無從驗證的方式擷取資料。MSDN 文件庫提供了[概觀和範例](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110))、[API 差異](http://msdn.microsoft.com/en-us/library/gg597392(v=vs.110))的討論、[使用可攜式類別庫實作 MVVM 模式](http://msdn.microsoft.com/en-us/library/hh563947(v=vs.110))的範例、其他[慣用指引](http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj714086(v=vs.105).aspx)，以及在可攜式類別庫專案中[管理資源](http://msdn.microsoft.com/en-us/library/hh871422(v=vs.110))的相關資訊。

