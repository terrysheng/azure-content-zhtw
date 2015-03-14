<properties 
	pageTitle="使用 Node.js 模組 " 
	description="" 
	services="" 
	documentationCenter="nodejs" 
	title="Using Node.js Modules with Azure applications" 
	authors="larryfr" 
	manager="wpickett" 
	editor="mollybos" />

<tags 
	ms.service="na" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr" />





# 使用 Node.js 模組與 Azure 應用程式搭配

本文提供有關使用 Node.js 模組與 Azure 上代管之應用程式搭配的指引。它提供有關確保應用程式使用模組特定版本，以及搭配原生模組與 Azure 使用的指引。

如果您已熟悉使用 Node.js 模組、**package.json** 和 **npm-shrinkwrap.json** 檔案，則下列是本文中討論內容的快速摘要：

* Azure 網站熟悉 **package.json** 和 **npm-shrinkwrap.json** 檔案，並可根據這些檔案中的項目安裝模組。
* Azure 雲端服務期望在開發環境上安裝所有模組，且 **node\_modules** 目錄會包括為部署封裝的一部分。

<div class="dev-callout">
<strong>注意</strong>
<p>本文中將不會討論 Azure 虛擬機器，因為 VM 中的部署經驗會視虛擬機器所代管的作業系統而定。</p>
</div>

<div class="dev-callout">
<strong>注意</strong>
<p>提供支援使用 Azure 上的 <b>package.json</b> 或 <b>npm-shrinkwrap.json</b> 檔案來安裝模組是有可能的，不過，這會需要自訂雲端服務專案所使用的預設指令碼。如需如何完成此目的的範例，請參閱<a href="http://nodeblog.azurewebsites.net/startup-task-to-run-npm-in-azure">執行 npm 安裝以避免部署節點模組的 Azure 啟動工作</a></p> (英文)
</div>

##Node.js 模組

模組是指可載入的 JavaScript 封裝，可為您的應用程式提供特定功能。模組的安裝方式通常是使用 **npm** 命令列工具，不過，也有一些模組 (例如 http 模組) 會以核心 Node.js 封裝的一部分提供。

安裝模組時，模組會儲存在應用程式目錄結構之根目錄的 **node\_modules** 目錄中。**node\_modules** 目錄中的每個模組都會維護它自己的 **node\_modules** 目錄 (其中包含它所依賴的任何模組)，且這會在相依性鏈結一路向下的每個模組中重複進行。這可讓每個已安裝模組都有它自己所相依的模組版本需求，不過，它會產生相當大的目錄結構。

將 **node\_modules** 目錄作為應用程式一部分進行部署時，相較於使用 **package.json** 或 **npm-shrinkwrap.json** 檔案，它的部署大小會增加；不過，它可以確實保證用於生產的模組版本與開發中所用的模組版本相同。

###原生模組

雖然大多數的模組是簡單的純文字 JavaScript 檔案，有些模組卻是平台特定的二進位影像。這些模組會在安裝時編譯，通常是採用 Python 和 node-gyp。Azure 網站的一個特定限制是它原本就了解如何安裝 **package.json** 或 **npm-shrinkwrap.json** 檔案中所指定的模組，它不提供 Python 或 node-gyp 且無法建立原生模組。

由於 Azure 雲端服務仰賴將 **node\_modules** 資料夾當作應用程式的一部分進行部署，任何包括為安裝模組一部分的原生模組應可在雲端服務中運作，只要它是在 Windows 開發系統上安裝與編譯的即可。 

Azure 網站不支援原生模組。某些模組 (例如 JSDOM 和 MongoDB) 擁有選擇性的原生相依性，且會使用 Azure 網站上代管的應用程式。

###使用 package.json 檔案

**package.json** 檔案是一種方法，可用來指定應用程式要求的最上層相依性，以便主控平台可安裝相依性，而不是要求您包含 **node\_packages** 資料夾作為部署的一部分。在部署應用程式之後，您可使用 **npm install** 命令，來剖析 **package.json** 檔案並安裝所有列出的相依性。

開發期間，當安裝模組將模組項目自動新增至 **package.json** 檔案時，您可以使用 **--save**、**--save-dev** 或 **--save-optional** 參數。如需詳細資訊，請參閱 [npm-install](https://npmjs.org/doc/install.html) (英文)。

有關 package.json** 檔案的一個潛在問題是它只指定最上層相依性的版本。每個已安裝模組不一定會指定它所相依的模組版本，而且您最終得到的相依性鏈結可能與在開發中所用的不同。 

> [WACOM.NOTE]
> 部署到 Azure 網站時，如果您的 <b>package.json</b> 檔案參考原生模組，當使用 Git 發行應用程式時，您將會看到一則如下所示的錯誤訊息：

>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1	


###使用 npm-shrinkwrap.json 檔案

**npm-shrinkwrap.json** 檔案嘗試解決 **package.json** 檔案的模組版本設定限制。**package.json** 檔案只包含最上層模組的版本，而 **npm-shrinkwrap.json** 檔案包含完整模組相依性鏈結的版本需求。

當您的應用程式準備好開始生產時，您可以鎖定版本需求，並使用 **npm shrinkwrap** 命令建立 **npm-shrinkwrap.json** 檔案。這會使用目前安裝在 **node\_modules** 資料夾中的版本，並將他們記錄到 **npm-shrinkwrap.json** 檔案。在部署應用程式到主控環境之後，使用 **npm install** 命令來剖析 **npm-shrinkwrap.json** 檔案，並安裝所有列出的相依性。如需詳細資訊，請參閱 [npm-install](https://npmjs.org/doc/install.html) (英文)。

> [WACOM.NOTE]
>部署到 Azure 網站時，如果您的 <b>npm-shrinkwrap.json</b> 檔案參考原生模組，當使用 Git 發行應用程式時，您將會看到一則如下所示的錯誤訊息：
		
>		npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>		npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##後續步驟

現在，您了解如何搭配 Node.js 模組與 Azure 使用，接著了解如何 [指定 Node.js 版本]、[建立與部署 Node.js 網站]，和 [如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]。

[指定 Node.js 版本]: /zh-tw/documentation/articles/nodejs-specify-node-version-azure-apps/
[如何使用適用於 Mac 和 Linux 的 Azure 命令列工具]: /zh-tw/documentation/articles/xplat-cli/
[建置並部署 Node.js 網站]: /zh-tw/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[使用 MongoDB (MongoLab) 上儲存體的 Node.js Web 應用程式]: /zh-tw/documentation/articles/store-mongolab-web-sites-nodejs-store-data-mongodb/
[使用 Git 來發行]: /zh-tw/documentation/articles/web-sites-publish-source-control/
[建立 Node.js 應用程式並部署到 Azure 雲端服務]: /zh-tw/documentation/articles/cloud-services-nodejs-develop-deploy-app/



<!--HONumber=46--> 
