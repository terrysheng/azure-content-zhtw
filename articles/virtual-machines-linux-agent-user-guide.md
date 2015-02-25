<properties pageTitle="適用於 Azure 的 Linux 代理程式使用者指南" description="了解如何安裝和設定 Linux 代理程式 (waagent)，來管理虛擬機器與 Azure 網狀架構控制器之間的互動。" services="virtual-machines" documentationCenter="" authors="szarkos" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="szarkos"/>





#Azure Linux 代理程式使用者指南

##簡介

Azure Linux 代理程式 (waagent) 可管理虛擬機器與 Azure 網狀架構控制器之間的互動。它具有下列功能：

* **映像檔佈建**
  - 建立使用者帳戶
  - 設定 SSH 驗證類型
  - 部署 SSH 公開金鑰和金鑰組
  - 設定主機名稱
  - 將主機名稱發佈至平台 DNS
  - 將 SSH 主機金鑰和指紋報告給平台
  - 管理資源磁碟
  - 格式化和掛接資源磁碟
  - 設定交換空間
* **網路**
  - 管理路由以提高平台 DHCP 伺服器的相容性
  - 確保網路介面名稱的穩定性
* **核心**
  - 設定虛擬 NUMA
  - 取用 /dev/random 的 Hyper-V Entropy
  - 設定根裝置 (可能在遠端) 的 SCSI 逾時
* **診斷**
  - 將主控台重新導向至序列埠
* **SCVMM 部署**
    - 在 System Center Virtual Machine Manager 2012 R2 環境中執行時偵測和啟動 Linux 的 VMM 代理程式

資訊經由兩個管道從平台流向代理程式：

* 在 IaaS 部署中，開機時連接的 DVD。此 DVD 包含 OVF 相容組態檔，內含實際 SSH 金鑰組以外的所有佈建資訊。

* TCP 端點，公開可用來取得部署和拓撲組態的 REST API。

###取得 Linux 代理程式
您可以直接從下列位置取得最新版的 Linux 代理程式：

