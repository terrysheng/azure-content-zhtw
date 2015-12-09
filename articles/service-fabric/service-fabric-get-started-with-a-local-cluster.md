<properties
   pageTitle="在您的本機叢集上開始部署和升級應用程式 | Microsoft Azure"
   description="設定本機 Service Fabric 叢集、將現有的應用程式部署至該叢集，然後升級該應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/20/2015"
   ms.author="seanmck"/>

# 在您的本機叢集上開始部署和升級應用程式
Service Fabric SDK 包含完整的本機開發環境，可讓您快速地在本機叢集上開始部署和管理應用程式。在本文中，您將從 Windows PowerShell 建立本機叢集、將現有的應用程式部署至該叢集，然後將該應用程式升級為新版本。

> [AZURE.NOTE]本文假設您已經[設定開發環境](service-fabric-get-started.md)。

## 建立本機叢集
Service Fabric 叢集代表一組您可以部署應用程式的硬體資源。通常，叢集是由任意數量的電腦 (從 5 部到數千部) 所組成，但 Service Fabric SDK 包含可在一部電腦上執行的叢集組態。

請務必了解 Service Fabric 本機叢集不是模擬器。它會執行在多部電腦的叢集上找到的相同平台程式碼。唯一的差別在於它會在一部電腦上執行通常分散於五部電腦的平台程序。

SDK 提供兩種方式來設定本機叢集：Windows PowerShell 指令碼和 [本機叢集管理員] 系統匣應用程式。在本教學課程中，我們將使用 PowerShell 指令碼。

> [AZURE.NOTE]如果您已藉由從 Visual Studio 部署應用程式來建立本機叢集，您可以略過本節。


1. 以系統管理員身分啟動新的 PowerShell 視窗。

2. 從 SDK 資料夾執行叢集設定指令碼：

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    叢集設定將需要一些時間，而後您應該會看到類似下列的輸出：

    ![叢集設定輸出][cluster-setup-success]

    您現在已準備好將應用程式部署至您的叢集。

## 部署應用程式
Service Fabric SDK 包含一組豐富的架構以及用來建立應用程式的開發人員工具。如果您有興趣學習如何在 Visual Studio 中建立應用程式，請參閱[在 Visual Studio 中建立第一個應用程式](service-fabric-create-your-first-application-in-visual-studio.md)。在本教學課程中，我們將使用現有的範例應用程式 (稱為 WordCount)，如此我們即可專注於平台的管理層面，包括部署、監視及升級。


1. 以系統管理員身分啟動新的 PowerShell 視窗。

