# 對 Azure 網站啟用 HTTPS

當人們使用 HTTPS 瀏覽您的網站時，網站與瀏覽器之間的通訊會以安全通訊端層 (SSL)
加密保護。這是最常用來保護在網際網路上傳送之資料的方法，並可讓訪客安心知道其與您網站進行的交易正受到安全保護。本文討論如何對 Azure
網站啟用 HTTPS。

> [WACOM.NOTE] 若要對自訂網域名稱啟用
> HTTPS，您必須設定網站為使用標準模式。如果您目前正使用免費或共用模式，則換成使用標準模式可能會帶來額外成本。如需共用與標準模式定價的詳細資訊，請參閱[定價詳細資料][1]。

<a href="bkmk_azurewebsites"></a><h2>\*.azurewebsites.net 網域</h2>

如果您不想使用自訂網域名稱，而是想使用 Azure 指派給您網站的 \*.azurewebsites.net 網域
(例如，contoso.azurewebsites.net)，則您的網站已受到 Microsoft 所提供之憑證的保護。只要使用
**https://mywebsite.azurewebsites.net**，就能安全地存取您的網站。

本文件的其餘內容提供關於對自訂網域名稱 (如 **contoso.com**、**www.contoso.com** 或
**\*.contoso.com**) 啟用 HTTPS 的詳細資訊。

<a href="bkmk_domainname"></a><h2>自訂網域名稱</h2>

若要對自訂網域名稱 (如 **contoso.com**) 啟用 HTTPS，您必須向網域名稱註冊機構註冊自訂網域名稱。如需關於如何設定
Azure 網站之網域名稱的詳細資訊，請參閱[設定 Azure
網站的自訂網域名稱](/en-us/develop/net/common-tasks/custom-dns-web-site/)。在註冊自訂網域名稱並設定您的網站來回應該自訂名稱之後，您必須要求網域的
SSL 憑證。

註冊網域名稱後，您也可以建立子網域 (如 **www.contoso.com** 或 **mail.contoso.com**)。在要求 SSL
憑證之前，您必須先決定有哪些網域名稱要由該憑證保護。這將決定您必須取得的憑證類型。如果您只需要保護單一網域名稱 (如
**contoso.com** 或 **www.contoso.com**)，則基本憑證可能就已足夠。如果您需要保護多個網域名稱 (如
**contoso.com**、**www.contoso.com** 和
**mail.contoso.com**)，則需要有萬用憑證或具主體替代名稱 (subjectAltName，簡稱 SAN) 的憑證。

> [WACOM.NOTE] 如果憑證中指定的網域名稱與瀏覽器中輸入的網域名稱不符，大部分的瀏覽器都會顯示警告。例如，如果憑證只列出
> www.contoso.com，但是在 Internet Explorer 中用來存取網站的網域名稱是
> login.contoso.com，則您會收到「此網站出示的安全性憑證是針對其他網站的位址所發行」警告。

**基本憑證**是指，憑證的一般名稱 (CN)
是設定為用戶端用來造訪網站的特定網域或子網域。例如，**www.contoso.com**。這些憑證只會保護 CN 所指定的單一網域名稱。

**萬用憑證**是指，憑證的 CN 在子網域層級使用萬用字元
\'\*\'。如此一來，憑證便適用於給定網域的單層子網域。例如，**\*.contoso.com** 的萬用憑證適用於
**www.contoso.com**、**payment.contoso.com** 和
**login.contoso.com**。但不適用於 **test.login.contoso.com**，因為這多了一層子網域。也不適用於
**contoso.com**，因為這只有根網域層級，而沒有子網域。

Microsof 針對自動為您網站建立的 \*.azurewebsites.net 網域名稱，提供了萬用憑證。

**subjectAltName** 是一種憑證延伸，可讓各種值 (或主體替代名稱) 與憑證產生關聯。就 SSL
憑證的用途而言，如此可讓您新增要讓憑證適用於的其他 DNS 名稱。例如，某憑證使用了 subjectAltName，該憑證可能有 CN
**contoso.com**，但也可能還有替代名稱
**www.contoso.com**、**payment.contoso.com**、**test.login.contoso.com**
甚至 **fabrikam.com**。這樣一個憑證將同時適用於一般名稱中與 subjectAltName 中所指定的所有網域名稱。

