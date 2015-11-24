<properties
   pageTitle="將公司網際網路網域指向流量管理員網域 | Microsoft Azure"
   description="本文將協助您將公司網域名稱指向流量管理員網域名稱。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />

# 將公司網際網路網域指向 Azure 流量管理員網域

若要將公司網域名稱指向流量管理員網域名稱，請將網際網路 DNS 伺服器上的 DNS 資源記錄改為使用 CNAME 記錄，這會將公司網域名稱對應到流量管理員設定檔的網域名稱。您可以在流量管理員設定檔的 [組態] 頁面上的 [一般] 區段查看流量管理員網域名稱。

例如，若要將公司網域名稱指向 www.contoso.com 流量管理員網域名稱 contoso.trafficmanager.net，您會更新 DNS 資源記錄如下：

    www.contoso.com IN CNAME contoso.trafficmanager.net

進入 *www.contoso.com* 的所有流量要求現在將會被導向至 *contoso.trafficmanager.net*。

>[AZURE.IMPORTANT]您無法將第二層網域 (例如 *contoso.com*) 指向流量管理員網域。這是 DNS 通訊協定的限制，不允許第二層網域名稱的 CNAME 記錄。

## 後續步驟

[流量管理員路由方法](traffic-manager-routing-methods.md)

[流量管理員 - 停用、啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[流量管理員 - 停用或啟用端點](disable-or-enable-an-endpoint.md)

<!---HONumber=Nov15_HO4-->