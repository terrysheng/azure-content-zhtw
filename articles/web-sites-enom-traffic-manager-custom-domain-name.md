<properties title="Learn how to configure an Azure web site that uses Traffic Manager to use a domain name registered with eNom" pageTitle="Configure an eNom domain name for an Azure web site that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

使用流量管理員來設定 Azure 網站的自訂網域名稱 (eNom)
====================================================

[自訂網域](/en-us/documentation/articles/web-sites-custom-domain-name "自訂網域")[GoDaddy](/en-us/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy")[網路解決方案](/en-us/documentation/articles/web-sites-network-solutions-custom-domain-name "網路解決方案")[Register.com](/en-us/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com")[Enom](/en-us/documentation/articles/web-sites-enom-custom-domain-name "Enom")[Moniker](/en-us/documentation/articles/web-sites-moniker-custom-domain-name "Moniker")[Dotster](/en-us/documentation/articles/web-sites-dotster-custom-domain-name "Dotster")[DomainDiscover](/en-us/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover")[Directnic](/en-us/documentation/articles/web-sites-directnic-custom-domain-name "Directnic")

[網站](/en-us/documentation/articles/web-sites-enom-custom-domain-name/ "網站") | [使用流量管理員的網站](/en-us/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/ "使用流量管理員的網站")

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文針對透過 Azure 網站在 [eNom](https://enom.com) 中購買的自訂網域名稱，提供使用的相關指示。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

本文內容：

-   [了解 DNS 記錄](#understanding-records)
-   [將網站設定為標準模式](#bkmk_configsharedmode)
-   [新增自訂網域的 DNS 記錄](#bkmk_configurecname)
-   [啟用網站的流量管理員](#enabledomain)

了解 DNS 記錄
-------------

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

將網站設定為標準模式
--------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

## 新增自訂網域的 DNS 記錄 

若要將您的自訂網域與 Azure 網站建立關聯，您必須使用由 eNom 所提供的工具，在 DNS 資料表中為您的自訂網域新增項目。在 enom.com 中，使用下列步驟來尋找 DNS 工具

1.  在 eNom 中登入您的帳戶，並依序選取 **[網域]**、**[我的網域]**。如此會顯示您的網域名稱。

2.  在 **[我的網域]** 頁面中，使用 **[管理網域]** 欄位來選取 **[主機記錄]**。如此會顯示主機記錄欄位。

    ![DNS Zone File tab](./media/web-sites-custom-domain-name/e-hostrecords.png)

3.  [主機記錄] 編輯器可讓您使用 **[記錄類型]** 欄位，來選取特定記錄類型。若是使用流量管理員的 Azure 網站，您只能使用 **[CNAME (別名)]** 選項，因為流量管理員僅適用於 CNAME 記錄。

    ![zone file editor](./media/web-sites-custom-domain-name/e-editrecordstm.png)

    -   新增 CNAME 記錄時，您必須將 **[主機名稱]** 欄位設定為您要使用的子網域。例如 **www**。您必須將 **[位址]** 欄位設定為 Azure 網站所用之流量管理員設定檔的 **.trafficmanager.net** 網域名稱。例如 **contoso.trafficmanager.net**。

4.  當您完成新增或修改記錄時，請按一下 **[儲存]** 以儲存變更。

啟用流量管理員網站
------------------

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

