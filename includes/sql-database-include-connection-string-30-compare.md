
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### 比較連接字串


下表比較 C# 程式連接到內部部署 SQL Server，以及連接到雲端中的 Azure SQL Database 時，所需要的連接字串。差異之處會以粗體顯示。


| Azure SQL Database 的<br/>連接字串 | Microsoft SQL Server 的<br/>連接字串 |
| :-- | :-- |
| Server=**tcp:**{your\_serverName\_here}**.database.windows.net,1433**;<br/>User ID={your\_loginName\_here}**@{your\_serverName\_here}**;<br/>Password={your\_password\_here};<br/>**Database={your\_databaseName\_here};**<br/>**Connection Timeout=30**;<br/>**Encrypt=True**;<br/>**TrustServerCertificate=False**; | Server={your\_serverName\_here};<br/>User ID={your\_loginName\_here};<br/>Password={your\_password\_here}; |


**Database=** 對 SQL Server 而言是選用的，但對 SQL Database 而言則是必要的。


[.NET ADO SqlConnectionStringBuilder 屬性](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx) - 詳細討論所有參數。


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=Sept15_HO2-->