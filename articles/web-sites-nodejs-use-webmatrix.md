<properties linkid="web-site-with-webmatrix" urlDisplayName="Website with WebMatrix" pageTitle="Node.js website with WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that teaches you how to WebMatrix to develop and deploy a Node.js application to an Azure website." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure using WebMatrix" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# 使用 WebMatrix 來建立 Node.js 網站並部署至 Azure

本教學課程說明如何使用 WebMatrix 來開發 Node.js 應用程式並部署至 Azure 網站。WebMatrix 是 Microsoft 提供的免費 Web 開發工具，包含網站開發所需的一切。WebMatrix 包含一些功能讓您輕鬆地使用 Node.js，包括程式碼自動完成、預先建立的範本，以及 Jade、LESS 和 CoffeeScript 的編輯器支援。深入了解 [WebMatrix for Azure][WebMatrix for Azure]。

完成本指南後，您將會有一個在 Azure 中執行的 Node.js 網站。

完成之應用程式的螢幕擷取畫面如下：

![Azure node Web site][Azure node Web site]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Sign into Azure

遵循下列步驟以建立 Azure 網站。

<div class="dev-callout"><strong>注意</strong>
<p>若要完成此教學課程，您需要已啟用 Azure 網站功能的 Azure 帳戶。</p>
<p>如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/zh-TW/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure 免費試用</a>。</p>
</div>

1.  啟動 WebMatrix
2.  如果這是您第一次使用 WebMatrix，則會提示您登入 Azure。否則，您可以按一下 [登入] 按鈕，然後選擇 [新增帳戶]。選擇使用您的 Microsoft 帳戶來 [登入]。

    ![Add Account][Add Account]

3.  如果您已註冊 Azure 帳戶，則可使用您的 Microsoft 帳戶登入：

    ![Sign into Azure][Sign into Azure]

## 使用 Azure 內建的範本建立網站

1.  在開始畫面上，按一下 [新增] 按鈕，然後選擇 [範本庫]，從範本庫建立新的網站：

    ![New site from Template Gallery][New site from Template Gallery]

2.  在 [依據範本的站台] 對話方塊中，選取 [Node]，再選取 [Express 網站]。最後，按 [下一步]。如果您遺漏 [Express 網站] 範本的任何必要元件，則會提示您安裝。

    ![select express template][select express template]

3.  如果您已登入 Azure，則現在可選擇建立 Azure 網站作為您的本機網站。請選擇唯一名稱，並選取您想在其中建立網站的資料中心。

    ![Create site on Azure][Create site on Azure]

4.  當 WebMatrix 完成建立網站之後，就會顯示 WebMatrix IDE。

    ![webmatrix ide][webmatrix ide]

## 將應用程式發行至 Azure

1.  在 WebMatrix 中，從 [首頁] 功能區按一下 [發行]，以顯示網站的 [發行預覽] 對話方塊。

    ![publish preview][publish preview]

2.  按一下 [繼續]。發行完成時，網站在 Azure 上的 URL 會顯示在 WebMatrix IDE 底部

    ![publish complete][publish complete]

3.  按一下連結在瀏覽器中開啟網站。

    ![Express web site][Express web site]

## 修改和重新發行應用程式

您可以輕鬆地修改和重新發行應用程式。以下，您將在 **index.jade** 檔案中簡單地變更標題，然後重新發行應用程式。

1.  在 WebMatrix 中，選取 [檔案]，然後展開 **views** 資料夾。按兩下以開啟 **index.jade** 檔案。

    ![webmatrix viewing index.jade][webmatrix viewing index.jade]

2.  將第二行變成：

        p Welcome to #{title} with WebMatrix on Azure!

3.  儲存您所做的變更，然後按一下發行圖示。最後，在 [發行預覽] 對話方塊中按一下 [繼續]，等待發行更新。

    ![publish preview][1]

4.  發行完成時，使用發行程序完成時所傳回的連結，以查看已更新的網站。

    ![Azure node Web site][Azure node Web site]

## 後續步驟

若要深入了解 Azure 隨附的 Node.js 版本，以及如何指定要與您的應用程式搭配使用的版本，請參閱[在 Azure 應用程式中指定 Node.js 版本][在 Azure 應用程式中指定 Node.js 版本] (英文)。

如果您在將應用程式部署到 Azure 後遇到問題，請參閱[如何在 Azure 網站中對 Node.js 應用程式進行偵錯][如何在 Azure 網站中對 Node.js 應用程式進行偵錯] (英文) 以獲得診斷問題的詳細資訊。

  [WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Azure node Web site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
  [Add Account]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
  [Sign into Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
  [New site from Template Gallery]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
  [select express template]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png
  [Create site on Azure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
  [webmatrix ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
  [publish preview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png
  [publish complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
  [Express web site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
  [webmatrix viewing index.jade]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
  [1]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
  [在 Azure 應用程式中指定 Node.js 版本]: /zh-TW/documentation/articles/nodejs-specify-node-version-azure-apps/
  [如何在 Azure 網站中對 Node.js 應用程式進行偵錯]: http://www.windowsazure.com/zh-TW/develop/nodejs/how-to-guides/Debug-Website/
