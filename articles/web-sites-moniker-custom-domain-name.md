<properties title="Learn how to configure an Azure web site to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure web site" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure web site to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

設定 Azure 網站的自訂網域名稱 (Moniker)
=======================================

[自訂網域](/en-us/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/en-us/documentation/articles/web-sites-moniker-custom-domain-name/ "網站") | [使用流量管理員的網站](/en-us/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

本文針對透過 Azure 網站在 [Moniker.com](https://moniker.com) 中購買的自訂網域名稱，提供使用的相關指示。

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

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 Moniker 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 Moniker.com 中，使用下列步驟來尋找 DNS 工具

1.  在 Moniker.com 中登入您的帳戶，並選取 **[我的網域]**，然後按一下 **[管理範本]**。

    ![My Domains page for Moniker](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2.  在 **[Zone Template Management]** 頁面上，選取 **[建立新範本]**。

    ![Moniker Zone Template Management](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3.  填入 **[範本名稱]**。

4.  接著建立 DNS 記錄，方法是首先選取 **[記錄類型]**。然後填入 **[主機名稱]** 和 **[位址]**。

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png)

    -   新增 CNAME 記錄時，您必須將 **[主機名稱]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[位址]** 欄位設定為您 Azure 網站的 **.azurewebsites.net** 網域名稱。例如 **contoso.azurwebsites.net**。

    -   新增 A 記錄時，您必須將 **[主機名稱]** 欄位設定為 **@** (這代表根網域名稱，例如 **contoso.com**)，或您要使用的子網域 (例如 **www**)。您必須將 **[位址]** 欄位設定為您 Azure 網站的 IP 位址。

        > [WACOM.NOTE] 如果您使用 A 記錄，則也必須利用以下其中一個組態來新增 CNAME 記錄：
        >
        > -   **www** 的 **[主機名稱]** 值加上 **&lt;yourwebsitename\>.azurewebsites.net** 的 **[位址]** 值。
        >
        > 或
        >
        > -   **awverify.www** 的 **[主機名稱]** 值加上 **awverify.&lt;yourwebsitename\>.azurewebsites.net** 的 **[位址]** 值。
        >
        > Azure 會使用 CNAME 記錄來驗證您擁有 A 記錄所述的網域

5.  按一下 **[新增]** 按鈕以新增項目。

6.  在新增所有項目之後，按一下 **[儲存]** 按鈕。

7.  選取 **[網域管理員]** 以返回您的 [網域] 清單。

8.  勾選目標網域的核取方塊，然後再按一下 **[管理範本]**。

9.  尋找您在前一個步驟中建立的新範本。然後按一下 **[place selected domains (1) into this Template]** 連結。

    ![Moniker Create Zone Template](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

在網站上啟用網域名稱
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

