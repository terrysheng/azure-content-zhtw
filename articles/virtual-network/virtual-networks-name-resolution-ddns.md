<properties 
   pageTitle="使用動態 DNS 來登錄主機名稱"
   description="此頁面會提供如何設定動態 DNS，以便在您的 DNS 伺服器中登錄主機名稱的部分詳細資料。"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# 使用動態 DNS 來登錄主機名稱
[Azure 會為 VM 及角色執行個體提供名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)。當您的名稱解析需求超過 Azure 所提供的名稱解析時，您可以提供自己的 DNS 伺服器。這讓您有能力量身打造自己的 DNS 方案，來符合您特定的需求。舉例來說，存取您的內部部署 AD 網域控制站。

Azure 沒有能力 (例如認證) 直接在您的 DNS 伺服器中登錄記錄，因此通常需要有其他的安排。以下提供某些較常見案例的部分高層級詳細資料。

## Windows 用戶端 ##
未加入網域的 Windows 用戶端在開機或在 IP 位址改變時，會嘗試進行不安全的 DDNS 更新。DNS 名稱就是主機名稱加上主要 DNS 尾碼。Azure 會讓主要 DNS 尾碼留白，但您可以在 VM 中透過 [UI](https://technet.microsoft.com/library/cc794784.aspx) 或 [自動化](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix)覆寫這個設定。

已加入網域的 Windows 用戶端會使用安全的 DDNS，向網域控制站登錄自己的 IP 位址。網域加入程序會設定用戶端上的主要 DNS 尾碼，以及管理信任關係。

## Linux 用戶端 ##
Linux 用戶端通常不會在啟動時向 DNS 伺服器登錄自己，它們假設 DHCP 伺服器會這麼做。繫結封裝有個稱為 *nsupdate* 的工具，可以用來傳送 DDNS 更新。由於 DDNS 通訊協定是標準化的，即使在沒有使用繫結時，*nsupdate*也能用在 DNS 伺服器上。

Linux DHCP 用戶端提供勾點，讓您可以在 IP 位址已指派或變更時執行指令碼。例如，在 */etc/dhcp/dhclient-exit-hooks.d/* 中。這方法可用來在 DNS 中登錄主機名稱。例如：
    
    	#!/bin/sh
    	requireddomain=mydomain.local
    
    	# only execute on the primary nic
    	if [ "$interface" != "eth0" ]
    	then
    		return
    	fi
    
		# when we have a new IP, perform nsupdate
		if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] || 
		   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
		then
    		host=`hostname`
	    	nsupdatecmds=/var/tmp/nsupdatecmds
  			echo "update delete $host.$requireddomain a" > $nsupdatecmds
  			echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
  			echo "send" >> $nsupdatecmds

  			nsupdate $nsupdatecmds
		fi

		#done
		exit 0;

*Nsupdate* 命令也可以執行安全的 DDNS 更新。舉例來說，當您使用繫結 DNS 伺服器時，公開/私密金鑰組會[產生](http://linux.yyz.us/nsupdate/)，且會利用金鑰的公開部分來[設定](http://linux.yyz.us/dns/ddns-server.html) DNS 伺服器，讓它能夠驗證要求的簽章。如要簽署 DDNS 更新要求，您必須利用 *nsupdate* 的 *-k* 選項來提供金鑰組給 nsupdate。

當您使用 Windows DNS 伺服器時，Kerberos 驗證可以搭配 nsupdate 的 *-g* 開關 (*nsupdate* 的 Windows 版本沒有提供) 來使用 。如要使用這個方法，請使用 *kinit* 來載入認證 (例如從 [keytab 檔案](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)載入)，然後 *nsupdate -g* 將會從快取得到認證。

如有需要，DNS 搜尋尾碼可以加入您的 VM。DNS 尾碼是在 */etc/resolv.conf* 檔案中指定。大多數的 Linux 發行版會自動管理這個檔案的內容，因此該檔案通常是無法編輯的。然而，尾碼是可以覆寫的，方法是使用 DHCP 用戶端的 *supersede* 命令，也就是在 */etc/dhcp/dhclient.conf* 中加入：

		supersede domain-name <required-dns-suffix>;

<!---HONumber=AcomDC_0204_2016-->