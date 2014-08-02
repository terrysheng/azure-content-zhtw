<properties linkid="manage-linux-fundamentals-intro-to-linux" urlDisplayName="Intro to Linux" pageTitle="Introduction to Linux in Azure - Azure Tutorial" metaKeywords="Azure Linux vm, Linux vm" description="Learn about using Linux virtual machines on Azure." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="Introduction to Linux on Azure" authors="" solutions="" manager="" editor="" />

Azure 上的 Linux 簡介
=====================

本主題摘要說明在 Azure 雲端中使用 Linux 虛擬機器的相關資訊。使用組件庫中現存的映像來部署 Linux 虛擬機器會很簡單。

目錄
----

-   [驗證：使用者名稱、密碼和 SSH 金鑰。](#authentication)
-   [產生和使用 SSH 金鑰來登入 Linux 虛擬機器。](#keygeneration)
-   [使用 sudo 取得超級使用者權限](#superuserprivileges)
-   [防火牆設定](#firewallconfiguration)
-   [主機名稱變更](#hostnamechanges)
-   [擷取虛擬機器映像](#virtualmachine)
-   [連接磁碟](#attachingdisks)

驗證：使用者名稱、密碼和 SSH 金鑰
---------------------------------

使用 Azure 管理入口網站來建立 Linux 虛擬機器時，系統會要求您提供使用者名稱、密碼和 (選擇性) SSH 公開金鑰。在 Azure 上部署 Linux 虛擬機器時，使用者名稱的選擇有下列限制：不允許使用已存在虛擬機器中的系統帳戶名稱，例如 root。如果不提供 SSH 公開金鑰，您也能夠使用指定的使用者名稱和密碼來登入虛擬機器。如果您在管理入口網站中選擇上傳 SSH 公開金鑰，則會停用密碼型驗證，您將必須使用對應的 SSH 私密金鑰向虛擬機器驗證和登入。

產生 SSH 金鑰
-------------

目前的管理入口網站版本只接受封裝在 X509 憑證中的 SSH 公開金鑰。請依照下列步驟來產生和使用適用於 Azure 的 SSH 金鑰。

1.  使用 openssl 以 2048 位元 RSA 金鑰組來產生 X509 憑證。

         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

    請回答 openssl 提示的幾個問題 (可不填)。平台不會使用這些欄位的內容。

2.  變更私密金鑰的權限以設定安全性。

         chmod 600 myPrivateKey.key

3.  將 myCert.pem 轉換成 myCert.cer (DER 編碼的 X509 憑證)

         openssl  x509 -outform der -in myCert.pem -out myCert.cer

4.  建立 Linux 虛擬機器時上傳 myCert.cer。佈建程序會針對虛擬機器中的指定使用者，自動將此憑證中的公開金鑰安裝至 authorized\_keys 檔案中。

5.  使用 ssh 來連線到 Linux 虛擬機器。

         ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

    第一次登入時，系統會提示您接受主機公用金鑰的指模。

6.  您可以選擇將 myPrivateKey.key 複製到 \~/.ssh/id\_rsa，讓 openssh 用戶端自動挑選此金鑰，而不需要使用 -i 選項。

使用 Sudo 取得超級使用者權限
----------------------------

在 Azure 上部署虛擬機器執行個體時所指定的使用者帳戶是特殊權限帳戶。此帳戶由 Azure Linux 代理程式設定，可使用 sudo 工具將權限提升至 root (超級使用者帳戶)。使用此使用者帳戶登入之後，您將能夠使用 "sudo" 命令來以 root 身分執行命令。您可以選擇使用 **sudo -s** 來取得 root shell。

防火牆設定
----------

Azure 提供輸入封包篩選器，可限制只能連線至管理入口網站中指定的連接埠。預設允許的連接埠只有 SSH。您可以在管理入口網站中加入規則，以開放存取 Linux 虛擬機器上的其他連接埠。

組件庫中的 Linux 映像不會啟用 Linux 虛擬機器內的 iptables 防火牆。如有需要，可設定 IPtables 防火牆來提供其他功能。

主機名稱變更
------------

初次部署 Linux 映像的執行個體時，您必須提供虛擬機器的主機名稱。當虛擬機器執行時，此主機名稱會發佈至平台 DNS 伺服器，讓彼此相連的多個虛擬機器可利用主機名稱來執行 IP 位址查閱。如果在部署虛擬機器之後需要變更主機名稱，請使用 **hostname newname** 命令。Azure Linux 代理程式可自動偵測此名稱變更，並適當地設定虛擬機器來保存此變更，另外還會將此變更發佈至平台 DNS 伺服器。如需此功能的其他詳細資訊和設定選項，請參閱 Azure Linux 代理程式的 README 檔案。

擷取虛擬機器映像
----------------

Azure 可將現有虛擬機器的狀態擷取到映像中，供以後用來部署其他虛擬機器執行個體。Azure Linux 代理程式可用來回復佈建過程中執行的一些自訂。您可以依照下列步驟將虛擬機器擷取為映像：

1.  執行 **waagent -deprovision** 來還原佈建自訂。或執行 **waagent -deprovision+user**，選擇性地刪除佈建期間指定的使用者帳戶及所有相關資料。

2.  使用 [管理入口網站/工具] 來關閉虛擬機器。

3.  按一下管理入口網站中的 [擷取] 或使用 [工具]，將虛擬機器擷取為映像。

連接磁碟
--------

[建立 Linux 虛擬機器](../virtual-machine-from-gallery/)的教學課程中涵蓋這部分的逐步指示。

