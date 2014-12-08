#對 Azure 網站啟用 HTTPS

> [WACOM.NOTE]
> 快速完成啟用 -- 使用全新的 Azure [引導式逐步解說](http://support.microsoft.com/kb/2990804)！在彈指之間完成自訂網域名稱的關聯，以及與 Azure 雲端服務或 Azure 網站之間的通訊 (SSL) 保護。

您可以透過採用安全通訊端層 (SSL) 加密的 HTTPS 來保護網站與瀏覽器之間的通訊。這是最常用來保護在網際網路上傳送之資料的方法，並可讓訪客安心知道其與您網站進行的交易正受到安全保護。本文討論如何對 Azure 網站設定 HTTPS。 

<a href="bkmk_azurewebsites"></a><h2>HTTPS for the \*.azurewebsites.net 網域</h2>

如果您不想使用自訂網域名稱，而是想使用 Azure 指派給您網站的 \*.azurewebsites.net 網域 (例如，contoso.azurewebsites.net)，則已利用 Microsoft 提供的憑證在您的網站上啟用 HTTPS。只要使用 **https://mywebsite.azurewebsites.net**，即可存取您的網站。不過，\*.azurewebsites.net 是萬用字元網域。如同[所有的萬用字元網域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)，這並不像使用自訂網域搭配自己的憑證那麼安全。 

本文件的其餘內容提供關於對自訂網域 (如 **contoso.com**、**www.contoso.com** 或 **\*.contoso.com**) 啟用 HTTPS 的詳細資訊。

<a href="bkmk_domainname"></a><h2>對自訂網域啟用 SSL</h2>

若要對自訂網域 (如 **contoso.com**) 啟用 HTTPS，您必須先向網域名稱註冊機構註冊自訂網域名稱。如需關於如何設定 Azure 網站之網域名稱的詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱](/zh-tw/develop/net/common-tasks/custom-dns-web-site/)。在註冊自訂網域名稱並設定您的網站來回應該自訂名稱之後，您必須要求網域的 SSL 憑證。 

> [WACOM.NOTE] 若要對自訂網域名稱啟用 HTTPS，您必須針對**標準** Web 主控方案模式設定您的網站。如果您目前正使用免費或共用模式，則換成使用標準模式可能會帶來額外成本。如需共用與**標準**定價的詳細資訊，請參閱[定價詳細資料][pricing]。 

一旦具有有效的自訂網域，請執行下列步驟以對您的網站啟用 HTTPS：

