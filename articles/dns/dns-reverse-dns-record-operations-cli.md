<properties 
   pageTitle="如何在資源管理員中使用 Azure CLI 管理服務的反向 DNS 記錄 | Microsoft Azure"
   description="如何在資源管理員中使用 Azure CLI 管理 Azure 服務的反向 DNS 記錄或 PTR 記錄"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="s-malone" />

# 如何使用 Azure CLI 管理服務的反向 DNS 記錄

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](dns-reverse-dns-record-operations-classic-ps.md)。

## 反向 DNS 記錄的驗證 
為了確保其他人無法建立對應至您 DNS 網域的反向 DNS 記錄，Azure 僅會在下列其中一種情況成立時允許建立反向 DNS 記錄：

- "ReverseFqdn" 與其所指定之公用 IP 位址資源的 "Fqdn" 相同，或是相同訂用帳戶內任何公用 IP 位址的 "Fqdn"，例如，"ReverseFqdn" 為 "contosoapp1.northus.cloudapp.azure.com."。

- "ReverseFqdn" 正向解析為其所指定之公用 IP 位址的名稱或 IP，或是相同訂用帳戶內任何公用 IP 位址的 "Fqdn" 或 IP，例如，"ReverseFqdn" 為 "app1.contoso.com."，這是 "contosoapp1.northus.cloudapp.azure.com." 的 CName 別名。

驗證檢查只會在設定或修改公用 IP 位址的反向 DNS 屬性時才會執行。不會執行定期的重新驗證。

## 將反向 DNS 新增至現有的公用 IP 位址
您可以使用 azure network public-ip set，將反向 DNS 新增至現有的公用 IP 位址：

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

如果您想要將反向 DNS 新增至尚未有 DNS 名稱的現有公用 IP 位址，您也必須指定 DNS 名稱。您可以使用 azure network public-ip set 來達到此目的：

	azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## 建立具有反向 DNS 的公用 IP 位址
您可以透過使用 azure network public-ip create 指定的反向 DNS 屬性，建立新的公用 IP 位址：

	azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.
 
## 檢視現有公用 IP 位址的反向 DNS
您可以使用 azure network public-ip show，檢視現有公用 IP 位址的設定值：

	azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS 

## 移除現有公用 IP 位址的反向 DNS
您可以使用 azure network public-ip set，移除現有公用 IP 位址的反向 DNS 屬性。這可以透過將 ReverseFqdn 屬性值設定為空白來完成：

	azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “” 

[AZURE.INCLUDE [常見問題集](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]

<!-----HONumber=AcomDC_0316_2016-->