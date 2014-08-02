<properties title="Learn how to configure an Azure web site to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

設定 Azure 網站的自訂網域名稱 (GoDaddy)
=======================================

[自訂網域](/en-us/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name/ "網站") | [使用流量管理員的網站](/en-us/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文針對透過 Azure 網站在 [Go Daddy](https://godaddy.com) 中購買的自訂網域名稱，提供使用的相關指示。

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

新增自訂網域的 DNS 記錄
-----------------------

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 GoDaddy 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 GoDaddy.com 中，使用下列步驟來尋找 DNS 工具

1.  在 GoDaddy.com 中登入您的帳戶，並依序選取 **[我的帳戶]**、**[Manage your domains]**。最後，選取您要在 Azure 網站中使用的網域名稱。

    ![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2.  在 **[網域詳細資料]** 頁面中，選取 **[DNS Zone File]** 索引標籤。此區段可用來新增與修改網域名稱的 DNS 記錄。選取 **[編輯]** 按鈕，以顯示 **[Zone File Editor]**。

    ![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

3.  **[Zone File Editor]** 會依每個記錄類型被分為不同區段，從作為第一個區段的 A 記錄開始 (列為 **[A (主機)]**)，接著是 CNAME 記錄 (列為 **[CNAME (別名)]**)。若要新增項目，請使用對應區段下方的 **[快速新增]** 按鈕。若要編輯現有項目，請選取該項目並修改現有資訊。

    > [WACOM.NOTE] 在將項目新增至區域檔案之前，請注意，GoDaddy 已為熱門子網域 (在編輯器中稱為 **[主機]**) 建立 DNS 記錄，例如**電子郵件**、**檔案**、**郵件**及其他。如果您要使用的名稱已存在，請修改現有記錄，而非建立新記錄。

    -   新增 CNAME 記錄時，您必須將 **[主機]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[指向]** 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

        ![zone file editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   **www** 的 **[主機]** 值加上 **&lt;yourwebsitename\>.azurewebsites.net** 的 **[指向]** 值。
        >
        > 或
        >
        > -   **awverify.www** 的 **[主機]** 值加上 **awverify.&lt;yourwebsitename\>.azurewebsites.net** 的 **[指向]** 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

    -   新增 A 記錄時，您必須將 **[主機]** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)、\* (符合多個子網域的萬用字元)，或您要使用的子網域 (例如 **www**)。您必須將 **[指向]** 欄位設定為您 Azure 網站的 IP 位址。

        ![zone file editor for a record](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

4.  當您完成新增或修改記錄時，請按一下 **[Save Zone File]** 以儲存變更。

在網站上啟用網域名稱
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