憑證可以同時支援使用萬用字元和 subjectAltName。

<a href="bkmk_getcert"></a><h2>取得憑證</h2>

與 Azure 網站搭配使用的 SSL 憑證必須經過憑證授權單位 (CA) (專門核發憑證的受信任第三方)
所簽署。如果您還沒有此類憑證，則必須向銷售 SSL 憑證的公司取得。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki
上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][2]。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：

* 憑證必須包含私密金鑰。

* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。

* 憑證的主體名稱必須符合用來存取網站的網域。如果您需要利用此憑證來供應多個網域，則必須如前文所討論，使用萬用字元值或指定
  subjectAltName 值。
  
  * 如需關於設定 Azure 網站之自訂網域名稱的詳細資訊，請參閱[設定 Azure
    網站的自訂網域名稱](/en-us/develop/net/common-tasks/custom-dns-web-site/)。
  
  > [WACOM.NOTE] 請不要嘗試取得或產生 azurewebsites.net 網域的憑證。

* 憑證至少應該要以 2048 位元加密。

> [WACOM.NOTE] Azure 網站不支援私人 CA 伺服器所核發的憑證。

若要向憑證授權單位取得 SSL 憑證，您必須產生憑證簽署要求 (CSR)，以傳送給 CA。CA 之後會傳回用來完成 CSR 的憑證。產生 CSR
的兩個常用方法為使用 certmgr.exe 或 [OpenSSL][3] 應用程式。Certmgr.exe 僅適用於
Windows，OpenSSL 則適用於大部分的平台。使用這兩種公用程式的步驟如下。

如果 CA 使用「中繼憑證」**** (也稱為鏈結憑證)，則您還需要取得這些中繼憑證。使用中繼憑證比使用「未鏈結憑證」安全，因此常會見到 CA
使用中繼憑證。中繼憑證通常是從 CA 網站個別下載而得。本文中的步驟提供關於如何確保任何中繼憑證已與上傳至您 Azure
網站的憑證相合併的步驟。

