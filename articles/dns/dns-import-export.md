<properties
   pageTitle="使用 Azure DNS 匯入和匯出網域區域檔案 | Microsoft Azure"
   description="了解如何使用 Azure CLI 匯入和匯出 DNS 區域檔案至 Azure DNS"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/11/2016"
   ms.author="jonatul"/>

# 匯入和匯出 DNS 區域檔案


本指南將說明如何在 Azure DNS 中匯入和匯出 DNS 區域檔案。

## DNS 區域移轉簡介

DNS 區域檔案是一個文字檔，其中包含區域中每筆網域名稱系統 (DNS) 記錄的詳細資料。它會遵循標準格式，使其適合於在 DNS 系統之間傳送 DNS 記錄。使用區域檔案是從 Azure DNS 移入或移出 DNS 區域的快速、可靠又方便的方法。

Azure DNS 支援使用 Azure 命令列介面 (CLI) 匯入和匯出區域檔案。本文說明如何使用此功能。

Azure CLI 是用來管理 Azure 服務的跨平台命令列工具。它可從 [Azure 下載頁面](https://azure.microsoft.com/downloads/)取得，且適用於 Windows、Mac 及 Linux 平台。跨平台支援對於匯入和匯出區域檔案特別重要，因為最常見的名稱伺服器軟體 [BIND](https://www.isc.org/downloads/bind/) 通常會在 Linux 上執行。

## 取得現有的 DNS 區域檔案

將 DNS 區域檔案匯入 Azure DNS 之前，您必須取得區域檔案的複本。此檔案的來源將取決於目前裝載 DNS 區域的位置。例如：

- 如果 DNS 區域是由合作夥伴服務 (例如網域註冊機構、專用 DNS 主機服務提供者或其他雲端提供者) 託管，該服務應提供下載 DNS 區域檔案的能力。
-	如果 DNS 區域託管在 Windows DNS 上，區域檔案的預設資料夾是 **%systemroot%\\system32\\dns**。DNS 服務管理主控台的 [一般] 索引標籤上，也會顯示每個區域檔案的完整路徑。
-	如果使用 BIND 裝載 DNS 區域，則 BIND 組態檔 **named.conf** 中會指定每個區域的區域檔案位置。

>[AZURE.NOTE] 下載自 GoDaddy 的區域檔案會有些微非標準格式。您必須先修正格式，再將這些區域檔案匯入 Azure DNS。每個 DNS 記錄 RData 中的 DNS 名稱會指定為完整名稱，但是結尾沒有 "."。 這表示其他 DNS 系統會將這些名稱解譯為相對名稱。將區域檔案匯入 Azure DNS 之前，您必須先加以編輯，以將結尾的 "." 附加至這些名稱。

## 將 DNS 區域檔案匯入 Azure DNS

用來匯入 DNS 區域的 Azure CLI 命令格式為：

	azure network dns zone import [options] <resource group> <zone name> <zone file name>

其中：

- `<resource group>` 是 Azure DNS 中區域的資源群組名稱。
- `<zone name>` 是區域的名稱。
- `<zone file name>` 是要匯入之區域檔案的路徑/名稱。

如果資源群組中不存在具有此名稱的區域，則會為您建立。如果區域已經存在，則匯入的記錄集將會與現有的記錄集合併。若要覆寫現有的記錄集，請使用 `--force` 選項。下面會提供[如何合併記錄集](#merge-the-file-with-existing-data)的進一步詳細資料。

若要確認區域檔案的格式，但不實際進行匯入，請使用 `--parse-only` 選項。

讓我們看看逐步範例。假設您要匯入區域 **contoso.com** 的區域檔案。

1. 使用 Azure CLI 登入您的 Azure 訂用帳戶。

	`azure login`

2. 選取您要建立新的 DNS 區域的訂用帳戶。

	`azure account set <subscription name>`

3. Azure DNS 是僅供 Azure 資源管理員使用的服務，因此 Azure CLI 必須切換到資源管理員模式。

	`azure config mode arm`

4. 使用 Azure DNS 服務之前，您必須註冊您的訂用帳戶才能使用 Microsoft.Network 資源提供者 (每個訂用帳戶只需執行一次此作業)。

	`azure provider register Microsoft.Network`

5. 如果您還沒有資源管理員資源群組，您也必須加以建立。

	`azure group create myresourcegroup westeurope`

6. 若要將 **contoso.com.txt** 檔案中的 **contoso.com** 區域匯入至 **myresourcegroup** 資源群組中的新 DNS 區域，請執行命令 `azure network dns zone import`。

	`azure network dns zone import myresourcegroup contoso.com contoso.com.txt`

    此命令將會載入並剖析該區域檔案。此命令會在 Azure DNS 服務上執行一系列的命令，以建立區域和區域中的所有記錄集。

    此命令也會在主控台視窗中報告進度，以及任何的錯誤或警告。由於記錄集是以系列方式建立，可能需要幾分鐘的時間來匯入大型的區域檔案。

## 在匯入檔案後驗證 DNS 區域

您可以使用下列 Azure CLI 命令來列出記錄。

	azure network dns record-set list myresourcegroup contoso.com

您也可以透過 PowerShell 使用 `Get-AzureRmDnsRecordSet` 來執行此作業。

您也可以使用 `nslookup` 來驗證記錄的名稱解析。因為尚未委派區域，您必須明確指定正確的 Azure DNS 名稱伺服器。以下範例顯示如何擷取已指派給區域的名稱伺服器名稱。IT 也會示範如何使用 `nslookup` 查詢 "www" 記錄。

	C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
	info:    Executing command network dns record-set show
	+ Looking up the DNS Record Set "@" of type "NS"
	data:    Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-01.azure-dns.com
	data:        Name server domain name     : ns2-01.azure-dns.net
	data:        Name server domain name     : ns3-01.azure-dns.org
	data:        Name server domain name     : ns4-01.azure-dns.info
	data:
	info:    network dns record-set show command OK

	C:\> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  40.90.4.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
            134.170.188.221

在確認已正確匯入區域後，您必須[更新 DNS 委派](dns-domain-delegation.md)以指向 Azure DNS 名稱伺服器。

## 合併檔案與現有資料

匯入區域檔案將會在 Azure DNS 中建立新區域 (如果區域不存在)。如果區域已經存在，則區域檔案中的記錄集必須與現有的記錄集合併。合併行為如下所示。

- 預設會合併現有和新的記錄集。合併的資料錄集內相同的記錄會進行重複資料刪除。
- 或者，藉由指定 `--force` 選項，匯入程序將會以新的記錄集取代現有記錄集。不會移除在匯入的區域檔案中沒有對應記錄集的現有記錄集。
- 合併記錄集時，會使用預先存在之記錄集的存留時間 (TTL)。使用 `--force` 時，會使用新記錄集的 TTL。
- 不論是否使用 `--force`，起始點授權 (SOA) 參數 (`host` 除外) 一律取自匯入的區域檔案。同樣地，對於位於區域頂點的名稱伺服器記錄集，TTL 一律取自匯入的區域檔案。
- 除非指定 `--force` 參數，否則匯入的 CNAME 記錄將不會取代具有相同名稱的現有 CNAME 記錄。
- 當 CNAME 記錄與另一筆同名但不同類型的記錄 (不論何者是現有或新的記錄) 之間發生衝突時，會保留現有的記錄。這與使用 `--force` 無關。

## 其他技術詳細資料
下列幾點提供有關區域匯入程序的其他技術詳細資訊。

- `$TTL` 指示詞為選擇性並受到支援。若未提供 `$TTL` 指示詞，將會匯入沒有明確 TTL 的記錄，並設定為預設 TTL 3600 秒。如果相同資料錄集中有兩筆記錄指定不同的 TTL，則會使用較低的值。
- `$ORIGIN` 指示詞為選擇性並受到支援。若未設定 `$ORIGIN`，則使用的預設值是在命令列上指定的區域名稱 (加上結尾的 ".")。
- `$INCLUDE` 和 `$GENERATE` 指示詞不受支援。
- 支援這些記錄類型：A、AAAA、CNAME、MX、NS、SOA、SRV 和 TXT。  
- Azure DNS 會在建立區域時，自動建立 SOA 記錄。當您匯入區域檔案時，所有 SOA 參數都會取自該區域檔案，但 `host` 參數*除外*。這個參數會使用 Azure DNS 所提供的值。這是因為此參數必須參照 Azure DNS 所提供的主要名稱伺服器。
- Azure DNS 也會在建立區域時，自動建立位於區域頂點的名稱伺服器記錄集。只會匯入此記錄集的 TTL。這些記錄包含 Azure DNS 所提供的名稱伺服器名稱。所匯入區域檔案中包含的值不會覆寫記錄資料。
- 在公開預覽期間，Azure DNS 僅支援單一字串 TXT 記錄。Multistring TXT 記錄會串連起來並截斷為 255 個字元。

## 從 Azure DNS 匯出 DNS 區域檔案

用來匯入 DNS 區域的 Azure CLI 命令格式為：

	azure network dns zone export [options] <resource group> <zone name> <zone file name>

其中：

- `<resource group>` 是 Azure DNS 中區域的資源群組名稱。
- `<zone name>` 是區域的名稱。
- `<zone file name>` 是要匯出之區域檔案的路徑/名稱。

和區域匯入時一樣，您必須先登入，選擇訂用帳戶，然後設定 Azure CLI 以使用資源管理員模式。讓我們看看逐步範例。

1. 使用 Azure CLI 登入您的 Azure 訂用帳戶。

	`azure login`

2. 選取您要建立新的 DNS 區域的訂用帳戶。

	`azure account set <subscription name>`

3. Azure DNS 是僅能以 Azure 資源管理員運作的服務。Azure CLI 必須切換為資源管理員模式。

	`azure config mode arm`

4. 若要將 **myresourcegroup** 資源群組中的現有 Azure DNS 區域 **contoso.com** 匯出至 **contoso.com.txt** 檔案 (在目前資料夾中)，請執行 `azure network dns zone export`。

	`azure network dns zone export myresourcegroup contoso.com contoso.com.txt`

此命令會呼叫 Azure DNS 服務，以列舉區域中的記錄集並將結果匯出至 BIND 相容的區域檔案。

<!---HONumber=AcomDC_0204_2016-->