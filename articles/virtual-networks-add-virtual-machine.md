<properties  linkid="manage-services-add-a-vm-to-a-virtual-network" urlDisplayName="Add a VM to virtual network" pageTitle="Add a virtual machine to a virtual network - Azure" metaKeywords="" description="A tutorial that teaches you how to create a storage account and virtual machine (VM) that you add to an Azure virtual network." metaCanonical="" services="virtual-machines,virtual-network" documentationCenter="" title="Add a Virtual Machine to a Virtual Network" authors="" solutions="" manager="" editor="" />

<h1>將虛擬機器新增至虛擬網路</h1>   


<!--SOMEWHERE IN THIS TUTORIAL I NEED TO XREF TO THE OTHER VMACHINE TUTORIAL -->

本教學課程引導您逐步完成 Azure 儲存體帳戶和可新增至[虛擬網路][1]之虛擬機器 (VM) 的建立步驟。

本教學課程假設您先前沒有使用 Azure 的經驗。
<div class="dev-callout"> 
<b>重要事項</b>

<p>如果您計劃建立 VM 來安裝新的 Active Directory 樹系，請遵循<a href="../active-directory-forest/">在 Azure 中安裝新的 Active Directory 樹系</a>中的指示。</p>
</div>

## 目標

在本教學課程中，您將了解：

* [如何建立儲存體帳戶](#CreateStorageAcct)

* [如何建立虛擬機器，以及將其部署至虛擬網路](#CreateVM)

## 必要條件

* 完成以下教學課程中任一項：
  
  * [在 Azure 中建立虛擬網路](/en-us/manage/services/networking/create-a-virtual-network/)
    
    -或-
  
  * [建立虛擬網路以實現跨單位連線](/en-us/manage/services/networking/cross-premises-connectivity/)

* 至少有一個有效作用中訂閱的 Windows Live 帳戶。

* 以下項目的名稱，如[在 Azure 中建立虛擬網路](/en-us/manage/services/networking/create-a-virtual-network/)或[建立虛擬網路以實現跨單位連線](/en-us/manage/services/networking/cross-premises-connectivity/)所述：
  
  * 指派給虛擬網路的同質群組。
  
  * 虛擬網路的名稱。
  
  * 子網路的名稱。

## <a name="CreateStorageAcct">建立儲存體帳戶</a>

1.  在 **Azure 管理入口網站][2]中建立虛擬網路後，請在畫面左下角按一下 [新增**。
    
    ![NewStorAcct](./media/virtual-networks-add-virtual-machine/VNTut3_01_NewStorageAccount.png)

2.  在瀏覽窗格中依序按一下 **資料服務**、**儲存體** 及 **快速建立**。
    
    ![QuickCreate](./media/virtual-networks-add-virtual-machine/VNTut3_02_StorageAcct_QuickCreate.png)

3.  輸入以下資訊，然後按一下畫面右下角的核取記號。

* **URL：**輸入 *yourstorage*。

* **區域/同質群組：**在下拉式清單中選取 **YourAffinityGroup**。

* **啟用地理區域複寫：**保留此方塊的核取狀態。
  
	![CreateNewAcct](./media/virtual-networks-add-virtual-machine/VNTut3_03_CreateNewStorageAccount.png)

1.  在 **儲存體** 頁面中，當程序完成時 **狀態** 欄會顯示 **線上**。
    
    ![NewStorAcctCreated](./media/virtual-networks-add-virtual-machine/VNTut3_04_NewStorageAcctCreated.png)

## <a name="CreateVM">建立虛擬機器並部署至虛擬網路</a>

**建立虛擬機器並將其部署至虛擬網路：**

1.  在建立儲存體帳戶後，請在畫面左下角按一下 **新增**。
    
    ![NewVM](./media/virtual-networks-add-virtual-machine/VNTut3_05_NewVM.png)

2.  在導覽窗格中依序按一下 **計算**、**虛擬機器** 及 **從組件庫**。
    
    ![FromGallery](./media/virtual-networks-add-virtual-machine/VNTut3_06_VM_FromGallery.png)

3.  在 **VM OS Selection** 畫面中選取 [Windows Server 2008 R2 SP1,
    October 2012]**** (或可用的最新版本)，然後按 [下一步] 箭頭。
    
    ![VMOS](./media/virtual-networks-add-virtual-machine/VNTut3_07_VMOSSelect_Win2008R2.png)

4.  在 **虛擬機器組態** 畫面中輸入以下資訊，然後按 [下一步] 箭頭。 <!-- SHOULD WE TELL USERS
    TO WRITE DOWN USER NAME AND PASS?? -->
    
    **秘訣：**請寫下使用者名稱和密碼，因為它們是用來登入新虛擬機器的認證。

* **虛擬機器名稱：**輸入 *YourVMachine*。

* **新使用者名稱：**唯讀。

* **新密碼：**輸入增強式密碼。

* **確認密碼：**重新輸入密碼。

* **大小：**選取 **小型**。
  
	![VMConfig](./media/virtual-networks-add-virtual-machine/VNTut3_08_VMConfig.png)

1.  在 **虛擬機器模式** 畫面中輸入以下資訊，然後按 [下一步] 箭頭。

* **獨立虛擬機器：**保留此選項的選取狀態。

* **DNS 名稱：**輸入 *yourcloudapplication*。

* **儲存體帳戶：**選取 **yourstorage**。

* **區域/同質群組/虛擬網路：**在下拉式清單中選取 **YourVirtualNetwork**。
  
	![VMMode](./media/virtual-networks-add-virtual-machine/VNTut3_09_VMMode.png)

1.  在 **虛擬機器選項** 畫面中輸入以下資訊，然後按一下核取記號按鈕。系統隨即會建立虛擬機器。新機器的建立最多可能需要花費 10
    分鐘的時間。
    <!-- CONFIRM HOW LONG IT CAN TAKE ON AVG FOR VMACHINE TO BE CREATED -->

* **可用性設定組：**選取 **無**。

* **虛擬網路子網路：**選取 **FrontEndSubnet**。
	<div class="dev-callout">
  	<b>注意</b>
  	<p>您至少應選取一個子網路，且「請勿」選取閘道子網路。</p>
	</div>
  
	![VMOptions](./media/virtual-networks-add-virtual-machine/VNTut3_10_VMOptions.png)

1.  待虛擬機器建立後，虛擬機器畫面中的 **狀態** 會變成 **執行中**。
    
	![VMInstances](./media/virtual-networks-add-virtual-machine/VNTut3_11_VMInstances.png)

2.  在導覽窗格中按一下 **ALL ITEMS**。所有已建立的物件和目前的狀態會顯示於此。
    
    ![AllTab](./media/virtual-networks-add-virtual-machine/VNTut3_12_AllTab.png)

## 後續步驟

若要在剛建立的 VM 上針對內部部署 Active Directory 網域安裝額外的網域控制站，請參閱[在 Azure 虛擬網路中安裝複寫 Active Directory 網域控制站](/en-us/manage/services/networking/replica-domain-controller/)。

## 另請參閱

* [Azure 虛擬網路][1]

* [使用網路組態檔設定虛擬網路][3]

<!-- LINKS -->



[1]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156007.aspx
[2]: http://manage.windowsazure.com/
[3]: http://msdn.microsoft.com/zh-tw/library/windowsazure/jj156097.aspx