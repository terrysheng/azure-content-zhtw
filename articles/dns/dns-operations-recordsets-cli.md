<properties 
   pageTitle="在 Azure DNS 管理 DNS 記錄集和記錄 |Microsoft Azure" 
   description="將網域裝載於 Azure DNS 時，在 Azure DNS 管理 DNS 記錄集和記錄。對記錄集和記錄執行作業的所有 PowerShell 命令。" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/07/2015"
   ms.author="joaoma"/>

# 如何管理 DNS 記錄

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)

本指南示範如何管理 DNS 區域的記錄集和記錄。

請務必了解 DNS 記錄集和個別 DNS 記錄之間的差別。記錄集是指一個區域中有相同名稱和相同類型的記錄集合。如需詳細資訊，請參閱[了解記錄集和記錄](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records)。

## 建立記錄集

請使用 `azure network dns record-set create` 命令建立記錄集。您必須指定記錄集名稱、區域、存留時間 (TTL) 和記錄類型。

>[AZURE.NOTE]記錄集名稱必須是相對名稱，不含區域名稱。比方說，區域 'contoso.com' 中的記錄集名稱 'www' 會建立具有完整名稱 'www.contoso.com' 的記錄集。

>針對位於區域頂點的記錄集，請使用 "@"做為記錄集名稱 (包括引號)。記錄集的完整名稱就等於區域名稱，在此案例中為 "contoso.com"。

Azure DNS 支援下列記錄類型： A、AAAA、CNAME、MX、NS、SOA、SRV、TXT。每個區域會自動建立 SOA 類型的記錄集，無法另外建立。

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]CNAME 記錄集不能與其他具有相同名稱的記錄集共存。例如，您無法同時建立具有相對名稱 'www' 的 CNAME 和具有相對名稱 'www' 的 A 記錄。因為區域頂點 (名稱 = '@') 一定會包含建立區域時所建立的 NS 和 SOA 記錄集，這表示您無法在區域頂點建立 CNAME 記錄集。這些條件約束源自於 DNS 標準，並不是 Azure DNS 的限制。

### 萬用字元記錄

Azure DNS 支援[萬用字元記錄](https://en.wikipedia.org/wiki/Wildcard_DNS_record)。會針對具有相符名稱的任何查詢傳回 (除非有來自非萬用字元記錄集的更接近相符項目)。

>[AZURE.NOTE]若要建立萬用字元記錄集，請使用記錄集名稱 "*"，或其第一個標籤為 "*" 的名稱，例如 "*.foo"。

>針對所有記錄類型支援 NS 和 SOA 以外的所有萬用字元記錄集。

## 取得記錄集
若要擷取現有的記錄集，請使用 `azure network dns record-set show`，指定資源群組、區域名稱、記錄集相對名稱以及記錄類型：

	azure network dns record-set show myresourcegroup contoso.com www A


## 列出記錄集

您可以使用 `azure network dns record-set list` 命令列出 DNS 區域中的所有記錄：

### 選項 1 
列出所有記錄集。這會傳回所有記錄集，而不論名稱或記錄類型：

	azure network dns record-set list myresourcegroup contoso.com

### 選項 2 

列出指定記錄類型的記錄集。這會傳回符合指定記錄類型 (此案例中為 A 記錄) 的所有記錄集：


	azure network dns record-set list myresourcegroup contoso.com A 

在這兩種情況中，您都要指定資源群組名稱和區域名稱。

## 將記錄加入至記錄集

使用 `azure network dns record-set add-record` 將記錄加入記錄集。

將記錄加入至記錄集的參數，根據記錄集的類型而所有不同。例如，使用 'A' 類型的記錄集時，您只能使用參數 "-a `<IPv4 address>`" 來指定記錄。

下列範例示範如何建立每一種記錄類型的記錄集 (包含單一記錄)。

### 建立含有單一記錄的 A 記錄集

若要建立記錄集，請使用 `azure network dns record-set create`，指定資源群組、區域名稱、記錄集相對名稱、記錄類型以及存留時間 (TTL)：
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]若未定義 --ttl 參數，預設值為 4 (以秒為單位)。


建立 A 記錄集之後，使用 `azure network dns record-set add-record` 將 IPv4 位址加入記錄集：

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### 建立含有單一記錄的 AAAA 記錄集

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### 建立含有單一記錄的 CNAME 記錄集

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]CNAME 記錄只允許一個單一字串值。

### 建立含有單一記錄的 MX 記錄集

此範例會使用記錄集名稱 "@"，在區域頂點 (例如 "contoso.com") 建立 MX 記錄。對於 MX 記錄而言，這很常見。

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### 建立含有單一記錄的 NS 記錄集

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### 建立含有單一記錄的 SRV 記錄集

如果在區域的根部建立 SRV 記錄，只需要在記錄名稱中指定 service 和 protocol — 記錄名稱不需要同時包含 '.@'

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### 建立含有單一記錄的 TXT 記錄集

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## 修改現有的記錄集


下列範例示範作法：

### 更新現有記錄集的記錄

在本範例中，我們會將另一個 IP 位址 (1.2.3.4) 加入現有的 A 記錄集 (www)：

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


您將使用 `azure network dns record-set delete-record` 從記錄集移除現有的值：
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## 從現有的記錄集移除記錄

使用 `azure network dns record-set delete-record` 可從記錄集移除記錄。請注意，要移除的記錄必須完全符合現有的記錄，包括所有參數。

移除記錄集的最後一筆記錄不會刪除記錄集。如需詳細資訊，請參閱下方的[刪除記錄集](#delete-a-record-set)。


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### 從記錄集移除 AAAA 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### 從記錄集移除 CNAME 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### 從記錄集移除 MX 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### 從記錄集移除 NS 記錄
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### 從記錄集移除 SRV 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### 從記錄集移除 TXT 記錄

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## 刪除記錄集
您可以使用 Remove-AzureDnsRecordSet Cmdlet 刪除記錄集。

>[AZURE.NOTE]您無法在建立區域時所自動建立的區域頂點 (名稱 = ‘@’) 刪除 SOA 和 NS 記錄集。刪除區域時會自動刪除它們。

在下列範例中，A 記錄集 "test-a" 將會從 contoso.com DNS 區域移除：

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

選擇性的 ’-q’ 參數可用來隱藏確認提示。


##另請參閱

[開始建立記錄集和記錄](dns-getstarted-create-recordset-cli.md)<BR> [在 DNS 區域上執行作業](dns-operations-dnszones-cli.md)<BR> [使用 .NET SDK 將作業自動化](dns-sdk.md)
 

<!---HONumber=Sept15_HO2-->