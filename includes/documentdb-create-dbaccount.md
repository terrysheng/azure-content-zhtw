1.	登入 [Azure Preview 入口網站](https://portal.azure.com/)。
2.	在 Jumpbar，按一下 [新增]，然後選取 [資料 + 儲存體]，然後按一下 [DocumentDB]。 

	![Azure 預覽入口網站的螢幕擷取畫面，反白顯示 [新增] 按鈕、[建立] 分頁中的 [資料 + 儲存體]，以及 [資料 + 儲存體] 分頁中的 **DocumentDB**][1]   

	或者，您也可以從「開始面板」瀏覽 Azure Marketplace，選取 [資料 + 分析] 類別，選擇 [DocumentDB]，然後按一下 [建立]。  
	
	![Azure 預覽入口網站的螢幕擷取畫面，反白顯示 [DocumentDB] 磚時，顯示 [服務商場] 分頁，反白顯示 [建立] 按鈕時，顯示 [DocumentDB] 分頁][2]   
   

3. 在 [新增 DocumentDB (預覽)]**** 刀鋒視窗中，指定想要的 DocumentDB 帳戶組態。 
 
	![新 DocumentDB (預覽) 分頁的螢幕擷取畫面][3] 


	- 在 [識別碼] 方塊中，輸入用來識別 DocumentDB 帳戶的名稱。這個值會變成 URI 中的主機名稱。此識別碼只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。請注意，documents.azure.com 會附加至您選擇的端點名稱後面，產生的結果將成為您的 DocumentDB 帳戶端點。

	- [定價層] 鏡頭已被鎖定，因為 DocumentDB 預覽版僅支援單一標準定價層。如需詳細資訊，請參閱 [DocumentDB 定價](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)。

	- [選擇性組態] 透鏡可用來指定配置給 DocumentDB 帳戶的初始容量。DocumentDB 利用容量單位讓您調整 DocumentDB 帳戶，每個容量單位包含保留的資料庫儲存體和輸送量。依預設會佈建 1 個容量單位。您可以隨時透過 [Preview 管理入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)來調整 DocumentDB 帳戶可用的容量單位數。如需有關 DocumentDB 帳戶容量和輸送量的詳細資料，請參閱[管理 DocumentDB 的容量和效能][documentdb-manage]一文。

	- 在 [資源群組] 中，為 DocumentDB 帳戶選取或建立資源群組。依預設會建立新的資源群組。不過，您可以選擇現有的資源群組，以便在其中加入您的 DocumentDB 帳戶。如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](azure-preview-portal-using-resource-groups.md).

	- 針對 [訂用帳戶]，選取要用於 DocumentDB 訂用帳戶的 Azure 訂用帳戶。如果您的帳戶只有一個訂用帳戶，將會自動選取該帳戶。*
 
	- 使用 [位置] 來指定將裝載您 DocumentDB 帳戶的地理位置。   

3.	設定新的 DocumentDB 帳戶選項之後，按一下 [建立]。建立 DocumentDB 帳戶可能需要數分鐘的時間。若要檢查狀態，您可以在「開始面板」上監視進度。  
	![在儀表板上建立磚的螢幕擷取畫面][4]  
  
	或者，您也可以從 [通知] 中樞監視進度。  

	![通知中樞的螢幕擷取畫面，其中顯示正在建立 DocumentDB 帳戶][5]  

	![通知中樞的螢幕擷取畫面，其中顯示已成功建立 DocumentDB 帳戶，並部署至資源群組][6]

4.	建立好的 DocumentDB 帳戶可立即以預設值來使用。

	請注意，DocumentDB 帳戶的預設一致性會設定為 [工作階段]。您可以透過 [Preview 管理入口網站](https://portal.azure.com/#gallery/Microsoft.DocumentDB)調整預設一致性設定。  
	![[資源群組] 分頁的螢幕擷取畫面][7]  


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[作法：建立 DocumentDB 帳戶]: #Howto
[後續步驟]: #NextSteps
[documentdb-manage]:../articles/documentdb-manage.md

<!--HONumber=49-->