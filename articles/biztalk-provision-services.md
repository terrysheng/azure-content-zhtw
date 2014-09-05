<properties  linkid="provisioning-biztalk-service" urlDisplayName="Provision BizTalk Services in management portal" pageTitle="Provision BizTalk Services in management portal | Azure" metaKeywords="Get started Azure biztalk services, provision, Azure unstructured data" description="Learn how to provision a BizTalk service in the Azure Management Portal, as well as create an optional SQL database server and Storage account." metaCanonical="http://www.windowsazure.com/zh-tw/manage/services/biztalk-services/provisioning-biztalk-service" services="biztalk-services" documentationCenter="" title="BizTalk Services: Provisioning Using Azure Management Portal" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

# BizTalk 服務：使用 Azure 管理入口網站進行佈建

<div  class="dev-callout" markdown="1">
**秘訣**
若要登入 Azure 管理入口網站，您需要 Azure 帳戶和 Azure
訂閱。如果沒有帳戶，您可在幾分鐘內建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][1]。


</div>

Azure BizTalk 服務由下列元件組成：

<table  border="1">
<tr  bgcolor="FAF9F9">
<td><strong>需求</strong>
</td>

<td><strong>說明</strong>
</td>

</tr>

<tr>
<td>Azure 訂閱</td>

<td><p>訂閱可以掌管 Azure 管理入口網站的存取權，而且是由 Azure 帳戶持有者在 <a href="https://account.windowsazure.com/Subscriptions">Azure 訂閱</a>
 (英文) 中建立。</p>

<p>Azure 帳戶可以具有多個訂閱，而且可由 Azure 帳戶持有者或不同人員或群組管理。例如，您的 Azure 帳戶持有者建立一個名稱為 <em>BizTalkServiceSubscription</em>
 的訂閱，並給與您公司內的 BizTalk 系統管理員 (例如 ContosoBTSAdmins@live.com) 存取此訂閱的權限。在此案例中，BizTalk 系統管理員可以登入 Azure 管理入口網站，並對訂閱中的所有代管服務 (包括 Azure BizTalk 服務) 具有完整系統管理員權限。BizTalk 系統管理員不是 Azure 帳戶持有者，因此無權存取任何任何計費資訊。</p>

<a href="http://go.microsoft.com/fwlink/p/?LinkID=267577">管理 Azure 管理入口網站中的訂閱和儲存體帳戶</a>
 (英文) 提供 Azure 帳戶和訂閱的相關資訊。
</td>

</tr>

<tr>
<td>Azure SQL Database</td>

<td><p>SQL Database 可以儲存 Azure BizTalk 服務所使用的資料表、檢視和儲存程序。</p>

<p>當佈建 BizTalk 服務時，您可以使用現有的 Azure SQL Server、Azure SQL Database，或自動建立新的伺服器或資料庫。當選擇建立新的 Azure SQL Server 和 Database 時，即會自動啟用 Azure 服務。</p>

<p>如果在現有的 Azure SQL Server 上建立新的 Azure SQL Database，則不會修改伺服器的防火牆規則。因此，可能不允許其他 Azure 服務存取伺服器的資料庫。</p>

SQL Database 設定沒有最低的調整需求。</td>

</tr>

<tr>
<td>Azure 存取控制命名空間</td>

<td>存取控制命名空間利用 Azure BizTalk 服務進行驗證。從 Visual Studio 部署 BizTalk 服務專案時，您可以輸入此存取控制命名空間。當佈建 BizTalk 服務時，會自動建立存取控制命名空間。</td>

</tr>


<tr>
<td>Azure 儲存體帳戶</td>

<td><p>Azure 儲存體帳戶提供資料表、Blob 和佇列的存取權。當佈建 BizTalk 服務時，您可以使用現有的儲存體帳戶，或自動建立新的儲存體帳戶。BizTalk 服務會使用資料表、Blob 和佇列，執行下列動作：</p>

<ul>
<li>儲存監視 BizTalk 服務的記錄檔。監視輸出也會顯示在 Azure 管理入口網站的 [監視] 索引標籤中。</li>

<li>在夥伴之間建立 X12 或 AS2 協定時，您可以啟用封存功能來儲存訊息內容。此追蹤資料儲存在此儲存體帳戶中。</li>

</ul>

</td>

</tr>


