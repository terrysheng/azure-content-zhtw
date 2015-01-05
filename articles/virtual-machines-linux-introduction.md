<properties urlDisplayName="Intro to Linux" pageTitle="Azure 上的 Linux 簡介 - Azure 教學課程" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="09/13/2014" ms.author="szark" />





#Azure 上的 Linux 簡介

本主題摘要說明在 Azure 雲端中使用 Linux 虛擬機器的相關資訊。使用組件庫中現存的映像來部署 Linux 虛擬機器會很簡單。 

## 目錄 ##

* [驗證：使用者名稱、密碼和 SSH 金鑰。](#authentication)
* [產生和使用 SSH 金鑰來登入 Linux 虛擬機器。](#keygeneration)
* [使用 sudo 取得超級使用者權限](#superuserprivileges)
* [防火牆設定](#firewallconfiguration)
* [主機名稱變更](#hostnamechanges)
* [擷取虛擬機器映像](#virtualmachine)
* [連接磁碟](#attachingdisks)

## <a id="authentication"></a>驗證：使用者名稱、密碼和 SSH 金鑰

使用 Azure 管理入口網站來建立 Linux 虛擬機器時，系統會要求您提供使用者名稱、密碼和 (選擇性) SSH 公開金鑰。在 Azure 上部署 Linux 虛擬機器的使用者名稱選擇受到下列限制：不允許使用已存在於虛擬機器中的系統帳戶名稱 (UID <100)，例如 'root'。

 - 請參閱[如何對 Azure 上的 Linux 使用 SSH](../linux-use-ssh-key/)


### <a id="keygeneration"></a>產生 SSH 金鑰

目前的管理入口網站版本只接受封裝在 X509 憑證中的 SSH 公開金鑰。請依照下列步驟來產生和使用適用於 Azure 的 SSH 金鑰。

1. 使用 openssl 以 2048 位元 RSA 金鑰組來產生 X509 憑證。
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	請回答 openssl 提示的幾個問題 (可不填)。平台不會使用這些欄位的內容。

2. 變更私密金鑰的權限來保護私密金鑰。

		chmod 600 myPrivateKey.key

3. 將 'myCert.pem' 轉換成 'myCert.cer' (DER 編碼的 X509 憑證)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. 建立 Linux 虛擬機器時上傳 'myCert.cer'。佈建程序會針對虛擬機器中的指定使用者，自動將此憑證中的公開金鑰安裝至 `~/.ssh/authorized_keys` 檔案中。

5. 使用 ssh 來連線到 Linux 虛擬機器。

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	第一次登入時，將提示您接受主機公開金鑰的指紋。

6. 您可以選擇將 `myPrivateKey.key` 複製到 `~/.ssh/id_rsa`，讓 openssh 用戶端自動挑選此金鑰，而不需要使用 -i 選項。
   或者，您可以修改 `~/.ssh/config` 以包含虛擬機器的區段：

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### 從現有 OpenSSH 相容金鑰產生金鑰
上一個範例說明如何建立用於 Windows Azure 的新金鑰。在某些情況下，使用者可能已經有現有的 OpenSSH 相容公用與私密金鑰組，而希望對 Windows Azure 使用相同的金鑰。

'openssl' 公用程式可以直接讀取 OpenSSH 私密金鑰。下列命令將使用現有的 SSH 私密金鑰 (在下例中為 id_rsa) 建立 Windows Azure 需要的 '.pem' 公開金鑰：

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** 檔案是之後可用來在 Windows Azure 上佈建 Linux 虛擬機器的公開金鑰。在佈建期間，'.pem' 檔案將轉譯為 'openssh' 相容的公開金鑰，並放在 `~/.ssh/authorized_keys` 中。


## <a id="superuserprivileges"></a>使用 'sudo' 取得超級使用者權限

在 Azure 上部署虛擬機器執行個體時所指定的使用者帳戶是特殊權限帳戶。此帳戶由 Azure Linux 代理程式設定，可使用 `sudo` 公用程式將權限提升至 root (超級使用者帳戶)。使用此使用者帳戶登入之後，您將能夠以 root 的身分使用命令語法來執行命令。

	# sudo <COMMAND>

您可以選擇使用 **sudo -s** 來取得 root shell。

- 請參閱[在 Azure 中的 Linux 虛擬機器上使用根權限](../virtual-machines-linux-use-root-privileges/)


## <a id="firewallconfiguration"></a>防火牆設定

Azure 提供輸入封包篩選器，可限制只能連線至管理入口網站中指定的連接埠。預設允許的連接埠只有 SSH。您可以在管理入口網站中設定端點，以開放存取 Linux 虛擬機器上的其他連接埠：

 - 請參閱：[如何設定虛擬機器的端點](../virtual-machines-set-up-endpoints/)

Azure 映像庫中的 Linux 映像依預設不會啟用 *iptables* 防火牆。如有需要，可設定防火牆來提供其他篩選。


## <a id="hostnamechanges"></a>主機名稱變更

初次部署 Linux 映像的執行個體時，您必須提供虛擬機器的主機名稱。當虛擬機器執行時，此主機名稱會發佈至平台 DNS 伺服器，讓彼此相連的多個虛擬機器可利用主機名稱來執行 IP 位址查閱。

如果在部署虛擬機器之後需要變更主機名稱，請使用命令

	# sudo hostname <newname>

Azure Linux 代理程式包括可自動偵測此名稱變更、適當地設定虛擬機器來保存此變更，以及將此變更發佈至平台 DNS 伺服器等功能。

 - [Azure Linux 代理程式使用者指南](../virtual-machines-linux-agent-user-guide/)

### Ubuntu 映像
Ubuntu 映像會利用可提供啟動載入虛擬機器其他功能的 cloud-init。

 - 請參閱 [Microsoft Azure 上的自訂資料和 Cloud-Init](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)


## <a id="virtualmachine"></a>擷取虛擬機器映像

Azure 可將現有虛擬機器的狀態擷取到映像中，供以後用來部署其他虛擬機器執行個體。Azure Linux 代理程式可用來回復佈建過程中執行的一些自訂。您可以依照下列步驟將虛擬機器擷取為映像：

1. 執行 **waagent -deprovision** 來還原佈建自訂。或執行 **waagent -deprovision+user** ，選擇性地刪除佈建期間指定的使用者帳戶及所有相關資料。

2. 關閉虛擬機器。

3. 按一下管理入口網站中的 [擷取]，或使用 Powershell 或 CLI 工具，將虛擬機器擷取為映像。

 - 請參閱：[如何擷取 Linux 虛擬機器作為範本使用](../virtual-machines-linux-capture-image/)


## <a id="attachingdisks"></a>連接磁碟

每個虛擬機器都會連接一個暫時性的本機資源磁碟。因為資源磁碟上的資料在重新開機之後就會消失，通常供虛擬機器中執行的應用程式和處理程序**暫時**儲存資料。也用來儲存作業系統的分頁檔或交換檔。

在 Linux 上，資源磁碟通常由 Azure Linux 代理程式管理，並自動掛接到 **/mnt/resource** (或 Ubuntu 映像上的 **/mnt**)。

	>[WACOM.NOTE] 請注意，資源磁碟是**暫存**磁碟，可能會在 VM 重新開機時遭到刪除及重新格式化。

在 Linux 上，核心有可能將資料磁碟命名為 `/dev/sdc`，且使用者必須分割、格式化及掛接該資源。本教學課程中將涵蓋這部分的逐步說明：[如何將資料磁碟附加至虛擬機器](../virtual-machines-linux-how-to-attach-disk/)。

 - 另請參閱：[在 Linux 上設定軟體 RAID](../virtual-machines-linux-configure-raid/)


<!--HONumber=35.1-->
