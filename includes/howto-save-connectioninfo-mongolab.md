雖然可以將 MongoLab URI 貼在程式碼中，但建議在環境中設定，以方便管理。如此一來，當 URI 變更時，只要透過 Azure
入口網站來更新即可，不必處理程式碼。

1.  在 Azure 入口網站中，選取 **網站**。
2.  在網站清單中，按一下網站的名稱。  
    ![WebSiteEntry](./media/howto-save-connectioninfo-mongolab/entry-website.png)
    
    [網站儀表板] 隨即出現。

3.  按一下功能表列的 **設定**。  
    ![WebSiteDashboardConfig](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png)

4.  向下捲動到 [連接字串] 區段。  
    ![WebSiteConnectionStrings](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png)

5.  在 **名稱** 中，輸入 MONGOLAB\_URI。
6.  在 **值** 中，貼上我們在上一節取得的連接字串。
7.  在 **類型** 下拉式清單中選取 **自訂** (取代預設的 **SQLAzure**)。
8.  按一下工具列的 **儲存**。  
    ![SaveWebSite](./media/howto-save-connectioninfo-mongolab/button-website-save.png)

**注意：**Azure 會在此變數前面加上 **CUSTOMCONNSTR\_**，所以上述程式碼會參考
**CUSTOMCONNSTR\_MONGOLAB\_URI**。

