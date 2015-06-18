<properties 
	pageTitle="規劃和準備升級至 SQL Database V12" 
	description="說明升級至 Azure SQL Database V12 的相關準備和限制。" 
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
	ms.date="03/05/2015" 
	ms.author="genemi"/>


# 規劃和準備升級至 Azure SQL Database V12


<!--
GeneMi，2015 年 3 月 05 日星期四下午 20:06
移除標題中的  'Azure'。
-->


本主題描述將 Azure SQL Database 從 V11 版升級至 V12 版必須執行的規劃和準備。


全新的 [Azure 入口網站](http://portal.azure.com/) 可協助您升級至 V12。


> [AZURE.NOTE]
> 測試資料庫、資料庫複本或新資料庫都適合升級至預覽。您的企業所依賴的實際執行資料庫應等到預覽期間後再升級。<br/><br/>
> 在 V12 版本系列中，您可以檢查 [SQL Database V12 新功能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/#V12AzureSqlDbPreviewGaTable) 中的區域表格，判斷您的地理區域為預覽狀態或 GA 狀態。


下表列出和描述 V12 的其他說明主題。


| 標題和連結 | 內容的描述 |
| :--- | :--- |
| [SQL Database V12 新功能](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/) | 描述 V12 如何提升 Azure SQL Database 以與 Microsoft SQL Server 2014 並駕齊驅的詳細資料。 |
| [逐步解說：註冊最新的 SQL Database Update V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-sign-up/) | 描述若要將 Azure SQL Database 升級至最新的預覽所必須執行的步驟。 |
| [在最新的 SQL Database Update V12 (預覽) 中建立資料庫](http://azure.microsoft.com/documentation/articles/sql-database-preview-create/) | 描述如何建立包含最新預覽新功能的新 Azure SQL Database。內容描述各種選項，而非只是空的資料庫。 |
| [升級至最新的 SQL Database Update V12 (預覽)](http://azure.microsoft.com/documentation/articles/sql-database-preview-upgrade/) | 描述如何將 Azure SQL Database 升級至最新的預覽。內容提到您應先備份資料庫，因為升級是永久性的，而且無法復原。 |


## A. 事先規劃


下列小節描述在您採取動作將 Azure SQL Database 升級至 V12 之前，必須學習的知識和決策制訂。

### A.1 版本釐清


本文件是關於將 Microsoft Azure SQL Database 從 V11 升級至 V12 版。更正式的版本編號接近 Transact-SQL 陳述式 **SELECT @@version;** 報告的下列兩個值：


- 11.0.9228.18 *(V11)*
- 12.0.2000.8 *(或更新的版本：V12)*


### A.2 服務層規劃


從 V12 開始，Azure SQL Database 將僅支援名為「基本」、「標準」和「高階」的服務層。V12 不支援 Web 和商務服務層。因此，如果您打算將目前是 Web 或商務服務層的 Azure SQL Database 升級，您必須決定新的服務層應該是什麼。


如需「基本」、「標準」和「高階」服務層的詳細資訊，請參閱：


- [將 SQL Database Web/商務資料庫升級至新的服務層](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/)
- [Azure SQL Database 定價](http://azure.microsoft.com/pricing/details/sql-database/)


### A.3 檢閱異地複寫設定


如果您的 Azure SQL Database 已設定異地複寫，您應先記錄其目前的設定、停止異地複寫，然後再開始升級準備動作。在升級完成後，您必須重新設定資料庫的異地複寫。


對策是保留來源，並在資料庫複本上測試。


### A.4 比較就地升級與將資料庫複製到新伺服器


若要將 Azure SQL Database 從 V11 升級至 V12，有兩種方法可供您選擇：


- 就地升級︰此程序完成後，V11 資料庫將不存在。SQL Database 伺服器的版本是 V12。
- 將資料庫複製到 V12 伺服器：此程序不會干擾 V11 資料庫，而是會在 V12 Azure SQL Database 伺服器上建立升級的 V11 資料庫版本。 <br/> 請注意，用戶端程式碼用來存取資料庫的 DNS 資訊將無法再存取 V11，必須編輯 DNS 資訊才能存取 V11 資料庫。


## B. 準備動作


規劃完成之後，您可以執行下列小節中描述的動作步驟，為最終升級階段做準備。


本說明主題上方所連結之說明主題中，有最終升級階段的詳細描述。


### B.1 服務層動作


V12 不支援 Web 和商務服務定價層級。


如果您的 V11 Azure SQL Database 是 Web 或商務資料庫，升級程序會提供將資料庫切換到支援之服務層的選項。升級會建議適合您資料庫之工作負載歷程記錄的服務層。不過，您可以選擇任何您喜歡之支援的服務層。


開始升級之前，您可以透過將您的 V11 資料庫切離 Web 和商務服務層，以減少升級期間的必要步驟。您可以使用 Azure 入口網站來執行這項工作。


如果您不確定要切換到哪個服務層，「標準」層的 S0 層級會是實用的初始選擇。


### B.2 在升級期間暫停異地複寫


如果異地複寫在您資料庫中是作用中狀態，將無法升級至 V12。您必須先重新設定資料庫以停止使用異地複寫。


在升級完成後，您可以設定資料庫再次使用異地複寫。


##<a id="limitations"></a>C. 升級至 V12 期間和之後的限制


這一節說明與升級至 Azure SQL Database V12 相關聯的限制。


### C.1 V12 的預覽入口網站


下列這兩個 Azure 管理入口網站，只有第一個支援 V12 資料庫：


- [http://portal.azure.com/](http://portal.azure.com/)
 - 這個較新的入口網站為預覽狀態，而且尚未公開上市 (GA)。<br/><br/>
- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)
 - 這個較舊的入口網站不會為支援 V12 而進行更新。


我們鼓勵客戶使用 Visual Studio 2013 (VS2013) 連線到其 Azure SQL Database。VS2013 可用於下列工作：


- 執行 T-SQL 陳述式。
- 設計結構描述。
- 開發線上或離線資料庫。


您可以改為連接 [Visual Studio Community 2013](https://www.visualstudio.com/zh-tw/news/vs2013-community-vs.aspx/)，這是 VS2013 的免費且功能完整版本。


您可以在較舊的 Azure 管理入口網站的 [資料庫] 頁面上，按一下 [**在 Visual Studio 中開啟**] 在電腦上啟動 VS2013，來連接至您的 Azure SQL Database。


如需另一個替代方式，您可以使用 SQL Server Management Studio (SSMS) 2014 與 [CU6](http://support.microsoft.com/kb/3031047/) 來連接至 Azure SQL Database。此部落格文章包含更多詳細資料：<br/>[Azure SQL Database 的用戶端工具更新](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/) (英文)。


### C.2 升級至 V12 *during*的限制

下表描述在升級期間生效的限制。


| 限制 | 說明 |
| :--- | :--- |
| 升級期間 | 對於大小可能超過 50 GB 的超大型資料庫，升級程序可能需要多達 24 小時。 |
| DNS 項目更新延遲 | 在升級完成後，系統需要幾分鐘的時間更新 V12 資料庫的 DNS 項目，以便能從您的用戶端應用程式連線。 |
| 無法建立資料庫 | 升級程序進行時，就無法在目的地 V12 Azure SQL Database 伺服器上使用下列建立資料庫的動作： <p></p> *建立新資料庫 <br/> *將資料庫複製到伺服器 <br/> *還原已刪除的資料庫 <br/> *將資料庫還原到某個時間點 <br/><br/> 不過，升級期間的時間點還原支援可能是 V12 預覽期間結束前支援的功能。 |
| 沒有異地複寫 | 目前正在進行從 V11 升級的 V12 伺服器不支援異地複寫。 |
| 警示規則 | 警示規則無法加入 V12 資料庫。 |


### C.3 升級至 V12 *after*的限制

下表描述在升級之後適用的限制。


| 限制 | 說明 |
| :--- | :--- |
| 指向 V11 資料庫的 DNS | 以將 V11 資料庫複製到新的 V12 伺服器的方式完成升級後，您的用戶端程式碼連線所用的 DNS 資訊就會參照 V12 資料庫。 <p> </p> 您的用戶端程式碼必須使用編輯過的 DNS 資訊，才能重新參照 V11 資料庫。 |
| 無法還原成 V11 | 在升級完成之後，就無法還原或復原結果。 |
| Web 或商務層 | 在升級開始之後，新的 V12 資料庫的伺服器就無法再辨識或接受 Web 或商務服務層。 |
| 50% 折扣不會反映在 Azure 入口網站的定價層卡片中 | 在預覽期間，在最新 Azure SQL Database Preview Update (V12) 中註冊的資料庫享有 50% 預覽折扣*。即使折扣不會顯示在預覽入口網站的服務定價層分頁，仍會一律提供折扣。<br/><br/> 在 **2015 年 3 月 31 日** 以前 (所有地區屆時便會過期)，50% 折扣在所有地理區域中仍然有效。即使在已公佈於公開上市 (GA) 狀態的區域中，此折扣仍有效。<br/><br/> (* 使用最新的 Azure SQL Database Update V12 功能時，必須遵循您的授權合約 (例如，Enterprise Agreement、Microsoft Azure Agreement 或 Microsoft Online Subscription Agreement) 中的預覽條款，以及任何適用的 [Microsoft Azure 預覽版補充使用條款](http://azure.microsoft.com/support/legal/preview-supplemental-terms/)。在預覽期間，Microsoft 將以一半的公開上市 (GA) 費率向您 (或您的轉銷商 (適用的話)) 收取所有在此預覽中註冊之資料庫的費用，以提供 50% 的預覽折扣。Microsoft 將透過電子郵件，提供預覽期間和折扣預覽費率到期前 30 天的通知。 |


### C.4 在升級至 V12 *after*匯出和匯入


您可以使用 [Azure Web 入口網站](http://portal.azure.com/) 匯出或匯入 V12 資料庫。或者，您可以使用任何下列工具進行匯出或匯入：


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- 資料層應用程式架構 (DacFx)


不過，若要使用這些工具，您必須先安裝其最新的更新，以確保它們支援新的 V12 功能：


- [SQL Server Management Studio 2014 的累積更新 6](http://support2.microsoft.com/kb/3031047)
- [適用於 Visual Studio 2013 中 SQL Server 資料庫工具的 2015 年 2 月更新](https://msdn.microsoft.com/data/hh297027)
- [適用於 Azure SQL Database V12 的 2015 年 2 月資料層應用程式架構 (DacFx) ](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 上述工具連結已於 2015 年 3 月 2 日當天或之後更新。我們建議您使用這些工具的較新更新。


#### C.4.1 匯入 bacpac 檔案時發生暫時性問題


 (迄 2015 年 2 月 18 日，星期三止)


從已升級至 V12 的 Azure SQL Database 伺服器匯出 bacpac 檔案時發生已知的問題。匯出的 bacpac 檔案將包含一個名為 script_deployment_databases 的錯誤物件。使用 SQL Server Management Studio (SSMS)、SqlPackage.exe 或資料層應用程式架構 (DacFx) API 等工具，無法匯入包含此錯誤物件的 bacpac 檔案。


不過，Azure Web 入口網站可用於將受影響的 bacpac 檔案匯入到 Azure SQL Database。我們預計在 2015 年 2 月 27 日星期五左右就此問題發行永久修正程式，包括受影響工具的更新。在過渡時期，如果需要進一步的協助來復原副檔名為 .bacpac 的已匯出檔案，請連絡 Microsoft 支援。


除了匯出問題，少數最近升級到 V12 版的伺服器和客戶在嘗試匯入 bacpac 檔案時，可能會遇到不同的錯誤。此權限相關錯誤是暫時性錯誤，通常會在一天內於受影響的伺服器上解決。我們預計此問題也將會在 2015 年 2 月 23 日星期一當週永久修正。在過渡時期，重試匯入作業可能會成功。將 bacpac 檔案匯入至 Azure SQL Database 時，如需進一步的協助，請連絡 Microsoft 支援。


如有必要，您可以遵循下列步驟來連絡 Microsoft 支援：


1. 瀏覽至 Azure 入口網站。
2. 以滑鼠右鍵按一下帳戶名稱 (位於右上角)。
3. 在顯示的內容功能表中，按一下此項目以取得支援。
 - 此項目可能標示為 [**連絡 Microsoft 支援**] 或 [**協助 + 支援**]。


> [AZURE.NOTE] (2015 年 3 月 2 日，星期一)
> 
> C.4.1 一節中所描述的問題已解決。客戶無需特殊知識或動作即可視需要繼續進行。
> 
> 通常，不論 bacpac 檔案新舊，自動化的匯入/匯出會正常運作。
> 
> 使用 DacFx 處理其 bacpac 檔案的客戶應該下載更新工具。您可以在 C.4 一節中找到下載連結。


### C.5 將刪除的 V11 資料庫還原至 V12

下列案例說明已刪除的 V11 Azure SQL Database 可以還原至 V12 Azure SQL Database 伺服器。

1. 假設您有一部 V11 Azure SQL Database 伺服器。 <br/> 在該伺服器上，您有一個 Web 和商務服務層已過時的資料庫。
2. 您刪除該資料庫。
3. 您將伺服器升級至 V12。
4. 接著，您將資料庫還原到該伺服器。 <br/> 資料庫因此會升級至 V12，「標準」服務層的 S0 層級。
5. 如果 S0 不是您偏好的選項，您可以將資料庫切換至任何支援的服務層。



## D. 失敗解決方式

如果升級因為任何異常原因而失敗，您的 V11 資料庫仍會保持作用中，並且能正常使用。


## 相關連結

- Microsoft Azure [預覽功能](http://azure.microsoft.com/services/preview/)


<!--Anchors-->
[子標題 1]: #subheading-1
<!--HONumber=47-->
 