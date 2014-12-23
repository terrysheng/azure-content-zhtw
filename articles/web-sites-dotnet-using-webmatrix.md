<properties urlDisplayName="Website with WebMatrix" pageTitle="使用 WebMatrix 的 .NET 網站 - Azure 教學課程 " metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure 網站 WebMatrix, Azure 網站 WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/27/2014" ms.author="tomfitz" />





#使用 Microsoft WebMatrix 來開發和部署網站
本指南說明如何使用 Microsoft WebMatrix 來建立網站並部署至 Azure。您將使用 WebMatrix 網站範本提供的範例應用程式。

您將了解：

* 如何從 WebMatrix 內登入 Azure
* 如何使用 WebMatrix 內建的範本來建立網站 
* 如何將自訂的網站直接從 WebMatrix 部署至 Azure。


[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Sign into Azure

1. 啟動 WebMatrix。
2. 如果這是您第一次使用 WebMatrix 3，則會提示您登入 Azure。否則，您可以按一下 [**登入**] 按鈕，然後選擇 [**新增帳戶**]。選擇使用您的 Microsoft 帳戶來 [**登入**]。

	![Add Account][addaccount]

3. 如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

	![Sign into Azure][signin]	


## 使用 Azure 內建的範本建立網站

1. 在開始畫面上，按一下 [**新增**] 按鈕，然後選擇 [**範本庫**]，從範本庫建立新的網站：

	![New site from Template Gallery][sitefromtemplate]

2. 範本庫會顯示可在本機或 Azure 上執行的可用範本清單。從範本的清單中選取 [**Bakery**]，並在 [**網站名稱**] 中輸入 **bakerysample**，然後按 [**下一步**]。

	![Create Site from Template][sitefromtemplatedetails]

3. 如果您已登入 Azure，則現在可選擇建立 Azure 網站作為您的本機網站。請選擇唯一名稱，並選取您想在其中建立網站的資料中心： 

	![Create site on Azure][sitefromtemplateazure]

	當 WebMatrix 完成建立網站之後，就會顯示 WebMatrix IDE：

	![WebMatrix IDE][howtowebmatrixide] 

## 設定電子郵件

這個糕點範例包括模擬的訂購表單，此訂購表單會傳送一則包含訂購項目的電子郵件訊息。您可以在 Azure 上使用 SendGrid 電子郵件服務，來從您的網站傳送電子郵件。

1. 遵循 [如何使用 Azure 上的 SendGrid 傳送電子郵件][sendgridexample] 教學課程中的步驟，來設定 SendGrid 帳戶並擷取連線資訊。您不需要執行整個教學課程，僅執行到取得連接資訊的步驟即可。

2. 將 SendGrid NuGet 封裝新增至 WebMatrix 專案。首先，按一下 [NuGet] 按鈕。

    ![Add SendGrid][addsendgrid]

    搜尋並安裝 SendGrid。

    ![Install SendGrid][installsendgrid]

    在封裝完成安裝之後，請注意 SendGrid 組件已新增至 bin。

    ![SendGrid added][binsendgrid]

3. 連按兩下檔案名稱來開啟 *Order.cshtml* 頁面。

	![][modify2]

4. 在檔案頂端新增下列程式碼：

        @using SendGrid;
        @using System.Net.Mail;

4. 尋找 //SMTP Configuration for Hotmail 註解，並刪除或取消註解有關使用 WebMail 的所有程式碼。

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. 新增使用 SendGrid (而不是使用 WebMail) 來傳送電子郵件的程式碼。新增下列程式碼以取代您在上一個步驟中所刪除的程式碼。

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. 在 WebMatrix 功能區上，按一下 [**執行**] 來測試網站。

	![][modify4]

7. 在其中一個產品上，按一下 [**立即訂購**]，將訂單傳送給您自己。

8. 檢查電子郵件，確定您收到訂單確認。如果您無法傳送電子郵件，請參閱《ASP.NET 網頁 (Razor) 疑難排解指南》中的 [傳送電子郵件時的問題][sendmailissues] (英文)。
 

## 將自訂的網站從 WebMatrix 部署至 Azure

1. 在 WebMatrix 中，按一下 [**首頁**] 功能區中的 [**發佈**]，以顯示網站的 [**發佈預覽**] 對話方塊。

	![WebMatrix Publish Preview][howtopublishpreview]

2. 按一下以選取 bakery.sdf 旁邊的核取方塊，然後按一下 [**繼續**]。發行完成時，已更新的網站在 Azure 上的 URL 會顯示在 WebMatrix IDE 底部。  

	![Publishing Complete][publishcomplete]

3. 按一下連結在瀏覽器中開啟網站：

	![Bakery Sample Site][bakerysample]

您也可以在 Azure 入口網站中按一下 [**網站**] 來顯示訂用帳戶的所有網站，一樣可以找到此網站的 URL。每一個網站的 URL 會顯示在網站頁面的 URL 欄。

## 修改網站並重新發行至 Azure 網站

您可以使用 WebMatrix 來修改網站，然後重新發佈到 Azure 網站。在下列程序中，您將新增核取方塊來指出訂單為禮物。

1. 開啟 *Order.cshtml* 頁面。

2. 找出 "shiping" 類別表單定義。在 <li> 區塊後面插入下列程式碼。
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. 在檔案中找到 "var shipping = Request["orderShipping"];"，在此行後面直接插入下列程式碼行。

		var gift = Request["isGift"];

4. 在驗證送貨地址不是空白的程式碼區塊後面，插入下列程式碼片段。

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	*order.cshtml* 檔案的內容應該如下圖所示。

	![][modify6]

5. 儲存檔案並在本機執行網站，然後將測試訂單傳送給您自己。請記得測試新的核取方塊。

	![][modify7]

6. 按一下 [**首頁**] 功能區上的 [**發佈**] 來重新發行網站。

7. 在 [**發佈預覽**] 對話方塊上，確定已勾選 Order.cshtml，然後按一下 [繼續]。

8. 按一下連結，在瀏覽器中開啟網站，然後在您的 Azure 網站上測試更新結果。

# 後續步驟

您已了解如何建立網站並從 WebMatrix 部署至 Azure。若要深入了解 WebMatrix，請參考下列資源：

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix 網站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/zh-tw/documentation/articles/sendgrid-dotnet-how-to-send-email/
