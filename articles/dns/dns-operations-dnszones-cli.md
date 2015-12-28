<properties 
   pageTitle="使用 CLI 在 DNS 區域上作業 | Microsoft Azure" 
   description="您可以使用 Azure CLI 管理 DNS 區域。如何在 Azure DNS 上更新、刪除及建立 DNS 區域" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# 如何使用 CLI 管理 DNS 區域

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)

本指南說明如何管理 DNS 區域。有助於了解管理 DNS 區域所需執行的作業序列。

>[AZURE.NOTE]Azure DNS 是僅能以 Azure 資源管理員運作的服務。它沒有 ASM API。因此您必須確認設定 Azure CLI 以使用資源管理員模式，並使用 azure config mode arm 命令。

>如果您看到「錯誤：'dns' 不是 azure 命令」，可能是因為您正在 ASM 模式中使用 Azure CLI，而非資源管理員模式中。
 
## 建立新的 DNS 區域

若要建立新的 DNS 區域來裝載您的網域，請使用 `azure network dns zone create`：

	azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

該作業會在 Azure DNS 中建立新的 DNS 區域。您可以選擇性地指定一系列的 Azure 資源管理員標記，如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#Etags-and-tags)。

區域的名稱在資源群組內必須是唯一的，且區域必須尚未存在，否則作業會失敗。

不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。雖然多個區域共用相同的名稱，但每個執行個體會被指派不同的名稱伺服器位址，而且從父系網域只能委派一個執行個體。如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

## 取得 DNS 區域

若要抓取 DNS 區域，請使用 `azure network dns zone show`：

	azure network dns zone show myresourcegroup contoso.com

此作業會傳回 DNS 區域的識別碼、資料錄集和標記的數量。


## 列出 DNS 區域

若要抓取資源群組中的 DNS 區域，請使用 `azure network dns zone list`：

	azure network dns zone list myresourcegroup


## 更新 DNS 區域

您可以使用 `azure network dns zone set` 變更 DNS 區域資源。這不會更新區域內的任何 DNS 記錄集 (請參閱[如何管理 DNS 記錄](dns-operations-recordsets.md))。它只用來更新區域資源本身的屬性。這在目前限於區域資源的 Azure 資源管理員「標記」。如需詳細資訊，請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#Etags-and-tags)。

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## 刪除 DNS 區域

可以使用 `azure network dns zone delete` 刪除 DNS 區域。
 
在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

	azure network dns zone delete myresourcegroup contoso.com 

此作業具有選擇性的 '-q' 參數，可隱藏要求您確認是否想要移除 DNS 區域的提示。


## 後續步驟


[管理 DNS 記錄](dns-operations-recordsets-cli.md)

[使用 .NET SDK 自動化作業](dns-sdk.md)

<!---HONumber=AcomDC_1217_2015-->