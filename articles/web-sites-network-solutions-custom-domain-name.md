<properties title="Learn how to configure an Azure web site to use a domain name registered with Network Solutions" pageTitle="Configure a Network Solutions domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

設定 Azure 網站的自訂網域名稱 (Network Solutions)
=================================================

[自訂網域](/zh-tw/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/zh-tw/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/zh-tw/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/zh-tw/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/zh-tw/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/zh-tw/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/zh-tw/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/zh-tw/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/zh-tw/documentation/articles/web-sites-network-solutions-custom-domain-name/ "網站") | [使用流量管理員的網站](/zh-tw/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文提供使用 Azure 網站，從 [Network Solutions](https://networksolutions.com) 購買的自訂網域名稱的使用指示。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為基本、共用或標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [在網站上啟用網域](#enabledomain)

了解 DNS 記錄
-------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

將網站設定為基本、共用或標準模式
--------------------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

## 新增自訂網域的 DNS 記錄 

若要將您的自訂網域與 Azure 網站建立關聯，必須使用 Network Solutions 提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找並使用 networksolutions.com 的 DNS 工具

1.  在 networksolutions.com 上登入您的帳戶，並選取右上角的 **[我的帳戶]**。

2.  在 **[我的產品和服務]** 索引標籤中，選取 **[編輯 DNS]**。

    ![edit dns page](./media/web-sites-custom-domain-name/ns-editdns.png)

3.  從 [網域名稱] 頁面的 [管理 ] 區段中，選取 [Edit Advanced DNS Records]。

    ![domain names page with edit advanced dns records highlighted](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4.  **[更新進階 DNS]** 頁面包含每個記錄類型的區段，每個區段底下都有 **[編輯]** 按鈕。

    -   若為 A 記錄，請使用 **[IP Address (A Records)]** 區段。
    -   若為 CNAME 記錄，請使用 **[Host Alias (CNAME Records)]** 區段。

    ![update advanced dns page](./media/web-sites-custom-domain-name/ns-updateadvanced.png)

5.  按一下 **[編輯]** 按鈕時會出現表單，可讓您用來修改現有的記錄或新增記錄。

    > [WACOM.NOTE] 新增項目前，請注意 Network Solutions 已為某些項目 (如根網域 ('@')，和子網域的萬用字元記錄 ('\*')) 建立一些預設 DNS 記錄。若要使用的記錄已存在，請修改記錄而非建立新記錄。

    -   新增 CNAME 記錄時，必須將 **[別名]** 欄位設為要使用的子網域。例如 **www**。您必須選取 **[其他主機]** 欄位旁的圓形欄位，並將 **[其他主機]** 設為 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。保留 **[參照主機名稱]** 為 **[選取]**，因此在建立與 Azure 網站搭配使用的 CNAME 記錄時不需要此欄位。

        ![cname form](./media/web-sites-custom-domain-name/ns-cname.png)

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   **[www]** 的 **[別名]** 值，包含 **[&lt;yourwebsitename\>.azurewebsites.net]** 的 **[其他主機]** 值。
        >
        > 或
        >
        > -   **[awverify.www]** 的 **[別名]** 值，包含 **[awverify.&lt;yourwebsitename\>.azurewebsites.net]** 的 **[其他主機]** 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域。

    -   新增 A 記錄時，您必須將 **[主機]** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)、\* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。您必須將 **[數值 IP]** 欄位設為 Azure 網站的 IP 位址。

        ![a record form](./media/web-sites-custom-domain-name/ns-arecord.png)

6.  完成記錄新增或修改後，按一下 **[繼續]** 檢閱變更。選取 **[僅儲存變更]** 以儲存變更。

在網站上啟用網域名稱
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

