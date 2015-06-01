<properties
   pageTitle="了解 Azure 帳單"
   description="了解 Azure 帳單"
   services=""
   documentationCenter="Azure"
   authors="kareni"
   manager="jocho"
   editor=""
   tags="billing"/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/07/2015"
   ms.author="kareni"/>


# 了解 Microsoft Azure 帳單

Microsoft Azure 訂用帳戶的費用會因費率方案而異。某些費率方案 (例如 Visual Studio Ultimate 含 MSDN) 包含您可以根據需求用於 Azure 服務的每月信用額度。

請注意，先前計費期間的最多 24 小時潛在使用量可以列於您的目前計費期間。

如需有關耗用量、使用量以及費率方案的詳細資訊，請參閱 [Microsoft Azure 購買選項頁面](http://azure.microsoft.com/pricing/purchase-options/)。

## 內容：

本主題將協助您在讀取帳單時進行下列工作。

-  檢視或下載 Azure 帳單
-  客戶資訊
-  了解發票摘要
-  了解目前費用
-  頁尾資訊
-  了解其他資訊
-  了解詳細的使用費用
-  分析每日使用狀況資料

### 檢視或下載 Microsoft Azure 帳單

在 [[使用量和計費入口網站](https://account.windowsazure.com/subscriptions)] 中，您可以檢視目前的帳單並下載過去的帳單。

檢視或下載帳單：

1. 使用 Microsoft 帳戶識別碼或工作或學校帳戶識別碼登入 [[使用量和計費入口網站](https://account.windowsazure.com/subscriptions)]。

2. 按一下您想要查看詳細資料和使用狀況的訂用帳戶。

3. 按一下 [**帳單**] 記錄

    ![摘要 - 帳單記錄 - 1](./media/azure-billing-understand-your-bill/ContentViewaBillforMA1.png)


4. [**帳單記錄**] 區段會列出您最後 6 個計費週期再加上目前未開立帳單期間的對帳單。目前期間的對帳單是產生估計時的估計費用。此資訊每天只會更新一次，而且可能不包含截至本日為止的所有使用量。您的每月帳單可能會與這項估計有所不同。

    ![摘要 - 帳單記錄 2](./media/azure-billing-understand-your-bill/ContentViewaBillforMA2.png)

5. 按一下 [**檢視目前對帳單**] 以檢視產生估計時的估計費用。此資訊每天只會更新一次，而且可能不包含截至本日為止的所有使用量。您的每月帳單可能會與這項估計有所不同。

    ![摘要 - 帳單記錄 3](./media/azure-billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![摘要 - 帳單記錄 4](./media/azure-billing-understand-your-bill/ContentViewaBillforMA4.png)

6. 按一下 [**下載發票**] 以檢視先前帳單的副本。

    ![摘要 - 帳單記錄 5](./media/azure-billing-understand-your-bill/ContentViewaBillforMA5.png)



**以下是 Visual Studio Ultimate 含 MSDN 和 Pay-As-You-Go 優惠的發票範例。**

*任何未使用的每月信用額度無法延續到後續月份。

 **優惠名稱** | **優惠類型** | **包含免費服務** | **下載** |
 :--------- |:-------- | :----------------: | :-------|
Azure MSDN-Visual Studio Ultimate| 權益方案 | 是* | [範例檔案](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_MSDN_Sample.pdf)
Pay-As-You-Go | 耗用量 | 否| [範例檔案](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_PAYG_Sample.pdf)
詳細的使用量 - csv | N/A | N/A | [範例檔案](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_Detailed_Usage_v1_csv.xlsx)


## 標頭 - 客戶資訊

客戶資訊區段可識別使用量和設定檔的相關資訊。![頁首](./media/azure-billing-understand-your-bill/Header.png)

### 發票編號
可用於追蹤的唯一發票識別碼

### 計費週期
發生使用量的時間範圍。

### 發票日期
產生發票的日期。

### 付款方法
帳戶 (亦即發票或信用卡) 上所使用的付款類型。

### 帳單收件者
Microsoft Azure 付款位址。

### 帳戶擁有者的電子郵件
註冊 Microsoft Azure 時所使用的帳戶電子郵件地址。



## 了解發票摘要
帳單的 [發票摘要] 區段會摘要說明上次帳單之後的交易以及目前的使用費用。

![發票摘要](./media/azure-billing-understand-your-bill/InvoiceSummary.png)

帳單的 [結餘、付款與其他信用額度] 區段會摘要說明上次帳單之後的交易。

### 前期結餘
前期結餘是上次帳單的應付總金額。

### 付款
付款是套用至上次帳單的總付款金額。

### 未付餘額 (從上一個計費週期)
上次帳單之後套用至您帳戶的任何帳單調整 (信用額度或結餘)。


## 了解目前費用
帳單的 [目前費用] 區段包含有關每月費用的詳細資料。連結分為下列各節。

### 使用費用
使用費用是訂用帳戶的每月總費用。我們會根據您過去一個月的使用量事後向您收取費用。

### 折扣
您的使用量服務折扣會反映在套用至您目前帳單的這個行項目中。

### 調整
其他調整是套用至您目前帳單的其他信用額度或未付費用。例如，如果您享有 Visual Studio Ultimate 含 MSDN 優惠，您會在這個行項目中看到每月信用額度。如果您取消訂用帳戶，您會看到超過您優惠所含每月信用額度 (從您目前計費期開始至訂用帳戶取消日期為止) 的每月使用費用。

## 頁尾資訊
![頁尾](./media/azure-billing-understand-your-bill/footerinformation.png)

## 了解其他資訊
[其他資訊] 頁面提供可了解發票的其他資源參考，以及可檢視您使用量的連結，以及帳單的其他相關資訊。

![其他資訊](./media/azure-billing-understand-your-bill/AdditionalInformation.png)

### 詳細的使用量
在 [詳細的使用量] 描述中的連結會將您引導至 Azure 使用量和計費入口網站，您可以在此檢視此訂用帳戶的詳細使用量。

### 其他資訊和有用資源
此區段包含關於運算執行個體大小、SQL DB 費用等簡單問題的連結，以及可幫助您進一步回答問題的有用連結。

### 買方
這會預先填入帳戶的設定檔地址。

### 付款指示
本章節包含當您的付款方法是發票時，有關寄送支票、電匯或隔天送達支票等付款指示。

## 了解詳細的使用費用

使用費用是訂用帳戶的每月總費用扣除任何信用額度或折扣。我們會根據您過去一個月的使用量事後向您收取費用。當您檢視發票上的使用費用清單時，下列資料行會顯示您要支付項目的詳細資料。您可以下載[範例檔案](https://understandingyourbill.blob.core.windows.net/appendices/UnderstandDetailedUsageCharges.xlsx)，它會顯示每個資料行的值。

### 服務名稱
識別這個使用所屬的最上層服務。

### 服務類型
Azure 服務可能會在此資料行中透過類型進一步定義，這可能會影響費率。

### 資源
識別耗用資源的度量單位。

### 區域
使用所套用且主要與雲端服務和虛擬機器和資料傳輸 (不包括 CDN) 相關的區域，因為這些費率可能會因地區而異。CDN 的區域會對應至提供服務流量的資料中心位置。

### 已耗用
包含計費期間所耗用的資源量。

### 已包括
識別您的優惠每個月所提供的數量。

### 可計費
如果已耗用數量超過所提供的數量，則此資料行會顯示差異。我們會針對此數量向您收費。若是不提供任何數量的隨收隨付優惠，則這個總數會與已耗用的數量相同。

### 費率
費率會顯示根據每個可計費單位向您收費的費率。

### 值
顯示將可計費資料行乘以費率資料行的結果。如果已耗用數量未超過所提供的數量，則此資料行中將不會有任何費用。

## 分析每日使用狀況資料
視您的使用量而定，每日使用量資料可能會有數以千計的資料行。如果您想要分析這項資料，請按一下 [下載使用量] 以將目前計費期間的每日使用量資料匯出成以逗號分隔的變數檔案 (CSV)，以便稍後在 Microsoft Office Excel 和其他程式中進行檢視。您可以下載範例 CSV 檔案以供參考。


![摘要 PAYG](./media/azure-billing-understand-your-bill/AnalyzeDailyUsageData1.png)

在 CSV 檔案中會細分項目，以顯示目前計費週期內每個資源的耗用量清單。

![csv 快照集](./media/azure-billing-understand-your-bill/csvsnapshotportal.png)

下列資料行會顯示在開始計費期間時影響費率的詳細資料：

**資料行名稱** | **描述** |
:---------------| :----------------|
計費期間 | 使用資源時的計費期間。
名稱 | 識別所使用的服務名稱。
類型 | 在某些情況下，Azure 服務可能會在此資料行中透過類型進一步定義，這可能會影響費率。
資源 | 識別已耗用的資源類型。例如，資料傳輸、計算時數和儲存體交易為資源類型。
區域 | 針對根據資料中心位置進行定價的某些服務 (例如，資料傳輸)，識別資料中心的位置。
SKU | 識別每個 Azure 資源的唯一系統識別碼。
單位 | 識別服務的計費單位。例如，GB、小時、10,000 秒
已耗用 | 包含當日已耗用的資源量。
已包括 | 包含目前計費期間免費提供的資源量。
可計費 | 包含目前計費期間可計費的資源量。
承諾用量期間 | 包含從您的 6 或 12 個月優惠相關承諾用量中遞減的資源費用。請注意，您的資源費用會依時間先後順序從承諾用量中進行遞減。
貨幣 | 識別會反映在您目前計費期間的貨幣。
超額部分 | 包含超過您的 6 或 12 個月優惠相關承諾用量的資源費用。
承諾用量費率 | 包含根據您的 6 或 12 個月優惠相關總承諾用量的承諾用量費率。
費率 | 包含資源的 Pay-As-You-Go 費率。
值 | 將費率乘以可計費資料行可計算出延伸成本。

如需這些資料行的詳細描述，請參閱**使用費用**一節中的上述資料行完整說明。下列資料行包含可能會很有幫助的其他資訊。視資源而定，其中某些資料行可能是空的。

### 使用日期
發出使用的日期

### ResourceGUID
計費器的 GUID

### 子區域
識別省略服務的特定位置 (亦即資料中心位置)。

### 服務
您可以利用這個資料行來追蹤可能無法在名稱資料行中特別識別的個別 Azure 平台服務。此服務資料行會指出與使用有關的特定服務。

### 元件
在適用的情況下，此資料行會進一步識別所耗用的資源。例如，若是裝載的計算服務，此資料行會報告虛擬機器的大小 (如果資源中尚未陳述)。

### 服務資訊 1
此資料行會提供訂用帳戶上服務所屬的專案名稱

### 服務資訊 2
此資料行會擷取協力廠商的特定資訊。

### 其他資訊
此資料行會顯示部分服務的額外資料。

如需詳細資訊，請參閱這些其他來源的資訊檔案。這些檔案會每月更新，並於下個月的 25 號發佈，從 2015 年 5 月開始提供使用。以下是服務下載欄位的基底檔案 URL：

>>  https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_v1_MMYYYY.xlsx


若要尋找最新版本，請輸入年和月 (若要尋找 2015 年 5 月的檔案，請在上述 URL 的區段 _**"MMYYYY"**_ 中輸入 **052015**)。此試算表提供 [**使用量下載報告**] 中服務相關欄位的所有可能組合清單。


<!--Image references-->

<!--HONumber=52-->
