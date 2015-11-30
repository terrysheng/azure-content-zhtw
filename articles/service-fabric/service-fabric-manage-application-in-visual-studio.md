<properties
   pageTitle="在 Visual Studio 中管理應用程式 | Microsoft Azure"
   description="使用 Visual Studio 來建立、開發、封裝、部署和偵錯 Service Fabric 應用程式和服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/17/2015"
   ms.author="jesseb"/>

# 使用 Visual Studio 簡化撰寫及管理 Service Fabric 應用程式。

您可以透過 Visual Studio 管理 Service Fabric 應用程式與服務。[設定開發環境](service-fabric-setup-your-development-environment.md)後，您可以使用 Visual Studio 在本機開發叢集中建立 Service Fabric 應用程式、新增服務，或封裝、註冊及部署應用程式。

若要管理應用程式，請在 [方案總管] 中，於應用程式專案上按一下滑鼠右鍵。

![在應用程式專案上按一下滑鼠右鍵以管理 Service Fabric 應用程式][manageservicefabric]

## 部署 Service Fabric 應用程式

部署應用程式會將以下幾個步驟結合成一個簡單的作業。

1. 建立應用程式封裝
2. 將應用程式封裝上傳至映像存放區
3. 註冊應用程式類型
4. 移除任何執行中的應用程式執行個體
5. 建立新的應用程式執行個體

在 Visual Studio 中按下 **F5** 也會部署應用程式，並將偵錯工具附加到所有應用程式執行個體。您可以使用 **Ctrl + F5** 來部署應用程式而不進行偵錯，或是使用發行設定檔來發行至本機或遠端叢集。請參考[使用 Visual Studio 將應用程式發行至遠端叢集](service-fabric-publish-app-remote-cluster.md)。

### 保留測試回合之間的資料

您通常是透過新增測試資料輸入、修改幾個程式碼區塊，然後再次於本機進行偵錯，以在本機測試服務。「Visual Studio Service Fabric 工具」提供一個稱為 [啟動時保留資料] 的便利屬性，可保留您在前一個工作階段中輸入的資料，讓您可以重複使用。

### 啟用 [啟動時保留資料] 屬性

1. 在應用程式專案的捷徑功能表上，選擇 [屬性] (或選擇 **F4** 按鍵)。
1. 在 [屬性] 視窗中，將 [啟動時保留資料] 屬性設定為 [是]。

	![設定 [啟動時保留資料] 屬性][preservedata]

當您重新執行應用程式時，這時部署指令碼就會將部署視為使用未受監視之自動模式的升級，將應用程式快速升級至較新版本並附加日期字串。此升級程序會保留您在前一個偵錯工作階段中輸入的所有資料。

![附加日期的新應用程式版本範例][preservedate]

保留資料時，所利用的是來自 Service Fabric 平台的升級功能。如需有關升級應用程式的詳細資訊，請參考 [Service Fabric 應用程式升級](service-fabric-application-upgrade.md)。

## 新增服務至 Service Fabric 應用程式

您可以將新的 Fabric 服務新增至應用程式以擴充其功能。若要確保應用程式封裝中包含該服務，請透過 [**新 Fabric 服務**] 功能表項目新增服務。

![新增新 Fabric 服務至應用程式][newservice]

選取要新增至應用程式的 Service Fabric 專案類型，並指定服務的名稱。請參閱[選擇服務架構](service-fabric-choose-framework.md)，以協助決定要使用的服務類型。

![選取要新增至應用程式的 Fabric 服務類型][addserviceproject]

新服務將會新增到方案與現有的應用程式封裝。服務參照與預設的服務執行個體將會新增到應用程式資訊清單。該服務會在下次您部署應用程式時建立並啟動。

![新服務將會新增到應用程式資訊清單][newserviceapplicationmanifest]

## 封裝 Service Fabric 應用程式

必須先建立應用程式封裝，才能將應用程式及其服務部署至叢集。封裝會以特定的配置來組織應用程式資訊清單、服務資訊清單和其他必要的檔案。Visual Studio 會在 'pkg' 目錄的應用程式專案資料夾中設定與管理封裝。從 [應用程式] 操作功能表中按一下 [封裝] 會建立或更新應用程式封裝。如果您使用自訂的 Powershell 指令碼來部署應用程式，您可能會想要這麼做。

## 移除應用程式

您可以使用「Service Fabric 總管」將應用程式類型從本機叢集解除佈建。存取叢集總管時，可以從叢集的「HTTP 閘道」端點 (通常是 19080 或 19007) 進行存取，例如 http://localhost:19080/Explorer。這會還原上述的部署步驟：

1. 移除任何執行中的應用程式執行個體
2. 取消註冊應用程式類型

![移除應用程式](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

- [Service Fabric 應用程式模型](service-fabric-application-model.md)
- [Service Fabric 應用程式部署](service-fabric-deploy-remove-applications.md)
- [管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)
- [偵錯 Service Fabric 應用程式](service-fabric-debugging-your-application.md)
- [使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=Nov15_HO4-->