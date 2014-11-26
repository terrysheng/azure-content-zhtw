<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

在 Azure 中建立 Linux 的管理憑證
================================

當您要使用服務管理 API 與 Azure 映像平台互動時，您必須要有管理憑證。

<http://msdn.microsoft.com/zh-tw/library/windowsazure/gg551721.aspx> 上已有相關文件說明如何建立及管理這些憑證。您也可以使用 OpenSSL 來建立管理憑證。如需詳細資訊，請參閱 [OpenSSL](http://openssl.org/)。但本文件主要說明如何使用可能無法供所有 Linux 使用者存取的 Silverlight 入口網站。本文說明如何取得這些憑證，將其與不同的工具、合作夥伴整合，以及您在此功能新增至 Azure 管理入口網站之前如何自行加以使用。

目錄
----

-   [從 publishsettings 檔案取得管理憑證](#createcert)
-   [使用 Azure 管理入口網站來安裝管理憑證](#management)

作法：建立及上傳管理憑證
------------------------

我們建立了簡便的方式，供您透過下列途徑建立管理憑證：<https://windows.azure.com/download/publishprofile.aspx>

此網站會要求您使用入口網站認證進行登入，然後為您產生連同 subscriptionID 封裝在 publishsettings 檔案中的管理憑證，以供您下載。

![linuxcredentials](./media/linux-create-management-cert/linuxcredentials.png)

請確實將此檔案儲存在安全之處，因為此檔案是無法復原的，一旦損毀，您將必須產生新的管理憑證。(您可在系統中使用的憑證有總數限制。請參閱此網站的適當區段，以確認此總數。)此憑證將有多方面的用途：

### 在 Visual Studio 中

![VSpublish](./media/linux-create-management-cert/VSpublish.png)

### 在 Linux Azure 命令列中

您可以匯入憑證，以藉由執行 Azure 帳戶匯入命令加以使用：

![cmdlinepublish](./media/linux-create-management-cert/cmdlinepublish.png)

對於任何其他需要此工具的合作夥伴或軟體，您必須從檔案本身擷取管理憑證，並對其執行 Base 64 解碼。有些合作夥伴 (例如 ScaleXtreme 和 SUSE Studio) 會直接以其目前的格式使用檔案。

若要擷取管理憑證，您必須執行此程序。

您必須從該檔案中的 ManagementCertificate 後面，擷取位於引號中的 base 64 編碼內容。

	?xml version="1.0" encoding="utf-8"?>
	<PublishData>
	  <PublishProfile
	    PublishMethod="AzureServiceManagementAPI"
	    Url="https://management.core.windows.net/"
	    ManagementCertificate="xxxxx">
	    <Subscription
	      Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
	      Name="hjereztest" />
	  </PublishProfile>
	</PublishData>
	
您必須將其複製到檔案中，並使用 Base 64 解碼器來解碼。在 Linux 中，您可以使用：

    Base64 -d [encodedfile] > [decodedfile].pfx

這會為您提供一個 pfx 檔案，且您將可視需要使用 openssl 擷取私密金鑰，將此檔案轉換為其他格式：

	openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem

在 Windows 中，您可以使用 powershell 或免費的 Windows base 64 解碼器 (例如 [http://www.fourmilab.ch/webtools/base64/base64.zip](http://www.fourmilab.ch/webtools/base64/base64.zip))，藉由執行下列命令來解碼及擷取 PFX 檔案：

    base64 -d key.txt ->key.pfx

#### 憑證產生限制

您可以使用此工具在平台上建立的憑證數上限為 10 個。然而，金鑰一旦產生之後即無法復原，因為我們並未將這些私密金鑰儲存在系統中的任何位置上。在達到系統中的憑證數上限後，您將必須透過 Azure 論壇要求支援人員將您的憑證清除，或使用可顯示舊版 Silverlight 入口網站的瀏覽器執行這些工作。

#### 如果您的私密金鑰已壓縮

如果您的私密金鑰已壓縮，您將必須使用支援 Silverlight 的瀏覽器存取舊版入口網站，並刪除檔案上的對應管理憑證，或透過論壇與支援人員連絡。
只是產生新憑證是不夠的，因為這 10 個憑證都還是有效的，且您舊有的壓縮金鑰仍將可存取網站。

使用 Azure 管理入口網站來安裝管理憑證
-------------------------------------

您可以透過多種方式建立管理憑證。如需建立憑證的詳細資訊，請參閱[建立 Azure 的管理憑證](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg551722.aspx)。建立憑證後，請將其新增至 Azure 中的訂閱。

1.  登入 Azure 管理入口網站。

2.  在導覽窗格中，按一下 **[設定]**。

3.  在 **[管理憑證]** 下，按一下 **[上傳管理憑證]**。

4.  在 **[上傳管理憑證]** 中，瀏覽至憑證檔案，然後按一下 **[確定]**。

### 取得憑證的指模和訂閱 ID

您必須要有已新增之管理憑證的指模和訂閱 ID，才能將 .vhd 檔案上傳至 Azure。

1.  在管理入口網站中，按一下 **[設定]**。

2.  在 **[管理憑證]** 下按一下您的憑證，然後從 **[屬性]** 窗格中將指模複製並貼到您後續可擷取的位置，以記錄指模。

要上傳 .vhd 檔案，您也必須要有訂閱 ID。

1.  在管理入口網站中，按一下 **[All Items]**。

2.  在中央窗格的 **[訂閱]** 下，將訂閱複製並貼到您後續可擷取的位置上。

### 如果您已自行產生金鑰，則將此資訊提供給工具

#### 針對 CSUPLOAD

1.  以系統管理員身分開啟 Azure SDK [命令提示字元] 視窗。
2.  使用下列命令，並將 **Subscriptionid** 和 **CertThumbprint** 取代為您先前取得的值，以設定連接字串：

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### 針對 Linux Azure 命令列工具

您必須透過下列命令，對您先前使用 openssl 建立的 PFX 檔案進行 base 64 編碼：

	Base64 [file] \> [econded file]

接著，您必須將訂閱 ID 和 base64 編碼的 pfx 合併為具有下列結構的單一檔案：

     
        xml version="1.0" encoding="utf-8"
        >
        <PublishData>
          <PublishProfile PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>

其中，xxxxx 是您透過下列命令將詳細資料提供給 Linux Azure 命令列工具時所將使用的 [enconded file] 內容：Azure account import (File)