<tr>
<td>SSL 專用憑證</td>

<td><p>當佈建 Azure BizTalk 服務時，您可以建立一個 URL，其中包括 BizTalk 服務名稱。對 BizTalk 服務 URL 提出要求時，會使用此專用 SSL 憑證 (.pfx) 作為 HTTPS 伺服器驗證憑證。當佈建 BizTalk 服務時，會自動建立自我簽署憑證。 </p>

<strong>重要 SSL 憑證資訊</strong>


<ul>
<li>憑證到期日必須少於 5 年。</li>

<li>所有專用憑證都需要一個密碼。知道此密碼，而且最佳作法為與系統管理員分享此密碼。</li>

<li>可在測試或開發環境中使用自我簽署憑證。當使用自我簽署憑證時，請將憑證匯入至您的個人憑證存放區和受信任的根授權單位憑證存放區。</li>

</ul>

<br  />
將實際執行憑證要求傳送至您的授權單位時，請指定下列憑證內容： <br  />


<ul>
<li><p><strong>增強金鑰使用方法</strong>
：可在憑證上啟用伺服器驗證其他金鑰使用方法。Azure BizTalk 服務至少需要伺服器驗證。</p>
</li>

<li><p><strong>一般名稱</strong>
：輸入 Azure BizTalk 服務 URL 的完整網域名稱 (FQDN)；這是當您在本主題<a href="#BizTalk">佈建 BizTalk 服務</a>
中佈建 BizTalk 服務時建立的名稱。</p>

<p>因此，您需要知道將憑證要求傳送至授權單位時 URL 將是什麼。在佈建 BizTalk 服務後，可以加入新的或不同的憑證。</p>
</li>

</ul>

<br  />

</td>

</tr>

</table>

本主題列出佈建 BizTalk 服務的步驟，包括：

