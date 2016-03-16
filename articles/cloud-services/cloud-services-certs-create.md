<properties 
	pageTitle="雲端服務和管理憑證 | Microsoft Azure" 
	description="了解如何建立憑證並搭配 Microsoft Azure 使用" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>

# Azure 雲端服務的憑證概觀
Azure 中的憑證用於雲端服務 ([服務憑證](#what-are-service-certificates)) 及用於向管理 API 進行驗證 (使用 Azure 傳統入口網站而非 ARM 時[管理憑證](#what-are-management-certificates))。本主題提供兩種憑證類型的一般概觀、如何[建立](#create)這些憑證類型，以及如何將其[部署](#deploy)到 Azure。

在 Azure 中使用的憑證是 x.509 v3 憑證，而且可以由其他受信任的憑證簽署，或者可以自我簽署。自我簽署的憑證是由自己的建立者簽署，因此，預設不受到信任。大部分的瀏覽器都可以忽略這個憑證。自我簽署的憑證應該僅由您自己在開發和測試雲端服務時使用。

Azure 所使用的憑證可以包含私密或公開金鑰。憑證具有指紋，可以明確的方式提供識別它們的方法。這個指紋用於 Azure [組態檔](cloud-services-configure-ssl-certificate.md)中，以識別雲端服務應該使用哪個憑證。

## 什麼是服務憑證？
服務憑證會附加至雲端服務，並啟用進出服務的安全通訊。例如，如果您部署了一個 Web 角色，您會想要提供可以驗證公開的 HTTPS 端點的憑證。在服務定義中定義的服務憑證會自動部署至執行您角色的執行個體的虛擬機器。

您可以使用 Azure 傳統入口網站或使用服務管理 API，將服務憑證上傳到 Azure 傳統入口網站。服務憑證會與特定的雲端服務相關聯，並指派給服務定義檔中的部署。

服務憑證可以與您的服務分開管理，而且可以由不同的人員管理。例如，開發人員所上傳的服務封裝，指的可能是 IT 管理員先前上傳至 Azure 的憑證。IT 管理員可以變更服務的組態來管理和更新該憑證，而不需要上傳新的服務封裝。這可能是因為憑證的邏輯名稱及其存放區名稱和位置是在服務定義檔中指定，而憑證指紋是在服務組態檔中指定。若要更新憑證，只需要上傳新憑證，並變更服務組態檔中的憑證指紋值即可。

## 什麼是管理憑證？
管理憑證可讓您使用 Azure 傳統所提供的服務管理 API 進行驗證。許多程式和工具 (例如 Visual Studio 或 Azure SDK) 會使用這些憑證，將各種 Azure 服務的設定與部署自動化。這些並不是真的與雲端服務相關。

>[AZURE.WARNING] 請務必小心！ 這些憑證類型允許使用它們進行驗證的任何人管理與他們相關聯的訂用帳戶。

### 限制
每個訂用帳戶有 100 個管理憑證的限制。此外，在特定服務管理員的使用者識別碼底下的所有訂用帳戶也有 100 個管理憑證的限制。如果帳戶系統管理員的使用者識別碼已經用來加入 100 個管理憑證，而且還需要有更多憑證，您可以新增共同管理員來加入其他憑證。

加入超過 100 個憑證之前，請查看您是否可以重複使用現有的憑證。使用共同管理員會對您的憑證管理程序增加可能不必要的複雜性。


<a name="create"></a>
## 建立新的自我簽署憑證
您可以使用任何可用的工具建立自我簽署的憑證，前提是，這些憑證遵守以下設定：

* X.509 憑證。
* 包含一個私密金鑰。
* 針對金鑰交換 (.pfx 檔案) 而建立。
* 主體名稱必須符合用來存取雲端服務的網域。
    > 您無法取得 cloudapp.net 網域 (或針對任何 Azure 相關網域) 的 SSL 憑證；憑證的主體名稱必須符合用來存取應用程式的自訂網域名稱。例如，**contoso.net**，而非**contoso.cloudapp.net**。
* 至少為 2048 位元加密。
* **僅限服務憑證**：用戶端憑證必須位於*個人*憑證存放區。

在 Windows 上建立憑證有兩個簡單的方法：使用 `makecert.exe` 公用程式或 IIS。

### Makecert.exe

此公用程式會與 Visual Studio 2013/2015 一起安裝。它是一個主控台公用程式，可讓您建立並安裝憑證。如果您啟動安裝 Visual Studio 時所建立的 **VS2015 的開發人員命令提示字元**捷徑，在路徑中將會出現一個包含此工具的命令提示字元。

    makecert -sky exchange -r -n "CN=[CertificateName]" -pe -a sha1 -len 2048 -ss My -sv [CertificateName].pvk [CertificateName].cer


### 網際網路資訊服務 (IIS)

在網際網路上有許多涵蓋如何使用 IIS 執行這項操作的網頁。[這裡](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html)是我找到的其中一個我認為說明得很好的網頁。

### Java
您可以使用 Java [建立憑證](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate)。

### Linux
[本文](../virtual-machines/virtual-machines-linux-use-ssh-key.md)說明如何使用 SSH 建立憑證。

## 後續步驟

[將服務憑證上傳至 Azure 傳統入口網站](cloud-services-configure-ssl-certificate.md) (或 [Azure 入口網站](cloud-services-configure-ssl-certificate-portal.md))。

將[管理 API 憑證](../azure-api-management-certs.md)上傳至 Azure 傳統入口網站。

>[AZURE.NOTE] Azure 入口網站不會使用管理憑證存取 API，但是會使用使用者帳戶。

<!---HONumber=AcomDC_0204_2016-->