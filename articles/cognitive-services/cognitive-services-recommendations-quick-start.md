<properties
	pageTitle="快速入門指南：Machine Learning 建議 API | Microsoft Azure"
	description="Azure Machine Learning Recommendations - 快速入門手冊"
	services="cognitive-services"
	documentationCenter=""
	authors="luisca"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="luisca"/>

# Cognitive Services Recommendations API 的快速入門指南

本文件說明如何裝載您的服務或應用程式，以使用 [Recommendations API](http://go.microsoft.com/fwlink/?LinkId=759710)。您可以在[這裡](http://go.microsoft.com/fwlink/?LinkId=759709)找到更多有關 Recommendations API 和其他 Cognitive Services 的詳細資料。在本指南中，您可能也會發現 [Recommendations API 參考](http://go.microsoft.com/fwlink/?LinkId=759348)非常實用。


<a name="Overview"></a>
## 一般概觀

本文件是一份逐步解說指南。我們的目標是逐步引導您進行訓練模型所需的步驟，並為您指點可讓您從生產環境取用模型的資源。

本練習需要大約 30 分鐘。

若要使用 [Recommendations API](http://go.microsoft.com/fwlink/?LinkId=759710)，您需要執行下列步驟：

1. 建立模型 - 模型是使用資料、目錄資料和建議模型的容器。
1. 匯入類別目錄資料 - 類別目錄包含有關項目的中繼資料資訊。
1. 匯入使用資料 - 上傳使用資料的方式有 2 種 (使用其中一種或兩種皆用)：
  -  上傳包含使用資料的檔案。
  -  傳送資料擷取事件。通常您會上傳使用方式檔案以便建立初始建議模型 (啟動程序)，並使用這個模型，直到系統收集採用資料擷取格式的足夠資料為止。
1. 建置建議模型 - 這是非同步作業，建議系統會接受所有使用資料並建立建議模型。視資料大小和組建組態參數而定，這項作業可能需要數分鐘或數小時的時間。當觸發組建時，您會取得一個組建識別碼。請在開始取用建議之前，使用它來查看建置流程何時結束。
1. 取用建議 - 取得特定項目或項目清單的建議。

<a name="GetStarted"></a>
### 現在就開始吧！

一開始先建置建議模型。然後我們將引導您如何使用模型對於電子商務網站上的電源建議所產生的結果。

<a name="Ex1Task1"></a>
#### 工作 1 - 註冊 Recommendations API ####

在這個工作中，您將會註冊 Recommendations API 服務，並建立建議模型。

1. 移至 **Azure 入口網站** (位於 [http://portal.azure.com/](http://portal.azure.com/))，然後以您的 Azure 帳戶登入。

1.  按一下 [+ 新增]。

1. 選取 [智慧] 選項。

1. 選取 [Cognitive Services APIs] 產品。此產品將可讓您針對任意的 Cognitive Services API (字體、文字分析、電腦版本等) 啟動訂用帳戶。我們今天會將重點放在 Recommendations API。

1. 在 Cognitive Services API 的登陸頁面上，輸入建議訂用帳戶的**帳戶名稱**(例如："MyRecommendations")。此名稱不應含有任何空格。

1. 在 [API 類型] 中，選取 [建議]。

1. 您可以在 [定價層] 中選取一個方案。您可以選取 [免費層]，每月 10,000 筆交易**。此為免費方案，因此很適合用來開始試用此系統。一旦您移至生產環境之後，建議您考慮要求的數量，並據此變更方案類型。

1. 選取 [資源群組]，或者，如果您還沒有群組，請建立一個新的。

1. 您可以變更 [建立] 對話方塊中的其他項目。我們必須強調，目前只能從美國資料中心支援資源提供者。

1. 一旦完成任何選取項目之後，按一下 [建立]。

1. 部署資源期間，請等候數分鐘。完成部署之後，您可以移至 [設定] 刀鋒視窗中的[金鑰] 區段 ，其中將會為您提供要使用 API 的主要和次要金鑰。複製主金鑰，因為您在建立第一個模型時需要用到它。

<a name="Ex1Task2"></a>
#### 工作 2 - 您是否已擁有資料？ ####

Recommendations API 將可從您的類別目錄和交易進行了解，以便提供良好的產品建議。這表示，您需要將良好的產品相關資料 (我們稱此為**類別目錄**檔案) 饋送給它，以及一組對其而言夠大的交易來尋找有興趣的取用模式 (我們稱之為**使用方式**)。

1. 通常您會針對這些部分的資訊來查詢交易資料庫。萬一您手邊沒有它們，我們會根據 Microsoft 市集交易資料，為您提供一些範例資料。

 您可以從[這裡](http://aka.ms/RecoSampleData)下載資料。複製 MsStoreData.Zip 並解壓縮到本機電腦上的資料夾。

 > **注意：**您將在工作 3 中下載並執行的範例程式碼已經內嵌範例資料，所以此工作是選擇性的。話雖如此，此工作將可讓您下載更實際的資料集，並讓您更加了解對於 Recommendations API 的輸入。

1.	現在讓我們看一下類別目錄檔案。瀏覽至您複製資料的位置。在**記事本**中開啟類別目錄檔案。

 您會注意到類別目錄檔案相當簡單。其具備下列格式 `<itemid>,<item name>,<product category>`

 >  AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office <br> AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office <br> C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 - Camel,Accessories

 我們應該註明類別目錄檔案可能更豐富，比方說，您可以新增產品相關的中繼資料 (我們稱之為「項目特徵」)。您應該查看＜API 參考＞中的[類別目錄格式](http://go.microsoft.com/fwlink/?LinkID=760716)一節，以取得更多有關類別目錄格式的詳細資料。

1. 讓我們利用使用方式資料來執行相同動作。您會注意到，使用方式日期的格式是 `<User Id>,<Item Id>`。

  > 00030000A11B024B,GZA-00202<br> 0003BFFD93B934B7,P2W-00004<br> 000300009C01C881,W6F-00121<br> 00060000AF0D2B04,QR2-00011<br>

 這是有效使用方式檔案所需的最基本資料。更複雜的使用方式檔案可能包含每一筆交易的其他資訊，包括交易的時間戳記及所發生的事件類型(按一下、購買等)。如需這個主題的詳細資訊，您可以參閱[使用方式格式](http://go.microsoft.com/fwlink/?LinkID=760712) 。

 > **您需要多少資料？**
 <p>
>  其實它真正相依於本身的使用方式資料。系統可以在使用者購買不同項目時進行學習。針對某些像是 FBT 的組建，請務必了解在相同交易中購買了哪些項目(我們稱之為*共同項目*)。好的經驗法則是讓大部分的項目位於 20 個以上的交易中，因此，如果您的類別目錄中有 10,000 個項目，我們建議您擁有的至少是該交易數目的 20 倍或大約 200,000 個交易。再次強調，此為經驗法則。您必須使用您的資料來試驗。
> </p>

<a name="Ex1Task3"></a>
#### 工作 3 - 建立建議模型 ####

現在您已擁有帳戶和資料，讓我們來建立第一個模型。

在這個工作中，您將使用範例應用程式來建置第一個模型。

1. 您首先應該注意的是 [Recommendations API 參考](http://go.microsoft.com/fwlink/?LinkId=759348)。

1. 將[範例應用程式](http://go.microsoft.com/fwlink/?LinkID=759344)下載到本機資料夾。

1. 在 Visual Studio 中開啟 **RecommendationsSample.sln** 方案(位於 **C#** 資料夾)。

1. 開啟 **SampleApp.cs** 檔案。請注意檔案中的下列步驟︰
 + 建立模型
 + 新增類別目錄檔案
 + 新增使用方式檔案
 + 觸發模型的組建
 + 根據一組項目配對來取得建議
<p></p>

1. 使用您的電子郵件與工作 1 的金鑰來取代 **accountEmail** 和 **accountKey** 欄位的值。

1. 逐步執行方案，您將會看到建立模型的方式。

1. 嘗試取代您剛剛下載的類別目錄和使用方式檔案，以便為 Microsoft 市集或預約建議建立新模型。您也必須變更模型名稱，以及您要求建議的項目。

1. 建立模型時，請記下**模型識別碼**，因為當您在生產環境中要求建議時需要用到它。

<a name="Ex1Task4"></a>
### 將您的模型放入生產環境中！ ###

既然您了解如何建立模型並取用建議，下一個步驟就是將它放入網站上的生產環境、行動裝置應用程式，或整合到 CRM 或 ERP 系統。很明顯地，這其中每一個實作都不同。由於 Recommendations API 是以 Web 服務形式來要求，因此，您應該能夠輕鬆地從這些不同環境中的任一個呼叫它。

**重要事項︰**如果您想要顯示來自公用用戶端 (例如電子商務網站) 的建議，您應該建立 Proxy 伺服器來提供建議。這點很重要，如此一來您的 API 金鑰就不會公開至外部 (可能不受信任) 的實體。

以下是一些您可在其中使用 Recommendations 的位置概念：

### 產品頁面

**項目建議**
<p>如果模型受過購買資料的相關訓練，將可讓您的客戶*探索很可能會讓已購買過原始項目的人感興趣的產品*。</p>
<p>如果模型受過按一下資料的相關訓練，將可讓您的客戶*探索很可能會讓已造訪過原始項目的人造訪的產品*。這種類型的模型可能會傳回類似的項目。</p>

**人氣組合建議**
<p>人氣組合組建是可以訓練的，因此，您就能讓項目組合可望與此項目一起購買。</p>

### 結帳頁面

**項目建議**
<p>建議模型可能需要取得項目清單做為輸入。因此您可以傳遞購物籃中的所有項目做為輸入來取得建議。在此情況下，模型會提供對於購物籃中所有項目特別有興趣的建議。
</p>

### 登陸頁面

**使用者建議**
<p>
建議模型可以採用使用者識別碼做為輸入。這將使用該使用者的交易歷程記錄，為指定的使用者提供個人化建議。
</p>

請參閱[取得項目建議文件](http://go.microsoft.com/fwlink/?LinkID=760719)。

<a name="Ex1Task6"></a>
### 後續步驟
如果您已進展至此，恭喜您！ 若要深入了解，您可以瀏覽完整的 [Recommendations API 參考](http://go.microsoft.com/fwlink/?LinkId=759348) 如果您有任何疑問，請隨時與我們連絡：mlapi@microsoft.com

<!---HONumber=AcomDC_0330_2016-->