2. 匯入 Service Fabric SDK PowerShell 模組。

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. 建立目錄以儲存您將下載和部署的應用程式，例如 c:\\ServiceFabric。

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. 從[這裡](http://aka.ms/servicefabric-wordcountapp)將 WordCount 應用程式下載至您建立的位置。

5. 連接到本機叢集：

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. 叫用 SDK 的部署命令來建立新的應用程式，並提供應用程式封裝的名稱和路徑。

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    如果順利執行，您應該會看到如下的輸出：

    ![將應用程式部署至本機叢集][deploy-app-to-local-cluster]

7. 若要查看動作中的應用程式，請啟動瀏覽器並瀏覽至 [http://localhost:8081/wordcount/index](http://localhost:8081/wordcount/index)。您應該會看到如下的結果：

    ![部署應用程式 UI][deployed-app-ui]

    WordCount 應用程式非常簡單。它包含的用戶端 JavaScript 程式碼可產生隨機五個字元的「單字」，這些字接著會透過 ASP.NET WebAPI 轉送至應用程式。具狀態服務會根據文字的第一個字元，追蹤已計算、分割的字數。我們所部署的應用程式包含四個資料分割，所以開頭為 A 到 G 的單字會儲存在第一個磁碟分割中，而開頭為 N 到 H 的單字會儲存在第二個資料分割中，依此類推。

## 檢視應用程式詳細資料和狀態
部署應用程式後，讓我們在 PowerShell 中看看一些應用程式詳細資料。

1. 查詢叢集上所有已部署的應用程式：

    ```powershell
    Get-ServiceFabricApplication
    ```

    假設您只部署 WordCount 應用程式，您會看到類似下面的內容：

    ![在 PowerShell 中查詢所有已部署的應用程式][ps-getsfapp]

2. 查詢 WordCount 應用程式中包含的服務集合，進而移至下一個層級。

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![在 PowerShell 中列出應用程式的服務][ps-getsfsvc]

    請注意，此應用程式是由兩個服務所組成：Web 前端服務以及可管理文字的具狀態服務。

3. 最後，看看 WordCountService 的資料分割清單：

    ![在 PowerShell 中檢視服務資料分割][ps-getsfpartitions]

    您剛使用的命令集 (例如所有的 Service Fabric PowerShell 命令) 適用於任何您可能連接的叢集 (本機或遠端)。

    若要以更具視覺效果的方式來與叢集互動，您可以在瀏覽器中瀏覽至 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)，以使用 Web 型 Service Fabric 總管工具。

    ![在 Service Fabric 總管中檢視應用程式詳細資料][sfx-service-overview]

    > [AZURE.NOTE]若要深入了解 Service Fabric 總管，請參閱[使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)

## 升級應用程式
Service Fabric 會在應用程式推展於叢集時監視其健康狀態，進而提供不需停機的升級。讓我們執行 WordCount 應用程式的簡單升級。

新版的應用程式現在只會計算以母音開頭的單字。進行升級時，我們會看到應用程式的行為出現兩項變更。第一，計數成長的速率應該變慢，因為計算的單字比較少。第二，由於第一個資料分割有兩個母音 (A 和 E) 而其他每個資料分割只包含一個母音，其計數最後應該會開始超越其他資料分割。

1. 從[這裡](http://aka.ms/servicefabric-wordcountappv2)將 v2 封裝下載到您下載 v1 封裝的相同位置。

2. 返回 PowerShell 視窗並使用 SDK 的升級命令在叢集中註冊新版本，並開始升級 fabric:/WordCount。

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    開始升級時，您應會在 PowerShell 中看到如下所示的輸出。

    ![在 PowerShell 中的升級進度][ps-appupgradeprogress]

3. 當升級正在進行時，您可能會發現從 Service Fabric 總管監視其狀態更加輕鬆。啟動瀏覽器視窗並瀏覽至 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)。按一下左側樹狀目錄中的 [應用程式]，然後選擇 [進行中的升級]。

    ![在 Service Fabric 總管中的升級進度][sfx-upgradeprogress]

    請注意，[升級進度] 指示器代表在您的叢集的升級網域中的升級狀態。透過每個網域繼續升級時，系統會執行健康狀態檢查，先確保應用程式運作正常，再繼續執行。

4. 如果您對 fabric:/WordCount 應用程式包含的服務集合重新執行先前的查詢，您會注意到，雖然 WordCountService 的版本已變更，但 WordCountWebService 的版本維持不變：

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![在升級後查詢應用程式服務][ps-getsfsvc-postupgrade]

    這強調 Service Fabric 如何管理應用程式升級，而只影響已變更的服務 (或這些服務中的程式碼/組態封裝) 集合，讓升級程序變得更快速且更可靠。

5. 最後，返回瀏覽器來觀察新應用程式版本的行為。如同預期，計數進度比較緩慢，而第一個磁碟分割最後會有稍微多一些的磁碟區。

    ![在瀏覽器中檢視新版的應用程式][deployed-app-ui-v2]

## 後續步驟
- 您現在已部署並升級某些預先建置的應用程式，您可以[嘗試在 Visual Studio 中建立自己的應用程式](service-fabric-create-your-first-application-in-visual-studio.md)。
- 本文中在本機叢集上執行的所有動作也可以在 [Azure 叢集](service-fabric-cluster-creation-via-portal.md)上執行。
- 本文中執行的升級非常基本。若要深入了解 Service Fabric 升級的功用和彈性，請參閱[升級文件](service-fabric-application-upgrade.md)。

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_1203_2015-->