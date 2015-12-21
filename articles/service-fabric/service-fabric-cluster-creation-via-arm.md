<properties
   pageTitle="使用 ARM 範本設定 Service Fabric 叢集 | Microsoft Azure"
   description="使用 ARM 範本設定 Service Fabric 叢集。"
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# 使用 ARM 範本設定 Service Fabric 叢集

本頁面可協助您使用 ARM 範本來設定「Service Fabric 叢集」。這會假設您的訂用帳戶有足夠的核心來部署將構成此叢集的 IaaS VM。

## 必要條件

- 如果您想要設定安全的叢集，請確定您已將 X509 憑證上傳到您的金鑰保存庫。您將需要「來源保存庫 URL」、「憑證 URL」及「憑證」指紋。
-  如需有關此做法的詳細資訊，請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)。

## 取得範例 ARM 範本

1. 您可以從 [github 上的 Azure 快速入門範本庫](https://github.com/Azure/azure-quickstart-templates)取得範例 ARM 範本。所有 Service Fabric 範本都是以 "service-fabric-.." 名稱開頭。您可以搜尋存放庫中的 "fabric"，或直接向下捲動到一組範例範本。
2. 為了協助您快速找出您可能要尋找的目標，範本已經依下列方式命名：
	1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) 用以表示 5 節點單節點非安全叢集範本。 
	3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) 用以表示已啟用 WAD 的 5 節點單節點安全叢集範本。 
	4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) 用以表示已啟用 WAD 的 10 節點單節點安全叢集範本。 
	

## 建立自訂 ARM 範本

2. 您有兩種選擇 
	1. 您可以從 [Github 上的 Azure 快速入門範本庫](https://github.com/Azure/azure-quickstart-templates)取得範例範本，並對其進行變更。
	2. 登入 Azure 入口網站，然後使用 Service Fabric 入口網站頁面來產生可供您自訂的範本。此做法的程序說明如下。
3. 登入「Azure 入口網站」[http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)。
2. 依照[透過入口網站建立 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md)所述，進行建立叢集的程序，但請不要按 [建立]，而是到 [摘要] 下載範本。

 ![DownloadTemplate][DownloadTemplate]

## 使用 Azure PS 將 ARM 範本部署到 Azure

如需如何使用 PowerShell 部署範本的詳細指引，請參閱[使用 PS 部署 ARM 範本](resource-group-template-deploy.md)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1210_2015-->