<properties
   pageTitle="Azure 資料目錄開始使用資料目錄"
   description="Azure 資料目錄案例和功能的端對端教學課程。"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="02/08/2016"
   ms.author="derrickv"/>

# 開始使用 Azure 資料目錄

本文是 Azure 資料目錄公開預覽的案例與功能的端對端教學課程。註冊預覽版本之後，您可以依照下列步驟建立資料目錄，以及註冊、加註和探索資料來源。

## 教學課程的必要條件

開始進行本教學課程之前，您必須具備下列條件：

-	**Azure 訂用帳戶** - 如果您沒有訂用帳戶，只需要幾分鐘就可以建立免費試用帳戶。如需詳細資料，請參閱[免費試用](https://azure.microsoft.com/pricing/free-trial/)一文。
-	**Azure Active Directory** - Azure 資料目錄使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 來管理身分識別和存取權。
-	**資料來源** - Azure 資料目錄提供資料來源探索的功能，若要繼續進行本教學課程，您必須具備一或多個資料來源的存取權。本教學課程是使用 Adventure Works 範例資料庫來編寫，但如果喜歡使用您熟悉且與您角色相關的資料，您可以使用任何支援的資料來源。

## 練習 1：安裝 Adventure Works 範例資料庫

在本練習中，您可以為 SQL Server Database Engine 和 SQL Server Analysis Services 多維度安裝 Adventure Works 範例。接下來的練習中會使用這些範例。

> [AZURE.NOTE] 本練習中是選擇性的。本教學課程的其餘練習都編寫成參考 Adventure Works 範例資料庫，但是您也可以選擇略過這個練習，使用您自己的資料來源。以下是安裝 Adventure Works 的步驟。

### 安裝 Adventure Works 2014 OLTP 和資料倉儲資料庫

Adventure Works OLTP 資料庫支援一家虛構自行車製造商 (Adventure Works Cycles) 的標準線上交易處理案例，包括製造、銷售和採購。Adventure Works DW 資料庫示範如何建立資料倉儲。

這些資料庫位於 [CodePlex.com](http://msftdbprodsamples.codeplex.com/)，請遵循 [Adventure Works 2014 範例資料庫讀我檔案](https://msftdbprodsamples.codeplex.com/downloads/get/880669)中的步驟來安裝。

在本練習中，您已安裝供其餘練習使用的 Adventure Works 範例資料庫。如果您選擇略過此練習，並使用您自己的企業資料來源，請準備記下名稱、標記和其他中繼資料。

## 練習 2：註冊資料來源

在本練習中，您將使用 Azure 資料目錄註冊工具向目錄註冊您的資料來源。註冊的過程會從資料來源及其包含的資產中，擷取重要的結構化中繼資料 (例如名稱、類型和位置)，並將該中繼資料複製到目錄。資料來源及其資料會留在原地，但目錄會利用中繼資料，讓您更輕鬆探索和了解資料來源及其資料。

### 以下說明如何註冊資料來源

1.	登入 Azure 資料目錄入口網站。

    ![register1][1]

2.	向下捲動並按一下 [發行資料]。

    ![register2][2]
3.	按一下 [啟動應用程式]。
4.	在 [歡迎使用] 頁面上，按一下 [登入]，並輸入您的認證。
5.	在 [Microsoft Azure 資料目錄] 頁面上，按一下 [SQL Server]。

    ![register3][3]
6.	輸入您的 [伺服器名稱]，然後按一下 [連接]。
7.	下一頁將註冊您的資料來源的中繼資料。在此範例中，您將註冊 AdventureWorks Production 命名空間中的 **Product** 物件。作法如下：

    a.在階層樹狀結構中，按一下 [Production]。

    b.Ctrl+按一下 Product、ProductCategory、ProductDescription 和 ProductPhoto。

    ![register4][4]

    c.按一下移動選取項目箭號 (**>**)。這會將所有 Product 物件移至 [**準備註冊**] 清單。

    ![register5][5]

    d.**選擇性**：您可以**包含預覽**和**加入資料來源專家**。

    e.在 [加入標記] 中，輸入描述、相片。這會加入這些資料資產的搜尋標記。標記可協助使用者尋找已註冊的資料來源，非常有用。

    f.按一下 [註冊]。Azure 資料目錄會註冊您選取的物件。本練習中會註冊從 Adventure Works 選取的物件。

    ![register6][6]

    g.若要查看您註冊的資料來源物件，請按一下 [檢視入口網站]。在 Azure 資料目錄入口網站中，您可以在**磚**或**清單**中檢視資料來源物件。

    ![register7][7]

在本練習中，您註冊 Adventure Works 範例資料庫中的物件，讓整個組織裡的使用者可以輕鬆找到它們。在下一個練習中，您將學習如何探索已註冊的資料資產。

## 練習 3：探索已註冊的資料資產

在本練習中，您將使用 Azure 資料目錄入口網站來探索已註冊的資料資產，並檢視其中繼資料。Azure 資料目錄提供多個工具來探索資料資產，包括簡單的關鍵字搜尋、互動式篩選，以及適合「高階」使用者的進階搜尋語法。

### 以下說明如何探索已註冊的資料資產

**Azure 資料目錄**提供簡單但功能強大的搜尋語法，可讓您輕鬆建立查詢來傳回使用者需要的資料。如需 **Azure 資料目錄**的詳細資訊，請參閱＜搜尋語法參考＞。

**Azure 資料目錄**具有下列搜尋選項：

- 關鍵字搜尋
- 篩選器
- 進階的搜尋

您也可以精簡想要檢視的資料資產。**Azure 資料目錄**具有下列檢視選項：

- 檢視屬性
- 檢視資料行
- 檢視預覽

在此範例中，您將使用關鍵字搜尋。**Azure 資料目錄**搜尋有幾種查詢技巧。此範例將使用**分組**搜尋查詢。

**查詢技巧** <table><tr><td><b>技巧</b></td><td><b>使用</b></td><td><b>範例</b></td></tr><tr><td>屬性範圍</td><td>只傳回搜尋詞彙符合指定屬性的資料來源</td><td>name:product</td></tr><tr><td>邏輯運算子</td><td>使用布林值運算擴大或縮小搜尋，如本頁的＜布林運算子＞一節所述</td><td>finance NOT corporate</td></tr><tr><td>使用括弧分組</td><td>使用括弧將查詢各部分予以分組，達到邏輯隔離，尤其是結合布林運算子</td><td>name:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>比較運算子</td><td>對具有數值和日期資料類型的屬性使用比較而非等式</td><td>creationTime:&gt;11/05/14</td></tr></table>

在此範例中，您對資料資產執行**分組**搜尋，其中 name 等於 product 且 tags 等於 illustration 或 tags 等於 photo。

1.	登入 **Azure 資料目錄**入口網站。
2.	按一下 [探索]。
3.	在 [搜尋] 方塊中，輸入**分組**查詢：(tags:description OR tags:photo)。
4.	按一下搜尋圖示，或按 Enter 鍵。**Azure 資料目錄**會顯示這個搜尋查詢傳回的資料資產。

    ![search][8]

在本練習中，您使用 **Azure 資料目錄**入口網站來探索及檢視已向目錄註冊的資料資產。

## 練習 4：加註已註冊的資料來源

在本練習中，您將使用 **Azure 資料目錄**入口網站來加註先前已在目錄中註冊的資料資產。您提供的註解可補充並增強在註冊期間擷取自資料來源的結構化中繼資料，將更容易探索和了解資料資產。因為每個**資料目錄**使用者可以提供自己的註解，每一位在資料方面有所見解的使用者可以輕鬆分享註解。

### 以下說明如何加註資料資產

1.	登入 **Azure 資料目錄**入口網站。
2.	按一下 [探索]。
3.	選擇一或多個**資料資產**。在此範例中，選擇 [ProductPhoto]，並輸入「行銷資料的產品相片」。
4.	在 [描述] 中輸入，幫助其他人探索並了解為何及如何使用選取的資料資產。例如，輸入「產品影像」。您也可以加入更多的標記，並檢視資料行。
5.	現在您可以使用已加入至目錄的描述性中繼資料，嘗試搜尋和篩選來探索資料資產。

    ![annotate][9]

在本練習中，您將描述性資訊加入至已註冊的資料資產，讓目錄使用者能夠利用他們所了解的詞彙來探索資料來源。

## 練習 5：群眾外包中繼資料

在本練習中，您將與另一位使用者合作，將中繼資料加入至目錄中的資料資產。Azure 資料目錄對於註解的群眾外包作法，可讓任何使用者加入標記、描述和其他中繼資料，任何對資料資產及其用法有所見解的使用者，都能將此見解記錄下來分享給其他使用者。

> [AZURE.NOTE] 如果沒有另一位使用者可配合您一起進行本教學課程，請別擔心！ 任何存取資料目錄的使用者都可以選擇加入自己的觀點。這種關於中繼資料的群眾外包方法，可讓目錄的內容和目錄中繼資料的豐富性隨著時間不斷成長。

### 以下說明您如何讓群眾外包資料資產的中繼資料

請同事重複上述的**加註已註冊的資料來源**練習。當您的同事將描述加入至資料資產之後，例如 ProductPhoto，您就會看到多個註解。


![crowdsource][13]

在本練習中，您探索 Azure 資料目錄的中繼資料群眾外包功能，任何目錄使用者都可以加註他找到的資料資產。


## 練習 6：連接到資料來源

在本練習中，您將使用 **Azure 資料目錄**入口網站，透過 Microsoft Excel 連接到資料來源。


> [AZURE.NOTE] 請務必記得，**Azure 資料目錄**不會讓使用者存取實際的資料來源，它只是讓使用者更容易探索和了解它們。當使用者連接到資料來源時，他們所選擇的用戶端應用程式會使用其 Windows 認證，或在必要時提示他們提供認證。如果先前未授權使用者存取資料來源，則他必須先獲得授權才能連線。

### 以下說明如何從 Excel 連接到資料來源

1.	登入 **Azure 資料目錄**入口網站。
2.	按一下 [探索]。
3.	選擇資料資產。在此範例中，選擇 [ProductCategory]。
4.	選擇 [開啟於] > [Excel]。

    ![connect1][10]
5.	在 [Microsoft Excel 安全性注意事項] 視窗中，按一下 [啟用]。
6.	開啟 **ProductCategory.odc** 檔案。
7.	資料來源會在 Excel 中開啟。

    ![connect2][11]

在本練習中，您連接到使用 Azure 資料目錄找到的資料來源。**Azure 資料目錄**入口網站可讓使用者利用已整合到 [開啟於] 功能表的用戶端應用程式來直接連接，還可讓使用者透過他們選擇的任何應用程式，使用包含在資產中繼資料中的連接位置資訊來連接。

## 練習 7：移除資料來源中繼資料

在本練習中，您將使用 **Azure 資料目錄**入口網站從已註冊的資料資產中移除預覽資料，並從目錄中刪除資料資產。

> [AZURE.NOTE] 目錄的預設行為是允許任何使用者註冊任何資料來源，並允許任何使用者刪除任何已註冊的資料資產。**標準版 Azure 資料目錄**中包含的管理功能提供其他選項，可用來取得資產的所有權、限制誰可以探索資產，以及限制誰可以刪除資產。

在 **Azure 資料目錄**中，您可以刪除個別資產或多個資產來移除預覽。

### 以下說明如何刪除多個資料資產

1.	登入 **Azure 資料目錄**入口網站。
2.	按一下 [探索]。
3.	選擇一或多個資料資產。
4.	按一下 [刪除]。

在本練習中，您從目錄移除已註冊的資料資產。

## 練習 8：管理已註冊的資料來源

在本練習中，您將使用 **Azure 資料目錄**的管理功能取得資料資產的擁有權，並控制哪些使用者可以探索和管理這些資產。

注意：本練習所述的管理功能只在標準版 Azure 資料目錄中提供，免費版本中沒有提供。在 **Azure 資料目錄**中，您可以取得資料資產的擁有權、新增資料資產的共同擁有者，以及設定資料資產的可見性。

### 以下說明如何取得資料資產的擁有權和限制可見性

1.	登入 **Azure 資料目錄**入口網站。
2.	按一下 [探索]。
3.	選擇一或多個資料資產。
4.	在 [屬性] 面板的 [管理] 區段中，按一下 [取得擁有權]。
5.	若要限制可見性，請按一下 [擁有者與這些使用者]。

    ![ownership][12]

在本練習中，您探索目錄的管理功能，以及選取的資料資產的受限制可見性。

## 摘要

在本教學課程中，您已瀏覽 **Azure 資料目錄**預覽的基本功能，包括註冊、註解、探索和管理企業資料來源。既然您已經完成本教學課程，現在可以開始使用。您可以立即開始註冊您和小組所依賴的資料來源，並邀請同事使用目錄。


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png
[13]: ./media/data-catalog-get-started/crowdsource.png

<!---HONumber=AcomDC_0211_2016-->