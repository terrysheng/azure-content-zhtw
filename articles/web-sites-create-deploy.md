<properties  linkid="manage-services-how-to-create-websites" urlDisplayName="How to create" pageTitle="How to create web sites - Azure service management" metaKeywords="Azure creating web site, Azure deleting website" description="Learn how to create a web site using the Azure Management Portal." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create and Deploy a Web Site" authors="timamm" solutions="" manager="" editor="" />

# 如何建立網站

本主題說明如何從藝廊或使用管理入口網站來建立網站。

如需關於如何將內容部署到已建立之 Azure 網站的詳細資訊，請參閱 [Azure
網站](/zh-tw/documentation/services/web-sites/)中的＜**部署**＞一節。

## 目錄

* [作法：使用管理入口網站來建立網站](#createawebsiteportal)
* [作法：從藝廊建立網站](#howtocreatefromgallery)
* [作法：刪除網站](#deleteawebsite)
* [後續步驟](#nextsteps)

## <a name="createawebsiteportal"></a>作法：使用管理入口網站來建立網站

執行下列步驟在 Azure 中建立網站。

1.  登入 [Azure 管理入口網站][1]。

2.  按一下 **管理入口網站] 左下角的 [新建** 圖示。

3.  依序按一下 **網站** 圖示和 **快速建立** 圖示，輸入 URL 的值，然後按一下頁面右下角的 **建立網站** 旁的打勾記號。

4.  建立網站後，您會看到「建立網站 <*網站名稱*> 成功」****的字樣。按一下入口網站頁面底部的 **瀏覽** 即可瀏覽到該網站。

5.  在入口網站中，按一下網站清單中顯示的網站名稱，以開啟網站的 **快速啟動** 管理頁面。

6.  **快速啟動** 頁面上提供各種選項，讓您取得網站開發工具、設定網站的發行，或從 TFS 或 Git 之類的原始檔控制提供者設定部署。預設已為網站設定 FTP 發行，並在 **儀表板** 頁面 **Quick Glance** 區段的 **FTP 主機名稱** 下顯示 FTP 主機名稱。使用 FTP 或 Git  進行發行前，請選擇 **儀表板** 頁面的 **Reset deployment credentials** 選項，以便能在將內容部署到網站時，根據 FTP 主機或 Git 儲存機制進行驗證。

7.  **設定** 管理頁面會以下列類別顯示網站的設定：

	* **一般**：設定 Web 應用程式所需的 .NET Framework 或 PHP 版本。若是處於標準模式的網站，**平台** 選項可讓您選擇 32 位元或 64 位元環境。

8. **憑證**：在標準模式中，您可以上傳自訂網域的 SSL 憑證。

* **網域名稱**：在共用模式和標準模式中，您可以檢視及管理網站的自訂網域名稱。

10. **SSL 繫結**：在標準模式中，您可以使用 IP 型或 SNI 型 SSL，將 SSL 憑證指派給您的自訂網域名稱。

	* **部署**：從原始檔控制來設定部署時，可在此處設定部署。

	* **應用程式診斷**：您可以設定選項，以便從使用追蹤檢測的 Web 應用程式中收集診斷追蹤。

* **網站診斷**：設定用於收集網站診斷資訊的記錄選項。選項包括 [Web 伺服器記錄]、[詳細的錯誤訊息] 或 [失敗要求的追蹤]。

* **監控**：若是處於標準模式的網站，您可以使用此功能來測試 HTTP 或 HTTPS 端點的可用性。

* **應用程式設定**：指定 Web 應用程式啟動時載入的名稱/值組。如果是 .NET 網站，這些設定會在執行階段插入 .NET 組態的 **AppSettings** 中，並請覆蓋現有的設定。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用。

	* **連接字串**：檢視和編輯連接字串。如果是 .NET 網站，這些連接字串會在執行階段插入 .NET 組態的 **connectionStrings** 設定中，並覆蓋索引鍵等於所連結資料庫名稱的現有項目。如果是 PHP 和 Node 網站，您可以在執行階段將這些設定視為環境變數使用。

	* **預設文件**：網站的預設文件就是在使用者瀏覽至網站時預設顯示的頁面。如果 Web 應用程式的預設文件未在此清單中，可將其加入清單中。網站的預設文件必須位於清單頂端。

* **處理常式對應**：指定負責為特定副檔名 (如 \*.php) 處理要求的指令碼處理器。

## <a name="howtocreatefromgallery"></a>作法：從藝廊建立網站

[WACOM.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

## <a name="deleteawebsite"></a>作法：刪除網站

您可以使用 Azure 管理入口網站中的 **刪除** 圖示來刪除網站。在 Azure 入口網站中，當您按一下 **網站** 來列出所有網站時就會看見 **刪除** 圖示，但此圖示也會顯示在每個網站管理頁面的底部。

## <a name="nextsteps"></a>後續步驟

如需詳細資訊，請參閱 [Azure 網站](/zh-tw/documentation/services/web-sites/)。



[1]: http://manage.windowsazure.com/