## 什麼是反向 DNS 記錄？

反向 DNS 記錄使用於多種情況。伺服器驗證和驗證伺服器之間的要求。例如，反向 DNS 記錄廣泛用於對抗垃圾郵件，方法是透過驗證電子郵件訊息寄件者的反向 DNS 記錄，此外，也辨識該主機是否已授權從原始網域傳送電子郵件。<BR> 反向 DNS 記錄 (或 PTR 記錄) 是可將可公開路由傳送 IP 位址轉譯回名稱的 DNS 記錄類型。在 DNS 中，像是 app1.contoso.com 這樣的名稱會在稱為正向解析的程序中解析為 IP 位址。若是使用反向 DNS，這項程序會反轉，將 IP 位址解析為名稱。<BR> 如需有關反向 DNS 記錄的詳細資訊，請參閱[這裡](http://en.wikipedia.org/wiki/Reverse_DNS_lookup)。<BR>

## Azure 如何支援 Azure 服務的反向 DNS 記錄？

Microsoft 與數個註冊管理機構合作，可提供足夠的可公開路由傳送 IP 區塊。每個區塊則被委派給 Microsoft 擁有及操作的授權 DNS 伺服器。Microsoft 會針對指派給它的所有可公開路由傳送 IP 區塊，裝載其反向 DNS 區域。<BR> Azure 會為指派給部署的可公開路由傳送 IP，指定自訂的完整網域名稱 (FQDN)。然後會傳回該 IP 的這些自訂 FQDN 與反向 DNS 查閱。<BR> 針對所有指派的可公開路由傳送 IP，以及使用傳統部署模型和 ARM 部署模型部署的服務，Azure 提供了反向 DNS 支援，而不需要額外的成本。

<!---HONumber=AcomDC_0309_2016-->