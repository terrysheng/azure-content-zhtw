<properties title="Learn how to configure an Azure web site to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, Directnic" description="Learn how to configure an Azure web site to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

設定 Azure 網站的自訂網域名稱 (Directnic)
=========================================

[自訂網域](/en-us/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/en-us/documentation/articles/web-sites-directnic-custom-domain-name/ "網站") | [使用流量管理員的網站](/en-us/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文針對透過 Azure 網站從 [DirectNic.com](https://directnic.com) 購買的自訂網域名稱，提供使用的相關指示。

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

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 Directnic 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。使用下列步驟來尋找 Directnic.com 的 DNS 工具

1.  在 Directnic.com 中登入您的帳戶，並依序選取 **[我的服務]**、**[網域]**。

    ![Directnic Services Menu](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2.  按一下您要在 Azure 網站中使用的網域名稱。

3.  在您的網域管理頁面上，在 **[服務]** 窗格的 **[DNS]** 中按一下 **[管理]** 按鈕。

    ![Services Panel](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4.  透過填入 **[類型]**、**[名稱]** 和 **[資料]** 等欄位來新增 DNS 記錄。完成時，請按一下 **[新增記錄]** 按鈕。

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    -   新增 CNAME 記錄時，您必須將 **[名稱]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[資料]** 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

    -   新增 A 記錄時，您必須將 **[名稱]** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 **[資料]** 欄位設定為您 Azure 網站的 IP 位址。

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   **www** 的 **[名稱]** 值加上 **&lt;yourwebsitename\>.azurewebsites.net** 的 **[資料]** 值。
        >
        > 或
        >
        > -   **awverify.www** 的 **[名稱]** 值加上 **awverify.&lt;yourwebsitename\>.azurewebsites.net** 的 **[資料]** 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

在網站上啟用網域名稱
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

