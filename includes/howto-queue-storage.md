
## <a name="what-is"> </a>什麼是佇列儲存體？

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。如果儲存體帳戶是在 2012 年 6 月 8 日之後建立，則總容量為 200TB。如果儲存體帳戶是在此日期之前建立，則總容量為 100TB。如需關於儲存體帳戶容量的詳細資訊，請參閱[Azure 儲存體延展性和效能目標][1](英文)。

佇列儲存體的一般用途包括：

* <span>建立積存的工作供非同步處理</span>
* 將訊息從 Azure Web 角色傳遞至 Azure 背景工作角色

## <a name="concepts"> </a> 概念

佇列服務包含下列元件：

![Queue1](./media/howto-queue-storage/queue1.png)

* **URL 格式：**可利用下列 URL 格式來定址佇列：  
   http://`<storage account>`.queue.core.windows.net/`<queue>`

下列 URL 可定址圖中的其中一個佇列：  
http://myaccount.queue.core.windows.net/imagesToDownload

-**儲存體帳戶：**一律透過儲存體帳戶來存取 Azure 儲存體。如需關於儲存體帳戶容量的詳細資訊，請參閱＜[Azure 儲存體延展性和效能目標][1]＞(英文)。

* **佇列：**佇列包含一組訊息。所有訊息都必須放在佇列中。

* **訊息：**訊息 (任何格式) 的大小上限為 64KB。



[1]: http://msdn.microsoft.com/zh-tw/library/dn249410.aspx