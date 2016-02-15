<properties
   pageTitle="從 Azure 入口網站建立 Service Fabric 叢集 | Microsoft Azure"
   description="從 Azure 入口網站建立 Service Fabric 叢集。"
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


# 從 Azure 入口網站建立 Service Fabric 叢集

本頁將協助您設定 Azure Service Fabric 叢集。您的訂用帳戶必須要有足夠的核心，來部署將構成此叢集的 IaaS VM。


## 建立叢集

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 [+ 新增] 來新增資源範本。在 [全部內容] 下方的 [Marketplace] 中搜尋範本，該範本名稱為「Service Fabric 叢集」。

    a.按一下最上層的 [Marketplace]。

    b.按一下 [全部內容] 下方的 [Fabric]，然後按下 Enter 鍵。有時自動篩選功能會無法運作，因此請務必按下 Enter 鍵。![在 Azure 入口網站上搜尋 Service Fabric 叢集範本的螢幕擷取畫面。][SearchforServiceFabricClusterTemplate]

3. 選取清單中的 [Service Fabric 叢集]

4. 瀏覽至 [Service Fabric 叢集] 刀鋒視窗，然後按一下 [建立] 並提供叢集的詳細資料

5. 在 [建立新的資源群組] 下方，為資源群組設定與叢集相同的名稱。這樣讓您稍後能輕易地找到它們，在您嘗試變更部署或刪除叢集時會格外有用。

    >[AZURE.NOTE] 雖然您可以決定使用現有的資源群組，但最好還是建立新的資源群組。這可讓您輕鬆地刪除您不需要的叢集。

 	![建立新資源群組的螢幕擷取畫面。][CreateRG]

6. 務必要選取您要部署叢集的 [訂用帳戶]，尤其是在您擁有多個訂用帳戶時。

7. 選取下拉式清單中的某個 [位置]，預設值為 [美國西部]。

8. 設定您的 [節點類型]。節點類型就像是雲端服務中的角色，可用來定義定義 VM 的大小、VM 的數目，以及 VM 的屬性。您的叢集可以有不只一個節點類型，但主要節點類型 (您在入口網站定義的第一個節點類型) 必須至少有 5 個 VM。如何設定節點類型：

	a.選取您需要的 VM 大小/定價層。預設值為 [D4 標準]，但如果您只是要用這個叢集來測試自己的應用程式，也可以選取 [D2] 或任何較小的 VM。

	b.選擇 VM 的數目。您可以稍後相應增加或減少節點類型中的 VM 數目，但第一個節點類型必須至少有 5 個 VM。

	c.選擇節點類型的名稱 (1 到 12 個字元，只能包含字母和數字)。

	d.選擇 VM 遠端桌面的 [使用者名稱] 和 [密碼]。

	e.如果您的叢集需要數個節點類型，請考慮下列問題。(如果您打算只為叢集部署單一節點類型，請跳到步驟 9。)

	* 假設您想要部署包含前端服務和後端服務的應用程式，而您想把前端服務放在容量較小 (A2、D2 等的 VM 大小)，且擁有可連線至網際網路之連接埠的 VM 上。但您想把計算密集的後端服務放在容量較大 (D4、D6、D12 等的 VM 大小)，且不連線至網際網路的 VM 上。

	* 雖然您可以把這兩種服務都放同一個節點類型上，但我們建議您把它們分別放在叢集中的兩個節點類型上。每個節點類型都可以有不同的屬性，例如網際網路連線能力、VM 大小，以及能單獨調整的 VM 數目。

	* 請先定義一個至少有 5 個 VM 的節點類型，而其他節點類型可以至少有 1 個 VM。

  	![建立節點類型的螢幕擷取畫面。][CreateNodeType]

9. 如果您打算立刻把應用程式部署到叢集，則在 [應用程式連接埠] 節點類型 (或您建立的節點類型) 上新增您要為應用程式開放的連接埠。您可以稍後再為節點類型新增連接埠，方法是修改與該這個節點類型相關聯的負載平衡器。(新增探查，然後把探查新增到負載平衡器規則。) 立刻進行對您來說會比較簡單，因為入口網站自動化功能會把所需的探查和規則新增至負載平衡器。

	a.您可以在服務資訊清單中尋找屬於應用程式封裝的應用程式連接埠。請前往每個應用程式，開啟服務資訊清單，然後記下應用程式與外界通訊時所需的輸入端點。

	b.在 [應用程式輸入端點] 欄位中新增所有連接埠 (以逗號分隔)。TCP 用戶端連接端點的預設值為 19000，因此您不需要指定該值。舉例來說，範例應用程式 WordCount 需要開啟連接埠 83。您將在應用程式封裝的 servicemanifest.xml 檔案中找到該範例應用程式。(但封裝可能會包含不只一個的 servicemanifest.xml 檔案。)

    c.大部分的範例應用程式都使用連接埠 80 和 8081，因此如果您打算把範例部署到此叢集，請新增這兩個連接埠。![連接埠][Ports]

