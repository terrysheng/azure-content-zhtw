<properties
	pageTitle="安裝 Azure 命令列介面 | Microsoft Azure"
	description="安裝適用於 Mac、Linux 和 Windows 的 Azure CLI 以開始使用 Azure 服務"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/29/2015"
	ms.author="danlep"/>

# 安裝 Azure CLI

快速安裝 Azure 命令列介面 (Azure CLI) 以使用一組開放原始碼的命令介面式命令，用於建立和管理 Microsoft Azure 中的資源。使用提供的安裝程式套件之一在作業系統上安裝 Azure CLI、使用 Node.js 和 **npm** 安裝 CLI，或將 Azure CLI 安裝為 Docker 主機中的容器。如需詳細的選項和背景，請參閱 [GitHub](https://github.com/azure/azure-xplat-cli) 中的專案儲存機制。

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]

安裝好 Azure CLI 之後，您就能[使用 Azure 訂用帳戶將其連接](xplat-cli-connect.md)，並從命令列介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令以使用 Azure 資源。




## 使用安裝程式

我們提供以下安裝程式套件：

* [Windows 安裝程式][windows-installer]

* [OS X 安裝程式](http://go.microsoft.com/fwlink/?LinkId=252249)

* [Linux 安裝程式][linux-installer]


## 安裝和使用 Node.js 和 npm

如果已經在系統上安裝 Node.js，請使用下列命令來安裝 Azure CLI：

	npm install azure-cli -g

> [AZURE.NOTE]在 Linux 散發套件中，您可能必須使用 `sudo`，才能順利執行 __npm__ 命令。

### 在使用 [dpkg](http://en.wikipedia.org/wiki/Dpkg) 封裝管理的 Linux 散發套件上安裝 Node.js 和 npm

最常見的這類散發套件是使用[進階封裝工具 (apt)](http://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 或其他以 `.deb` 封裝格式為基礎的工具。部分範例包括 Ubuntu 和 Debian。

大部分較新的這類散發套件都需要安裝 **nodejs-legacy**，以取得正確設定的 **npm** 工具來安裝 Azure CLI。下列程式碼會顯示在 Ubuntu 14.04 上適當安裝 **npm** 的命令。

	sudo apt-get install nodejs-legacy
	sudo apt-get install npm
	sudo npm install -g azure-cli

有些較舊的發佈版本 (例如 Ubuntu 12.04) 需要安裝 Node.js 目前的二進位檔發佈。下列程式碼示範如何透過安裝並使用 **curl** 來執行此動作。

>[AZURE.NOTE]命令取自於[這裡](https://github.com/joyent/node/wiki/installing-node.js-via-package-manager)找到的安裝指示。不過，使用 **sudo** 做為管道目標時，您應該一律檢查正在安裝的指令碼，並驗證它們的功能如您預期，然後透過 **sudo** 執行它們。功能愈強大，責任愈多。

	sudo apt-get install curl
	curl -sL https://deb.nodesource.com/setup | sudo bash -
	sudo apt-get install -y nodejs
	sudo npm install -g azure-cli

### 在使用 [rpm](http://en.wikipedia.org/wiki/RPM_Package_Manager) 封裝管理的 Linux 散發套件上安裝 Node.js 和 npm

在以 RPM 為基礎的發佈版本上安裝 Node.js 時必須啟用 EPEL 儲存機制。下列程式碼會顯示在 CentOS 7 上進行安裝的最佳作法。(請注意，在下列第一行中，'-' (連字號) 很重要！)

	su -
	yum update [enter]
	yum upgrade –y [enter]
	yum install epel-release [enter]
	yum install nodejs [enter]
	yum install npm [enter]
	npm install -g azure-cli  [enter]

### 在 Windows 和 Mac OS X 上安裝 Node.js 和 npm

您可以在 Windows 與 OS X 上，使用來自 [Nodejs.org](https://nodejs.org/en/download/) 的安裝程式來安裝 Node.js 和 npm。您可能需要重新啟動電腦才能完成安裝。請檢查是否已正確安裝節點和 npm，方法是開啟命令或終端機視窗並輸入

	npm -v

如果顯示安裝的 npm 版本，您就可以繼續執行並安裝 Azure CLI 以及

	npm install -g azure-cli

在安裝結束時，您會看到類似下列的項目：

	azure-cli@0.8.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

>[AZURE.NOTE]對於 Linux 系統，您也可以安裝 Azure CLI，方法是從[原始碼](http://go.microsoft.com/fwlink/?linkid=253472)建置它。如需從原始碼進行建置的詳細資訊，請參閱封存中內含的 INSTALL 檔案。

## 使用 Docker 容器

在 Docker主機中，執行：

```
docker run -it microsoft/azure-cli
```

## 執行 Azure CLI 命令
安裝好 Azure CLI 之後，您就能從命令列使用者介面 (Bash、終端機及命令提示字元等) 中執行 **azure** 命令。例如，若要執行 [說明] 命令，請輸入下列命令：

```
azure help
```

若要查看您所安裝的 Azure CLI 版本，請輸入下列命令：

```
azure --version
```

您現在已經準備就緒！ 若要存取所有 Azure CLI 以搭配您的資源使用，[請從 Azure CLI 連接到您的 Azure 訂用帳戶](xplat-cli-connect.md)。

## 更新 CLI

Microsoft 經常發行 Azure CLI 的更新版本。使用適用於您作業系統的安裝程式重新安裝 CLI，或如果已安裝 Node.js 和 npm，輸入下列命令以更新 (您需要在 Linux 散發套件上使用 **sudo**)。

```
npm upgrade -g azure-cli
```

## 其他資源

* [搭配使用 Azure CLI 與 Azure 資源管理員][cliarm]

* [搭配使用 Azure CLI 與 Azure 服務管理][cliasm]

* 若要深入了解 Azure CLI、下載來源程式碼、回報問題，或是參與專案，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。

* 如果您有關於使用 Azure CLI 或 Azure 方面的問題，請造訪 [Azure 論壇](https://social.msdn.microsoft.com/Forums/zh-TW/home?forum=azurescripting) (英文)。



[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/?linkid=9828653&clcid=0x409
[linux-installer]: http://go.microsoft.com/fwlink/?linkid=253472
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0107_2016-->