* [步驟 1：佈建 BizTalk 服務](#BizTalk)
* [步驟 2：佈建後續步驟](#PostProv)
* [選用：建立 SQL Database 伺服器](#SQLDB)
* [選用：建立儲存體帳戶](#Storage)

## <a name="BizTalk"></a>步驟 1：佈建 BizTalk 服務

BizTalk 服務可以主控您的 Azure BizTalk 服務應用程式。當佈建 BizTalk 服務時，會自動建立存取控制命名空間和自我簽署 SSL。您可以選擇建立新的 Azure SQL Database 和新的儲存體帳戶。在佈建 BizTalk 服務後，可以更新其中一些需求。

下列步驟會佈建新的 Azure BizTalk 服務：

1.  登入 [Azure 管理入口網站][2]。

2.  在導覽窗格底端，選取 **+NEW**：
    
    ![選取 [新增] 按鈕。](./media/biztalk-provision-services/WABS_New.png)

3.  依序選取 **APP 服務**、**BIZTALK 服務** 和 **自訂建立**：
    
    ![Select BizTalk Service and select Custom
    Create](./media/biztalk-provision-services/WABS_NewBizTalkService.png)

4.  輸入下列 BizTalk 服務設定：
    
    <table  border="1">
     <tr>
    <td><strong>BizTalk 服務名稱</strong>
    </td>
    
    <td>輸入 BizTalk 服務的名稱。".biztalk.windows.net" 會自動新增至您輸入的名稱。這會產生用來存取 BizTalk 服務的 URL。您可以輸入任何名稱，但是名稱最好是特有的。部分範例包括：<br  />
    <br  />
    <em>mycompany</em>
    .biztalk.windows.net<br  />
     <em>mycompanymyapplication</em>
    .biztalk.windows.net<br  />
     <em>myapplication</em>
    .biztalk.windows.net
     </td>
    
     </tr>
    
     <tr>
    <td><strong>網域 URL</strong>
    </td>
    
    <td><strong>選用</strong>
    。依預設，網域 URL 為 <em>YourBizTalkServiceName</em>
    .biztalk.windows.net。也可以輸入自訂網域。比方說，如果您的網域為 <em>contoso</em>
    ，則可以輸入：<br  />
    <br  />
     <em>MyCompany</em>
    .contoso.com<br  />
     <em>MyCompanyMyApplication</em>
    .contoso.com<br  />
     <em>MyApplication</em>
    .contoso.com<br  />
     <em>YourBizTalkServiceName</em>
    .contoso.com<br  />
    
     </td>
    
     </tr>
    
     <tr>
    <td><strong>版本</strong>
    </td>
    
    <td>選項包括：
     <ul>
    <li>開發人員</li>
    
    <li>標準</li>
    
    <li>基本</li>
    
    <li>高級</li>
    
     </ul>
    
    <a href="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk 服務：開發人員、基本、標準和高級版本圖表 (英文)</a>
     列出版本差異。如果您是處於測試/開發階段，請選擇 [開發人員]<strong  />
    。如果您處於實際執行階段，請使用圖表來判定高級、標準還是基本為您的商業案例的正確選擇。
     </td>
    
     </tr>
    
     <tr>
    <td><strong>區域</strong>
    </td>
    
    <td>選取地理區域來主控您的 BizTalk 服務。</td>
    
     </tr>
    
     <tr>
    <td><strong>追蹤資料庫</strong>
    </td>
    
    <td><p>選取您的 SQL Database，來儲存您的 BizTalk 服務所使用的資料表。從下列選項中進行選擇：</p>
    
     <ul>
    <li><strong>使用現有的 SQL Database 執行個體</strong>
    ：選取此選項來使用現有的 Azure SQL Database。如果另一個 BizTalk 服務未使用現有的 Azure SQL Database，則可以使用它。您需要建立 Azure SQL Database 伺服器時指定的登入名稱和密碼。</li>
    
    <li><p><strong>建立新的 SQL Database 執行個體</strong>
    ：選取此選項來建立新的 SQL Database。</p>
    </li>
    
    <p><strong>注意</strong>
    </p>
    
    <p>當建立新的 Azure SQL Server 和 Database 時，即會在 SQL Database 上自動啟用 Azure 服務。BizTalk 服務需要在 Azure SQL Database 上啟用 Azure 服務。</p>
    
    <p><strong>秘訣</strong>
    </p>
    
    在與 BizTalk 服務相同的區域中建立追蹤資料庫和監視/封存儲存體帳戶。
     </ul>
    
     </td>
    
     </tr>
    
     <tr>
    <td><strong>訂閱</strong>
    </td>
    
    <td><strong>選用</strong>
    。僅在有多個訂閱時才能使用。選取您的訂閱來主控您的 BizTalk 服務。</td>
    
     </tr>
    
     </table>
    
    選取下一個箭頭。

5.  輸入您的資料庫設定：
    
    <table  border="1">
     <tr>
    <td><strong>訂閱</strong>
    </td>
    
    <td><strong>選用</strong>
    。僅在有多個訂閱時才能使用。選取您的訂閱來主控 Azure SQL Database。</td>
    
     </tr>
    
     <tr>
    <td><strong>資料庫</strong>
    </td>
    
    <td><p>可在前一個畫面中選取 [使用現有的 SQL Database 執行個體]<strong  />
     時使用。</p>
    
    選取您的 SQL Database，來儲存您的 BizTalk 服務所使用的資料表。
     </td>
    
     </tr>
    
     <tr>
    <td><strong>名稱</strong>
    </td>
    
    <td><p>可在前一個畫面中選取 [建立新的 SQL Database 執行個體]<strong  />
     時使用。</p>
    
    輸入 BizTalk 服務要使用的 SQL Database 名稱。依預設，已輸入 <em>YourBizTalkServiceName</em>
    _db。</td>
    
     </tr>
    
     <tr>
    <td><strong>伺服器</strong>
    </td>
    
    <td><p>可在前一個畫面中選取 [建立新的 SQL Database 執行個體]<strong  />
     時使用。</p>
    
    選取現有的 SQL Database 伺服器。或者，選取 [新增 SQL Database 伺服器]<strong  />
     來建立新的 SQL Database 伺服器。</td>
    
     </tr>
    
     <tr>
    <td><strong>伺服器登入名稱</strong>
    </td>
    
    <td>輸入登入使用者名稱。</td>
    
     </tr>
    
     <tr>
    <td><strong>伺服器登入密碼</strong>
    </td>
    
    <td>輸入登入密碼。</td>
    
     </tr>
    
     <tr>
    <td><strong>區域</strong>
    </td>
    
    <td>可在選取 [建立新的 SQL Database 執行個體]<strong  />
     時使用。選取地理區域來主控您的 SQL Database。</td>
    
     </tr>
    
     </table>
    
    選取下一個箭頭。

6.  輸入 Azure 監視設定：
    
    <table  border="1">
     <tr>
    <td><strong>監視/封存儲存體帳戶</strong>
    </td>
    
    <td>選取現有的儲存體帳戶或選取 [建立新的儲存體帳戶]<strong  />
    。</td>
    
     </tr>
    <tr>
    <td><strong>儲存體帳戶名稱</strong>
    </td>
    
    <td>可在選取 [建立新的儲存體帳戶]<strong  />
     時使用。輸入 BizTalk 服務所使用的儲存體帳戶名稱。</td>
    
     </tr>
    
     </table>

選取核取記號來完成精靈。完成時，進度圖示即會顯示：  


![Progress icon displays when
complete](./media/biztalk-provision-services/WABS_ProgressComplete.png)

完成時，會佈建 Azure BizTalk 服務，並備妥供應用程式使用。

預設設定已夠用。如果想要修改預設設定，請在左導覽窗格中選取 **BIZTALK 服務**，然後選取 BizTalk 服務。其他設定會顯示在
[儀表板]、[監視器] 和 [調整] 索引標籤中。

有一些無法完成的作業，視 BizTalk 服務的狀態而定。如需這些作業的清單，請參閱 [BizTalk 服務：BizTalk 服務狀態圖][3]
(英文)。

## <a name="PostProv"></a>步驟 2：佈建後續步驟

本節列出下列步驟：

* [新增專用憑證](#AddCert)
* [擷取存取控制命名空間](#ACS)

#### <a name="AddCert"></a>新增專用憑證

當佈建 Azure BizTalk 服務時，會建立一個 URL，其中包括 BizTalk 服務名稱。對 BizTalk 服務 URL
提出要求時，會使用專用 SSL 憑證 (.pfx) 作為 HTTPS 伺服器驗證憑證。

為 BizTalk 服務自動建立自我簽署憑證。可在 BizTalk 服務儀表板上下載或取代此憑證。自我簽署憑證是在開發環境中使用。

若要新增實際執行備妥憑證：

1.  登入 [Azure 管理入口網站][2]。 2.  在左導覽窗格中選取 **BIZTALK 服務**，然後選取 BizTalk 服務。 3.  選取 **儀表板** 索引標籤。 4.  選取 **更新 SSL 憑證**：  
      
    ![Modify SSL
    Certificate](./media/biztalk-provision-services/WABS_QuickGlance.png)

5.  瀏覽至包括 BizTalk 服務名稱的專用 SSL 憑證 (*CertificateName*.pfx)、輸入密碼，然後選取核取記號。

#### <a name="ACS"></a>擷取存取控制命名空間

存取控制命名空間利用 Azure BizTalk 服務進行驗證。從 Visual Studio 部署 BizTalk
服務專案時，您可以輸入此存取控制命名空間。

為 BizTalk 服務自動建立存取控制命名空間。若要擷取存取控制命名空間、預設簽發者及簽發者金鑰，請在 BizTalk 服務儀表板上選取
**連接資訊** 按鈕。

若要擷取存取控制命名空間：

1.  登入 [Azure 管理入口網站][2]。 2.  在左導覽窗格中選取 **BIZTALK 服務**，然後選取 BizTalk 服務。 3.  在工作列中，選取 **連接資訊**：  
      
    ![Select Connection
    Information](./media/biztalk-provision-services/WABS_ACSConnectInformation.png)

您可以複製並貼上存取控制值。

當建立存取控制命名空間時，存取控制值可與任何應用程式搭配使用。當佈建 Azure BizTalk 服務時，此存取控制命名空間會利用
BizTalk 服務部署來控制驗證。如果想要變更訂閱或管理命名空間，請在左導覽窗格中選取 [ACTIVE DIRECTORY]****，然後選取您的命名空間。導覽窗格底端會列出您的選項。

按一下 **管理**，即可開啟存取控制管理入口網站。在存取控制管理入口網站中，BizTalk 服務會使用 **服務身分識別**：  
  
![ACS Service Identities in the Access Control
Management Portal](./media/biztalk-provision-services/WABS_ACSServiceIdentities.png)

存取控制服務身分識別是一組認證，可讓應用程式或用戶端直接使用 Azure AD 存取控制進行驗證，並接收權杖。

**重要事項**  
 BizTalk 服務會使用 **擁有者** 作為預設服務身分識別，並使用 **密碼**
值。如果您使用對稱金鑰值而不是密碼值，則可能發生下列錯誤：

*無法利用指定的認證連接至存取控制管理服務帳戶*

[管理您的 ACS 命名空間][4] (英文) 列出一些指導方針和建議。

## <a name="SQLDB"></a>選用：建立 SQL Database 伺服器

當佈建 Azure BizTalk 服務時，會自動建立新的 SQL Database 伺服器。如果您偏好建立與 BizTalk 服務無關的
SQL Database 伺服器，請參閱[如何在 .NET 應用程式中使用 Azure SQL Database][5] (英文)。

完成時，有一個您可以登入的新 Azure SQL Database，並可在其中建立資料表、檢視和儲存程序。

依預設，會利用下列項目設定 SQL Database 調整：

* Web 版本
* 1GB 資料庫大小

預設組態對 BizTalk 服務已夠用。如果想要修改調整組態設定，請在左導覽窗格中選取 **SQL Database**、按兩下以選取您的
SQL Database，然後選取 **設定** 索引標籤。修改調整可能影響定價。[Azure SQL Database
中的帳戶和計費][6] (英文) 提供版本和計費的相關資訊。

## <a name="Storage"></a>選用：建立儲存體帳戶

當佈建 Azure BizTalk 服務時，會自動建立 Azure 儲存體帳戶。如果您偏好建立與 BizTalk 服務無關的 Azure
儲存體帳戶，請參閱[如何建立儲存體帳戶][7] (英文)。

完成時，有一個可讓您存取資料表、Blob 和佇列的新 Azure 儲存體帳戶。

預設設定對 BizTalk 服務已夠用。如果想要修改預設設定，請在左導覽窗格中選取 **儲存體**，然後選取儲存體帳戶。這些設定會顯示在
[儀表板]、[監視器]、[設定] 和 [容器] 索引標籤中。

當建立儲存體帳戶時，會自動建立主要金鑰和次要金鑰。這些金鑰可控制儲存體帳戶的存取。BizTalk 服務會自動使用主要金鑰。

[儲存體][8]提供儲存體帳戶的相關資訊。

## 下一步

既然已佈建 BizTalk 服務，則您可以讓自己熟悉 [BizTalk 服務：儀表板、監視器和調整索引標籤][9] (英文)
中的不同索引標籤。您的 BizTalk 服務已準備好可供您的應用程式使用。若要開始建立應用程式，請移至 [Azure BizTalk
服務][10] (英文)。

## 另請參閱

* [BizTalk 服務：開發人員、基本、標準和高級版本圖表][11]  (英文)
* [BizTalk 服務：BizTalk 服務狀態圖][3]  (英文)
* [BizTalk 服務：備份與還原][12]  (英文)
* [BizTalk 服務：節流][13]  (英文)
* [BizTalk 服務：簽發者名稱和簽發者金鑰][14]   (英文)
* [如何開始使用 Azure BizTalk 服務 SDK][15]



[1]: http://go.microsoft.com/fwlink/p/?LinkID=239738
[2]: http://go.microsoft.com/fwlink/p/?LinkID=213885
[3]: http://go.microsoft.com/fwlink/p/?LinkID=329870
[4]: http://go.microsoft.com/fwlink/p/?LinkID=285670
[5]: http://go.microsoft.com/fwlink/p/?LinkID=251285
[6]: http://go.microsoft.com/fwlink/p/?LinkID=234930
[7]: http://go.microsoft.com/fwlink/p/?LinkID=279823
[8]: http://go.microsoft.com/fwlink/p/?LinkID=285671
[9]: http://go.microsoft.com/fwlink/p/?LinkID=302281
[10]: http://go.microsoft.com/fwlink/p/?LinkID=235197
[11]: http://go.microsoft.com/fwlink/p/?LinkID=302279
[12]: http://go.microsoft.com/fwlink/p/?LinkID=329873
[13]: http://go.microsoft.com/fwlink/p/?LinkID=302282
[14]: http://go.microsoft.com/fwlink/p/?LinkID=303941
[15]: http://go.microsoft.com/fwlink/p/?LinkID=302335