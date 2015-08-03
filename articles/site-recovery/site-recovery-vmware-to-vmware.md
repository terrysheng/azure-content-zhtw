<properties 
	pageTitle="設定內部部署 VMWare 網站之間的保護" 
	description="使用這份文件，使用 Azure Site Recovery 設定兩個兩個 VMware 網站之間的保護。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="raynew"/>


# 設定內部部署 VMware 網站之間的保護


## 概觀

Azure Site Recovery 中的 InMage Scout 可提供內部部署 VMware 網站之間的即時複寫。InMage Scout 包含在 Azure Site Recovery 服務訂用帳戶中。


## 必要條件

- **Azure 帳戶** — 您將需要一個 [Microsoft Azure](http://azure.microsoft.com/) 帳戶。您可以從[免費試用](pricing/free-trial/)開始。


## 步驟 1：建立保存庫

1. 登入[管理入口網站](https://portal.azure.com)。
2. 按一下 [資料服務] > [復原服務]，然後按一下 [Site Recovery 保存庫]。
3. 按一下 [新建] > [快速建立]。
4. 在 [名稱] 中，輸入一個可識別保存庫的易記名稱。
5. 在 [區域] 中，選取保存庫的地理區域。若要查看支援的區域，請參閱 [Azure Site Recovery 價格詳細資料](pricing/details/site-recovery/)中的＜各區域上市情況＞。

檢查狀態列，以確認是否順利建立保存庫。保存庫在主要復原服務頁面上會列為 [使用中]。

## 步驟 2：設定保存庫並下載 InMage Scout 元件

1. 按一下 [建立保存庫]。
2. 在 [復原服務] 頁面中，按一下保存庫以開啟 [快速啟動] 頁面。
3. 在下拉式清單中，選取 [**兩個內部部署 VMWare 網站之間**]。
4. 下載 InMage Scout。所有必要元件的安裝程式檔案都在下載的 zip 檔中。


## 步驟 3：安裝元件更新

深入了解最新的[更新](#updates)。您將會依下列順序安裝更新檔案：

1. RX 伺服器 (如果有的話)
2. 設定伺服器
3. 處理序伺服器
3. 主要目標伺服器。
4. vContinuum 伺服器。

按照以下方式安裝：

1. 下載 [更新] (http://download.microsoft.com/download/9/F/D/9FDC6001-1DD0-4C10-BDDD-8A9EBFC57FDF/ASRScout 8.0.1 Update1.zip) zip 檔案。此 zip 檔案包含下列檔案：

	-  RX_8.0.1.0_GA_Update_1_3279231_23Jun15.tar.gz
	-  CX_Windows_8.0.1.0_GA_Update_1_3259146_23Jun15.exe
	-  UA_Windows_8.0.1.0_GA_Update_1_3259401_23Jun15.exe
	-  UA_RHEL6-64_8.0.1.0_GA_Update_1_3259401_23Jun15.tar.gz
	-  vCon_Windows_8.0.1.0_GA_Update_1_3259523_23Jun15.exe
2. 解壓縮 zip 檔案。
2. **RX 伺服器**：將 **RX_8.0.1.0_GA_Update_1_3279231_23Jun15.tar.gz** 複製到 RX 伺服器並將其解壓縮。在解壓縮的資料夾中執行 **/Install**。
2. **設定伺服器/處理序伺服器**：將 **CX_Windows_8.0.1.0_GA_Update_1_3259146_23Jun15.exe** 複製設定伺服器和處理序伺服器。連按兩下加以執行。
3. **Windows 主要目標伺服器**：若要更新整合代理程式，請將 **UA_Windows_8.0.1.0_GA_Update_1_3259401_23Jun15.exe** 複製到主要目標伺服器。連按兩下加以執行。請注意，適用於 Windows 的整合代理程式不適用於來源伺服器。它應該只安裝在 Windows 主要目標伺服器上。
4. **Linux 主要目標伺服器**：若要更新整合代理程式，請將 **UA_RHEL6-64_8.0.1.0_GA_Update_1_3259401_23Jun15.tar.gz** 複製到主要目標伺服器並將它解壓縮。在解壓縮的資料夾中執行 **/Install**。
5. **vContinuum 伺服器**：將 **vCon_Windows_8.0.1.0_GA_Update_1_3259523_23Jun15.exe** 複製到 vContinuum 伺服器。確定您已經關閉 vContinuum 精靈。連按兩下檔案加以執行。

## 步驟 4：設定複寫
5. 設定來源與目標 VMware 網站之間的複寫。
6. 如需指引，請使用隨產品下載的 InMage Scout 文件。或者，您可以存取下列文件：

	- [版本資訊](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [相容性矩陣](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [使用者指南](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [RX 使用者指南](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [快速安裝指南](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## 更新

### ASR Scout 8.0.1 Update 1

這個最新的更新包含錯誤修正和新功能：

- 每個伺服器執行個體享有 31 天的免費保護。這可讓您測試功能或設定概念證明。
	- 從使用 ASR Scout 第一次保護伺服器的時間開始計算的前 31 天，伺服器上的所有作業 (包括容錯移轉和容錯回復) 都是免費的。
	- 從第 32 天起，每個受保護的伺服器都會依照標準執行個體費率，向客戶擁有的網站收取 Azure Site Recovery 保護費用。
	- 在 Azure Site Recovery 保存庫的 [儀表板] 頁面上，隨時可取得目前計費的受保護伺服器數目。
- 已加入 vCLI 5.5 Update 2 的支援。
- 已加入來源伺服器上 Linux 作業系統的支援：
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- 用以處理下列問題的錯誤修正：
	- 設定伺服器或 RX 伺服器的保存庫註冊失敗。
	- 當叢集虛擬機器在恢復期間重新受到保護，叢集磁碟區就不會如預期般出現。
	- 當主要目標伺服器裝載於與內部部署實際執行虛擬機器不同的 ESXi 伺服器時，容錯回復會失敗。
	- 升級至 8.0.1 時，組態檔案權限會變更，進而影響保護和作業。
	- 不會如預期般強制執行重新同步臨界值，因而導致不一致的複寫行為。
	- RPO 並未正確地顯示在設定伺服器介面中。未壓縮的資料值未正確地顯示壓縮的值。
	-  未如預期在 vContinuum 精靈中刪除移除作業，而且未從設定伺服器介面中刪除複寫作業。
	-  在 VContinuum 精靈中，在 MSCS 虛擬機器的保護期間，按一下磁碟檢視中的 [**詳細資料**]，磁碟就會自動取消選取。
	- 在 P2V 案例期間，必要的 HP 服務 (例如 CIMnotify、CqMgHost) 不會移至復原虛擬機器中的 [手動]，結果是產生額外的開機時間。
	- 主要目標伺服器上有超過 26 個磁碟時，受保護的 Linux 虛擬機器就會失敗。
	
## 後續步驟

若有任何問題，請將問題張貼在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) (英文)。

<!---HONumber=July15_HO4-->