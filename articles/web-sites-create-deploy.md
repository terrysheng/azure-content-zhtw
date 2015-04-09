<properties
	pageTitle="如何建立 Web 應用程式 - Azure 服務管理"
	description="了解如何使用 Azure 入口網站建立 Web 應用程式。"
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

#如何建立 Web 應用程式

本主題說明如何從程式庫或使用 Azure 入口網站來建立 Web 應用程式。

如需如何將內容部署到已建立之 Web 應用程式的相關資訊，請參閱 [Azure Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) 中的　＜部署＞一節。

>[AZURE.NOTE] 如果您想要在註冊 Azure 帳戶之前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，讓您能夠在 App Service 中立即建立短期的入門 Web 應用程式。不需要信用卡，無需承諾。

##<a name="createawebsiteportal"></a>作法：使用 Azure 入口網站建立 Web 應用程式

執行下列步驟，即可在 Azure 中建立 Web 應用程式。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)。

2. 按一下 Azure 入口網站左下方的 [新增] 圖示。

3. 按一下 [Web + 行動裝置] 圖示、按一下 [Web 應用程式] 圖示、輸入 URL 的值，然後按一下建立刀鋒視窗右下角的 [建立]。

4. 建立 Web 應用程式之後，您將會看見如下的文字：已成功部署至資源群組 <資源群組名稱>。

5. 在入口網站中，按一下 Web 應用程式清單中顯示的 Web 應用程式名稱，即可開啟該 Web 應用程式的刀鋒視窗。

6. 刀鋒視窗上提供各種選項，讓您可以取得 Web 應用程式開發工具、設定 Web 應用程式的發行，或是從 TFS 或 Git 之類的原始檔控制提供者設定部署。預設會針對 Web 應用程式設定 FTP 發行，而 FTP 主機名稱會顯示於 Web 應用程式刀鋒視窗的 [基本功能] 區段中。使用 FTP 或 Git 進行發行前，請選擇 Web 應用程式刀鋒視窗上的 [重設發行設定檔] 選項，以便能夠在將內容部署到 Web 應用程式時，根據 FTP 主機或 Git 儲存機制進行驗證。

7. [設定] 刀鋒視窗會公開 Web 應用程式的設定，例如：

	- 適用於 Web 應用程式的 .NET、PHP、Java 或 Python 版本
	- 在 Visual Studio Online 中編輯
	- SSL 繫結
	- 自訂網域名稱
	- 驗證/授權
	- 應用程式和網站診斷
	- 監視端點
	- 記錄選項
	- Azure 環境的應用程式設定 (例如，在您開發環境的 Web.config 中覆寫 <appSettings>)
	- 連接字串 (例如，在您開發環境的 Web.config 中覆寫 <connectionStrings>)
	- 特定副檔名的指令碼處理器，例如 *.php

##<a name="howtocreatefromgallery"></a>作法：從程式庫建立 Web 應用程式

[AZURE.INCLUDE [website-from-gallery](../includes/website-from-gallery.md)]

##<a name="deleteawebsite"></a>作法：刪除 Web 應用程式
您可以使用 Azure 入口網站中的 [刪除] 圖示來刪除 Web Apps。[刪除] 圖示位於 Web 應用程式刀鋒視窗頂端。

##<a name="nextsteps"></a>後續步驟

如需詳細資訊，請參閱 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)。

## 相關變更
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從舊版入口網站變更為新版入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->