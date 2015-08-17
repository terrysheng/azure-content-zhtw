<properties
   pageTitle="如何註冊資料來源"
   description="強調如何利用 [Azure 資料目錄] 註冊資料來源 (包括擷取的中繼資料欄位，以及預覽期間支援的資料來源) 的說明文章。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/27/2015"
   ms.author="maroche"/>


# 如何註冊資料來源

## 簡介
[Microsoft Azure 資料目錄] 是全面管理的雲端服務，可作為企業資料來源的註冊系統和探索系統。換句話說，[Azure 資料目錄] 的重點在於協助人們探索、了解，以及使用資料來源，並可協助組織從現有的資料獲得更多價值。而透過 [Azure 資料目錄] 讓資料來源得以被探索的第一步是註冊該資料來源。
## 註冊資料來源
註冊是指從資料來源擷取中繼資料並將該資料複製到 [Azure 資料目錄] 服務的程序。資料會保留在它目前的位置，並且仍然在目前系統的系統管理員及原則的控制之下。

只要從 [Azure 資料目錄] 入口網站啟動 [Azure 資料目錄] 資料來源註冊工具即可註冊資料來源。使用您的工作或學校帳戶 (與您用來登入入口網站時相同的 Azure Active Directory 認證) 登入，然後選取您要註冊的資料來源。請參閱[開始使用 Azure 資料目錄](data-catalog-get-started.md)教學課程，取得更多的逐步詳細資料。

資料來源一經註冊之後，[目錄] 會追蹤它的位置並為其中繼資料建立索引，使用者就能夠搜尋、瀏覽及探索資料來源，然後利用他們選擇的應用程式或工具，使用資料來源的位置來連接到資料來源。
## 支援的來源
在目前的預覽版中，[Azure 資料目錄] 支援註冊這些資料來源與物件類型：

* SQL Server Database Engine 資料表和檢視
* Oracle 資料庫資料表和檢視
* SQL Server Analysis Services 多維度的維度、量值，和 KPI
* SQL Server Analysis Services 表格式資料表
* SQL Server Reporting Services 報表

> [AZURE.NOTE]SQL Server 支援也包括 Microsoft Azure SQL Database

<br/>

> [AZURE.NOTE]SQL Server Reporting Services 支援僅限原生模式伺服器 – 尚未支援 SharePoint 模式

<br/>

## 結構化中繼資料
當註冊資料來源時，註冊工具會擷取您所選物件之結構的相關資訊，這些資訊稱為結構化中繼資料。對於所有物件，此結構化中繼資料會包含物件的位置，讓用戶端工具可以探索資料。其他的結構化中繼資料包含物件名稱和類型，以及屬性/資料行名稱和資料類型。
## 描述性中繼資料
除了從資料來源擷取的核心結構化中繼資料，資料來源註冊工具也會擷取描述性中繼資料。對於 SQL Server Analysis Services 和 SQL Server Reporting Services 而言，這是取自這些服務所公開的 Description 屬性。對於 SQL Server 而言，將擷取使用 ms\_description 擴充屬性所提供的值。對於 Oracle 資料庫而言，資料來源註冊工具會從 ALL\_TAB\_COMMENTS 檢視的 COMMENTS 資料行擷取。

除了從資料來源擷取的描述性中繼資料之外，使用者也可以使用資料來源註冊工具來輸入描述性中繼資料。使用者可以新增標記，並識別所註冊之物件的專家。所有的描述性中繼資料會與結構化中繼資料一併複製到 [Azure 資料目錄] 服務。
## 包括預覽
根據預設，只有中繼資料是擷取自資料來源並複製到 [Azure 資料目錄] 服務，但查看資料來源所包含之資料的範例，通常能夠更容易了解資料來源。

[Azure 資料目錄] 資料來源註冊工具可讓使用者在每個已註冊的資料表和檢視中包含資料的快照預覽。如果使用者選擇要在註冊期間包含預覽，註冊工具將包含每個資料表和檢視最多 20 筆記錄。此快照之後會與結構化和描述性中繼資料一同複製到 [目錄]。請注意：含有大量資料行的寬型資料表可能會在預覽中包含少於 20 筆的記錄。
## 更新註冊
註冊資料來源可以讓您使用在註冊期間擷取的中繼資料和選擇性預覽來於 [Azure 資料目錄] 中探索資料來源。如果在 [目錄] 中的資料來源需要更新 (例如，如果物件的結構描述已經變更、或應包含原先排除的資料表，或使用者想要更新在預覽中所包含的資料)，可以重新執行資料來源註冊工具。

重新註冊已註冊的資料來源會執行合併 “upsert” 作業：將會更新現有的物件，同時建立新的物件。任何由使用者透過 [Azure 資料目錄] 入口網站提供的中繼資料將會保留。

## 摘要
利用 [Azure 資料目錄] 註冊資料來源，可讓您透過將結構化和描述性中繼資料從資料來源複製到 [目錄] 服務，來更容易地探索及了解資料來源。資料來源一經註冊之後，便可以使用 [Azure 資料目錄] 入口網站加以註解、管理，及探索。

<!---HONumber=August15_HO6-->