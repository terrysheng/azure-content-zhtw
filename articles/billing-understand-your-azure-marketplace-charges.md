<properties
	pageTitle="了解您的 Azure Marketplace 費用 | Microsoft Azure"
	description="說明如何了解 Marketplace 訂單的相關費用。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cjiang"/>

# 了解您的 Azure Marketplace 費用
本文提供如何了解 Marketplace 訂單之相關費用的概觀。檢視您的帳單時需要注意的是價格類型的不同 (只有基本、只有使用量以及基本 + 使用量)。請注意，您訂購的每個 Marketplace 訂單有它自己的每月計費週期。以下將會詳細說明這些主題。

## 檢閱您的 Marketplace 帳單
登入 [Azure 帳戶入口網站](https://account.windowsazure.com/subscriptions/)之後，您可以檢閱及下載目前和過去的帳單。

若要檢視或下載帳單：

1. 使用您的 Org 識別碼，登入 Azure 帳戶入口網站。
2. 按一下 [Marketplace]。所顯示的清單是由您的 Azure 帳戶訂購的所有 Marketplace 訂單的完整清單。您會注意到訂單是依據父 Azure 訂用帳戶名稱和識別碼加以群組。![Marketplace 訂單](./media/billing-understand-your-azure-marketplace-charges/marketplace-orders.png)
3. 按一下任何顯示的訂單，將會帶您前往該訂單目前計費週期的摘要概觀。不同價格模型的說明可以在 [Marketplace 常見問題集](https://azure.microsoft.com/marketplace/faq/)中找到。![訂單摘要](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

[摘要] 檢視包含每個訂單的下列資訊：- 價格 - 計費週期 - 購買日期 - 相關聯父訂用帳戶 - 帳單貨幣

請注意，我們已經對這項資訊的顯示方式做一些變更。先前我們顯示包含稅金的訂單成本。顯示已更新為依據您所在的國家/地區顯示含稅或未稅的價格。價格的位置也已經從瀏覽的右手邊移至中間。為了協助追蹤這些費用會關聯至哪一個 Azure 訂用帳戶，我們也將 Azure 訂用帳戶名稱和識別碼新增至瀏覽的右手邊，並且新增 [變更付款方式] 選項。

## 更新付款方式和訂單管理
[摘要] 頁面中有使用者動作，可讓您更新付款模型和訂單管理功能：

> [AZURE.NOTE] 如果您使用您的組織識別碼來變更個人資訊，您需要具有支援的票證。

若要更新您的付款方式，請按一下頁面右側的 [變更付款方式] 連結。![訂單摘要](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

此連結會帶您前往不同的入口網站，您可以在那裡變更您的慣用付款方式。

若要變更您的付款方式，請遵循下列步驟：

1. 按一下 [變更您的付款方式]。![訂用帳戶](./media/billing-understand-your-azure-marketplace-charges/subscriptions.jpg)
2. 選取您想變更的付款方式。[付款方式] 選項可讓您選取您的信用卡。[新增新的支付方式] 選項可讓您新增新的信用卡。![變更付款方式](./media/billing-understand-your-azure-marketplace-charges/change-payment-method.jpg)

## 訂單管理
從 [摘要概觀] 頁面，您會看到 [管理訂單] 連結。此連結會帶您前往您的入口網站，您可以在那裡看到所有 Marketplace 訂單，檢視服務健康情況，以及要求支援。![入口網站](./media/billing-understand-your-azure-marketplace-charges/portal.jpg)

若要要求支援，按一下 [說明 + 支援] 磚。這會顯示下列 [說明 + 支援] 頁面，您可以在其中執行下列動作：- 開啟新的支援要求。- 管理現有的支援要求。- 管理資源健康狀態。

![要求支援](./media/billing-understand-your-azure-marketplace-charges/request-support.jpg)

## 帳單記錄
[摘要] 頁面上的新功能是檢視使用量型的優惠，例如虛擬機器。現在，您可以針對目前和已關閉的期間，根據計費週期下載使用量。若要檢視使用量，請按一下您想要檢視的每個期間的 [下載使用量] 連結。

如果您想要檢視實際費用，請按一下 [檢視費用]。系統會將您重新導向至不同的入口網站，您可以在那裡檢視所有含稅的費用。如果您使用組織識別碼，[檢視費用] 按鈕將不會啟用，您必須提交支援票證以要求您的費用摘要。![帳單記錄](./media/billing-understand-your-azure-marketplace-charges/billing-history.png)

若要檢視實際費用，請遵循下列步驟：

1. 按一下 [帳單記錄] 頁面上的 [檢視費用]。新的索引標籤會開啟，顯示[訂單歷程記錄](https://account.microsoft.com/billing/orders#/)。請注意，如果您嘗試在「帳單與帳戶管理入口網站」存取提取原始的使用量，您必須按一下頁面底部的支援連結以連絡支援人員。)![訂單歷程記錄](./media/billing-understand-your-azure-marketplace-charges/order-history.jpg)
2. 找出您想要檢閱的訂單，然後按一下 [詳細資料] 以檢視費用的細項，包含小計、稅金和總計費用。![訂單詳細資料](./media/billing-understand-your-azure-marketplace-charges/order-details.jpg)

<!---HONumber=AcomDC_0204_2016-->