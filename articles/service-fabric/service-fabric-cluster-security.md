<properties
   pageTitle="如何保護 Service Fabric 叢集 | Microsoft Azure"
   description="如何保護 Service Fabric 叢集。有哪些選項？"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# 保護 Service Fabric 叢集

Service Fabric 叢集是您擁有的資源，防止未經授權的資源存取，您必須保護其安全性，尤其是當其中有執行中的生產工作負載。本文將逐步引導您處理程序及方式。

##  您應該考慮的叢集安全性案例？

Service Fabric 會提供下列案例的安全性：

1. **節點對節點安全性**或保護節點對節點通訊的叢集。保護叢集中 VM/電腦之間的通訊。這可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務

	![節點對節點][Node-to-Node]

2. **用戶端對節點安全性**或保護與叢集中的特定節點通訊的 Fabric 用戶端驗證並保護用戶端通訊，可確保只有獲得授權的使用者可以存取叢集與 Windows Fabric 叢集上部署的應用程式。用戶端是透過其 Windows 安全性認證或其憑證安全性認證唯一識別。

	![用戶端對節點][Client-to-Node]

	對於任一種通訊案例 (節點對節點或用戶端對節點)，Service Fabric 支援使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。節點對節點或用戶端對節點安全性的選擇是彼此獨立，可以相同或不同。

	當您建立叢集時，Azure Service Fabric 會使用您指定為節點類型組態一部分的 x509 伺服器憑證。如需這些憑證是什麼以及如何才能取得/建立憑證的快速概觀，請向下捲動至此頁面底部。

3. **角色型存取控制 (RBAC)**：能夠限制從叢集上的唯讀作業以至一組憑證的系統管理作業。

4. **服務帳戶和 RunAs**：Service Fabric 本身會執行為 Windows 服務處理程序 (Fabric.exe)，且 Fabric.exe 處理程序所執行的安全性帳戶可以設定。可以保護 Fabric.exe 在叢集中的每個節點執行的處理程序帳戶，以及為每個服務啟動的服務主機處理程序。如需詳細資訊，請參閱[應用程式安全性和 Runas](service-fabric-application-runas-security.md) 文件
  

## 如何使用憑證保護 Service Fabric 叢集。

若要設定安全的 Service Fabric 叢集，您需要至少一部伺服器 / x509 憑證。您接著會將該憑證上傳至 Azure 金鑰保存庫並將它使用於叢集建立程序

有三個不同的步驟

1. 取得 x509 憑證
2. 將憑證上傳至 Azure 金鑰保存庫。
3. 將憑證的位置和詳細資料提供給 Service Fabric 叢集建立程序。

 
## 步驟 1：取得 x509 憑證

