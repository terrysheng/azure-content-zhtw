<properties linkid="develop-dotnet-website-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle=".NET website with WebMatrix - Azure tutorials" metaKeywords="WebMatrix Azure, WebMatrix Azure, Azure web site WebMatrix, Azure website WebMatrix, Web Matrix Azure, WebMatrix Azure" description="Learn how to develop and deploy an Azure website with WebMatrix." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Develop and deploy a website with Microsoft WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# 使用 Microsoft WebMatrix 來開發和部署網站

本指南說明如何使用 Microsoft WebMatrix 來建立網站並部署至 Azure。您將使用 WebMatrix 網站範本提供的範例應用程式。

您將了解：

-   如何從 WebMatrix 內登入 Azure
-   如何使用 WebMatrix 內建的範本來建立網站
-   如何將自訂的網站直接從 WebMatrix 部署至 Azure。

[WACOM.INCLUDE [create-account-and-websites-note][]]

## Sign into Azure

1.  啟動 WebMatrix。
2.  如果這是您第一次使用 WebMatrix 3，則會提示您登入 Azure。否則，您可以按一下 [登入] 按鈕，然後選擇 [新增帳戶]。選擇使用您的 Microsoft 帳戶來 [登入]。

    ![Add Account][]

3.  如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

    ![Sign into Azure][]

## 使用 Azure 內建的範本建立網站

1.  在開始畫面上，按一下 [新增] 按鈕，然後選擇 [範本庫]，從範本庫建立新的網站：

    ![New site from Template Gallery][]

2.  範本庫會顯示可在本機或 Azure 上執行的可用範本清單。從範本清單中選取 [糕點]，在 [站台名稱] 中輸入 **bakerysample**，然後按 [下一個]。

    ![Create Site from Template][]

3.  如果您已登入 Azure，則現在可選擇建立 Azure 網站作為您的本機網站。請選擇唯一名稱，並選取您想在其中建立網站的資料中心。

    ![Create site on Azure][]

    當 WebMatrix 完成建立網站之後，就會顯示 WebMatrix IDE：

    ![WebMatrix IDE][]

## 測試網站

糕點範例包含模擬的訂購表單，可將訂購項目的電子郵件傳送至您提供的 Windows Live Hotmail 帳戶。

1.  在 WebMatrix 的左瀏覽窗格中，展開 **bakerysample** 資料夾。

    ![][]

2.  按兩下檔名來開啟 *Order.cshtml* 頁面。

    ![][1]

3.  找出這一行註解：//Hotmail 的 SMTP 組態。

    ![][2]

4.  將下列幾行的值變更為符合您自己的電子郵件提供者：

        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort  = 25;
        WebMail.EnableSsl = true; 

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

    將 WebMail.SmtpServer 的值變更為您平常用來傳送電子郵件的電子郵件伺服器名稱。然後，填入使用者名稱和密碼的值。將 From 屬性設為您的電子郵件地址。

5.  在 WebMatrix 功能區，按一下 [執行] 來測試網站。

    ![][3]

6.  在其中一個產品上，按一下 [立即訂購]，將訂單傳送給您自己。

7.  檢查電子郵件，確定您收到訂單確認。如果您無法傳送電子郵件，請參閱＜ASP.NET 網頁 (Razor) 疑難排解指南＞中的＜[傳送電子郵件時的問題][]＞(英文)。

## 將自訂的網站從 WebMatrix 部署至 Azure

1.  在 WebMatrix 中，從 [首頁] 功能區按一下 [發行]，以顯示網站的 [發行預覽] 對話方塊。

    ![WebMatrix Publish Preview][]

2.  按一下以選取 bakery.sdf 的核取方塊，然後按一下 [繼續]。發行完成時，已更新的網站在 Azure 上的 URL 會顯示在 WebMatrix IDE 底部。

    ![Publishing Complete][]

3.  按一下連結在瀏覽器中開啟網站：

    ![Bakery Sample Site][]

    在 Azure 入口網站中按一下 [網站] 來顯示訂閱的所有網站，一樣可以找到此網站的 URL。每一個網站的 URL 會顯示在網站頁面的 URL 欄。

## 修改網站並重新發行至 Azure 網站

您可以使用 WebMatrix 來修改網站，然後重新發行至您的 Azure 網站。在下列程序中，您將加入核取方塊來指出訂單為禮物。

1.  開啟 *Order.cshtml* 頁面。

2.  找出 "shiping" 類別表單定義。在 \<li\> 區塊後面插入下列程式碼。

        <li class="gift">
            <div class="fieldcontainer" data-role="fieldcontain">
                <label for="isGift">This is a gift</label>           
                <div>@Html.CheckBox("isGift")</div>
            </div>
        </li>

    ![][4]

3.  在檔案中出 "var shipping = Request["orderShipping"];"，在此行下方直接插入下列這一行程式碼。

        var gift = Request["isGift"];

4.  在驗證送貨地址不是空白的程式碼區塊後面，插入下列程式碼片段。

        if(gift != null) {
            body += "This is a gift." + "<br/>";
        }

    *order.cshtml* 檔案的內容應該如下圖所示。

    ![][5]

5.  儲存檔案並在本機執行網站，然後將測試訂單傳送給您自己。請記得測試新的核取方塊。

    ![][6]

6.  在 [首頁] 功能區按一下 [發行]，以重新發行網站。

7.  在 [發行預覽] 對話方塊中，確定已勾選 Order.cshtml，然後按一下 [繼續]。

8.  按一下連結，在瀏覽器中開啟網站，然後在您的 Azure 網站上測試更新結果。

# 後續步驟

您已了解如何建立網站並從 WebMatrix 部署至 Azure。若要深入了解 WebMatrix，請參考下列資源：

-   [WebMatrix for Azure][]

-   [WebMatrix 網站][]

  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Add Account]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
  [Sign into Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
  [New site from Template Gallery]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
  [Create Site from Template]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
  [Create site on Azure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png
  [WebMatrix IDE]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide.png
  []: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
  [1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
  [2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
  [3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
  [傳送電子郵件時的問題]: http://go.microsoft.com/fwlink/?LinkId=253001#email
  [WebMatrix Publish Preview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
  [Publishing Complete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
  [Bakery Sample Site]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
  [4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
  [5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
  [6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png
  [WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix 網站]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
