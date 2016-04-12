<properties
   pageTitle="Azure 資料目錄支援的資料來源 | Microsoft Azure"
   description="目前支援的資料來源的規格。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager=""
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/30/2016"
   ms.author="maroche"/>

# Azure 資料目錄支援的資料來源

Azure 資料目錄的使用者可以使用公用 API、Click Once 註冊工具，或手動將資訊直接輸入至資料目錄 Web 入口網站，發佈中繼資料。以下方格摘要說明目前目錄支援的所有來源，以及針對每個來源的發佈功能。也列出每個來源可以從我們的入口網站「開啟」經驗啟動的外部資料工具。接下來是第二個方格，有每個資料來源連接屬性的更詳細技術規格。


## 支援的資料來源的清單

<table>

    <tr>
       <td><b>資料來源物件</b></td>
       <td><b>API</b></td>
       <td><b>手動輸入</b></td>
       <td><b>註冊工具</b></td>
       <td><b>開啟工具</b></td>
       <td><b>注意事項</b></td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區目錄</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區檔案</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 儲存體 Blob</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Azure 儲存體目錄</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS 目錄</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>HDFS 檔案</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Hive 檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>MySQL 檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 資料庫資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Oracle 資料庫檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>其他 (一般資產)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 資料倉儲資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL 資料倉儲檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 維度</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 量值</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services 報表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>[瀏覽器]</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server 資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server 檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel、PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 資料表</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Teradata 檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 檢視</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 資料表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Db2 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>檔案系統檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 目錄</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ftp 檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 報告</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 結束點</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Http 檔案</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 實體集</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Odata 函式</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 資料表</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Postgresql 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SAP Hana 檢視</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Salesforce 物件</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SharePoint 清單 </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>


<br> <br>
## 資料來源參考規格

