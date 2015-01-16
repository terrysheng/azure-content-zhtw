<properties urlDisplayName="Issuer name and issuer key" pageTitle="BizTalk 服務中的簽發者名稱和簽發者金鑰 | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="理解如何在 BizTalk Services 中擷取服務匯流排或 Access Control (ACS) 的簽發者名稱和簽發者金鑰。" metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="dwrede" editor="susanjo" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />




# BizTalk 服務：簽發者名稱和簽發者金鑰 (英文)

Azure BizTalk 服務使用服務匯流排簽發者名稱和簽發者金鑰，以及存取控制簽發者名稱和簽發者金鑰。具體而言：


<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>工作</strong></td>
<td><strong>什麼簽發者名稱和簽發者金鑰</strong></td>
</tr>
<tr>
<td>從 Visual Studio 部署應用程式</td>
<td>存取控制簽發者名稱和簽發者金鑰</td>
</tr>
<tr>
<td>設定 Azure BizTalk 服務入口網站</td>
<td>存取控制簽發者名稱和簽發者金鑰</td>
</tr>
<tr>
<td>在 Visual Studio 中使用 BizTalk 配接器服務建立 LOB 轉送</td>
<td>服務匯流排簽發者名稱和簽發者金鑰</td>
</tr>
</table>

本主題列出擷取簽發者名稱和簽發者金鑰的步驟。 

## 存取控制簽發者名稱和簽發者金鑰
下列項目會使用存取控制簽發者名稱和簽發者金鑰：

- 在 Visual Studio 中建立的 Azure BizTalk 服務應用程式：為了在 Visual Studio 中將 BizTalk 服務應用程式順利部署至 Azure，您需要輸入存取控制簽發者名稱和簽發者金鑰。 
- Azure BizTalk 服務入口網站：第一次登入 BizTalk 服務入口網站時，您需要輸入 BizTalk 服務名稱做為服務提供者，並輸入存取控制簽發者名稱和存取控制簽發者金鑰。

### 擷取存取控制簽發者名稱和簽發者金鑰

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，選取 [**BizTalk 服務**]。
3. 選取 BizTalk 服務。 
4. 在工作列選取 [**連接資訊**]。這時會列出存取控制命名空間、預設簽發者 (簽發者名稱) 和預設金鑰 (簽發者金鑰)，供您複製和貼上。<br/><br/>
總結：<br/>
簽發者名稱 = 預設簽發者<br/>
簽發者金鑰 = 預設金鑰


您也可以按一下 [**Open ACS Management Portal**] 來擷取存取控制值：

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，選取 [**BizTalk 服務**]。
3. 選取 BizTalk 服務。
4. 選取 [連接資訊] 按鈕，然後選取 [**Open ACS Management Portal**]。
5. 在入口網站的 [**服務設定**] 下，按一下 [**服務身分識別**]。這會顯示您的服務身分識別，也就是您的存取控制簽發者名稱值。按一下 [服務身分識別] 連結來查看密碼，這是您的簽發者金鑰值。您可以複製這些值。<br/><br/>
例如，在 [**服務身分識別**] 中，您會看到 "owner"。"Owner" 就是您的存取控制簽發者名稱。按一下 "owner" 連結時，您會看到 [**密碼**]。按一下 [密碼] 連結就可以看到值。此密碼值就是您的存取控制簽發者金鑰。 <br/><br/>
總結：<br/>
簽發者名稱 = 服務身分識別名稱<br/>
簽發者金鑰 = 密碼值

在左導覽窗格中，您也可以選取 [**Active Directory**] 來擷取存取控制值。 

<div class="dev-callout"> 
<b>重要事項</b> 
<p>使用 <strong>Active Directory</strong> 建立存取控制命名空間時，<strong>不會</strong>自動建立服務身分識別。佈建 BizTalk 服務時，會自動建立存取控制命名空間、服務身分識別 "owner" (簽發者名稱)、密碼 (簽發者金鑰) 和對稱金鑰。</p> 
<p><a href="http://go.microsoft.com/fwlink/p/?LinkID=303942">作法：使用 ACS 管理服務來設定服務身分識別</a>＞提供存取控制服務身分識別的詳細資訊。</p>
</div>


## 服務匯流排簽發者名稱和簽發者金鑰
BizTalk 配接器服務會使用服務匯流排簽發者名稱和簽發者金鑰。在 Visual Studio 中，您在 BizTalk 服務專案中使用 BizTalk 配接器服務來連線至內部部署企業營運 (LOB) 系統。若要連線，您需要建立 LOB 轉送並輸入 LOB 系統詳細資料。如果這樣做，則您也需要輸入服務匯流排簽發者名稱和簽發者金鑰。

### 擷取服務匯流排簽發者名稱和簽發者金鑰

1. 登入 [Azure 管理入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左導覽窗格中，按一下 [**服務匯流排**]。
3. 按一下您的命名空間。在工作列中，按一下 [**連接資訊**]。這會顯示 [**預設簽發者**] (簽發者名稱) 和 [**預設金鑰**] (簽發者金鑰)。您可以複製這些值。<br/><br/>
總結：<br/>
簽發者名稱 = 預設簽發者<br/>
簽發者金鑰 = 預設金鑰

## 下一步
其他 Azure BizTalk 服務主題：

-  [安裝 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [教學課程：Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## 另請參閱
-  [作法：使用 ACS 管理服務來設定服務身分識別](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/> (英文)
- [BizTalk 服務：使用 Azure 管理入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/> (英文)
- [BizTalk 服務：佈建狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/> (英文)
- [BizTalk 服務：儀表板、監視器和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/> (英文)
- [BizTalk 服務：備份與還原](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/> (英文)
- [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/> (英文)
