當您佈建 MongoLab 資料庫時，MongoLab 會以 MongoDB 標準連接字串格式，將連線 URI 傳送至
Azure。此值會透過您選擇的 MongoDB 驅動程式，初始化 MongoDB 連線。如需關於連接字串的詳細資訊，請參閱
mongodb.org 上的[連線][1]。

**此 URI 包含您的資料庫使用者名稱和密碼。這是機密資訊，請勿洩露。**

在 Azure 入口網站中，您可以利用下列步驟來擷取此 URI：

1.  選取 **附加元件**。  
    ![AddonsButton](./media/howto-get-connectioninfo-mongolab/button-addons.png)
2.  在附加元件清單中，找出您的 MongoLab 服務。  
    ![MongolabEntry](./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png)
3.  按一下附加元件的名稱，前往附加元件頁面。
4.  按一下 **連線資訊**。  
    ![ConnectionInfoButton](./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png)
    
     MongoLab URI 隨即出現：  
    ![ConnectionInfoScreen](./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png)
5.  按一下 MONGOLAB\_URI 值右側的剪貼簿按鈕，將整個值複製到剪貼簿。



[1]: http://www.mongodb.org/display/DOCS/Connections