1. [取得 SSL 憑證](#bkmk_getcert)
1. [設定標準模式](#bkmk_standardmode)
1. [設定 SSL](#bkmk_configuressl)
1. [在 Azure 網站上強制使用 HTTPS](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>取得 SSL 憑證</h2>

在要求 SSL 憑證之前，您必須先決定有哪些網域名稱要由該憑證保護。這將決定您必須取得的憑證類型。如果您只需要保護單一網域名稱 (如 **contoso.com** 或 **www.contoso.com**)，則基本憑證就已足夠。如果您需要保護多個網域名稱，例如 **contoso.com**、**www.contoso.com** 和 **mail.contoso.com**，您可以取得[萬用字元憑證](http://en.wikipedia.org/wiki/Wildcard_certificate)或是具[主體替代名稱](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName) 的憑證。

使用 Azure 網站的 SSL 憑證必須經過簽署的[憑證授權單位](http://en.wikipedia.org/wiki/Certificate_authority) (CA)。如果您還沒有此類憑證，則必須向發行 SSL 憑證的公司取得。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][cas]。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
* 憑證的主體名稱必須符合用來存取網站的網域。如果您需要利用此憑證來供應多個網域，則必須如前文所討論，使用萬用字元值或指定 subjectAltName 值。
* 憑證至少應該要以 2048 位元加密。
* Azure 網站不支援私人 CA 伺服器所核發的憑證。

若要取得可搭配 Azure 網站使用的 SSL 憑證，您可將憑證簽署要求 (CSR) 提交至憑證授權單位，然後從您收回的憑證產生 .pfx 檔案。您可以使用您所選的工具來執行此作業。下面是取得憑證的一些常見方式：

- [使用 Certreq.exe 取得憑證](#bkmk_certreq)
- [使用 IIS 管理員取得憑證](#bkmk_iismgr)
- [使用 OpenSSL 取得憑證](#bkmk_openssl)
- [使用 OpenSSL 取得 SubjectAltName 憑證](#bkmk_subjectaltname)
- [產生自我簽署的憑證 (僅供測試)](#bkmk_selfsigned) 

> [WACOM.NOTE] 遵循步驟時，系統會提示您輸入**一般名稱**，例如 `www.contoso.com`。對於萬用字元憑證，這個值應該是 \*.domainname (例如，\*.contoso.com)。如果您需要同時支援萬用字元名稱 (如 \*.contoso.com) 和根網域名稱 (如 contoso.com)，則可以使用萬用 subjectAltName 憑證。

> [WACOM.NOTE] Azure 網站支援橢圓曲線密碼編譯 (ECC) 憑證；不過，他們相對而言比較新，您應該在具體步驟中使用 CA 來建立 CSR。

如果 CA 使用**[中繼憑證](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (也稱為鏈結憑證)，則您還需要取得這些中繼憑證。使用中繼憑證比使用「未鏈結憑證」安全，因此常會見到 CA 使用中繼憑證。中繼憑證通常是從 CA 網站個別下載而得。本文中的步驟提供關於如何確保任何中繼憑證已與上傳至您 Azure 網站的憑證相合併的步驟。 

<a href="bkmk_certreq"></a>
###使用 Certreq.exe 取得憑證 (僅限 Windows)

Certreq.exe 是一項用來建立憑證要求的 Windows 公用程式。自 Windows XP/Windows Server 2000 起，它就已是基本 Windows 安裝的一部分，因此新版 Windows 系統上應該會提供。請透過下列步驟，使用 certreq.exe 來取得 SSL 憑證。

1. 開啟 [**記事本**]，然後建立含有下列內容的新文件。將 Subject 一行中的 **mysite.com** 取代為您網站的自訂網域名稱。例如，Subject = "CN=www.contoso.com"。

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	如需上面所指定選項以及其他可用選項的詳細資訊，請參閱 [Certreq 參考文件](http://technet.microsoft.com/library/cc725793.aspx)。

2. 將文字檔儲存為 **myrequest.txt**。

3. 從 [**開始畫面**] 或 [**開始功能表**] 執行 **cmd.exe**。

4. 在命令提示字元中，使用下列命令來建立憑證要求檔案：

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	指定步驟 1 中所建立 **myrequest.txt** 檔案的路徑，以及在建立 **myrequest.csr** 檔案時所要使用的路徑。

5. 將 **myrequest.csr** 提交至憑證授權單位，以取得 SSL 憑證。這可能包括上傳檔案，或在 [記事本] 中開啟檔案並將內容直接貼到 Web 表單。

	如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][cas]。

6. 等到憑證授權單位提供憑證檔 (.CER) 給您後，將該檔案儲存至用來產生要求的電腦，然後使用下列命令來接受要求並完成憑證產生程序。

		certreq -accept -user mycert.cer

	在此情況下，從憑證授權單位那邊收到的 **mycert.cer** 憑證將會用來完成憑證簽章。沒有檔案會建立；憑證會儲存至 Windows 憑證存放區中。

6. 如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取針對 Microsoft IIS 所提供的版本。

	下載憑證之後，請在檔案總管中於該憑證上按一下滑鼠右鍵，然後選取 [**安裝憑證**]。使用 [**憑證匯入精靈**] 中的預設值，並持續選取 [**下一步**]，直到匯入完成。

7. 若要從憑證存放區中匯出憑證，請從 [**開始畫面**] 或 [**開始功能表**] 執行 **certmgr.msc**。出現 [**憑證管理員**] 時，展開 [**個人**] 資料夾，然後選取 [**憑證**]。在 [**核發給**] 欄位中，找出有哪個項目具有您為了它而要求憑證的自訂網域名稱。[**核發者**] 欄位中應該會列出您用於此憑證的憑證授權單位。

	![insert image of cert manager here][certmgr]

9. 在憑證上按一下滑鼠右鍵，並選取 [**所有工作**]，然後選取 [**匯出**]。在 [**憑證匯出精靈**] 中，按 [**下一步**]，然後選取 [**是，匯出私密金鑰**]。按 [**下一步**]。

	![Export the private key][certwiz1]

10. 選取 [**個人資訊交換 - PKCS #12**]、[**包含憑證鏈結中的所有憑證**] 和 [**匯出所有延伸內容**]。按 [**下一步**]。

	![include all certs and extended properties][certwiz2]

11. 選取 [**密碼**]，然後輸入並確認密碼。按 [**下一步**]。

	![specify a password][certwiz3]

12. 提供將包含所匯出憑證的路徑和檔名。檔名的副檔名應該為 **.pfx**。按 [**下一步**]，完成此程序。

	![provide a file path][certwiz4]

您現在可以將所匯出的 PFX 檔案上傳至 Azure 網站。

<a href="bkmk_openssl"></a>
###使用 OpenSSL 取得憑證

1. 從命令列、Bash 或終端機工作階段中，使用下列命令產生私密金鑰和憑證簽署要求：

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. 當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []: 

	此程序完成之後，您應會有兩個檔案：**myserver.key** 和 **server.csr**。**server.csr** 包含憑證簽署要求。

3. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][cas]。

4. 從 CA 取得憑證之後，將它儲存成名稱為 **myserver.crt** 的檔案。如果您的 CA 提供文字格式的憑證，則只需要將憑證文字貼入 **myserver.crt** 檔案。在文字編輯器中檢視檔案時，檔案內容應該與下列類似：

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	儲存檔案。

5. 從命令列、Bash 或終端機工作階段中，使用下列命令，將 **myserver.key** 和 **myserver.crt** 轉換為 **myserver.pfx** (Azure 網站所需的格式)：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，輸入 .pfx 檔案的保護密碼。

	<div class="dev-callout"> 
	<b>注意</b>
	<p>如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。</p>
	<p>下列命令示範如何建立內註有中繼憑證 (其包含在 <b>intermediate-cets.pem</b> 檔案中) 的 .pfx 檔案：</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	執行此命令之後，您應該會有適合用於您 Azure 網站的 **myserver.pfx** 檔案。

###<a name="bkmk_iismgr"></a>使用 IIS 管理員取得憑證

如果您熟悉 IIS 管理員，可以用它來產生可用於 Azure 網站的憑證。

1. 使用 IIS 管理員產生 CSR，以傳送至憑證授權單位。如需產生 CSR 的詳細資訊，請參閱[要求網際網路伺服器憑證 (IIS 7)][iiscsr]。

2. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][cas]。

3. 使用憑證授權單位廠商所提供的憑證來完成 CSR。如需完成 CSR 的詳細資訊，請參閱[安裝網際網路伺服器憑證 (IIS 7)][installcertiis]。

4. 如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取針對 Microsoft IIS 所提供的版本。

	下載憑證之後，請在檔案總管中於該憑證上按一下滑鼠右鍵，然後選取 [**安裝憑證**]。使用 [**憑證匯入精靈**] 中的預設值，並持續選取 [**下一步**]，直到匯入完成。

4. 從 IIS 管理員匯出憑證。如需匯出憑證的詳細資訊，請參閱[匯出伺服器憑證 (IIS 7)][exportcertiis]。後面的步驟中會將所匯出的檔案上傳至 Azure 來用於您的 Azure 網站。

	<div class="dev-callout"> 
	<b>注意</b>
	<p>在匯出程序期間，請務必選取 [<strong>是，匯出私密金鑰</strong>] 選項。這樣會在匯出的憑證中納入私密金鑰。</p>
	</div>

	<div class="dev-callout"> 
	<b>注意</b>
	<p>在匯出程序期間，請務必選取 [<strong>包含憑證路徑中的所有憑證</strong>] 和 [<strong>匯出所有延伸內容</strong>] 選項。這樣會在匯出的憑證中納入所有中繼憑證。</p>
	</div>


###<a href="bkmk_subjectaltname"></a>使用 OpenSSL 取得 SubjectAltName 憑證

OpenSSL 可以用來建立憑證要求 (並讓該要求使用 SubjectAltName 延伸來透過單一憑證支援多個網域名稱)，不過，這需要有組態檔。下列步驟會逐步引導您建立組態檔，然後使用它來要求憑證。

1. 建立名稱為 __sancert.cnf__ 的新檔案，並使用下列程式碼作為檔案的內容：
 
		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default = 
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default = 
		localityName = Locality Name (eg, city)
		localityName_default = 
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  = 
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	請注意開頭為 'subjectAltName' 的那行。請將目前列出的網域名稱取代為您除了一般名稱外，還想要支援的網域名稱。例如：

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	您不需要變更 commonName_default 欄位，因為系統會在下列其中一個步驟中提示您輸入一般名稱。

2. 儲存 __sancert.cnf__ 檔案。

1. 使用 sancert.cnf 組態檔，以產生私密金鑰和憑證簽署要求。從 Bash 或終端機工作階段中，使用下列命令：

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. 當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	此程序完成之後，您應會有兩個檔案：**myserver.key** 和 **server.csr**。**server.csr** 包含憑證簽署要求。

3. 將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][cas]。

4. 從 CA 取得憑證之後，將它儲存成名稱為 **myserver.crt** 的檔案。如果您的 CA 提供文字格式的憑證，則只需要將憑證文字貼入 **myserver.crt** 檔案。在文字編輯器中檢視檔案時，檔案內容應該與下列類似：

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	儲存檔案。

5. 從命令列、Bash 或終端機工作階段中，使用下列命令，將 **myserver.key** 和 **myserver.crt** 轉換為 **myserver.pfx** (Azure 網站所需的格式)：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，輸入 .pfx 檔案的保護密碼。

	<div class="dev-callout"> 
	<b>注意</b>
	<p>如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA 個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。</p>
	<p>下列命令示範如何建立內註有中繼憑證 (其包含在 <b>intermediate-cets.pem</b> 檔案中) 的 .pfx 檔案：</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	執行此命令之後，您應該會有適合用於您 Azure 網站的 **myserver.pfx** 檔案。


###<a href="bkmk_selfsigned"></a>產生自我簽署的憑證 (僅供測試) 

在某些情況下，您可能會想要取得憑證來進行測試，並等到要實際投入使用時，才將向信任 CA 購買憑證。自我簽署憑證就有這樣的功能。自我簽署憑證是您將自己當成憑證授權單位來建立和簽署的憑證。雖然此憑證可以用來保護網站，但是大部分的瀏覽器在造訪該網站時都會傳回錯誤，因為憑證不是由信任的 CA 所簽署。部分瀏覽器甚至會拒絕讓您檢視網站。

- [使用 makecert 產生自我簽署憑證](#bkmk_ssmakecert)
- [使用 OpenSSL 產生自我簽署憑證](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### 使用 makecert 產生自我簽署憑證 ####

您可以在已安裝 Visual Studio 的 Windows 系統上執行下列步驟來建立測試憑證：

1. 從 [**開始功能表**] 或 [**開始畫面**] 搜尋 [**開發人員命令提示字元**]。最後，在 [**開發人員命令提示字元**] 上按一下滑鼠右鍵，然後選取 [**以系統管理員身分執行**]。

	如果您收到 [使用者帳戶控制] 對話方塊，請選取 [**是**] 繼續。

2. 從 [開發人員命令提示字元] 中，使用下列命令建立新的自我簽署憑證。您必須將 **serverdnsname** 替換成您網站的DNS。

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	此命令會建立有效日期為 01/01/2013 到 01/01/2014 的憑證，而且會將位置儲存在 CurrentUser 憑證存放區中。

3. 從 [**開始功能表**] 或 [**開始畫面**] 搜尋 **Windows PowerShell** 並啟動此應用程式。

4. 從 Windows PowerShell 提示字元中，使用下列命令匯出先前建立的憑證：

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	這會將指定的密碼以安全字串形式儲存在 $mypwd 中，並使用 **dnsname** 參數所指定的 DNS 名稱來尋找憑證，然後匯出至 **filepath** 參數所指定的檔案。而含有密碼的安全字串會用來保護匯出的檔案。

<a href="bkmk_ssopenssl"></a>
####使用 OpenSSL 產生自我簽署憑證 ####

1. 建立名稱為 **serverauth.cnf** 的新文件，並使用下列程式碼作為此檔案的內容：

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your website's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	這指定產生 Azure 網站可用之 SSL 憑證時所需的組態設定。

2. 從命令列、Bash 或終端機工作階段中，使用下列命令產生新的自我簽署憑證：

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	這會使用 **serverauth.cnf** 檔案中指定的組態設定來建立新的憑證。

3. 若要將憑證匯出至可上傳至 Azure 網站的 .PFX 檔案，請使用下列命令：

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	當系統提示時，輸入 .pfx 檔案的保護密碼。

	此命令所產生的 **myserver.pfx** 可以用來基於測試目的來保護您的 Azure 網站。

<a href="bkmk_standardmode"></a><h2>設定標準模式</h2>

若要對自訂網域啟用 HTTPS，Azure 網站必須處於**標準** Web 主控方案模式。請使用下列步驟切換至**標準**模式。

> [WACOM.NOTE] 在將網站從免費網站模式切換至**標準**模式之前，您應該先移除網站訂閱的花費上限，以免帳單期間還未結束，網站就因為已達花費上限而變得無法使用。如需共用與**標準**模式定價的詳細資訊，請參閱[定價詳細資料][pricing]。

1. 在瀏覽器中開啟[管理入口網站][portal]。

2. 在 [**網站**] 索引標籤中，按一下您網站的名稱。

	![selecting a web site][網站]

3. 按一下 [**調整**] 索引標籤。

	![The scale tab][調整]

4. 在 [**一般**] 區段中，按一下 [**標準**] 以設定 Web 主控方案模式。

	![standard mode selected][標準]

5. 按一下 [**儲存**]。當提示出現時，按一下 [**是**]。

	> [WACOM.NOTE]如果發生『設定網站「&lt;網站名稱&gt;」的規模失敗』錯誤，您可以利用詳細資料按鈕來取得詳細資訊。您可能會收到 [可用標準執行個體伺服器不足，無法滿足此要求] 錯誤。如果您收到此錯誤，請連絡 [Azure 支援](http://www.windowsazure.com/zh-tw/support/options/)。


##<a href="bkmk_configuressl"></a>設定 SSL

在執行本節中的步驟之前，您必須先建立自訂網域名稱與 Azure 網站的關聯。如需詳細資訊，請參閱[設定 Azure 網站的自訂網域名稱][customdomain]。

1. 在瀏覽器中開啟 [Azure 管理入口網站][portal]。

2. 在 [**網站**] 索引標籤中，按一下您網站的名稱，然後選取 [**設定**] 索引標籤。

	![the configure tab][configure]

3. 在 [**憑證**] 區段中，按一下 [**上傳憑證**]

	![upload a certificate][uploadcert]

4. 使用 [**上傳憑證**] 對話方塊，選取之前以 IIS 管理員或 OpenSSL 建立的 .pfx 憑證檔案。指定當初用來保護 .pfx 檔案的密碼 (如果有的話)。最後，按一下 [**檢查**] 以上傳憑證。

	![upload certificate dialog][uploadcertdlg]

5. 在 [**設定**] 索引標籤的 [**SSL 繫結**] 區段中，使用下拉式清單選取要以 SSL 保護的網域名稱，以及要使用的憑證。您也可以選擇要使用[伺服器名稱指示][sni] (SNI) 還是 IP SSL。

	![ssl bindings][sslbindings]
	
	* IP SSL 會將伺服器的專用公用 IP 位址對應至網域名稱，以建立憑證與網域名稱的關聯。這需要與您服務相關聯的每個網域名稱 (contoso.com、fabricam.com 等) 都有專用 IP 位址。這是傳統用來建立 SSL 憑證與網頁伺服器之關聯的方法。

	* SNI SSL 是 SSL 和[傳輸層安全性][tls] (TLS) 的延伸，可讓多個網域共用相同的 IP 位址，而每個網域都有個別的安全性憑證。現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI，不過，較舊的瀏覽器可能不支援 SNI。如需 SNI 的詳細資訊，請參閱 Wikipedia 上的[伺服器名稱指示][sni]一文。

6. 按一下 [**儲存**]，儲存變更並啟用 SSL。

> [WACOM.NOTE] 如果您已選取 [**IP SSL**]，而且您的自訂網域是以 A 記錄設定，則必須執行下列額外步驟：
>
> 在設定 IP SSL 繫結之後，您的網站會獲指派專用 IP 位址。您可以在網站的 [**儀表板**] 頁面上 (在 [**快速瀏覽**] 區段中) 找到這個 IP 位址。它將會列出成為 [**虛擬 IP 位址**]：
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
> 請注意，此 IP 位址與先前用來設定您網域之 A 記錄的虛擬 IP 位址不同。如果您設定成使用 SNI SSL，或未設定成使用 SSL，則不會列出此項目的位址。
>
> 2. 使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟的 IP 位址。


現在，您應該可以使用 `HTTPS://` 而非 `HTTP://` 來造訪您的網站，確認已正確設定憑證。

##<a href="bkmk_enforce"></a>在 Azure 網站上強制使用 HTTPS

Azure 網站*不* 強制使用 HTTPS。訪客可能仍會使用 HTTP 存取您的網站，這可能會危及您的網站安全性。如果想要強制您的網站使用 HTTPS，您可以使用 **URL Rewrite** 模組。Azure 網站包含 URL Rewrite 模組，此模組可讓您定義連入要求在送達應用程式之前要套用的規則。**它可用於以任何 Azure 網站支援的程式設計語言所撰寫的應用程式。** 

> [WACOM.NOTE] .NET MVC 應用程式應使用 [RequireHttps](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.requirehttpsattribute.aspx) 篩選，而非 URL Rewrite。如需使用 RequireHttps 的詳細資訊，請參閱[將安全的 ASP.NET MVC 5 應用程式部署至 Azure 網站](/zh-tw/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/)。
> 
> 如需使用其他程式設計語言和架構的程式設計方式重新導向要求的相關資訊，請參閱這些技術的文件。

您可以在儲存於應用程式根目錄的 **web.config** 檔案中定義 URL Rewrite 規則。下列範例包含可強制所有連入流量使用 HTTPS 的基本 URL Rewrite 規則。

<a name="example"></a>**URL Rewrite 範例 Web.Config**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

此規則的運作方式是當使用者使用 HTTP 要求頁面時，便傳回 HTTP 狀態碼 301 (永久重新導向)。301 會將要求重新導向至與訪客要求相同的 URL，但使用 HTTPS 來取代要求的 HTTP 部分。例如，HTTP://contoso.com 會被重新導向至 HTTPS://contoso.com。

> [WACOM.NOTE]如果您的應用程式是以 **Node.js**、**PHP**、**Python Django** 或 **Java** 撰寫而成，則它可能不包含 web.config 檔案。不過，在 Azure 網站上託管時，**Node.js**、**Python Django** 和 **Java** 都會確實用到 web.config - Azure 會在部署過程中自動建立此檔案，因此您永遠看不到此檔案。如果您在應用程式中加入一個 web.config 檔案，則它將覆寫 Azure 自動產生的同名檔案。

###.NET

若是 .NET 應用程式，請修改應用程式的 web.config 檔案，並將[範例](#example)中的 **&lt;rewrite>** 區段新增至 **&lt;system.WebServer>** 區段。

如果您的 web.config 檔案已包含 **&lt;rewrite>** 區段，請將[範例](#example)中的 **&lt;rule>** 區段新增為 **&lt;rules>** 區段中的第一個項目。

###PHP

若是 PHP 應用程式，只需將[範例](#example)另存新檔為應用程式根目錄中的 web.config 檔案，然後將應用程式重新部署至 Azure 網站即可。

###Node.js、Python Django 和 Java

如果 Node.js、Python Django 和 Java 應用程式未提供 web.config 檔案，則此檔案會自動建立，但因為此檔案會在部署過程中建立，因此它只會存在於伺服器上。這個自動產生的檔案會包含告知 Azure 如何主控應用程式的設定。

若要從網站擷取與修改自動產生的檔案，請使用下列步驟。

1. 使用 FTP 下載檔案 (請參閱[透過 FTP 上傳/下載檔案及收集診斷記錄](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)) (英文)。

2. 將檔案新增至應用程式的根目錄。

3. 使用下列資訊新增重寫規則。

	* **Node.js 和 Python Django**

		為 Node.js 和 Python Django 應用程式產生的 web.config 檔案已包含 **&lt;rewrite>** 區段，其中會包含網站正常運作所需的 **&lt;rule>** 項目。若要強制網站使用 HTTPS，請將範例中的 **&lt;rule>** 新增為 **&lt;rules>** 區段中的第一個項目。這會在保留其他規則不變的情況下強制使用 HTTPS。

	* **Java**
	
		在使用 Apache Tomcat 的 Java 應用程式中，其 web.config 檔案不包含 **&lt;rewrite>** 區段，因此您必須將範例中的 **&lt;rewrite>** 區段新增至 **&lt;system.webServer>** 區段。

4. 將專案 (包含更新的 web.config) 重新部署至 Azure

一旦部署包含重寫規則以強制使用 HTTPS 的 web.config 後，它應會立即生效並將所有要求重新導向至 HTTPS。

如需 IIS URL Rewrite 模組的詳細資訊，請參閱 [URL Rewrite](http://www.iis.net/downloads/microsoft/url-rewrite) 文件。 

## 其他資源 ##
- [Microsoft Azure 信任中心](/zh-tw/support/trust-center/security/)
- [Azure 網站中解除鎖定的組態選項](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [啟用診斷記錄](/zh-tw/documentation/articles/web-sites-enable-diagnostic-log/)
- [設定網站](/zh-tw/documentation/articles/web-sites-configure/)
- [Azure 管理入口網站](https://manage.windowsazure.com)

[customdomain]: /zh-tw/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/zh-tw/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/zh-tw/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/zh-tw/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[網站]: ./media/configure-ssl-web-site/sslwebsite.png
[調整]: ./media/configure-ssl-web-site/sslscale.png
[標準]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/zh-tw/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png