<table>
    <tr>
       <td><b>來源類型</b></td>
       <td><b>根類型</b></td>
       <td><b>物件類型</b></td>
       <td><b>包含於物件類型</b></td>
       <td><b>DSR 結構<b></td>
    </tr>


    <tr>
      <td>Azure 資料湖存放區</td>
      <td>資料表</td>
      <td>目錄</td>
      <td>資料湖</td>
      <td>
        <font size=2>通訊協定：webhdfs
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>資料湖</td>
      <td>
        <font size=2>通訊協定：webhdfs}
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>



    <tr>
      <td>SQL Server Reporting Services</td>
      <td>容器</td>
      <td>伺服器</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：reporting-services
            <br>驗證：{windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 版本 {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Azure 儲存體</td>
      <td>資料表</td>
      <td>Blob</td>
      <td>容器</td>
      <td>
        <font size=2>通訊協定：azure-blobs
            <br>驗證：{azure-access-key}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 網域
            <br>&#160;&#160;&#160;&#160;&#160; 帳戶
            <br>&#160;&#160;&#160;&#160;&#160; 容器
            <br>&#160;&#160;&#160;&#160;&#160; 名稱</font>
      </td>
    </tr>

    <tr>
      <td>Azure 儲存體</td>
      <td>資料表</td>
      <td>目錄</td>
      <td>容器</td>
      <td>
        <font size=2>通訊協定：azure-blobs
            <br>驗證：{azure-access-key}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 網域
            <br>&#160;&#160;&#160;&#160;&#160; 帳戶
            <br>&#160;&#160;&#160;&#160;&#160; 容器
            <br>&#160;&#160;&#160;&#160;&#160; 名稱</font>
      </td>
    </tr>

    <tr>
      <td>Azure 儲存體</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>Azure 儲存體</td>
      <td>
        <font size=2>通訊協定：azure-tables
            <br>驗證：{azure-access-key}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 網域
            <br>&#160;&#160;&#160;&#160;&#160; 帳戶
            <br>&#160;&#160;&#160;&#160;&#160; 名稱</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>容器</td>
      <td>模型</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>
            <br>*基本/匿名只能透過 https 使用* </font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：db2
            <br>驗證：{基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：db2
            <br>驗證：{基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Db2</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：db2
            <br>驗證：{基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>檔案系統</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>伺服器</td>
      <td>
        <font size=2>通訊協定：檔案
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 路徑</font>
      </td>
    </tr>

    <tr>
      <td>Ftp</td>
      <td>資料表</td>
      <td>目錄</td>
      <td>伺服器</td>
      <td>
        <font size=2>通訊協定：ftp
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Ftp</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>伺服器</td>
      <td>
        <font size=2>通訊協定：ftp
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 資料表</td>
      <td>容器</td>
      <td>模型</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>
            <br>*基本/匿名只能透過 https 使用* </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>資料表</td>
      <td>目錄</td>
      <td>叢集</td>
      <td>
        <font size=2> 通訊協定：webhdfs
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>叢集</td>
      <td>
        <font size=2> 通訊協定：webhdfs
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure 資料湖存放區</td>
      <td>容器</td>
      <td>資料湖</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：webhdfs
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：hive
            <br>驗證：{hdinsight、基本、使用者名稱}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：hive
            <br>驗證：{hdinsight、基本、使用者名稱}
            <br>位址：伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; connectionProperties
            <br>&#160;&#160;&#160;&#160;&#160; serverprotocol: {hive2} </font>
      </td>
    </tr>

    <tr>
      <td>Azure 儲存體</td>
      <td>容器</td>
      <td>Azure 儲存體</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：azure-tables
            <br>驗證：{azure-access-key}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 網域
            <br>&#160;&#160;&#160;&#160;&#160; 帳戶</font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>容器</td>
      <td>網站</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：http
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>報告</td>
      <td>報告</td>
      <td>網站</td>
      <td>
        <font size=2>通訊協定：http
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>資料表</td>
      <td>結束點</td>
      <td>網站</td>
      <td>
        <font size=2>通訊協定：http
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Azure 儲存體</td>
      <td>容器</td>
      <td>容器</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：azure-blobs
            <br>驗證：{Azure-Access-Key}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 網域
            <br>&#160;&#160;&#160;&#160;&#160; 帳戶
            <br>&#160;&#160;&#160;&#160;&#160; 容器</font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：mysql
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：mysql
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Hadoop Distributed File System</td>
      <td>容器</td>
      <td>叢集</td>
      <td>N/A</td>
      <td>
        <font size=2> 通訊協定：webhdfs
            <br>驗證：{基本、oauth}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Http</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>網站</td>
      <td>
        <font size=2>通訊協定：http
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>容器</td>
      <td>實體容器</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：odata
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>Hive</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：hiveserver2
            <br>驗證：{hdinsight、基本、使用者名稱、無}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 連接埠
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>資料表</td>
      <td>實體集</td>
      <td>實體容器</td>
      <td>
        <font size=2>通訊協定：odata
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; 資源</font>
      </td>
    </tr>

    <tr>
      <td>Oracle 資料庫</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：oracle
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Oracle 資料庫</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：oracle
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Odata</td>
      <td>資料表</td>
      <td>函式</td>
      <td>實體容器</td>
      <td>
        <font size=2>通訊協定：odata
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; 資源</font>
      </td>
    </tr>

    <tr>
      <td>其他</td>
      <td>資料表</td>
      <td>其他</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：generic-asset
            <br>驗證：{無、基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：postgresql
            <br>驗證：{基本、windows}
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：postgresql
            <br>驗證：{基本、windows}
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Postgresql</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：postgresql
            <br>驗證：{基本、windows}
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>MySQL</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：mysql
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>伺服器</td>
      <td>
        <font size=2>通訊協定：sap-hana-sql
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Salesforce</td>
      <td>資料表</td>
      <td>Object</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：salesforce-com
            <br>驗證：{基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>

    <tr>
      <td>SharePoint</td>
      <td>資料表</td>
      <td>列出</td>
      <td>網站</td>
      <td>
        <font size=2>通訊協定：sharepoint-list
            <br>驗證：{基本、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>

    <tr>
      <td>SQL 資料倉儲</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>SQL 資料倉儲</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>資料表</td>
      <td>資料表值函數</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 結構描述
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Oracle 資料庫</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：oracle
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>模型</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; objectType：{Kpi}
            <br>
            <br>*基本/匿名只能透過 https 使用* </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>模型</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; objectType：{計量} </font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services</td>
      <td>資料表</td>
      <td>維度</td>
      <td>模型</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; objectType：{維度} </font>
      </td>
    </tr>

    <tr>
      <td>SAP Hana</td>
      <td>容器</td>
      <td>伺服器</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：sap-hana-sql
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services 資料表</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>模型</td>
      <td>
        <font size=2>通訊協定：analysis-services
            <br>驗證：{windows、基本、匿名}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 模型
            <br>&#160;&#160;&#160;&#160;&#160; 物件
            <br>&#160;&#160;&#160;&#160;&#160; objectType：{資料表}
            <br>
            <br>*基本/匿名只能透過 https 使用* </font>
      </td>
    </tr>

    <tr>
      <td>SQL 資料倉儲</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：tds
            <br>驗證：{sql、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services</td>
      <td>報告</td>
      <td>報告</td>
      <td>伺服器</td>
      <td>
        <font size=2>通訊協定：reporting-services
            <br>驗證：{windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 路徑
            <br>&#160;&#160;&#160;&#160;&#160; 版本 {ReportingService2010} </font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>N/A</td>
      <td>
        <font size=2>通訊協定：teradata
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫</font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：teradata
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>

    <tr>
      <td>Teradata</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>資料庫</td>
      <td>
        <font size=2>通訊協定：teradata
            <br>驗證：{通訊協定、windows}
            <br>位址：
            <br>&#160;&#160;&#160;&#160;&#160; 伺服器
            <br>&#160;&#160;&#160;&#160;&#160; 資料庫
            <br>&#160;&#160;&#160;&#160;&#160; 物件</font>
      </td>
    </tr>



</table>

<!---HONumber=AcomDC_0330_2016-->