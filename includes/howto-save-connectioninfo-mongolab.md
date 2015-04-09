雖然可以將 MongoLab URI 貼在程式碼中，但建議在環境中設定，以方便管理。如此一來，當 URI 變更時，只要透過 Azure 入口網站來更新即可，不必處理程式碼。


1. 在 Azure 入口網站中，選取 [網站]。
1. 按一下網站清單中的網站的名稱。  
![WebSiteEntry][entry-website]  
[網站儀表板] 隨即顯示。

1. 按一下功能表列中的 [設定]。  
![WebSiteDashboardConfig][focus-mongolab-websitedashboard-config]

1. 向下捲動到 [連接字串] 區段。  
![WebSiteConnectionStrings][focus-mongolab-websiteconnectionstring]

1. 在 [名稱] 中，輸入 MONGOLAB_URI。
1. 在 [值] 中貼上在上一節取得的連接字串。
1. 在 [類型] 下拉式清單中選取 [自訂] (而非預設的 **SQLAzure**)。
1. 按一下工具列上的 [儲存]。  
![SaveWebSite][button-website-save]

**注意：**Azure 會將 **CUSTOMCONNSTR\_** 前置詞加入至此變數，這就是為什麼上述程式碼參考 **CUSTOMCONNSTR\_MONGOLAB_URI** 的原因。

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png

<!--HONumber=49-->