<properties 
   pageTitle="Azure DNS 概觀 | Microsoft Azure" 
   description="Microsoft Azure 上的 Azure DNS 主機服務和開始將網域裝載於 Microsoft Azure 的概觀" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/09/2016"
   ms.author="joaoma"/>

# Azure DNS 概觀

網際網路上的任何網站或服務背後都有 IP 位址。例如，www.microsoft.com 使用 IP 位址 134.170.185.46。網域名稱系統 (DNS) 負責將網站或服務名稱轉譯 (或解析) 為其 IP 位址。

Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。只要將您的網域裝載於 Azure，您就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

Azure DNS 中的 DNS 網域裝載於 Azure 的 DNS 名稱伺服器全球網路。我們使用「任一傳播」網路，所以每個 DNS 查詢是由最接近的可用 DNS 伺服器回答。這為您的網域提供快速的效能與高可用性。

此服務是以 Azure 資源管理員 (ARM) 為基礎。這樣，它就可得益於 ARM 功能，如角色型存取控制、稽核記錄檔，以及資源鎖定。

您可以透過 Azure 入口網站、Azure PowerShell Cmdlet 和跨平台 Azure CLI 來管理網域和記錄。需要自動化 DNS 管理的應用程式可以透過 REST API 和 SDK 與服務進行整合。

Azure DNS 目前不支援購買網域名稱。若要購買網域，您洽詢第三方網域名稱註冊機構，通常會收取些微年費。然後，這些網域可以裝載於 Azure DNS 來管理 DNS 記錄，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 取得詳細資訊。


## 後續步驟

[開始建立 DNS 區域](dns-getstarted-create-dnszone.md)

[使用 .NET SDK 自動化 Azure 作業](dns-sdk.md)

[Azure DNS REST API 參考](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=AcomDC_0309_2016-->