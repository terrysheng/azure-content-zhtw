# 如何在 Azure 中建立 MySQL 資料庫

本指南將示範如何從 [Azure 市集](/en-us/store/overview/)使用 [ClearDB][1] 建立 MySQL
資料庫，以及如何在建立 [Azure 網站](/en-us/manage/services/web-sites/)時建立 MySQL
資料庫作為連結的資源。[ClearDB][1] 是一項可容錯的「資料庫即服務」提供者，可讓您在 Azure 資料中心內執行和管理 MySQL
資料庫，以及從任何應用程式連線至這些資料庫。
## 目錄

* [作法：從 Azure 市集建立 MySQL 資料庫](#CreateFromStore)
* [作法：為 Azure 網站建立 MySQL 資料庫作為連結的資源](#CreateForWebSite)

 
<div  class="dev-callout"> 
<b>注意</b> 
<p>在網站建立過程中建立 MySQL 資料庫時，您只能建立免費資料庫。從 Azure 市集建立 MySQL 資料庫時，則可以建立免費資料庫或選擇付費選項。</p> 
</div>

 <h2><a  id="CreateFromStore" ></a>作法：從 Azure 市集建立 MySQL 資料庫</h2>


若要從 [Azure 市集](/en-us/store/overview/)建立 MySQL 資料庫，請執行下列動作：

1.  登入 [Azure 管理入口網站][2]。
2.  按一下頁面底部的 **+新增**，然後選取 **市集**。
    
    ![從市集中選取附加元件](./media/create-mysql-db/select-store.png)

3.  選取 **ClearDB MySQL 資料庫**，然後按一下框架底部的箭頭。
    
    ![選取 ClearDB MySQL
    資料庫](./media/create-mysql-db/select-cleardb-mysql.png)

4.  選取方案、輸入資料庫名稱、選取區域，然後按一下框架底部的箭頭。
    
    ![從市集購買 MySQL 資料庫](./media/create-mysql-db/purchase-mysql.png)

5.  按一下打勾記號完成選購。
    
    ![檢查並完成選購](./media/create-mysql-db/complete-mysql-purchase.png)

6.  建立資料庫之後，您可以從管理入口網站的 **附加元件** 索引標籤加以管理。
    
    ![在 Azure 入口網站中管理 MySQL
    資料庫](./media/create-mysql-db/manage-mysql-add-on.png)

7.  按一下頁面底部的 **連接資訊** (如下所示)，即可取得資料庫連接資訊。
    
    ![MySql 連接資訊](./media/create-mysql-db/mysql-conn-info.png)

<h2><a  id="CreateForWebSite" ></a>作法：為 Azure 網站建立 MySQL 資料庫作為連結的資源</h2>


若要在建立 [Azure 網站](/en-us/manage/services/web-sites/)時建立 MySQL
資料庫作為連結的資源，請執行下列動作：

1.  登入 [Azure 管理入口網站][2]。
2.  按一下頁面底部的 **+新增**，然後依序選取 **運算**、**網站** 和
    **和資料庫一起建立**。
    
    ![和資料庫一起建立網站](./media/create-mysql-db/custom_create.png)

3.  提供網站的 **URL**，選取網站的 **區域**，然後從 **資料庫** 下拉式清單中選擇 [建立新的
    MySQL 資料庫]****。(選用) 您可以取代連接字串的預設名稱。按一下頁面底部的箭頭。
    
    ![提供網站詳細資料](./media/create-mysql-db/provide-website-details.png)

4.  提供資料庫 **名稱**，選取資料庫的 **區域** (這應該與網站的區域相同)，同意 ClearDB
    的法律條款，然後按一下框架底部的打勾記號。
    
    ![提供 MySQL 詳細資料](./media/create-mysql-db/provide-mysql-details.png)

5.  在網站建立好之後，按一下網站的名稱，以移至網站的儀表板。
    
    ![移至網站儀表板](./media/create-mysql-db/go-to-website-dashboard.png)

6.  按一下 **設定**。
    
    ![移至設定索引標籤](./media/create-mysql-db/go-to-configure-tab.png)

7.  向下捲動到 **連接字串** 區段，然後按一下 **顯示連接字串**。
    
    ![顯示連接字串](./media/create-mysql-db/show-conn-string.png)

8.  複製連接字串以用於應用程式中。
    
    ![顯示的連接字串](./media/create-mysql-db/shown-conn-string.png)

 
<div  class="dev-callout"> 
<b>注意</b> 
<p>您的網站應用程式可以根據連接字串名稱來存取連接字串。在 .NET 應用程式中，連接字串位於 <b>connectionStrings</b> 物件中。在其他程式設計語言中，連接字串則可以環境變數的形式受到存取。如需詳細資訊，請參閱<a  href="/en-us/manage/services/web-sites/how-to-configure-websites/">如何設定網站</a>。</p> 
</div>

 

[1]: http://www.cleardb.com/
[2]: http://manage.windowsazure.com
