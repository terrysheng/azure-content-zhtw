<properties linkid="scripting-center-index" urlDisplayName="index" pageTitle="Scripting Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Manage Origins in a Media Services Account" authors="juliako" solutions="" manager="" editor="" />

如何管理媒體服務帳戶中的原始來源
================================

媒體服務可讓您在帳戶中新增多個串流原始來源，以及設定原始來源。每個媒體服務帳戶都至少會與一個名為**「預設」**的串流原始來源相關聯。

新增及刪除原始來源
------------------

1.  在[管理入口網站](https://manage.windowsazure.com/)中，按一下 **[媒體服務]**。接著，按一下媒體服務的名稱。
2.  選取 [ORIGINS] 頁面。
3.  按一下頁面底部的 [新增] 或 [刪除] 按鈕。請注意，預設原始來源不可刪除。
4.  按一下 [啟動] 按鈕以啟動原始來源。
5.  如果您要設定原始來源，請按一下原始來源的名稱。

    ![Origin page](./media/media-services-manage-origins/media-services-origins-page.png)

設定原始來源
------------

[設定] 索引標籤可讓您執行下列設定：

1.  設定會在 HTTP 回應的 cache control 標頭中指定的快取期間上限。此值將不會覆寫在 Blob 內容上明確設定的快取值上限。

2.  指定能夠連接到已發佈之串流端點的 IP 位址。若未指定 IP 位址，則任何 IP 位址都可連接。

3.  為來自 Akami 伺服器的 G20 驗證要求指定組態。

    ![Configure origin](./media/media-services-manage-origins/media-services-origins-configure.png)

    此頁面上的組態只會套用到至少有一個保留單元的原始來源。若要保留隨選串流保留單元，請移至 [調整] 索引標籤。如需保留單元的詳細資訊，請參閱[調整媒體服務](http://go.microsoft.com/fwlink/?LinkID=275847&clcid=0x409/) (英文)。


