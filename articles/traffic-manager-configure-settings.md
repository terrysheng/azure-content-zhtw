<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Configure Traffic Manager Settings" authors="" solutions="" manager="" editor="" />

如何進行流量管理員設定
======================

Azure 流量管理員可讓您控制 Azure 代管服務的使用者流量分配。

流量管理員的運作方式是在主要公司網域名稱的 DNS 查詢上套用情報原則引擎。更新貴公司擁有的 DNS 資源記錄，以指向流量管理員網域。連接至這些網域的流量管理員原則會接著將主要公司網域名稱的 DNS 查詢，解析成流量管理員原則中所包含之特定 Azure 代管服務的 IP 位址。如需詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)。

目錄
----

-   [作法：將公司網際網路網域指向流量管理員網域](#howto_point_company)
-   [作法：測試原則](#howto_test)
-   [作法：暫時停用原則和代管服務](#howto_temp_disable)
-   [作法：編輯原則](#howto_edit_policy)
-   [作法：代管服務集合中的平均負載平衡流量](#howto_load_balance)
-   [作法：建立容錯移轉原則](#howto_create_failover)
-   [作法：根據網路效能將連入流量導向代管服務](#howto_direct)

作法：將公司網際網路網域指向流量管理員網域
------------------------------------------

若要將公司網域名稱指向流量管理員網域，請使用 CNAME 編輯 DNS 伺服器上的 DNS 資源記錄。

例如，若要將主要公司網域 **www.contoso.com** 指向名為 **contoso.trafficmanager.net** 的流量管理員網域，請更新 DNS 資源記錄，如下所示：`www.contoso.com IN CNAME contoso.trafficmanager.net`

進入 *www.contoso.com* 的所有流量現在便會被重新導向至 *contoso.trafficmanager.net*。請確定您使用的是要將所有流量重新導向至流量管理員的網域。

作法：測試原則
--------------

測試原則的最佳方式是設定許多用戶端，然後一次關閉一個原則中的服務。下列提示可協助您測試流量管理員原則：

-   **設定非常低的 DNS TTL**，方便快速傳播變更 - 例如 30 秒。

-   知道正在測試原則中 **Azure 代管服務的 IP 位址**。您可以從 Azure 管理入口網站中取得此資訊。按一下服務的生產部署。在右側內容窗格中，最後一個項目會是 VIP，也就是該代管服務的虛擬 IP 位址。

    ![hosted service IP location](./media/traffic-manager-configure-settings/hosted_service_IP_location.png)

    **圖 1** - 代管服務 IP 位置

-   **使用可讓您將 DNS 名稱解析成 IP 位址的工具**，並顯示該位址。您正查看公司網域名稱是否解析成原則中代管服務的 IP 位址。他們的解析方法應與流量管理員原則的負載平衡方法一致。如果您處於 Windows 系統，您可以從 CMD 視窗中使用 nslookup (解釋如下)。可讓您「挖掘」IP 位址的其他公開可用工具在網際網路上隨手可得。

-   **檢查流量管理員原則**，方法是使用 *nslookup*。

> **使用 nslookup 檢查流量管理員原則**

> 1.  透過依序按一下 [開始]、**[執行]**，並輸入 **CMD** 來啟動 CMD 視窗。

> 1.  為了排清 DNS 解析程式快取，請輸入 `ipconfig /flushdns`。

> 1.  輸入命令 `nslookup <your traffic manager domain>`。例如，下列命令會檢查包含 *myapp.contoso* 前置詞的網域 `nslookup myapp.contoso.trafficmanager.net`

> > 典型的結果顯示如下：

> > -   正在存取 DNS 伺服器的 DNS 名稱和 IP 位址，藉此解析此流量管理員網域。

> > -   您在命令列上 "nslookup" 後面所輸入的流量管理員網域名稱和流量管理員網域解析成的 IP 位址。第二個 IP 位址是要檢查的重點。它應符合正在測試流量管理員原則中其中一個代管服務的 VIP。

> > ![nslookup command example](./media/traffic-manager-configure-settings/nslookup_command_example.png)

> > **圖 2** - nslookup 命令範例

-   **使用以下列出的其中一個其他測試方法。** 為正在測試的負載平衡類型選取一個適當方法。

### 效能原則

您將需要位於世界各地的用戶端來有效測試您的網域。您可以在 Azure 中建立嘗試透過公司網域名稱呼叫服務的用戶端。或者，如果您的公司是全球企業，您可以在國內各地遠端登入用戶端，並從這些用戶端中進行測試。

您可以找到免費的網頁式 nslookup 和挖掘服務。其中部分提供您從不同位置檢查 DNS 名稱解析的能力。例如在 nslookup 上執行搜尋。另一個選項是使用協力廠商解析 (如 Gomez 或 Keynote)，確認您的原則正如預期般分配流量。

### 容錯移轉原則

1.  將所有服務保持運作。

2.  使用單一用戶端。

3.  使用 ping 或類似公用程式，要求公司網域的 DNS 解析。

4.  確定取得的 IP 位址適用於您的主要代管服務。

5.  關閉您的主要服務或移除監視檔案，所以流量管理員會認為它已關閉。

6.  至少等候 2 分鐘的時間。

7.  要求 DNS 解析。

8.  請確定所取得的 IP 位址適用於您的次要代管服務，如 [編輯流量管理員原則] 對話方塊中的服務順序所示。

9.  重複此程序，關閉次要服務，然後第三個，依此類推。每一次，請確定 DNS 解析傳回清單中下一個服務的 IP 位址。關閉所有服務之後，您應可再次取得主要代管服務的 IP 位址。

### 循環配置資源原則

1.  將所有服務保持運作。

2.  使用單一用戶端。

3.  為您的最上層網域要求 DNS 解析。

4.  請確定所取得的 IP 位址是清單中的其中之一。

5.  重複此程序以使得 DNS TTL 過期，因此您將會收到新的 IP 位址。您應可看到為每個代管服務傳回的 IP 位址。然後將重複執行此程序。

作法：暫時停用原則和代管服務
----------------------------

您可以停用先前建立的流量管理員原則，他們便不會路由流量。停用流量管理員原則時，此原則的資訊將保持不變，並可在流量管理員介面中進行編輯。您可以輕易地重新啟用原則，路由將會繼續進行。

您也可以停用屬於流量管理員原則一部分的個別代管服務。此動作可有效地將代管服務保留為原則的一部分，但原則會像不包含代管服務般運作。此動作對於暫時移除處於維護模式、或被重新部署而變得不穩定的代管服務而言非常有用。在重新啟動並執行代管服務之後，您可以將它重新啟用。

**注意**
 停用代管服務與其在 Azure 中的部署狀態無關。健全的服務將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。另外，在一個原則中停用代管服務並不會影響它在另一個原則中的狀態。

### 停用原則

1.  在流量管理員介面樹狀結構中，選取一個已啟用原則。

2.  按一下頂端工具列上的 **[停用原則]**。請注意，如果您反白顯示已停用的原則，則此按鈕會呈現灰色。

### 啟用原則

1.  在流量管理員介面樹狀結構中，選取一個已停用原則。

2.  按一下頂端工具列上的 **[啟用原則]**。請注意，如果您反白顯示已停用的原則，則此按鈕會呈現灰色。

### 停用代管服務

1.  在流量管理員介面的原則中，選取一個已啟用的代管服務。您必須展開原則，才能看到其中所包含的代管服務。

2.  按一下頂端工具列上的 **[Disable Service Policy]**。請注意，如果您反白顯示已停用的代管服務，則此按鈕會呈現灰色。

3.  根據流量管理員網域 (屬於原則一部分) 的 DNS TTL 時間設定，流量將會停止流向代管服務。如需詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜在 Azure 流量管理員中監視代管服務＞一節。

### 啟用代管服務

1.  在流量管理員介面的原則中，選取一個已停用的代管服務。您必須展開原則，才能看到其中所包含的代管服務。

2.  按一下頂端工具列上的 **[Enable Service Policy]**。請注意，如果您反白顯示已啟用的代管服務，則此按鈕會呈現灰色。

3.  流量會如原則指定再次開始流向代管服務。

作法：編輯原則
--------------

如果您只需要暫時關閉原則或原則中的特定代管服務，則您可以在無需變更原則的情況下將他們暫時停用。

若要將原則變更成不同類型，請使用下列步驟：

1.  **登入管理入口網站中的流量管理員區域**，網址是 <http://manage.windowsazure.com> (英文)。在入口網站頁面的左下方按一下 **[虛擬網路]**，然後從左窗格的選項中選擇 **[流量管理員]**。

2.  在管理入口網站的 [流量管理員] 畫面中，**選取您要變更的原則**。

3.  在頂端功能表列中，**按一下 [設定]**。

4.  **對原則採取所需的變更。** 請注意，如果您變更負載平衡方法，則取決於所選取的選項，在 **[Selected hosted services]** 清單中的代管服務順序可能重要也可能不重要。

5.  按一下 **[儲存]**。

作法：代管服務集合中的平均負載平衡流量
--------------------------------------

常見的負載平衡模式提供一組完全相同的代管服務，並以循環配置資源方式將流量傳送到每一個代管服務。本文將概述設定流量管理員網域和原則，以執行這個負載平衡類型的步驟。

如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

1.  **將您的代管服務部署**到您的生產環境中。如需開發與部署 Azure 代管服務的詳細資訊，請參閱 [Azure 代管服務](http://msdn.microsoft.com/library/gg432967.aspx) (英文)。

2.  **登入管理入口網站中的流量管理員區域**，網址是 <http://manage.windowsazure.com> (英文)。在入口網站頁面的左下方按一下 **[虛擬網路]**，然後從左窗格的選項中選擇 **[流量管理員]**。

3.  **選擇原則，並按一下 [建立]。** 從左導覽樹狀結構中選擇 **Policies** 資料夾，以啟用頂端工具列中的 **[建立]**。選擇 **[建立]**。**[Create Traffic Manager policy]** 對話方塊便會隨即出現。

    ![Create button for policies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **圖 1** - 建立原則的按鈕

4.  **選擇訂閱。** 原則和網域會與單一訂閱相關聯。

5.  **選取 [循環配置資源] 負載平衡方法。**如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

6.  **尋找代管服務，並將他們新增至原則。** 使用篩選方塊，來尋找內含您在方塊中所輸入字串的代管服務。清除此方塊以顯示生產中的所有代管服務，以取得您在步驟 4 中所選取的訂閱。使用箭頭按鈕將他們新增至原則。**[Selected DNS names]** 方塊中的順序不會影響此負載平衡方法。

7.  **設定監視。** 監視可確保處於離線狀態的代管服務不會傳送流量。您必須具備特定路徑和檔案名稱才能設定。如需詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜在 Azure 流量管理員中監視代管服務＞一節。

8.  **命名您的流量管理員網域。** 為您的網域提供唯一名稱。您僅只指定網域的前置詞。保留其預設時間作為 DNS 有效時間。如需有關此設定的效果相關資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜使用 Azure 流量管理員時的代管服務和原則最佳做法＞一節。

    **[Create Traffic Manager policy]** 對話方塊應該類似以下範例。

    ![Dialog box for Round Robin load balancing method](./media/traffic-manager-configure-settings/Dialog_box_for_Round_Robin_load_balancing_method.png)

    **圖 2** - [循環配置資源] 負載平衡方法的對話方塊

9.  **測試流量管理員網域和原則。** 如需指示，請參閱[作法：測試 Azure 流量管理員原則](#howto_test)。

10. **將您的 DNS 伺服器指向流量管理員網域。** 在您的流量管理員網域已設定並執行之後，您可以編輯授權 DNS 伺服器上的 DNS 記錄，以將您的公司網域指向流量管理員網域。例如，下列命令會將所有進入 **www.contoso.com** 的流量，路由至流量管理員網域 **contoso.trafficmanager.net**：`www.contoso.com IN CNAME contoso.trafficmanager.net` 如需詳細資訊，請參閱[作法：將公司網際網路網域指向 Azure 流量管理員網域](#howto_point_company)。

作法：建立容錯移轉原則
----------------------

組織通常會想要為其服務提供可靠性。這樣做的方法是透過提供備份服務，以避免發生其主要服務當機的情況。服務容錯移轉的一個常見模式是提供一組完全相同的代管服務，並將流量連同一或多個備份清單傳送到主要服務。本文將概述設定流量管理員原則，以執行這個容錯移轉備份類型的步驟。

如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

1.  **將您的代管服務部署**到您的生產環境中。如需開發與部署代管服務的詳細資訊，請參閱 [Azure 代管服務](http://msdn.microsoft.com/library/gg432967.aspx) (英文)。

2.  **登入管理入口網站中的流量管理員區域**，網址是 <http://manage.windowsazure.com> (英文)。在入口網站頁面的左下方按一下 **[虛擬網路]**，然後從左窗格的選項中選擇 **[流量管理員]**。

3.  **選擇原則，並按一下 [建立]。** 從左導覽樹狀結構中選擇 **Policies** 資料夾，以啟用頂端工具列中的 **[建立]**。選擇 **[建立]**。**[Create Traffic Manager policy]** 對話方塊便會隨即出現。

    ![Create button for policies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **圖 1** - 建立原則的按鈕

4.  **選擇訂閱。** 原則和網域會與單一訂閱相關聯。

5.  **選取 [容錯移轉原則] 負載平衡方法。**如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

6.  **尋找代管服務，並將他們新增至原則。** 使用篩選方塊，來尋找內含您在方塊中所輸入字串的代管服務。清除此方塊以顯示生產中的所有代管服務，以取得您在步驟 4 中所選取的訂閱。使用箭頭按鈕將他們新增至原則。當您選取 **[容錯移轉]** 負載平衡方法時，選取服務的順序便很重要。主要的代管服務會在上方。視需要使用向上和向下箭頭來變更順序。

7.  監視可確保處於離線狀態的代管服務不會傳送流量。在沒有設定監視的情況下使用容錯移轉原則並沒有意義，因為即使主要代管服務顯示為離線，系統仍會將流量傳送至該代管服務。為了要能夠監視代管服務，您必須指定特定路徑和檔案名稱。如需詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜在 Azure 流量管理員中監視代管服務＞一節。

8.  **命名您的流量管理員網域。** 為您的網域提供唯一名稱。您僅只指定網域的前置詞。保留其預設時間作為 **[DNS 有效時間 (TTL)]**。如需有關此設定的效果相關資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜使用 Azure 流量管理員時的代管服務和原則最佳做法＞一節。

    **[Create Traffic Manager policy]** 對話方塊看起來應該類似以下範例。

    ![Dialog box for Failover load balancing method](./media/traffic-manager-configure-settings/Dialog_box_for_Failover_load_balancing_method.png)

    **圖 2** - [容錯移轉] 負載平衡方法的對話方塊

9.  **測試流量管理員網域和原則。** 如需詳細資訊，請參閱[作法：測試 Azure 流量管理員原則](#howto_test)。

10. **將您的 DNS 伺服器指向流量管理員網域。** 在您的流量管理員網域已設定並執行之後，您可以編輯授權 DNS 伺服器上的 DNS 記錄，以將您的公司網域指向流量管理員網域。如需詳細資訊，請參閱[如何將公司網際網路網域指向流量管理員網域](#howto_point_company)。例如，下列命令會將所有進入 **www.contoso.com** 的流量，路由至流量管理員網域 **contoso.trafficmanager.net** `www.contoso.com IN CNAME contoso.trafficmanager.net`

作法：根據網路效能將連入流量導向代管服務
----------------------------------------

為了要能夠負載平衡位於全球不同資料中心的代管服務，您可以將連入流量導向最靠近的代管服務。雖然「最靠近」可能直接對應地理位置的距離，但它也可以對應針對服務要求提供最低延遲的位置。[效能] 負載平衡方法可讓您根據位置和延遲進行分配，但無法考量在網路組態或負載中的即時變更。如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

下列步驟將逐步引導您完成此程序：

1.  **將您的代管服務部署**到您的生產環境中。如需詳細資訊，請參閱[建立 Azure 的代管服務](http://msdn.microsoft.com/zh-tw/library/windowsazure/gg432967.aspx) (英文)。另請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文) 中的＜代管服務和原則的最佳做法＞一節。

2.  **登入管理入口網站中的流量管理員區域**，網址是 <http://manage.windowsazure.com> (英文)。在入口網站頁面的左下方按一下 **[虛擬網路]**，然後從左窗格的選項中選擇 **[流量管理員]**。

3.  **選擇原則，並按一下 [建立]。** 從左導覽樹狀結構中選擇 **Policies** 資料夾，以啟用頂端工具列中的 **[建立]**。選擇 **[建立]**。**[Create Traffic Manager policy]** 對話方塊便會隨即出現。

    ![Create button for policies](./media/traffic-manager-configure-settings/Create_button_for_policies.png)

    **圖 1** - 建立原則的按鈕

4.  **選擇訂閱。** 原則和網域會與單一訂閱相關聯。

5.  **挑選 [效能] 負載平衡方法。**如需流量管理員所提供的其他負載平衡方法詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/hh744833.aspx) (英文)，並捲動到＜Azure 流量管理員中的負載平衡方法＞一節。

6.  **尋找代管服務，並將他們新增至原則。** 使用篩選方塊，來尋找內含您在方塊中所輸入字串的代管服務。清除此方塊以顯示生產中的所有代管服務，以取得您在步驟 4 中所選取的訂閱。使用箭頭按鈕將他們新增至原則。**[Selected DNS names]** 中的順序不會影響此負載平衡方法。

7.  **設定監視。** 監視可確保處於離線狀態的代管服務不會傳送流量。您必須具備特定路徑和檔案名稱才能設定。如需詳細資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜在 Azure 流量管理員中監視代管服務＞一節。

8.  **命名您的流量管理員網域。** 為您的網域提供唯一名稱。您僅只指定網域的前置詞。保留其預設時間作為 **[DNS 有效時間 (TTL)]**。如需有關此設定的效果相關資訊，請參閱 [Azure 流量管理員的概觀](http://msdn.microsoft.com/zh-tw/library/windowsazure/5229dd1c-5a91-4869-8522-bed8597d9cf5#BKMK_Monitoring) (英文)，並捲動到＜使用 Azure 流量管理員時的代管服務和原則最佳做法＞一節。

    **[Create Traffic Manager policy]** 對話方塊看起來應該類似以下範例。

    ![Dialog box for Performance load balancing method](./media/traffic-manager-configure-settings/Dialog_box_for_Performance_load_balancing_method.png)

    **圖 2** - [效能] 負載平衡方法的對話方塊

9.  **測試流量管理員網域和原則。** 如需測試的詳細資訊，請參閱[作法：測試 Azure 流量管理員原則](#howto_test)。

10. **將您的 DNS 伺服器指向流量管理員網域。** 在您的流量管理員網域已設定並執行之後，您可以編輯授權 DNS 伺服器上的 DNS 記錄，以將您的公司網域指向流量管理員網域。例如，下列命令會將所有進入 **www.contoso.com** 的流量，路由至流量管理員網域 **contoso.trafficmanager.net**。`www.contoso.com IN CNAME contoso.trafficmanager.net` 如需詳細資訊，請參閱[作法：將公司網際網路網域指向 Azure 流量管理員網域](#howto_point_company)。


