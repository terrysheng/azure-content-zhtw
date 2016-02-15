<properties
   pageTitle="使用 Azure 資源管理員範本來設定 Service Fabric 叢集 | Microsoft Azure"
   description="使用 Azure 資源管理員範本來設定 Service Fabric 叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="chackdan"/>


# 使用 Azure 資源管理員範本來設定 Service Fabric 叢集

本頁將協助您使用 Azure 資源管理員範本來設定 Service Fabric 叢集。但您的訂用帳戶必須要有足夠的核心，來部署將構成此叢集的 IaaS VM。

## 先決條件

- 如要設定安全叢集，您必須先將 X.509 憑證上傳到自己的金鑰保存庫。您將需要來源保存庫 URL、憑證 URL，以及憑證指紋。
- 如需如何設定安全叢集的詳細資訊，請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)。

## 取得範例資源管理員範本

您可以從 [GitHub 上的 Azure 快速啟動範本資源庫](https://github.com/Azure/azure-quickstart-templates)取得範例資源管理員範本。所有 Service Fabric 範本的名稱開頭都是「service-fabric...」。您可以在存放庫中搜尋「fabric」，或是向下捲動到範例範本集。為協助您迅速找出您在尋找的項目，範本已經依照下列方式命名：

- [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923)：代表 5 節點單節點非安全叢集範本。

- [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924)：代表已啟用 WAD 的 5 節點單節點安全叢集範本。

- [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925)：代表已啟用 WAD 的 10 節點雙節點安全叢集範本。

## 建立自訂的資源管理員範本

建立自訂的資源管理員範本的方式有兩種：

1. 您可以從 [Github 上的 Azure 快速啟動範本資源庫](https://github.com/Azure/azure-quickstart-templates)取得範例範本，然後加以變更。

2. 您可以登入 Azure 入口網站，然後使用 Service Fabric 入口網站頁面來產生可供您自訂的範本。若要這樣做，請遵循下列步驟：

    a.登入 [Azure 入口網站](https://portal.azure.com/)。

    b.依照[透過 Azure 入口網站建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)一文所述的叢集建立程序來進行，但不要按一下 [建立]，而是要前往 [摘要] 並下載範本，如下列螢幕擷取畫面所示。

 ![Service Fabric 叢集頁面的螢幕擷取畫面，顯示下載資源管理員範本的連結][DownloadTemplate]

## 使用 Azure PowerShell 來將資源管理員範本部署到 Azure

如需如何使用 PowerShell 部署範本的詳細指示，請參閱[使用 PowerShell 部署資源管理員範本](resource-group-template-deploy.md)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_0204_2016-->