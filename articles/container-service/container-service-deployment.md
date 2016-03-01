<properties
   pageTitle="部署 Azure 容器服務叢集 | Microsoft Azure"
   description="使用 Azure 入口網站、Azure CLI 或 PowerShell 來部署 Azure 容器服務叢集。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、容器、微服務、Mesos、Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# 部署 Azure 容器服務叢集

Azure 容器服務支援快速部署常用的開放原始碼容器叢集和協調流程解決方案。Azure 容器服務可讓您使用 Azure 資源管理員範本或入口網站來部署 Marathon Mesos 和 Docker Swarm 叢集。這些叢集是使用 Azure 虛擬機器擴展集來部署，並利用 Azure 網路功能與儲存體供應項目。若要存取 Azure 容器服務，您需要有 Azure 訂用帳戶。如果您沒有訂用帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。

這份文件使用 [Azure 入口網站](#creating-a-service-using-the-azure-portal)、[Azure CLI](#creating-a-service-using-the-azure-cli) 和 [Azure PowerShell 模組](#creating-a-service-using-powershell)，逐步部署和 Azure 容器服務叢集。
   
## 使用 Azure 入口網站建立服務
 
選取下列其中一個範本來部署 Mesos 或 Docker Swarm 叢集。**注意** - 除了預設 Orchestrator 選項，這兩個範本都相同。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
每個範本頁面都有 [部署至 Azure] 按鈕，按一下此按鈕將會啟動 Azure 入口網站，其中會顯示如下所示的表單。<br />

![建立部署](media/create-mesos-params.png) <br />

請根據本指引來填寫表單，完成時選取 [確定]。<br />

欄位 | 說明
----------------|-----------
DNSNAMEPREFIX | 這必須是全球唯一的值。它會用來為每個服務的主要部分建立 DNS 名稱。以下提供詳細資訊。
AGENTCOUNT | 這是將在 ACS 代理程式擴展集中建立的虛擬機器數目。
AGENTVMSIZE | 指定代理程式虛擬機器的大小。請小心選取可提供足夠資源來裝載最大容器的大小。
ADMINUSERNAME | 這是在 ACS 叢集中的每一個虛擬機器和虛擬機器擴展集上，將用於帳戶的使用者名稱。
ORCHESTRATORTYPE| 選取您想要在 ACS 叢集中使用的 Orchestrator。
MASTERCOUNT | 這是設定做為叢集主機的虛擬機器數目。您可以選取 1，但這樣無法提供叢集的任何恢復功能，只建議用於測試。針對生產叢集，建議的數目是 3 或 5。 
SSHRSAPUBLICKEY | 您需要使用 SSH 向虛擬機器進行驗證。這是您加入公開金鑰的地方。將金鑰值貼入此方塊時請特別小心。某些編輯器會在內容中插入分行符號，這將會破壞金鑰。請確認金鑰不含分行符號，而且包含 'ssh rsa' 前置詞和 'username@domain' 後置詞。看起來應該類似於 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'。如果您需要建立 SSH 金鑰，您可以在 Azure 文件網站上找到 [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) 和 [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) 的指引。
  
一旦您已設定參數的適當值，請按一下 [確定]。接下來，提供資源群組名稱、選取區域，然後檢閱並同意法律條款。

> 在預覽期間，Azure 容器服務不收費，只有標準計算會收費，例如虛擬機器、儲存體、網路功能等。
 
![選取資源群組](media/resourcegroup.png)
 
最後，按一下 [建立]。您將會回到您的儀表板，假設您在 [部署] 刀鋒視窗上未取消選取 [釘選到儀表板]，則會看到類似如下的動畫磚：

![部署](media/deploy.png)
 
正在建立叢集，現在可休息一下。完成後，您會看到一些刀鋒視窗，其中顯示構成 ACS 叢集的資源。
 
![已完成](media/final.png)

## 使用 Azure CLI 建立服務

若要使用命令列建立 Azure 容器服務的執行個體，您需要有 Azure 訂用帳戶。如果您沒有帳戶，您可以註冊免費試用。您也必須安裝並設定 Azure CLI。
 
選取下列其中一個範本來部署 Mesos 或 Docker Swarm 叢集。**注意** - 除了預設 Orchestrator 選項，這兩個範本都相同。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
接下來，請確定 Azure CLI 已連線至 Azure 訂用帳戶。您可以使用下列命令來完成此動作。

```bash
Azure account show
```
如果沒有傳回 Azure 帳戶，請使用下列命令將 CLI 登入 Azure。
 
```bash
azure login -u user@domain.com
```

接下來，請確定設定 Azure CLI 工具來使用 Azure 資源管理員。
 
```bash
azure config mode arm
```
 
如果您想要在新的資源群組中建立叢集，您必須先建立資源群組。使用此命令，其中 `GROUP_NAME` 是您想要建立的資源群組的名稱，`REGION` 是您想要建立資源群組的區域。
 
```bash
azure group create GROUP_NAME REGION
```
 
一旦有資源群組，您就可以使用這個命令來建立叢集，其中：

- **RESOURCE\_GROUP** 是您想要用於此服務的資源群組名稱。
- **DEPLOYMENT\_NAME** 是此部署的名稱。
- **TEMPLATE\_URI** 是部署檔案的位置。**注意** - 這必須是 RAW 檔案，而不是 GitHub UI 的指標。若要尋找這個 URL，請在 GitHub 中選取 azuredeploy.json 檔案，並按一下 [RAW] 按鈕：

> 請注意，當執行此命令時，殼層會提示您輸入部署參數值。
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### 提供範本參數
 
此版本的命令需要使用者以互動方式定義參數。如果您想要以 json 格式字串提供參數，您可以 `-p` 參數來這樣做。例如：
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

或者，您也可以使用 `-e` 參數來提供 json 格式的參數檔案：

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
在 GitHub 中找到名為 `azuredeploy.parameters.json` 的範例參數檔連同 ACS 範本。
 
## 使用 PowerShell 建立服務

您也可以使用 PowerShell 部署 ACS 叢集。這份文件以 1.0 版和更高版本的 Azure [PowerShell 模組](https://azure.microsoft.com/blog/azps-1-0/)為基礎。

選取下列其中一個範本來部署 Mesos 或 Docker Swarm 叢集。**注意** - 除了預設 Orchestrator 選項，這兩個範本都相同。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

在 Azure 訂用帳戶中建立叢集之前，請確認您的 PowerShell 工作階段已登入 Azure。您可以使用 `Get-AzureRMSubscription` 命令完成此動作。

```powershell
Get-AzureRmSubscription
```

如果您需要登入 Azure，請使用 `Login-AzureRMAccount` 命令。

```powershell
Login-AzureRmAccount
```
 
如果部署到新的資源群組，您必須先建立資源群組。若要建立新的資源群組，請使用 `New-AzureRmResourceGroup` 命令，並指定資源群組名稱和目的地區域。

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

一旦有資源群組，您就可以使用下列命令來建立叢集。`-TemplateUri` 參數指定所需範本的 URI。當執行此命令時，PowerShell 會提示您輸入部署參數值。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## 後續步驟
 
既然您有一個可運作的叢集，請瀏覽這些文件來了解連接和管理的詳細資料。
 
- [連線到 ACS 叢集](./container-service-connect.md)
- [使用 ACS 和 Mesos](./container-service-mesos-marathon-rest.md)

 

<!---HONumber=AcomDC_0224_2016-->