1. 對於執行生產工作負載的叢集，您必須使用[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 簽署的 x509 憑證來保護叢集。如需取得這些憑證的詳細資訊，請移至 [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx)。
2. 對於用於測試的叢集，您可以選擇使用自我簽署憑證。步驟 2.5 將探討如何進行步驟。


## 步驟 2：將 x509 憑證上傳至金鑰保存庫

這是複雜的程序，因此我們將 powershell 模組上傳至 Git Repp，它會為您完成此程序。

**步驟 2.1**：從這個 [Git 儲存機制](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)將此資料夾複製到您的電腦。

**步驟 2.2**：確定 Azure SDK 1.0+ 已安裝在您的電腦上。

**步驟 2.3**：開啟 Powershell 視窗並匯入 ServiceFabricRPHelpers.psm

```
Remove-Module ServiceFabricRPHelpers
```

複製下列項目，並且將路徑變更為您的電腦的 .psm1 的路徑。範例如下：```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```
  

**步驟 2.4**：如果您使用已經取得的憑證，則遵循下列步驟，否則請跳至步驟 2.5。


登入您的 Azure 帳戶

```
Login-AzureRmAccount
```

如果它們原本不存在，指令碼會建立新的資源群組和/或保存庫。

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file> 
```
以下是已填入指令碼的範例。```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ") -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx 
```

指令碼順利完成時，您會看到類似下面的輸出，您需要這些項目以進行步驟 #3。

1. **憑證指紋**：2118C3BCE6541A54A0236E14ED2CCDD77EA4567A
2. **SourceVault** /KeyVault 的資源識別碼：/subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **憑證 URL** /金鑰保存庫中的憑證位置 URL：https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea 

您會有您設定安全叢集所需的資訊。移至 [步驟 3]


**步驟 2.5**：如果想要建立新的自我簽署憑證，並將它上傳至金鑰保存庫。

登入您的 Azure 帳戶

```
Login-AzureRmAccount
```

如果它們原本不存在，指令碼會建立新的資源群組和/或保存庫。

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file> 
```
您提供給指令碼的 OutputPath 將包含我們上傳至金鑰保存庫的新自我簽署憑證。


**注意** DnsName <字串> 指定一或多個 DNS 名稱，以在未透過 CloneCert 參數指定要複製的憑證時放入憑證的主體別名副檔名中。第一個 DNS 名稱也會儲存為主體名稱。如果未指定任何簽署憑證，則第一個 DNS 名稱也會儲存為簽發者名稱。

您可以在下列位置深入了解建立自我簽署憑證：[https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)

以下是已填入指令碼的範例。```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ") -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

因為它是自我簽署憑證，您必須將其匯入您電腦的「受信任的人」存放區，才能使用這個憑證以連線到安全的叢集。```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
``````
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

指令碼順利完成時，您會看到類似下面的輸出，您需要這些項目以進行步驟 #3。

1. **憑證指紋**：64881409F4D86498C88EEC3697310C15F8F1540F
2. **SourceVault** /KeyVault 的資源識別碼：/subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc
3. **憑證 URL** /金鑰保存庫中的憑證位置 URL：https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea 

##步驟 3：設定安全的叢集 

請依照 [Service Fabric 叢集建立程序](service-fabric-cluster-creation-via-portal.md)文件所述的步驟，直到您進入安全性組態。以下是您設定安全性組態的方式。

需要使用的憑證會指定於 [安全性組態] 下的 NodeType 層級。您必須為您的叢集中的每個節點類型指定此項目。雖然這份文件會逐步解說如何使用入口網站執行這項作業，但您可以使用 ARM 範本執行相同的作業。

![SecurityConfigurations\_01][SecurityConfigurations_01]

必要參數

- **安全性模式**：務必選取 [x509 憑證]。這向 Service Fabric 表示您想要設定安全的叢集。 
- **叢集保護層級**：請參閱這份[保護層級文件](https://msdn.microsoft.com/library/aa347692.aspx)，了解每一個值所代表的意義。雖然我們在此允許使用三個值 - EncryptAndSign、Sign、None。最好是保留預設值 "EncryptAndSign"，除非您知道您在做什麼。
- **來源保存庫**指的是金鑰保存庫的資源識別碼，其格式應為 ```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **憑證 URL**指的是金鑰保存庫中已上傳憑證的位置 URL，其格式應為 ```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **憑證指紋**指的是憑證的指紋，其位於您稍早指定的 URL。

選擇性參數 - 您可以選擇性地指定用戶端機器用來在叢集上執行作業的其他憑證。根據預設，您在 [必要] 參數中指定的指紋會加入至能夠執行用戶端作業的已授權指紋清單。

系統管理用戶端 - 此資訊用來驗證連接到叢集管理端點的用戶端是否確實出示正確的認證，以在叢集上執行系統管理和唯讀動作。您可以指定一個以上想要對系統管理作業進行授權的憑證。


- **授權依據** - 向 Service Fabric 表示是否應該使用主體名稱或依指紋查詢這個憑證。使用要授權的主體名稱不是很好的安全性作法，不過，這可提供更多的彈性。


- **主體名稱**：只有在您已指定依主體名稱授權時才需要。
- **簽發者指紋**：當用戶端向伺服器出示其認證時，這可讓伺服器執行其他層級的檢查。

唯讀用戶端 - 此資訊用來驗證連接到叢集管理端點的用戶端是否確實出示正確的認證，以在叢集上執行唯讀動作。您可以指定一個以上想要對唯讀作業進行授權的憑證。


- **授權依據** - 向 Service Fabric 表示是否應該使用主體名稱或依指紋查詢這個憑證。使用要授權的主體名稱不是很好的安全性作法，不過，這可提供更多的彈性。

- **主體名稱**：只有在您已指定依主體名稱授權時才需要。
- **簽發者指紋**：當用戶端向伺服器出示其認證時，這可讓伺服器執行其他層級的檢查。


## 如何更新叢集中的憑證

Service Fabric 允許您指定兩個憑證 (主要和次要)。您在建立時指定的憑證會預設為主要憑證。若要新增其他憑證，您需要將該憑證部署到叢集中的 VM。上述的步驟 #2 概述如何將新的憑證上傳至 keyvalult。您可以使用相同的 keyvalult，就像對第一個憑證使用的一樣。

請參考[將憑證從客戶管理的金鑰保存庫部署到 VM](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)文件中的作法。

一旦順利完成該作業，請移至入口網站或透過 ARM，向 Service Fabric 表示您具有的次要憑證也可以使用。您只需要指紋。

程序如下：在入口網站上，瀏覽至您想要加入此憑證的叢集資源，按一下憑證設定，輸入次要憑證指紋並且按 [儲存]。將會開始進行部署，而在成功完成該部署時，您目前可以使用主要或次要憑證在叢集上執行管理作業。

![SecurityConfigurations\_02][SecurityConfigurations_02]

如果您現在想要移除其中一個憑證，您可以這麼做。請務必在移除憑證後按下 [儲存]，如此就會開始進行新的部署。一旦完成該部署，您所移除的憑證便無法再用來連接到叢集。對於安全的叢集，您一律需要至少部署一個有效 (未撤銷或過期) 的憑證，否則您將無法存取叢集。

診斷事件可讓您知道是否有任何憑證快要到期。



## 什麼是 X509 憑證？

X509 數位憑證通常用來驗證用戶端與伺服器、加密及數位簽署訊息。如需這些憑證的詳細資訊，請移至 [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)

**注意**

1. 使用於執行生產工作負載的叢集中的憑證，應使用正確設定的 Windows Server 憑證服務來建立，或透過已核准的[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 取得。
2. 絕對不要在生產環境中使用透過 MakeCert.exe 等工具建立的暫存或測試憑證
3. 對於用於測試的叢集，您可以選擇使用自我簽署憑證。 


## 什麼是伺服器憑證和用戶端憑證？

**伺服器/x509 憑證**

伺服器憑證的主要工作是向用戶端驗證伺服器 (節點) 或是向伺服器 (節點) 驗證伺服器 (節點)。當用戶端或節點驗證節點時的其中一項初始檢查是比較 [主體] 欄位中的一般名稱值，以確保它存在於已設定的允許一般名稱清單中。此一般名稱或其中一個憑證主體別名必須存在於允許的一般名稱清單中。

下列文章說明如何產生具有主體別名 (SAN) 的憑證。[http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351)
 
**注意：**[主體] 欄位可以包含數個值，而每個值的前面會加上代表該值的起首字母。最常見的起首字母是 "CN" 代表一般名稱，例如 "CN = www.contoso.com"。[主體] 欄位也可能空白。也請注意選擇性 [主體別名] 欄位；若已填入資料，此欄位必須包含憑證的一般名稱，以及每個主體別名的一個項目。這些會被輸入為 DNS 名稱值。

也請注意憑證的 [預定目的] 欄位值應包含適當的值，例如「伺服器驗證」或「用戶端驗證」。

**用戶端憑證**

用戶端憑證通常不是由第三方憑證授權單位所發行。然而，目前使用者位置的個人存放區通常包含由根授權單位所放置的憑證，其預定目的為「用戶端驗證」。用戶端可以在需要相互驗證時使用這類憑證。Service Fabric 叢集上的所有管理作業都需要伺服器憑證。不應該使用用戶端憑證。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
- [Service Fabric 叢集升級程序與您的期望](service-fabric-cluster-upgrade.md)
- [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric 健康情況模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_1210_2015-->