- [在 Azure 上支援 Linux 的不同散發提供者](http://support.microsoft.com/kb/2805216)
- 或從[適用於 Azure Linux 代理程式的 Github 開放原始碼儲存機制](https://github.com/WindowsAzure/WALinuxAgent)


###支援的 Linux 散發套件
* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

其他支援的系統：

* FreeBSD 9+ (WALinuxAgent v2.0.0+)


###需求

Waagent 需要一些系統封裝才能正確運作：

* Python 2.5+
* Openssl 1.0+
* Openssh 5.3+
* 檔案系統公用程式：sfdisk、fdisk、mkfs
* 密碼工具：chpasswd、sudo
* 文字處理工具：sed、grep
* 網路工具：ip-route

##安裝

安裝和升級 Windows Azure Linux Azure 時，建議使用散發的封裝儲存機制所提供的 RPM 或 DEB 封裝來安裝。

如果是手動安裝，請將 waagent 複製到 /usr/sbin/waagent，然後執行下列命令來安裝： 

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

代理程式的記錄檔保存在 /var/log/waagent.log。


##命令列選項

###旗標

- verbose:提高指定命令的詳細程度
- force：略過某些命令的互動式確認

###命令

- help:列出支援的命令和旗標。

- install：手動安裝代理程式
 * 檢查系統的必要相依性

 * 建立 SysV init 指令碼 (/etc/init.d/waagent)、logrotate 組態檔 (/etc/logrotate.d/waagent) 及設定映像檔在開機時執行 init 指令碼

 * 將範例組態檔寫入 /etc/waagent.conf

 * 任何現有的組態檔會移至 /etc/waagent.conf.old

 * 偵測核心版本，必要的話套用 VNUMA 解決方案

 * 將可能干擾網路 (/lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) 的 udev 規則移動至 /var/lib/waagent/  

- 解除安裝：移除 waagent 和相關聯的檔案
 * 從系統中取消註冊 init 指令碼並刪除它

 * 在 /etc/waagent.conf 在刪除 logrotate 設定和 waagent 組態檔

 * 還原安裝期間移動的任何 udev 規則

 * 不支援自動還原 VNUMA 解決方案，必要的話，請手動編輯 GRUB 組態檔來重新啟用 NUMA。

- deprovision：嘗試清理系統，使之適合重新佈建。此作業會刪除下列項目：
 * 所有 SSH 主機金鑰 (如果組態檔中的 Provisioning.RegenerateSshHostKeyPair 是 'y')

 * /etc/resolv.conf 中的名稱伺服器設定

 * /etc/shadow 中的根密碼 (如果組態檔中的 Provisioning.DeleteRootPassword 是 'y')

 * 快取的 DHCP 用戶端租用

 * 將主機名稱重設為 localhost.localdomain

 **警告：**取消佈建不能保證映像檔中的所有機密資訊都清除完畢而適合再度散發。

- deprovision+user：執行 -deprovision (上述) 下的一切動作，並刪除最後佈建的使用者帳戶 (取自於 /var/lib/waagent) 和相關聯的資料。此參數是為了取消佈建 Azure 上先前佈建的映像檔，以便擷取並重複使用。

- version：顯示 waagent 的版本

- serialconsole：設定 GRUB 將 ttyS0 (第一個序列埠) 標示為
   開機主控台。這樣可以確保核心開機記錄會傳送至
   序列埠，並可供偵錯之用。

- daemon：以精靈方式執行 waagent 來管理與平台之間的互動。
   此引數是在 waagent init 指令碼中指定給 waagent。

##組態

組態檔 (/etc/waagent.conf) 控制 waagent 的動作。 
範例組態檔如下所示：
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

以下詳細說明各種組態選項。 
組態選項分成三種：布林、字串或整數。 
布林組態選項可指定為 "y" 或 "n"。 
特殊關鍵字 "None" 可用於某些字串類型組態項目，詳述如下。

**Role.StateConsumer：**

輸入：String  
預設：None

如果指定可執行程式的路徑，則當 waagent 已佈建映像檔，且即將向網狀架構報告 "Ready" 狀態時，就會叫用此程式。指定給程式的引數為 "Ready"。代理程式會繼續執行而不會等待程式返回。

**Role.ConfigurationConsumer：**

輸入：String  
預設：None

如果指定可執行程式的路徑，則當網狀架構指出虛擬機器有可用的組態檔時，就會叫用此程式。XML 組態檔的路徑當做引數提供給可執行檔。只要組態檔有變動，就會叫用程式，因此可能叫用多次。附錄中提供一個範例檔。此檔案目前的路徑為 /var/lib/waagent/HostingEnvironmentConfig.xml。

**Role.TopologyConsumer：**

輸入：String  
預設：None

如果指定可執行程式的路徑，則當網狀架構指出有新的網路拓撲配置可用於虛擬機器時，就會叫用此程式。XML 組態檔的路徑當做引數提供給可執行檔。只要網路拓撲有變動 (例如由於維修)，就會叫用程式，因此可能叫用多次。附錄中提供一個範例檔。此檔案目前的位置為 /var/lib/waagent/SharedConfig.xml。

**Provisioning.Enabled：**

輸入：Boolean  
預設：y

這可讓使用者啟用或停用代理程式的佈建功能。有效值為 "y" 或 "n"。如果停用佈建，則會保留映像檔中的 SSH 主機金鑰和使用者金鑰，並忽略 Azure 佈建 API 中指定的任何組態。

**Provisioning.DeleteRootPassword：**

輸入：Boolean  
預設：n

如果設定，則佈建過程中會清除 /etc/shadow 檔案中的根密碼。

**Provisioning.RegenerateSshHostKeyPair：**

輸入：Boolean  
預設：y

如果設定，則佈建過程會從 /etc/ssh/ 中刪除所有 SSH 主機金鑰組 (ecdsa、dsa 和 rsa)。還會產生單一全新的金鑰組。

全新金鑰組的加密類型可由 Provisioning.SshHostKeyPairType 項目來設定。請注意，重新啟動 SSH 精靈時 (例如重新開機時)，某些散發套件會針對任何遺失的加密類型而重建 SSH 金鑰組。

**Provisioning.SshHostKeyPairType：**

輸入：String  
預設：rsa

這可設為虛擬機器上的 SSH 精靈所支援的加密演算法類型。支援的值通常為 "rsa"、"dsa" 和 "ecdsa"。請注意，Windows 的 "putty.exe" 不支援 "ecdsa"。因此，如果想要在 Windows 上使用 putty.exe 來連線至 Linux 部署，請使用 "rsa" 或 "dsa"。

**Provisioning.MonitorHostName：**

輸入：Boolean  
預設：y

如果設定，waagent 會監視 Linux 虛擬機器的主機名稱有無變動 (由 "hostname" 命令傳回)，並自動更新映像檔中的網路組態來反映此變動。為了將名稱變更發送至 DNS 伺服器，虛擬機器中將重新啟動網路功能。這會導致網際網路連線短暫中斷。

**ResourceDisk.Format：**

輸入：Boolean  
預設：y

如果設定，如果使用者在 "ResourceDisk.Filesystem" 中要求的檔案系統類型不是 "ntfs"，則 waagent 會格式化並掛接平台所提供的資源磁碟。磁碟上將會有 Linux (83) 類型的單一磁碟分割。請注意，如果可順利掛接此磁碟分割，則不會格式化。

**ResourceDisk.Filesystem：**

輸入：String  
預設：ext4

這指定資源磁碟的檔案系統類型。支援的值隨 Linux 散發套件而不同。如果字串為 X，則 Linux 映像檔上應該會出現 mkfs.X。SLES 11 映像檔通常會使用 'ext3'。在此，FreeBSD 映像檔應該是使用 'ufs2'。

**ResourceDisk.MountPoint：**

輸入：String  
預設：/mnt/resource 

這指定資源磁碟的掛接路徑。請注意，資源磁碟是  *temporary* 磁碟，可能會在 VM 取消佈建時清空。

**ResourceDisk.EnableSwap：**

輸入：Boolean  
預設：n 

如果設定，則會在資源磁碟上建立交換檔 (/swapfile) 並加入至系統交換空間。

**ResourceDisk.SwapSizeMB：**

輸入：Integer  
預設：0

交換檔的大小 (MB)。

**LBProbeResponder：**

輸入：Boolean  
預設：y

如果設定，waagent 會回應來自平台的負載平衡器探查 (若有的話)。

**Logs.Verbose：**

輸入：Boolean  
預設：n

如果設定，則記錄詳細程度會提高。Waagent 會記錄到 /var/log/waagent.log，並利用系統 logrotate 功能來輪換記錄檔。

**OS.RootDeviceScsiTimeout：**

輸入：Integer  
預設：300

這會在 OS 磁碟和資料磁碟機上設定 SCSI 逾時 (以秒為單位)。如果未設定，則會使用系統預設值。

**OS.OpensslPath：**

輸入：String  
預設：None

這可用來指定加密作業使用的 openssl 二進位檔的替代路徑。

##附錄

###範例角色組態檔

	<?xml version="1.0" encoding="utf-8"?>
	<HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
	  <StoredCertificates>
	    <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
	  </StoredCertificates>
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
	  <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
	    <CAS mode="full" />
	    <PrivilegeLevel mode="max" />
	    <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
	    <ApplicationSettings>
	      <Setting name="__ModelData" value="&lt;m role=&quot;LinuxVM&quot; xmlns=&quot;urn:azure:m:v1&quot;>&lt;r name=&quot;LinuxVM&quot;>&lt;e name=&quot;HTTP&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput&quot; />&lt;e name=&quot;SSH&quot; />&lt;/r>&lt;/m>" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
	      <Setting name="startpage" value="Hello World!" />
	      <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
	    </ApplicationSettings>
	    <ResourceReferences>
	      <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore\" disableQuota="false" />
	    </ResourceReferences>
	  </HostingEnvironmentConfig>

###範例角色拓撲檔

	<?xml version="1.0" encoding="utf-8"?>
	<SharedConfig version="1.0.0.0" goalStateIncarnation="2">
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
	  <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
	    <Probes>
	      <Probe name="LinuxVM" />
	      <Probe name="03F7F19398C4358108B7ED059966EEBD" />
	      <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
	    </Probes>
	  </LoadBalancerSettings>
	  <OutputEndpoints>
	    <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
	      <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	    </Endpoint>
	  </OutputEndpoints>
	  <Instances>
	    <Instance id="LinuxVM_IN_1" address="10.115.38.202">
	      <FaultDomains randomId="1" updateId="1" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="80" to="80" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	          <RemoteInstances>
	            <RemoteInstance instance="LinuxVM_IN_0" />
	            <RemoteInstance instance="LinuxVM_IN_2" />
	          </RemoteInstances>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="20000" to="20000" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="22" to="22" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_0" address="10.115.58.82">
	      <FaultDomains randomId="0" updateId="0" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_2" address="10.115.58.148">
	      <FaultDomains randomId="0" updateId="2" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	  </Instances>
	</SharedConfig>


<!--HONumber=42-->
