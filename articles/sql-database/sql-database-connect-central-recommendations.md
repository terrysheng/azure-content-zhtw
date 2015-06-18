<properties 
	pageTitle="連接 Azure SQL Database：重要的建議" 
	description="「一個重要的主題，可針對用以連接 Azure SQL Database 的多種驅動程式 (例如 Azure SQL Database) 提供指向更明確主題的連結。」" 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


# 連接 SQL Database：重要的建議


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


本主題提供指向各種技術之程式碼範例的連結，可用來連接到 Azure SQL Database 並與其互動。這些技術包括企業程式庫、JDBC 和 PHP。建議一般適用於所有特定連線技術。


## 與技術無關的建議


本節中的資訊適用於您用來連接到 SQL Database 的所有特定技術。


- [以程式設計方式連接至 Azure SQL Database 的指導方針](http://msdn.microsoft.com/library/azure/ee336282.aspx) - 討論包括：
 - 連接埠
 - 防火牆
 - 連接字串
- [Azure SQL Database 資源管理](https://msdn.microsoft.com/library/azure/dn338083.aspx) - 討論包括：
 - 資源管理
 - 限制強制執行
 - 節流


不論您使用的連線技術，SQL Database 伺服器，甚或是個別資料庫的某些防火牆設定與下列有關：


- [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx)


### 快速建議


#### 連線


- 在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。
 - 預設 15 秒對於依賴網際網路的連線而言太短。
- 如果您使用的是[連線集區](http://msdn.microsoft.com/library/8xx3tyca.aspx)，請在程式未主動使用時即時關閉連線，而不只是準備重複使用連線。
 - 除非您的程式將立即重複使用其他作業的連線，而不暫停，否則我們建議使用下列模式：
<br/><br/>開啟連線。
<br/>透過連線執行一項作業。
<br/>關閉連線。<br/><br/>
- 使用重試邏輯搭配連線邏輯，但僅用於暫時性錯誤。使用 SQL Database 時，嘗試開啟連線或發出查詢都可能因為多種原因而失敗。
 - 失敗的其中一個持續性原因可能是您的連接字串格式錯誤。
 - 失敗的其中一個暫時性原因可能是 Azure SQL Database 系統需要平衡整體負載。暫時性原因會自己消失，這表示您的程式應該重試。
 - 重試查詢時，請先關閉連線，然後再開啟另一個連線。
- 確定您的 [Azure SQL Database 防火牆](http://msdn.microsoft.com/library/ee621782.aspx)允許連接埠 1433 上的傳出 TCP 通訊。
 - 您可以在 SQL Database 伺服器上或個別的資料庫中設定[防火牆](http://msdn.microsoft.com/library/azure/ee621782.aspx)設定。


#### 驗證


- 使用 SQL Database 驗證，而非 Windows 驗證。
- 指定一個特定的資料庫，而非預設為  *master* 資料庫。
- 有時候必須提供使用者名稱的後置詞 *@yourservername*，但有時候必須省略後置詞。這取決於撰寫工具或 API 的方式。
 - 檢查每個個別技術的詳細資料。
- 透過在[自主資料庫](http://msdn.microsoft.com/library/ff929071.aspx)中指定使用者來連線。
 - 此方法會因為不需要在 master 資料庫中登入，而提供較好的效能和延展性。
 - 您無法在 SQL Database 上使用 Transact-SQL **USE myDatabaseName;** 陳述式。


### 暫時性錯誤


有些 SQL Database 連線錯誤是持續性的，沒有理由要立即重試連線。有些錯誤則是暫時性的，因此建議您的程式自動嘗試重試。範例：


- *持續性：*如果您在嘗試連線時，拼錯 SQL Database 伺服器的名稱，重試毫無意義。
- *暫時性：*如果 Azure 節流或負載平衡系統稍後會強制結束 SQL Database 的有效連線，建議嘗試重新連線。


呼叫 SQL Database 所擲回的 [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) 在其 [**數字**] 屬性中包含一個數字錯誤碼。如果錯誤碼是列為暫時性錯誤，則您的程式應該重試呼叫。


- [錯誤訊息 (Azure SQL Database)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - 其 [**連線中斷錯誤**] 區段是一個有理由重試的暫時性錯誤清單。
 - 例如，如果出現錯誤編號 40613，表示發生類似如下的狀況，則重試：<br/>*目前無法使用伺服器 'theserver'上的資料庫 'mydatabase'。*


發生持續性或暫時性錯誤時，如需進一步的協助，請參閱：


- [疑難排解 Azure SQL Database 的連線問題](http://support.microsoft.com/zh-tw/kb/2980233/zh-tw)


## 技術


下列主題包含指向數種連線技術的程式碼範例的連結：


- [Azure SQL Database 開發：作法主題](http://msdn.microsoft.com/library/azure/ee621787.aspx)


對於包含企業程式庫與暫時性錯誤處理類別的 ADO.NET，請參閱：


- [做法：可靠地連線到 Azure SQL Database](http://msdn.microsoft.com/library/azure/dn864744.aspx)


對於 Elastic Scale，請參閱：


- [開始使用 Azure SQL Database Elastic Scale 預覽](sql-database-elastic-scale-get-started.md)
- [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)


## 不完整或過時的文章


本節中的連結指向部落格文章或類似來源，因此它們可能會不完整或已過時。但是它們可能會有一些背景價值。


- [Microsoft Azure SQL Database 中暫時性失敗的重試邏輯](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49--> 