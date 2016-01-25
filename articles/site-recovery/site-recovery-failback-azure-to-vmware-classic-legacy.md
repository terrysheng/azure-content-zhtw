<properties 
   pageTitle="將 VMware 虛擬機器和實體伺服器從 Azure 容錯回復到 VMware | Microsoft Azure" 
   description="這篇文章說明如何利用 Azure Site Recovery 容錯回復已複寫至 Azure 的 VMware 虛擬機器。" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="storage-backup-recovery" 
   ms.date="12/14/2015"
   ms.author="ruturajd@microsoft.com"/>

# 利用 Azure Site Recovery 將 VMware 虛擬機器和實體伺服器從 Azure 容錯回復到 VMware (舊版)

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-failback-azure-to-vmware-classic.md)
- [Legacy](site-recovery-failback-azure-to-vmware-classic-legacy.md)


## 概觀

本文件說明如何將 VMware 虛擬機器和 Windows/Linux 實體伺服器從 Azure 容錯回復到內部部署網站。

若要設定這個案例的複寫和容錯移轉，請遵循[這篇文章](site-recovery-vmware-to-azure.md)中的指示。成功利用 Site Recovery 將 VMware 虛擬機器或實體伺服器容錯移轉至 Azure 之後，機器將可在 [Azure 虛擬機器] 索引標籤中使用。

>[AZURE.NOTE]您可以只將 VMware 虛擬機器和 Windows/Linux 實體伺服器從 Azure 容錯移轉到內部部署主要網站中的 VMware 虛擬機器。如果您正在容錯回復實體機器，容錯移轉至 Azure 會將它轉換至 Azure VM，容錯回復到 VMware 會將它轉換至 VMware VM。

這個圖代表容錯移轉和容錯回復案例。藍線是在容錯移轉期間使用的連線。紅線是在容錯回復期間使用的連線。帶有箭號的線條表示會透過網際網路。

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## 步驟 1：安裝 vContinuum 內部部署

您必須在內部部署安裝 vContinuum 伺服器並將它指向組態伺服器。