> [WACOM.NOTE] 不論遵循哪個系列的步驟，系統都會提示您輸入「一般名稱」****。如果您將會取得萬用憑證來用於多個網域
> (www.contoso.com、sales.contoso.com)，則此值應該是 \*.domainname
> (例如，\*.contoso.com)。如果您將會取得單一網域名稱的憑證，則此值必須是使用者將在瀏覽器中輸入來造訪您網站的確切值。例如，www.contoso.com。
> 
> 如果您需要同時支援萬用字元名稱 (如 \*.contoso.com) 和根網域名稱 (如
> contoso.com)，則可以使用萬用字元主體替代名稱 (SAN) 憑證。如需建立憑證要求並讓其使用 SubjectAltName
> 延伸的範例，請參閱 [SubjectAltName 憑證](#bkmk_subjectaltname)。
> 
> 如需關於如何設定 Azure 網站之網域名稱的詳細資訊，請參閱[設定 Azure
> 網站的自訂網域名稱](/en-us/develop/net/common-tasks/custom-dns-web-site/){:
> }。
### 使用 Certreq.exe 取得憑證 (僅限 Windows)

Certreq.exe 是一項用來建立憑證要求的 Windows 公用程式。自 Windows XP/Windows Server 2000
起，它就已是基本 Windows 安裝的一部分，因此新版 Windows 系統上應該會提供。請透過下列步驟，使用 certreq.exe 來取得
SSL 憑證。

如果您想要建立自我簽署憑證以進行測試，請參閱本文件的[自我簽署憑證](#bkmk_selfsigned)一節。

如果您想要使用 IIS 管理員建立憑證要求，請參閱[使用 IIS 管理員取得憑證](#bkmk_iismgr)一節。

1.  開啟 **記事本**，然後建立含有下列內容的新文件。將 Subject 一行中的 **mysite.com**
    取代為您網站的自訂網域名稱。例如，Subject = "CN=www.contoso.com"。
    
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
    
    如需上面所指定選項以及其他可用選項的詳細資訊，請參閱 [Certreq 參考文件][4]。

2.  將文字檔儲存為 **myrequest.txt**。

3.  從 **開始畫面** 或 **開始功能表** 中，執行 **cmd.exe**。

4.  在命令提示字元中，使用下列命令來建立憑證要求檔案：
    
         certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr
    
    指定步驟 1 中所建立 **myrequest.txt** 檔案的路徑，以及在建立 **myrequest.csr**
    檔案時所要使用的路徑。

5.  將 **myrequest.csr** 提交至憑證授權單位，以取得 SSL 憑證。這可能包括上傳檔案，或在 [記事本]
    中開啟檔案並將內容直接貼到 Web 表單。
    
    如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki 上的 [Windows 與 Windows Phone 8
    SSL 根憑證計劃 (成員 CA)][2]。

6.  等到憑證授權單位提供憑證檔 (.CER) 給您後，將該檔案儲存至用來產生要求的電腦，然後使用下列命令來接受要求並完成憑證產生程序。
    
         certreq -accept -user mycert.cer
    
    在此情況下，從憑證授權單位那邊收到的 **mycert.cer** 憑證將會用來完成憑證簽章。沒有檔案會建立；憑證會儲存至
    Windows 憑證存放區中。

7.  如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA
    個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取針對 Microsoft IIS 所提供的版本。
    
    下載憑證之後，請在檔案總管中於該憑證上按一下滑鼠右鍵，然後選取 **安裝憑證**。使用 **憑證匯入精靈**
    中的預設值，並持續選取 **下一步**，直到匯入完成。

8.  若要從憑證存放區中匯出憑證，請從 **開始畫面** 或 **開始功能表** 執行 **certmgr.msc**。出現
    **憑證管理員** 時，展開 **個人** 資料夾，然後選取 **憑證**。在 **核發給**
    欄位中，找出有哪個項目具有您為了它而要求憑證的自訂網域名稱。**核發者** 欄位中應該會列出您用於此憑證的憑證授權單位。
    
    ![在這裡插入憑證管理員的影像](./media/configure-ssl-web-site/waws-certmgr.png)

9.  在憑證上按一下滑鼠右鍵，並選取 **所有工作**，然後選取 **匯出**。在 **憑證匯出精靈** 中，按
    **下一步**，然後選取 **是，匯出私密金鑰**。按 **下一步**。
    
    ![匯出私密金鑰](./media/configure-ssl-web-site/waws-certwiz1.png)

10. 選取 **個人資訊交換 - PKCS #12**、**包含憑證鏈結中的所有憑證** 和
    **匯出所有延伸內容**。按 **下一步**。
    
    ![包括所有憑證和延伸內容](./media/configure-ssl-web-site/waws-certwiz2.png)

11. 選取 **密碼**，然後輸入並確認密碼。按 **下一步**。
    
    ![指定密碼](./media/configure-ssl-web-site/waws-certwiz3.png)

12. 提供將包含所匯出憑證的路徑和檔名。檔名的副檔名應該為 **.pfx**。按 **下一步**，完成此程序。
    
    ![提供檔案路徑](./media/configure-ssl-web-site/waws-certwiz4.png)

您現在可以將所匯出的 PFX 檔案上傳至 Azure 網站。
### 使用 OpenSSL 取得憑證

1.  從命令列、Bash 或終端機工作階段中，使用下列命令產生私密金鑰和憑證簽署要求：
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []: 

	Once this process completes, you should have two files; **myserver.key** and **server.csr**. The **server.csr** contains the Certificate Signing Request.

1.  將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki
    上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][2]。

2.  從 CA 取得憑證之後，將它儲存成名稱為 **myserver.crt** 的檔案。如果您的 CA
    提供文字格式的憑證，則只需要將憑證文字貼入 **myserver.crt** 檔案。在文字編輯器中檢視檔案時，檔案內容應該與下列類似：
    
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

3.  從命令列、Bash 或終端機工作階段中，使用下列命令，將 **myserver.key** 和 **myserver.crt** 轉換為
    **myserver.pfx** (Azure 網站所需的格式)：
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    當系統提示時，輸入 .pfx 檔案的保護密碼。
    
    <div  class="dev-callout" markdown="1">

**注意**
如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA
個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。


下列命令示範如何建立內註有中繼憑證 (其包含在 **intermediate-cets.pem** 檔案中) 的 .pfx 檔案：


	
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem



    

    
 執行此命令之後，您應該會有適合用於您 Azure 網站的 **myserver.pfx** 檔案。

<a href="bkmk_standardmode"></a><h2>設定標準模式</h2>

若要對自訂網域啟用 HTTPS，Azure 網站必須處於標準模式。請使用下列步驟切換至標準模式。

> [WACOM.NOTE]
> 在將網站從免費網站模式切換至標準網站模式之前，您應該先移除網站訂閱的花費上限，以免帳單期間還未結束，網站就因為已達花費上限而變得無法使用。如需共用與標準模式定價的詳細資訊，請參閱[定價詳細資料][1]。

1.  在瀏覽器中開啟[管理入口網站][5]。

2.  在 **網站** 索引標籤中，按一下您網站的名稱。
    
    ![選取網站](./media/configure-ssl-web-site/sslwebsite.png)

3.  按一下 **調整** 索引標籤。
    
    ![調整索引標籤](./media/configure-ssl-web-site/sslscale.png)

4.  在 **一般** 區段中，按一下 **標準** 以設定網站模式。
    
    ![已選取標準模式](./media/configure-ssl-web-site/sslreserved.png)

5.  按一下 **儲存**。當提示出現時，按一下 **是**。
    
    > [WACOM.NOTE] 如果您收到 [設定網站 \'<site name>\' 的調整失敗]
    > 錯誤，可以使用詳細資料按鈕以取得更多資訊。您可能會收到 [可用標準執行個體伺服器不足，無法滿足此要求]
    > 錯誤。如果您收到此錯誤，請連絡 [Azure 支援][6]。

<a href="bkmk_configuressl"></a><h2>設定 SSL</h2>

在執行本節中的步驟之前，您必須先建立自訂網域名稱與 Azure 網站的關聯。如需詳細資訊，請參閱[設定 Azure
網站的自訂網域名稱](/en-us/develop/net/common-tasks/custom-dns-web-site/)。

1.  在瀏覽器中開啟 [Azure 管理入口網站][5]。

2.  在 **網站** 索引標籤中，按一下您網站的名稱，然後選取 **設定** 索引標籤。
    
    ![設定索引標籤](./media/configure-ssl-web-site/sslconfig.png)

3.  在 **憑證** 區段中，按一下 **上傳憑證**
    
    ![上傳憑證](./media/configure-ssl-web-site/ssluploadcert.png)

4.  使用 **上傳憑證** 對話方塊，選取之前以 IIS 管理員或 OpenSSL 建立的 .pfx 憑證檔案。指定當初用來保護
    .pfx 檔案的密碼 (如果有的話)。最後，按一下 **檢查** 以上傳憑證。
    
    ![上傳憑證對話方塊](./media/configure-ssl-web-site/ssluploaddlg.png)

5.  在 **設定** 索引標籤的 **SSL 繫結** 區段中，使用下拉式清單選取要以 SSL
    保護的網域名稱，以及要使用的憑證。您也可以選擇使用[伺服器名稱指示][7] (SNI) 還是 IP SSL。
    
    ![SSL 繫結](./media/configure-ssl-web-site/sslbindings.png)
    
    * IP SSL 會將伺服器的專用公用 IP 位址對應至網域名稱，以建立憑證與網域名稱的關聯。這需要與您服務相關聯的每個網域名稱
      (contoso.com、fabricam.com 等) 都有專用 IP 位址。這是傳統用來建立 SSL
      憑證與網頁伺服器之關聯的方法。
    
    * SNI SSL 是 SSL 和[傳輸層安全性][8] (TLS) 的延伸，可讓多個網域共用相同的 IP
      位址，而每個網域都有個別的安全性憑證。現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox
      和 Opera) 都支援 SNI，不過，較舊的瀏覽器可能不支援 SNI。如需 SNI 的詳細資訊，請參閱 Wikipedia
      上的[伺服器名稱指示][7]一文。

6.  按一下 **儲存**，儲存變更並啟用 SSL。

> **WACOM.NOTE] 如果您已選取 [IP SSL**，而且您的自訂網域是以 A 記錄設定，則必須執行下列額外步驟：
> 
> 1.  在設定 IP SSL 繫結之後，您的網站會獲指派專用 IP 位址。您可以在網站的 **儀表板** 頁面上 (在
>     **Quick Glance** 區段中) 找到這個 IP 位址。它將會列出成為 **虛擬 IP 位址**：
>     
>     ![虛擬 IP 位址](./media/configure-ssl-web-site/staticip.png)
>     
>     請注意，此 IP 位址與先前用來設定您網域之 A 記錄的虛擬 IP 位址不同。如果您設定成使用 SNI SSL，或未設定成使用
>     SSL，則不會列出此項目的位址。
> 
> 2.  使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟的 IP 位址。

現在，您應該可以使用 HTTPS 來造訪您的網站，確認已正確設定憑證。

<a href="bkmk_subjectaltname"></a><h2>SubjectAltName 憑證 (選用)</h2>

OpenSSL 可以用來建立憑證要求 (並讓該要求使用 SubjectAltName
延伸來透過單一憑證支援多個網域名稱)，不過，這需要有組態檔。下列步驟會逐步引導您建立組態檔，然後使用它來要求憑證。

1.  建立名稱為 **sancert.cnf** 的新檔案，並使用下列程式碼作為檔案的內容：

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
    
    請注意開頭為 \'subjectAltName\' 的那行。請將目前列出的網域名稱取代為您除了一般名稱外，還想要支援的網域名稱。例如：
    
         subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
    
    您不需要變更 commonName\_default 欄位，因為系統會在下列其中一個步驟中提示您輸入一般名稱。

2.  儲存 **sancert.cnf** 檔案。

3.  使用 sancert.cnf 組態檔，以產生私密金鑰和憑證簽署要求。從 Bash 或終端機工作階段中，使用下列命令：
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  當系統提示時，輸入適當資訊。例如：

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	Once this process completes, you should have two files; **myserver.key** and **server.csr**. The **server.csr** contains the Certificate Signing Request.

1.  將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki
    上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][2]。

2.  從 CA 取得憑證之後，將它儲存成名稱為 **myserver.crt** 的檔案。如果您的 CA
    提供文字格式的憑證，則只需要將憑證文字貼入 **myserver.crt** 檔案。在文字編輯器中檢視檔案時，檔案內容應該與下列類似：
    
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

3.  從命令列、Bash 或終端機工作階段中，使用下列命令，將 **myserver.key** 和 **myserver.crt** 轉換為
    **myserver.pfx** (Azure 網站所需的格式)：
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    當系統提示時，輸入 .pfx 檔案的保護密碼。
    
    <div  class="dev-callout" markdown="1">

**注意**
如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA
個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取以 PEM 檔案 (副檔名為 .pem) 形式提供的版本。


下列命令示範如何建立內註有中繼憑證 (其包含在 **intermediate-cets.pem** 檔案中) 的 .pfx 檔案：



	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem



    

    
  執行此命令之後，您應該會有適合用於您 Azure 網站的 **myserver.pfx** 檔案。
## <a name="bkmk_iismgr"></a>使用 IIS 管理員取得憑證 (選用)

如果您熟悉 IIS 管理員，可以用它來產生可用於 Azure 網站的憑證。

1.  使用 IIS 管理員來產生憑證簽署要求 (CSR)，以便傳送至憑證授權單位。如需產生 CSR 的詳細資訊，請參閱[要求網際網路伺服器憑證
    (IIS 7)][9]。

2.  將您的 CSR 提交至憑證授權單位，以便取得 SSL 憑證。如需憑證授權單位的清單，請參閱 Microsoft TechNet Wiki
    上的 [Windows 與 Windows Phone 8 SSL 根憑證計劃 (成員 CA)][2]。

3.  使用憑證授權單位廠商所提供的憑證來完成 CSR。如需完成 CSR 的詳細資訊，請參閱[安裝網際網路伺服器憑證 (IIS 7)][10]。

4.  如果 CA 使用中繼憑證，您必須先安裝這些憑證，再於接下來的步驟匯出憑證。這些憑證通常是從 CA
    個別下載而得，並且有分數種格式來用於不同的網頁伺服器類型。請選取針對 Microsoft IIS 所提供的版本。
    
    下載憑證之後，請在檔案總管中於該憑證上按一下滑鼠右鍵，然後選取 **安裝憑證**。使用 **憑證匯入精靈**
    中的預設值，並持續選取 **下一步**，直到匯入完成。

5.  從 IIS 管理員匯出憑證。如需匯出憑證的詳細資訊，請參閱[匯出伺服器憑證 (IIS 7)][11]。後面的步驟中會將所匯出的檔案上傳至
    Azure 來用於您的 Azure 網站。
    
 	<div  class="dev-callout" markdown="1">

**注意**
在匯出程序期間，請務必選取 **是，匯出私密金鑰**
選項。這樣會在匯出的憑證中納入私密金鑰。


 	

    
    

**注意**
在匯出程序期間，請務必選取 **包含憑證路徑中的所有憑證** 和
**匯出所有延伸內容** 選項。這樣會在匯出的憑證中納入所有中繼憑證。


    


<a href="bkmk_selfsigned"></a><h2>自我簽署憑證 (選用)</h2>

在某些情況下，您可能會想要取得憑證來進行測試，並等到要實際投入使用時，才將向信任 CA
購買憑證。自我簽署憑證就有這樣的功能。自我簽署憑證是您將自己當成憑證授權單位來建立和簽署的憑證。雖然此憑證可以用來保護網站，但是大部分的瀏覽器在造訪該網站時都會傳回錯誤，因為憑證不是由信任的
CA 所簽署。部分瀏覽器甚至會拒絕讓您檢視網站。

雖然有多種方式可以建立自我簽署憑證，但是本文只提供使用 **makecert** 和 **OpenSSL** 時的資訊。
### 使用 makecert 建立自我簽署憑證

您可以在已安裝 Visual Studio 的 Windows 系統上執行下列步驟來建立測試憑證：

1.  從 **開始功能表** 或 **開始畫面** 中，搜尋 **開發人員命令提示字元**。最後，在
    **開發人員命令提示字元** 上按一下滑鼠右鍵，然後選取 **以系統管理員身分執行**。
    
    如果您收到 **使用者帳戶控制] 對話方塊，請選取 [是** 繼續。

2.  從 [開發人員命令提示字元] 中，使用下列命令建立新的自我簽署憑證。您必須將 **serverdnsname**
    替換成您網站的DNS。
    
         makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048
    
    此命令會建立有效日期為 01/01/2013 到 01/01/2014 的憑證，而且會將位置儲存在 CurrentUser
    憑證存放區中。

3.  從 **開始功能表** 或 **開始畫面** 中，搜尋 **Windows PowerShell**，然後啟動此應用程式。

4.  從 Windows PowerShell 提示字元中，使用下列命令匯出先前建立的憑證：
    
         $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
        get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd
    
    這會將指定的密碼以安全字串形式儲存在 $mypwd 中，並使用 **dnsname** 參數所指定的 DNS 名稱來尋找憑證，然後匯出至
    **filepath** 參數所指定的檔案。而含有密碼的安全字串會用來保護匯出的檔案。
### 使用 OpenSSL 建立自我簽署憑證

1.  建立名稱為 **serverauth.cnf** 的新文件，並使用下列程式碼作為此檔案的內容：
    
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

2.  從命令列、Bash 或終端機工作階段中，使用下列命令產生新的自我簽署憑證：
    
         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
    
    這會使用 **serverauth.cnf** 檔案中指定的組態設定來建立新的憑證。

3.  若要將憑證匯出至可上傳至 Azure 網站的 .PFX 檔案，請使用下列命令：
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    當系統提示時，輸入 .pfx 檔案的保護密碼。
    
    此命令所產生的 **myserver.pfx** 可以用來基於測試目的來保護您的 Azure 網站。



[1]: https://www.windowsazure.com/en-us/pricing/details/
[2]: http://go.microsoft.com/fwlink/?LinkID=269988
[3]: http://www.openssl.org/
[4]: http://technet.microsoft.com/library/cc725793.aspx
[5]: https://manage.windowsazure.com/
[6]: http://www.windowsazure.com/en-us/support/options/
[7]: http://en.wikipedia.org/wiki/Server_Name_Indication
[8]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[9]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
[10]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
[11]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx
