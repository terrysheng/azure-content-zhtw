下列步驟將在 Azure 中建立新的行動服務，並將可用來存取此新服務的程式碼加入您的專案。在建立行動服務之前，您必須將 publishsettings 檔案從您的 Azure 訂閱匯入 Visual Studio。這可讓 Visual Studio 代表您連線到 Azure。建立新的行動服務時，您必須指定該行動服務用來儲存應用程式資料的 Azure SQL Database。

1.  在 Visual Studio 2013 中，開啟 [方案總管]、在專案上按一下滑鼠右鍵，然後依序按一下 **新增**、**已連接服務**。
    
    ![新增已連接服務](./media/mobile-services-create-new-service-vs2013/mobile-add-connected-service.png)

2.  在 [服務管理員] 對話方塊中，按一下 **建立服務**，然後從 [建立行動服務] 對話方塊的 **訂閱** 中，選取 **<匯入...>**。
    
    ![從 VS 2013
    建立新的行動服務](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013.png)

3.  在 **匯入 Azure 訂閱 中按一下 下載訂閱檔案**，登入您的 Azure 帳戶 (如有必要)，在瀏覽器要求儲存檔案時，按一下 **儲存**。
    
    ![在 VS
    中下載訂閱檔案](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription.png)
    
    
	<div  class="dev-callout"><strong>注意</strong> <p>登入視窗會顯示在瀏覽器中，它有可能會在 Visual Studio 視窗下面。請記得記下儲存 .publishsettings 檔案的下載位置。如果您的專案已連線到 Azure 訂閱，您可以略過此步驟。</p></div>


4.  按一下 **瀏覽** 並瀏覽至儲存 .publishsettings 檔案的位置，選取此檔案，然後依序按一下 **開啟**、**匯入**。
    
    ![在 VS
    中匯入訂閱](./media/mobile-services-create-new-service-vs2013/mobile-import-azure-subscription-2.png)
    
    Visual Studio 會匯入連線到 Azure 訂閱所需的資料。當訂閱已經有一或多個現有的行動服務時，便會顯示這些服務名稱。
    
    
	<div  class="dev-callout"><strong>安全性注意事項</strong> <p>在匯入發佈設定之後，請考慮刪除下載的 .publishsettings 檔案，因為它包含了他人可用來存取您帳戶的資訊。如果您打算保留此檔案以供其他連線應用程式專案使用，請確保此檔案安全無虞。</p></div>


5.  回到 **建立行動服務** 對話方塊，為您的行動服務選取 **訂閱** 和所需的 **區域**，然後輸入此行動服務的 **名稱**。
    
    
	<div  class="dev-callout"><strong>注意</strong> <p>行動服務名稱必須是唯一的。當您提供的名稱無法使用時，<strong>[名稱]</strong> 旁邊便會顯示一個紅色 X。 </p></div>


6.  在 **資料庫** 中選取 **<建立免費的 SQL Database>**，提供 **伺服器使用者名稱**、**伺服器密碼** 和 **伺服器密碼確認**，然後按一下 **建立**。

 ![從 VS 2013 建立服務 (第 2 部分)](./media/mobile-services-create-new-service-vs2013/mobile-create-service-from-vs2013-2.png)

   
 > [WACOM.NOTE]
    > 作為本教學課程的一部分，您需要建立新的 SQL Database 執行個體和伺服器。您可以重複使用這個新的資料庫，並像管理其他任何 SQL Database 執行個體一樣管理這個新的資料庫。您只能有一個免費的資料庫執行個體。如果新行動服務的區域已經有資料庫存在，您可以改選現有的資料庫。當您選擇現有的資料庫時，請確保您提供的是正確的登入認證。如果您提供不正確的登入認證，則行動服務會建立在不健全的狀態下。

 在建立行動服務之後，專案便會新增行動服務用戶端程式庫的參考，並更新您的專案原始程式碼。 