10. 您不需要設定 [放置屬性]，因為系統會新增「NodeTypeName」的預設放置屬性。如果應用程式有需要，您可以新增更多。

## 設定安全性

目前，Service Fabric 只支透過 X509 憑證援來保護叢集。在這個程序開始之前，您必須先把憑證上傳至金鑰保存庫。如需有關如何進行的詳細資訊，請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)。

您可選擇是否保護您的叢集，但強烈建議您這麼做。如果您選擇不要保護叢集，請將 [安全性模式] 切換至 [無]。

如需安全性考量及指示的文件，請參閱 [Service Fabric 叢集安全性](service-fabric-cluster-security.md)。

![Azure 入口網站中安全性組態的螢幕擷取畫面。][SecurityConfigs]

## 選用：設定診斷功能

預設會在您的叢集上啟用診斷功能，以協助排解疑難問題。如果您要停用診斷功能：

1. 瀏覽至 [診斷組態] 刀鋒視窗。

2. 將 [狀態] 切換成 [關閉]。

## 選用：設定 Service Fabric 叢集的設定

您可以利用這個進階選項，來變更 Service Fabric 叢集的預設設定。我們建議您不要變更該預設設定，除非您確定自己的應用程式或叢集有這個需要。

## 完成叢集建立程序

如要完成叢集建立程序，請按一下 [摘要] 來查看您提供的組態，或是下載您將用來部署叢集的 Azure 資源管理員範本。當您提供所有必要的設定之後，[建立] 按鈕將會啟用，讓您只要按一下就能啟動叢集建立程序。

您可以在通知功能中看到叢集的建立進度。(請按一下畫面右上角狀態列附近的鈴噹圖示)。 如果您在建立叢集時曾經按一下 [釘選到「開始面板」]，現在您就會看到 [部署 Service Fabric 叢集] 已釘選到**開始**面板

![顯示 [部署 Service Fabric 叢集] 的開始面板的螢幕擷取畫面。][Notifications]

## 檢視叢集狀態

當您部署完畢之後，就可以在入口網站檢查您的叢集：

1. 前往 [瀏覽]，然後按一下 [Service Fabric 叢集]。

2. 尋找您的叢集，然後按一下它。![在入口網站尋找叢集的螢幕擷取畫面。][BrowseCluster]

3. 現在儀表板會顯示叢集的詳細資料，包括叢集的公用 IP 位址。只要把滑鼠游標移動到 [叢集公用 IP 位址] 上，畫面就會出現剪貼簿，您只要按一下剪貼簿就能複製該 IP 位址。![顯示叢集詳細資料的儀表板螢幕擷取畫面。][ClusterDashboard]

  叢集的儀表板刀鋒視窗上的 [節點監視器] 區段，會顯示健康狀態良好和不良的 VM 數目。如需進一步了解叢集健康狀態，請參閱 [Service Fabric 健康狀態模型簡介](service-fabric-health-introduction.md)。

## 連線到叢集並部署應用程式

當您建立叢集之後，就可以連線到叢集，並開始部署應用程式。首先，請在已安裝 Service Fabric SDK 的電腦上啟動 Windows PowerShell。然後，如要連線到叢集，請視您建立的是安全或不安全的叢集，來執行下列其中一組 PowerShell 命令。

- 選項 1：連線到不安全的叢集。

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- 選項 2：連線到安全的叢集。

    1. 執行下列命令，以便在您即將用來執行「Connect-serviceFabricCluster」PowerShell 命令的電腦上設定憑證。

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. 執行下列 PowerShell 命令來連線到安全的叢集。憑證的詳細資料與您在入口網站所提供的資訊相同。

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        舉例來說，上述 PowerShell 命令應該會類似下列內容：

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

現在您已經建立連線了，請執行下列命令來部署應用程式，但別忘了要先用您電腦上適當的路徑來取代以下所示的路徑。下列範例會部署 WordCount 範例應用程式。

1. 將封裝複製到您之前連線到的叢集。

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. 向 Service Fabric 註冊您的應用程式類型。

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. 針對您剛註冊的應用程式類型來建立新的執行個體。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. 現在，開啟您選擇的瀏覽器，然後連線到應用程式正在接聽的端點。對於範例應用程式 WordCount 來說，URL 看起來類似：

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 後續步驟

- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric 叢集安全性](service-fabric-cluster-security.md)
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0204_2016-->