1.  [下載 vContinuum](http://go.microsoft.com/fwlink/?linkid=526305)。 
2.  下載之後，請下載更新的 [vContinuum 更新](http://go.microsoft.com/fwlink/?LinkID=533813)版本。
3.  執行最新版本的安裝程式以安裝 vContinuum。在 [歡迎] 頁面中，按 [下一步]。![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  在精靈的第一個頁面上，指定 CX 伺服器 IP 位址和 CX 伺服器連接埠。選取 [使用 HTTPS]。

	![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  在 Azure 中組態伺服器 VM 的 [儀表板] 索引標籤上尋找組態伺服器 IP 位址。![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  在 Azure 中組態伺服器 VM 的 [端點] 索引標籤上尋找組態伺服器 HTTPS 公用連接埠。

	![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. 在 [CS 複雜密碼詳細資料] 頁面上，指定您在註冊組態伺服器時記下的複雜密碼。如果您不記得，請在組態伺服器 VM 上的 **C:\\Program Files (x86) \\InMage Systems\\private\\connection.passphrase** 中查看。

	![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  在 [選取目的地位置] 頁面上，指定您要安裝 vContinuum 伺服器的地方，並按一下 [安裝]。

	![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. 安裝完成後，您可以啟動 vContinuum。![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## 步驟 2：在 Azure 中安裝處理序伺服器 

您必須在 Azure 上安裝處理序伺服器，讓 Azure 中的 VM 可以將資料傳回內部部署的主要目標伺服器。

1.  在 Azure 的 [組態伺服器] 頁面中，選取要加入新的處理序伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  指定處程序伺服器名稱，以及要以系統管理員身分連接到虛擬機器的名稱和密碼。選取您要向其註冊處理序伺服器的組態伺服器。這應該是您用來保護並容錯移轉虛擬機器的同一部相伺服器。
3.  指定應在其中部署處理序伺服器的 Azure 網路。它應該是與組態伺服器相同的網路。指定唯一的 IP 位址選取的子網路並開始部署。

	![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  已觸發作業部署處理序伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  在 Azure 中部署處理序伺服器之後，您就能使用指定的認證來登入該伺服器。以您註冊內部部署處理序伺服器的相同方式註冊此處理序伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE]在容錯回復期間註冊的伺服器將不會顯示於 Site Recovery 中的 VM 屬性下方。它們將會只顯示於它們已註冊之組態伺服器的 [伺服器] 索引標籤下方。可能需要大約 10-15 分鐘，直到處理序伺服器出現在索引標籤上。


## 步驟 3：安裝主要目標伺服器內部部署

根據您的來源虛擬機器作業系統而定，您可能需要安裝 Linux 或 Windows 主要目標伺服器內部部署。

### 部署 Windows 主要目標伺服器

Windows 主要目標已經隨附於 vContinuum 安裝程式中。當您安裝 vContinuum 時，主要伺服器也會部署於同一部機器上，並向組態伺服器註冊。

1.  若要開始部署，請在 ESX 主機上建立空的機器內部部署，該主機是您想要從 Auzre 將 VM 復原到其中的主機。

2.  確定至少已將兩個磁碟連接到 VM - 一個用於作業系統，另一個則用於保留磁碟機。

3.  安裝作業系統。

4.  在伺服器上安裝 vContinuum。這也會完成主要目標伺服器的安裝。

### 部署 Linux 主要目標伺服器

1.  若要開始部署，請在 ESX 主機上建立空的機器內部部署，該主機是您想要從 Auzre 將 VM 復原到其中的主機。

2.  確定至少已將兩個磁碟連接到 VM - 一個用於作業系統，另一個則用於保留磁碟機。

3.  安裝 Linux 作業系統。Linux 主要目標系統不應針對根或保留儲存空間使用 LVM。預設會設定 Linux 主要伺服器，以避免 LVM 磁碟分割/磁碟探索。
4.  您可以建立的磁碟分割：

	![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  開始安裝主要伺服器之前，請執行下列的安裝前步驟。


#### 作業系統的安裝前步驟

若要取得 Linux 虛擬機器中每個 SCSI 硬碟的 SCSI 識別碼，請啟用參數 “disk.EnableUUID = TRUE”，如下所示：

1. 關閉虛擬機器。
2. 以滑鼠右鍵按一下左面板中的 VM 項目 > [編輯設定]。
3. 按一下 [選項] 索引標籤。選取 [進階] > [一般項目] > [組態參數]。[組態參數] 選項只有在機器關機時才可以使用。

	![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. 查看含有 **disk.EnableUUID** 的資料列是否已經存在？如果存在且已設定為 **False**，請將它設定為 **True** (不區分大小寫)。如果存在且已設為 True，可按一下 [取消]，然後在其開機之後，於客體作業系統內部測試 SCSI 命令。如果不存在，請按一下 [加入資料列]。
5. 在 [名稱] 欄中加入 disk.EnableUUID。將其值儲存為 TRUE。請勿為上述值加上雙引號。

	![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### 下載並安裝其他封裝

注意：請先確定系統具有網際網路連線，然後再下載並安裝其他封裝。

\# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools

此命令會從 CentOS 6.6 儲存機制下載這 15 個封裝，並加以安裝：

bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm
	
perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm

注意：如果來源機器會針對根目錄或開機裝置使用 Reiser 或 XFS 檔案系統，則下列封裝應該下載並安裝於 Linux 主要目標上，才能提供保護。

\# cd /usr/local

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\# wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm

\# wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### 套用自訂組態變更

套用這些變更之前，請確定您已完成上一節，然後請遵循下列步驟：


1. 將 RHEL 6-64 整合代理程式二進位檔複製到新建立的作業系統。

2. 執行這個命令來解壓縮二進位檔：**tar -zxvf <檔案名稱>**

3. 執行此命令，以授與權限：# **chmod 755 ./ApplyCustomChanges.sh**

4. 執行指令碼：**# ./ApplyCustomChanges.sh**。只需在伺服器上執行指令碼一次。指令碼執行後，請重新啟動伺服器。



### 安裝 Linux 伺服器


1. [下載](http://go.microsoft.com/fwlink/?LinkID=529757)安裝檔案。
2. 使用您選擇的 sftp 用戶端公用程式，將檔案複製到 Linux 主要目標虛擬機器。或者，您可以登入 Linux 主要目標虛擬機器，並使用 wget 從提供的連結下載安裝套件
3. 使用您選擇的 ssh 用戶端，登入 Linux 主要目標伺服器虛擬機器
4. 如果您已連接到 Azure 網路 (您已在其上透過 VPN 連線部署 Linux 主要目標伺服器)，則可使用您在虛擬機器 [儀表板] 索引標籤中發現之伺服器的內部 IP 位址，以及使用連接埠 22 來連接到使用安全殼層的 Linux 主要目標伺服器。
5. 如果您透過公用網際網路連線連接到 Linux 主要目標伺服器，則可使用 Linux 主要目標伺服器的公用虛擬 IP 位址 (從虛擬機器 [儀表板] 索引標籤)，以及針對 ssh 建立的公用端點來登入 Linux 伺服器。
6. 藉由從包含安裝程式檔案的目錄執行：*“tar –xvzf Microsoft-ASR\_UA\_8.2.0.0\_RHEL6-64*”*，從 gzipped Linux 主要目標伺服器安裝程式 tar 封存檔解壓縮檔案。

	![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. 如果您將安裝程式檔案解壓縮到不同的目錄，請變更為要解壓縮 tar 封存內容的目錄。從這個目錄路徑執行 “sudo ./install.sh”。

	![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. 當系統提示您選擇主要角色時，選取 [2 (主要目標)]。保留其他互動式安裝選項的預設值。
9. 請等待安裝繼續進行以及主機組態介面出現。適用於 Linux 主要目標伺服器的主機組態公用程式是一個命令列公用程式。請勿調整 ssh 用戶端公用程式視窗的大小。使用方向鍵來選取 [全域] 選項，然後按鍵盤上的 ENTER 鍵。

	![](./media/site-recovery-failback-azure-to-vmware/image18.png)

	![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. 在 [IP] 欄位中，輸入組態伺服器的內部 IP 位址 (您可以在組態伺服器 VM 的 [儀表板] 索引標籤中找到它) 然後按 ENTER 鍵。在 [連接埠] 中，輸入 **22**，然後按 ENTER 鍵。
11.  保留 [使用 HTTPS] 為 [是]，然後按 ENTER 鍵。
12.  輸入組態伺服器上產生的複雜密碼。如果您從 Windows 機器使用 PUTTY 用戶端 ssh 到 Linux 虛擬機器，您可以使用 Shift+Insert 鍵來貼上剪貼簿的內容。使用 Ctrl+C 鍵，將複雜密碼複製到本機剪貼簿，然後使用 Shift+Insert 鍵來貼上它。按 ENTER 鍵。
13.  使用向右鍵瀏覽到結束並按 ENTER 鍵。等待安裝完成。

	![](./media/site-recovery-failback-azure-to-vmware/image20.png)

如果您基於某些原因而無法向 Linux 主要目標伺服器註冊您的組態伺服器，則您應該從 /usr/local/ASR/Vx/bin/hostconfigcli 執行主機組態公用程式，再次執行這個動作 (您必須先以進階使用者身分執行 chmod，來設定此目錄的存取權限)。


#### 驗證主要目標註冊

您可以驗證主要目標伺服器已成功向 Azure Site Recovery 保存庫 > [組態伺服器] > [伺服器詳細資料] 中的組態伺服器註冊。

>[AZURE.NOTE]在註冊主要目標伺服器之後，如果您收到虛擬機器可能已從 Azure 刪除，或未正確設定端點等錯誤，這是因為在 Azure 中部署主要目標時，雖然 Azure 端點偵測到主要目標組態，但是內部部署主要目標伺服器內部部署卻不是這個情形。這並不會影響容錯回復，您可以忽略這些錯誤。



## 步驟 4：保護虛擬機器至內部部署網站

您要保護 VM 至內部部署網站，然後再容錯回復。

### 開始之前

將 VM 容錯移轉到 Azure 時，它會新增額外的暫存磁碟機以供分頁檔使用。這通常是您已容錯移轉的 VM 不需要的額外磁碟機，因為它可能已經有分頁檔專用的磁碟機。在開始反向保護虛擬機器之前，您需要確定磁碟機已離線，使它不會受到保護。以下列方式來執行此動作：

1.  開啟 [電腦管理] 並選取 [儲存管理]，即會列出磁碟已上線且已連接到機器。
2.  選取連接到機器的暫存磁碟，並選擇使其離線。 

### 保護 VM

1. 在 Azure 入口網站中，查看虛擬機器的狀態，並確定它們已容錯移轉。

	![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. 啟動機器上的 vContinuum。

	![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. 按一下 [新增保護] 並選取作業系統類型，這個

4.  在開啟的新視窗中，選取 [OS 類型] > [取得詳細資料] 以取得您想要容錯回復的 VM。在 [主要伺服器詳細資料] 中，識別並選取您想要保護的虛擬機器。在容錯移轉之前，VM 會列在其隸屬的主機名稱之下。
5.  當您選取要保護的虛擬機器 (而且已經將它容錯移轉到 Azure) 時，快顯視窗會提供兩個適用於虛擬機器的項目。這是因為組態伺服器偵測到兩個已向其註冊的虛擬機器執行個體。您需要移除內部部署 VM 的項目，如此就能保護正確的 VM。若要在此處識別正確的 Azure VM 項目，您可以登入 Azure VM，然後移至 C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\Application Data\\etc。在 drscout.conf 檔案中，識別主機識別碼。在 vContinuum 對話方塊中，保留可在 VM 上找到主機識別碼的項目。刪除所有其他項目。若要選取正確的 VM，您可以參考其 IP 位址。IP 位址範圍內部部署將會在內部部署 VM 上。

	![](./media/site-recovery-failback-azure-to-vmware/image22.png)

	![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. 在 vCenter 伺服器上，停止虛擬機器。您也可以刪除 VM 內部部署。
7. 接下來，指定您要保護 VM 的內部部署 MT 伺服器。若要這樣做，請連接到您想要容錯回復的目標 vCenter 伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. 根據您要復原 VM 的目標主機來選取主要目標伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. 提供每個虛擬機器的複寫選項。若要這樣做，您需要選取要將 VM 還原到其中的復原端資料存放區。下表摘要說明您必須提供給每個 VM 的不同選項。

	![](./media/site-recovery-failback-azure-to-vmware/image25.png)

	**選項** | **選項建議值**
	---|---
	處理序伺服器 IP 位址 | 選取部署在 Azure 中的處理序伺服器
	保留大小 (以 MB 為單位)| 
	保留值 | 1
	天/小時 | 星期幾
	一致性間隔 | 1
	選取目標資料存放區 | 可在復原端使用的資料存放區。這個資料存放區應具備足夠空間，而且可供您要在其上還原虛擬機器的 ESX 主機使用。


10. 設定虛擬機器將在容錯移轉到內部部署站台之後取得的屬性。下表中會摘要說明這些屬性。

	![](./media/site-recovery-failback-azure-to-vmware/image26.png)

	**屬性** | **詳細資料**
	---|---
	網路組態| 針對偵測到的每個網路介面卡，請選取它，然後按一下 [變更] 來設定虛擬機器的容錯回復 IP 位址。 
	硬體組態| 指定 VM 的 CPU 和記憶體。這些設定可套用到您嘗試保護的所有 VM。若要識別 CPU 和記憶體的正確值，您可以參考 IAAS VM 角色大小，並查看核心數及指派的記憶體。
	顯示名稱 | 容錯回復到內部部署之後，您可以在虛擬機器出現在 vCenter 詳細目錄中時將其重新命名。預設名稱是虛擬機器的電腦主機名稱。若要識別 VM 名稱，您可以參考保護群組中的 VM 清單。
	NAT 組態 | 以下將詳細討論

	![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### 設定 NAT 原則

1. 若要啟用虛擬機器的保護，需要建立兩個通訊通道。第一個通道位於虛擬機器和處理序伺服器之間。此通道會從 VM 收集資料，並將其傳送至處理序伺服器，該伺服器會再將資料傳送給主要目標伺服器。如果處理序伺服器和受保護的虛擬機器位於相同的 Azure 虛擬網路上，則您不需使用 NAT 設定。否則，請指定 NAT 設定。在 Azure 中檢視處理序伺服器的公用 IP 位址。 

	![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. 第二個通道介於處理序伺服器和主要目標伺服器之間。是否使用 NAT 的選項取決於您是使用以連線為基礎的 VPN，還是透過網際網路通訊。如果您使用 VPN，請不要選取 NAt，只有當您使用網際網路連線時才選取。

	![](./media/site-recovery-failback-azure-to-vmware/image29.png)

	![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. 如果您尚未刪除指定的內部部署虛擬機器，而且如果您要容錯回復的目標資料存放區仍包含舊的 VMDK，則您必須確定容錯回復的 VM 會建立於新的位置上。若要這樣做，請選取 [進階] 設定，並且在 [資料夾名稱設定] 中指定要還原到其中的替代資料夾。保留其他選項的預設設定。將資料夾名稱設定套用到所有伺服器。

	![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. 執行整備檢查，以確保虛擬機器已準備好回復到內部部署接受保護。

	![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. 等候它完成。如果已順利位於所有 VM 上，您可以指定保護方案的名稱。然後按一下 [保護] 以開始進行。

	![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. 您可以在 vContinuum 中監視進度。

	![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. 在 [啟用保護計劃] 步驟完成之後，您可以監視 Site Recovery 入口網站中的 VM 保護。

	![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. 您可以按一下 VM 並監視其進度以查看確切狀態。

	![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## 準備容錯回復方案

您可以使用 vContinuum 來準備容錯回復方案，讓應用程式隨時都能容錯回復到內部部署網站。這些復原方案非常類似 Site Recovery 中的復原方案。

1.  啟動 vContinuum，然後選取 [管理方案] > [復原]。 您可以看到所有已用來容錯移轉 VM 的方案清單。您可以使用相同的方案來復原。

	![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. 選取保護方案，以及您想要在其中復原的所有 VM。當您選取每個 VM 時，您可以看到更多詳細資料，包括目標 ESX 伺服器與來源 VM 磁碟。按 [下一步] 以開始 [復原精靈]，然後選取您想要復原的 VM。

	![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. 您可以根據多個選項進行復原，但建議您使用 **最新的標記**，然後選取 [套用所有 VM] 以確保使用虛擬機器的最新資料。
4. 執行 [整備檢查]。 這會檢查是否設為啟用 VM 復原的正確參數。如果所有檢查都成功，請按 [下一步]。如果未成功，請檢查記錄檔並解決錯誤。

	![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  在 [VM 組態] 中，確定已正確設定復原設定。您可以在必要時變更 VM 設定。

	![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. 檢閱將復原的虛擬機器清單並指定復原順序。請注意，VM 會使用電腦主機名稱列出。可能很難將電腦主機名稱對應到虛擬機器。若要對應名稱，請移至 Azure 中的虛擬機器 [儀表板] 並檢查 VM 主機名稱。

	![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. 指定方案名稱，然後選取 [稍後復原]。建議稍後復原，因為初始保護可能不完整。
11. 如果您選取立即復原而不是稍後復原，請按一下 [復原] 來儲存方案或觸發復原。您可以檢查復原狀態以查看是否已儲存方案。

	![](./media/site-recovery-failback-azure-to-vmware/image42.png)

	![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## 復原虛擬機器

建立方案之後，您可以復原虛擬機器。在您復原之前，請檢查虛擬機器是否已完成同步處理。如果複寫狀態顯示正常，則表示保護已完成且已符合 RPO 閾值。您可以確認 VM 屬性中的健全狀況。

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

起始復原之前，請關閉 Azure 虛擬機器。這可確保不會產生核心分裂，而且使用者將只能存取應用程式的複本。


1.  啟動儲存的方案。在 vContinuum 中，選取 [監視] 方案。這樣會列出所有已執行的方案。

	![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  在 [復原] 中選取方案並按一下 [啟動]。您可以監視復原。開啟 VM 之後，您可以在 vCenter 中與它們連接。

	![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## 在容錯回復後重新保護至 Azure

完成容錯回復之後，您可能會想要再次保護虛擬機器。以下列方式來執行此動作：

1.  檢查虛擬機器內部部署正在運作，而且應用程式能夠聯繫。
2.  在 Azure Site Recovery 入口網站中，選取虛擬機器，並將它們刪除。選取停用虛擬機器的保護。如此可確保 VM 沒有其他保護。
3.  在 Azure 中，刪除已容錯移轉的 Azure 虛擬機器
4.  刪除 vSpehere 上的舊虛擬機器。這些是您先前容錯移轉到 Azure 的 VM。
5.  在 Site Recovery 入口網站中，保護最近容錯移轉的 VM。在它們受保護之後，您可以將它們加入復原方案。
 
## 後續步驟

[深入了解](site-recovery-vmware-to-azure-classic-legacy.md)將 VMWare 虛擬機器複寫至 Azure

 

<!---HONumber=AcomDC_0114_2016--->