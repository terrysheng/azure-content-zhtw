<properties
   pageTitle="移轉 Azure 訂用帳戶 | Microsoft Azure"
   description="如何將 Azure 訂用帳戶轉移到另一位使用者和關於程序的一些常見問題集 (FAQ)"
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/21/2015"
   ms.author="curtand;kareni;ruchic"/>

# 移轉 Azure 訂用帳戶

您是否：

- 需要將 Azure 訂用帳戶的帳單擁有權交給其他人？
- 想要變更用來註冊 Azure 的帳戶？ 或許您是使用 Microsoft 帳戶，但原本是想要使用您的公司或學校帳戶？
- 想要將您的 Azure 訂用帳戶移到另一個目錄？
- Azure 和 Office 365 在不同的租用戶中，想要合併？

您現在可以針對隨用隨付、MSDN、行動套件或 BizSpark 訂用帳戶，輕鬆地在 Microsoft Azure 帳戶中心執行此動作。我們已經可讓您將您的訂用帳戶轉移給另一位使用者。換句話說，您現在可以在所擁有的任何隨用隨付、MSDN、行動套件或 BizSpark 訂用帳戶上變更帳戶管理員，而不論您是在哪個國家/地區進行操作。

## 如何移轉 Azure 訂用帳戶

1.  登入 <https://account.windowsazure.com/Subscriptions>

2.  選取要移轉的訂用帳戶。

3.  按一下 [轉移訂用帳戶] 選項。

    ![Azure 帳戶的訂用帳戶索引標籤](./media/billing-subscription-transfer/image1.png)

4.  依照提示來指定接受者。

    ![移轉訂用帳戶對話方塊](./media/billing-subscription-transfer/image2.PNG)

5.  接受者會自動收到含有接受連結的電子郵件。

    ![給接受者的訂用帳戶移轉電子郵件](./media/billing-subscription-transfer/image3.png)

6.  接受者按一下連結並遵循指示進行，包括輸入他們的付款資訊。

    ![第一個訂用帳戶移轉網頁](./media/billing-subscription-transfer/image4.PNG)

    ![第二個訂用帳戶移轉網頁](./media/billing-subscription-transfer/image5.PNG)

7. 成功！ 現在已移轉訂用帳戶。

## 常見問題集 (FAQ)

-   **訂用帳戶移轉會造成服務中斷嗎？**

    不會影響服務。這實際上會在目前的帳戶管理員下取消訂用帳戶，並在接受者的帳戶下建立新的訂用帳戶，但會將基礎的 Azure 服務與新的訂用帳戶產生關聯。訂用帳戶 ID 維持不變。

-   **如何使用這項機制變更訂用帳戶的目錄？**- Azure 訂用帳戶建立在帳戶管理員所屬的目錄中。所以，若要變更目錄，只要將訂用帳戶轉移到目標目錄中的使用者帳戶即可。當使用者完成步驟接受轉移時，訂用帳戶就會自動移至目標目錄。
   
-   **如果我接管另一個組織的訂用帳戶帳單擁有權，他們可以繼續存取我的資源嗎？**

    如果訂用帳戶轉移到另一個租用戶，與先前的租用戶相關聯的使用者將失去訂用帳戶的存取權。即使使用者不再是服務管理員或共同管理員，他們仍可能透過其他安全性機制來存取訂用帳戶。這些包括：將訂用帳戶資源的管理權限授與使用者的管理憑證。如需詳細資訊，請參閱[建立及上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx) - 儲存體等服務的存取金鑰。如需詳細資訊，請參閱[檢視、複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) - Azure 虛擬機器等服務的遠端存取認證

    這不是完整的清單。如果接受者需要限制對其資源的存取權，則應該考慮更新與服務相關聯的任何密碼。大部分資源可以更新如下：

    1.   移至 Azure 入口網站：[**https://portal.azure.com*](https://portal.azure.com)

    2.    按一下 [全部瀏覽] -&gt; [所有資源]

    3.    選取資源。這樣會開啟資源刀鋒視窗。

    4.    在資源刀鋒視窗中，按一下 [設定]。您可以在這裡檢視並更新現有的密碼。


-   **如果我在計費週期中途移轉訂用帳戶，接受者需要支付整個計費週期嗎？**

    傳送者負責支付移轉完成當時所報告的任何使用量。接受者負責支付移傳以後所報告的使用量。可能有某些使用量是在移轉之前發生，但在之後才報告。這些將算在接受者的帳單中。

-   **接受者可存取使用量及帳單記錄嗎？**

    目前，接受者看到的資訊只有最新帳單的金額 (或目前餘額，如果訂用帳戶是在產生第一份帳單之前移轉的話)。其餘的使用量及帳單記錄不會隨著訂用帳戶一起移轉。

-   **移轉期間可以變更優惠嗎？**

    優惠必須維持不變。若要變更優惠，您必須[連絡支援服務](http://go.microsoft.com/fwlink/?LinkID=619338)。

-   **我可以將訂用帳戶轉移給另一個國家/地區的使用者帳戶嗎？**

    否，目前不支援。接受者的使用者帳戶必須在相同的國家/地區。

-   **接受者可以使用不同的付款機制嗎？**

    是，事實上您可以使用這項機制，將訂用帳戶的付款方法從發票變更為信用卡。請移轉至您擁有的另一個帳戶，然後在收到訂用帳戶時輸入您的信用卡。有以下限制：現在訂用帳戶帳單記錄拆成兩個帳戶。但好處是您不必[連絡支援服務](http://go.microsoft.com/fwlink/?LinkID=619338)也可這樣做。

## 接受訂閱擁有權後的後續步驟

1. 您現在是帳戶管理員。請檢視並更新服務管理員和共同管理員。在 [Azure 管理入口網站](https://manage.windowsazure.com)中移至 [設定] 來管理系統管理員。[深入了解](http://go.microsoft.com/fwlink/?LinkID=533293)。
2. 您也可以針對訂用帳戶和服務使用角色型存取控制 (RBAC)。請造訪 [Azure Preview 入口網站](https://portal.azure.com)[深入了解 RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. 更新與此訂閱服務相關聯的認證。其中包含：
    -   可將使用者管理權限授與給訂用帳戶資源的管理憑證。如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/azure/gg551722.aspx)。
    -	服務 (例如儲存體) 的存取金鑰。如需詳細資訊，請參閱[檢視、複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -	服務 (例如 Azure 虛擬機器) 的遠端存取認證
4. 請至 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)[深入了解](http://go.microsoft.com/fwlink/?LinkID=533292) 更新此訂用帳戶的計費警示。
5. 	如果您正與合作夥伴協力作業，請考慮更新此訂用帳戶的合作夥伴 ID。您可以在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)中執行這個動作。

<!---HONumber=AcomDC_1125_2015-->