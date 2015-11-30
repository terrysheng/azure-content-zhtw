<properties
   pageTitle="如何保護 Service Fabric 叢集的安全 |Microsoft Azure"
   description="如何保護 Service Fabric 叢集的安全。有哪些選項？"
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

# 如何使用憑證保護 Service Fabric 叢集。

若要設定安全的 Service Fabric 叢集，您需要至少一部伺服器 / x509 憑證。您接著會將該憑證上傳至 Azure 金鑰保存庫並將它使用於 [Service Fabric 叢集建立程序](service-fabric-cluster-creation-via-portal.md)中所述的叢集建立程序

有三個不同的步驟

1. 取得 x509 憑證
2. 將憑證上傳至 Azure 金鑰保存庫。
3. 將憑證的位置和詳細資料提供給 Service Fabric 叢集建立程序。

在我們深入討論細節之前，讓我們先取得一些有關案例的基本概念。

##  涵蓋哪些案例？

Service Fabric 會提供下列案例的安全性：

1. 保護節點間通訊的叢集。
2. 保護與叢集中的特定節點通訊的 Fabric 用戶端
3. 角色型存取控制 (RBAC) - 能夠限制從叢集上的唯讀作業以至一組憑證的系統管理作業。   

當您建立叢集時，Service Fabric 會使用您指定為節點類型組態一部分的 x509 伺服器憑證。如需這些憑證是什麼以及如何才能取得/建立憑證的快速概觀，請向下捲動至此頁面底部。

 
## 取得 x509 憑證

1. 對於執行生產工作負載的叢集，您必須使用[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 簽署的 x509 憑證來保護叢集。如需取得這些憑證的詳細資訊，請移至 [http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx)。
2. 對於用於測試的叢集，您可以選擇使用自我簽署憑證。


## 建立自我簽署憑證以供測試

建立自我簽署憑證的詳細資訊位於 [https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx)
    
以下是我用來建立測試憑證的 PS，但請務必閱讀上述文件以確定它符合您的需求。```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**注意** DnsName <字串> 指定一或多個 DNS 名稱，以在未透過 CloneCert 參數指定要複製的憑證時放入憑證的主體別名副檔名中。第一個 DNS 名稱也會儲存為主體名稱。如果未指定任何簽署憑證，則第一個 DNS 名稱也會儲存為簽發者名稱。

## 將 x509 憑證上傳至金鑰保存庫

有關如何將憑證上傳至保存庫金鑰的指示在此：[金鑰保存庫文件的連結](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)。

請務必記下來源保存庫 URL、憑證 URL 和憑證指紋。您在設定安全的 Service Fabric 叢集時會需要這些資料。您需要的資料如下所示：



1. **KeyVault/來源保存庫 URL 的資源識別碼** : /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **金鑰保存庫中的憑證位置 URL** : https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **憑證指紋** : 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##設定安全的叢集 

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

Service Fabric 允許您指定兩個憑證 (主要和次要)。您在建立時指定的憑證會預設為主要憑證。若要新增其他憑證，您需要將該憑證部署到叢集中的 VM。請參考[將憑證從客戶管理的金鑰保存庫部署到 VM](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)文件中的作法。

一旦順利完成該作業，請移至入口網站或透過 ARM，向 Service Fabric 表示您具有的次要憑證也可以使用。您只需要指紋。

在入口網站上，瀏覽至您想要加入此憑證的叢集資源，按一下憑證設定，輸入次要憑證指紋並且按 [儲存]。將會開始進行部署，而在成功完成該部署時，您目前可以使用主要或次要憑證在叢集上執行管理作業。

![SecurityConfigurations\_02][SecurityConfigurations_02]

如果您現在想要移除其中一個憑證，您可以這麼做。請務必在移除憑證後按下 [儲存]，如此就會開始進行新的部署。一旦完成該部署，您所移除的憑證便無法再用來連接到叢集。對於安全的叢集，您一律需要至少部署一個有效 (未撤銷或過期) 的憑證，否則您將無法存取叢集。

診斷事件可讓您知道是否有任何憑證快要到期。



## 什麼是 X509 憑證？

X509 數位憑證通常用來驗證用戶端與伺服器、加密及數位簽署訊息。如需這些憑證的詳細資訊，請移至 [http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx)

**注意**

1. 使用於執行生產工作負載的叢集中的憑證，應 使用正確設定的 Windows Server 憑證服務來建立，或透過已核准的[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) 取得。
2. 絕對不要在生產環境中使用透過 MakeCert.exe 等工具建立的暫存或測試憑證
3. 對於用於測試的叢集，您可以選擇使用自我簽署憑證。 


##什麼是伺服器憑證和用戶端憑證？

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
- [Service Fabric 健康狀態模型簡介](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->