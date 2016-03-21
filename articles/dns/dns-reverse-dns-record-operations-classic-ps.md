<properties 
   pageTitle="如何在傳統部署模型中使用 PowerShell 管理服務的反向 DNS 記錄| Microsoft Azure"
   description="如何在傳統部署模型中使用 PowerShell 管理 Azure 服務的反向 DNS 記錄或 PTR 記錄。"
   services="DNS"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/22/2016"
   ms.author="joaoma" />

# 如何使用 PowerShell 管理服務 (傳統) 的反向 DNS 記錄

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-classic-selectors-include.md](../../includes/dns-reverse-dns-record-operations-classic-selectors-include.md)]<BR>[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]<BR>[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](dns-reverse-dns-record-operations-ps.md)。

## 將反向 DNS 記錄新增至現有的雲端服務
您可以使用 "Set-AzureService" Cmdlet，將反向 DNS 新增至現有的雲端服務。

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 建立具有反向 DNS 記錄的新雲端服務 
您可以使用 "Set-AzureService" Cmdlet，建立指定了反向 DNS 屬性的新雲端服務：

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## 檢視現有雲端服務的反向 DNS 記錄
您可以使用 "Get-AzureService" Cmdlet，檢視現有雲端服務的設定值。

	PS C:\> Get-AzureService "contosoapp1"

## 移除現有雲端服務的反向 DNS 記錄
您可以使用 "Set-AzureService" Cmdlet，移除現有雲端服務的反向 DNS 屬性。這可以透過將 DNS 屬性值設定為空白來完成。

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

## 反向 DNS 記錄的驗證
為了確保其他人無法建立對應至您 DNS 網域的反向 DNS 記錄，Azure 僅會在下列其中一種情況成立時允許建立反向 DNS 記錄：

- 反向 DNS FQDN 為其所指定之「雲端服務」的名稱，或是相同訂用帳戶內的任何「雲端服務」名稱，例如，例如，反向 DNS 為 "contosoapp1.cloudapp.net."。
- 反向 DNS FQDN 正向解析為其所指定之「雲端服務」的名稱或 IP，或是相同訂用帳戶內任何「雲端服務」的名稱或 IP，例如，反向 DNS 為 "app1.contoso.com."，這是 contosoapp1.cloudapp.net. 的 CName 別名。驗證檢查只會在設定或修改「雲端服務」的反向 DNS 屬性時才會執行。不會執行定期的重新驗證。

[AZURE.INCLUDE [常見問題集](../../includes/dns-reverse-dns-record-operations-faq-include.md)]

<!---HONumber=AcomDC_0309_2016-->