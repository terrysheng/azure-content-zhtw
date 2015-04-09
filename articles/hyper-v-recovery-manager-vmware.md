<properties 
	pageTitle="教學課程：設定內部部署 VMWare 站台之間的保護" 
	description="Azure Site Recovery 中的 InMage 可處理內部部署 VMWare 站台之間的複寫、容錯移轉和復原作業。" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# 教學課程：設定內部部署 VMWare 站台之間的保護


<h2><a id="overview" name="overview" href="#overview"></a>概觀</h2>

<p>Azure Site Recovery 中的 InMage 可提供內部部署 VMWare 站台之間的即時複寫。InMage 包含在 Azure Site Recovery 服務的訂用帳戶中。</p>




<h2><a id="before" name="before" href="#before"></a>必要條件</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Azure 帳戶</b>-您將需要一個 Azure 帳戶。如果您沒有帳戶，請參閱 <a href="http://aka.ms/try-azure">Azure 免費試用</a>。您可以從 <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery 管理員定價詳細資料</a>取得訂閱定價資訊。</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>教學課程步驟</h2>
<a name="vault"></a> <h3>步驟 1：建立保存庫並下載 InMage</h3>

1. 登入[管理入口網站](https://manage.windowsazure.com)。


2. 依序展開 [資料服務]<b></b>、[復原服務]<b></b>，然後按一下 [Site Recovery 保存庫]<b></b>。


3. 按一下 [新建]<b></b>，然後按一下 [快速建立]<b></b>。
	
4. 在 [名稱]<b></b> 中，輸入用於識別保存庫的易記名稱。

5. 在 [地區]<b></b> 中，選取保存庫的地理區域。若要查看支援的地區，請參閱 <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Azure Site Recovery 定價詳細資料</a> (英文) 中的＜各地區上市情況＞。

6. 按一下 [建立保存庫]<b></b>。 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>檢查狀態列，以確認是否順利建立保存庫。保存庫將會在主要 [復原服務] 頁面上列為 [使用中]<b></b>。</P>

<a name="upload"></a> <h3>步驟 2：設定保存庫</h3>


1. 在 [復原服務]<b></b> 頁面中，按一下保存庫以開啟 [快速入門] 頁面。您也可以使用圖示隨時開啟 [快速入門]。

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. 在下拉式清單中，選取 [在兩個內部部署 VMWare 站台之間]。
3. 下載 InMage Scout。
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. 使用隨 InMage 產品下載的文件來設定兩個 VMWare 站台之間的複寫。


<h2><a id="next" name="next" href="#next"></a>後續步驟</h2>
<UL>

<LI>若有任何問題，請造訪 <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure 復原服務論壇</a> (英文)。</LI> 
</UL>

<!--HONumber=49-->