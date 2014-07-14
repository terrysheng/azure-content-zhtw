首先，您會在 Visual Studio 2013 中使用 [加入推播通知] 精靈向 Windows 市集註冊您的應用程式、設定您的行動服務以啟用推播通知，以及將您的程式碼加入至您的應用程式以註冊裝置通道。

1.  如果您尚未這麼做，請遵循[在 Visual Studio 2013 中匯入 publishsettings 檔案](/en-us/documentation/articles/mobile-services-windows-how-to-import-publishsettings/)中的步驟，將 publisher.settings 檔案匯入 Visual Studio。
    
    如果您已使用 Visual Studio 建立或管理 Azure 訂閱中的行動服務，則不需要這麼做。

2.  在 Visual Studio 2013 中開啟 **方案總管]，在專案上按一下滑鼠右鍵，依序按一下 [新增** 和
    **推播通知...**。
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)
    
    這樣會啟動 [加入推播通知] 精靈。

3.  按 **下一步**、登入您的 Windows 市集帳戶，然後在 **Reserve a new name** 中提供名稱，然後按一下 **保留**。
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    這樣會建立新的應用程式註冊。

4.  按一下 **應用程式名稱** 清單中的新註冊，然後按 **下一步**。
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  在 **選取服務** 對話方塊中，按一下您完成[開始使用行動服務](/en-us/develop/mobile/tutorials/get-started/)或[開始使用資料](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)時建立的行動服務名稱，然後按 **下一步** 和 **完成**。
    
    行動服務會進行更新，以註冊您的應用程式封裝 SID 和用戶端密碼，而且會建立新的 **channels** 資料表。現在已將行動服務設定為搭配 Windows 推播通知服務 (WNS) 運作，以便傳送通知給您的應用程式。
    
    
<div  class="dev-callout"><b>注意</b>
<p>若您的應用程式尚未設定要連接到行動服務，此精靈也會完成在<strong>開始使用資料</strong>中示範的相同設定工作。</p>
 </div>



<!-- URLs. -->