<properties
   pageTitle="建立和註冊發佈者帳戶 | Microsoft Azure"
   description="關於建立 Microsoft 賣方帳戶的指示，經過核准後即可在 Azure Marketplace 上銷售各種優惠類型。"
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# 建立 Microsoft 賣方帳戶
本文將逐步引導您完成要成為 Azure Marketplace 核准的 Microsoft 賣方所必須執行的帳戶建立和註冊程序。

## 1\.建立 Microsoft 帳戶
> [AZURE.WARNING]為了完成發佈程序，您必須建立一個 Microsoft 帳戶。此帳戶將用來註冊和登入發佈入口網站和賣方儀表板。您所有的 Azure Marketplace 供應項目應該只會共有一個 Microsoft 帳戶。其不應該特別屬於服務或優惠。

構成使用者名稱的電子郵件地址應該位於您的網域中，並由您的 IT 小組所控制 (例如 publishing@example.com))。付款方式、稅務資訊和報告都將透過這個帳戶路由傳送。

  >[AZURE.WARNING]Microsoft 帳戶註冊不支援使用 "Azure" 和 "Microsoft" 之類的單字。請避免使用這些字，以完成帳戶建立及註冊程序。

1. 在公司的網域內建立通訊群組清單 (DL) 或安全性群組 (SG)。
  - 將您的上線小組加入 DL。
  - DL 必須在執行中才能收到確認電子郵件。
  - 使用 marketplace@example.com 做為 DL 的電子郵件地址。
  - 這必須在內部系統中完成。
2. 開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入現有帳戶。
3. 使用 DL 電子郵件註冊 Microsoft 帳戶。
 - 您可以在 [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) 註冊 Microsoft 帳戶。
 - 使用 marketplace@example.com 做為電子郵件地址。
 - 您的 Microsoft 帳戶識別碼現在是 marketplace@example.com。

    <!--[drawing][img-msalive]-->

4. 註冊時，請使用有效的電話號碼。如果需要身分識別驗證，系統將以簡訊或自動語音的方式傳送驗證碼。
5. 請確認傳送至 DL 的電子郵件地址。

    <!--[drawing][img-email]-->

6. 您現在可以在賣方儀表板中開始使用新的 Microsoft 帳戶。

> [AZURE.IMPORTANT]使用 DL 可讓多人收到電子郵件通知，此通知在報告付款資訊時非常重要。它也可以確保 Microsoft 帳戶的擁有權可以轉移且不受限於單一個人。

## 2\.建立您的賣方儀表板帳戶
Microsoft 賣方儀表板用於註冊一次公司資訊。註冊者必須是公司的有效代表，而且必須提供個人資訊用來驗證其身分識別。註冊的人員必須使用公司內共用的 Microsoft 帳戶，而且 Azure 發佈入口網站中必須使用相同的帳戶。當您嘗試建立賣方儀表板帳戶之前，應該先檢查公司是否確實未曾擁有賣方儀表板帳戶。在這個程序期間，我們會收集銀行帳戶資訊、稅務資訊和公司地址資訊。這些資訊通常可以從金融或商務連絡人處取得。

> [AZURE.IMPORTANT]您必須完成下列賣方設定檔元件，才能進行優惠建立和部署程序的各個階段。


| 賣方設定檔 | 開始草擬 | 預備 | 免費發佈與解決方案範本 | 商業發佈 |
|----|----|----|----|----|
|公司註冊 | 必備 | 必備 | 必備 | 必備 |
|稅務設定檔識別碼 | 選用 | 選用 | 選用 | 必備 |
|銀行帳戶 | 選用 | 選用 | 選用 | 必備 |


> [AZURE.NOTE]虛擬機器僅支援自備授權 (BYOL)，並將它視為**免費**供應項目。


### 註冊您的公司帳戶
1. 開啟新的 Internet Explorer InPrivate 或 Chrome Incognito 瀏覽工作階段，確定您未登入個人帳戶

2. 移至 [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com)。

    ![繪圖][img-sd-url]

3. 使用您的公司註冊 Microsoft 帳戶登入 (亦即 marketplace@example.com))。

    ![繪圖][img-signin]

4. 請完成 [協助我們保護您的帳戶] 精靈，它會透過電話號碼或電子郵件地址驗證您的身分識別。

    ![繪圖][img-verify]

5. 移至 [帳戶詳細資料]。您將會在這個畫面中輸入個人資訊，該資訊僅會用於身分識別驗證。包含您的名稱、電子郵件地址、居住地址和個人電話號碼。此資訊不會與 Microsoft 以外的任何人士共用。

    ![繪圖][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. 將您的帳戶類型指定為 [公司]，而不是 [個人]，以代表您的公司來註冊。按 [下一步]。

    ![繪圖][img-sd-type]

7. 填寫公司詳細資料。這必須是正確資訊 -- 總部和推薦人將由 Microsoft 團隊來檢查。

    ![繪圖][img-sd-mktg1]

8. 發佈入口網站會使用公司名稱，因此，該名稱必須是正確的。

    <!--[drawing][img-sd-mktg2]-->

9. 使用公司總部的地址。

    ![繪圖][img-sd-addr]

10. 使用可連繫且可視為公司代表的推薦人。

    ![繪圖][img-sd-legal]

11. 按一下 [送出以供核准] 即可完成。

    ![繪圖][img-sd-submit]


如果您只打算發佈免費優惠，您可以跳到第 3 節＜在發佈入口網站中註冊您的帳戶＞。

如果您打算發佈商業供應項目，您必須在賣方儀表板帳戶中填寫稅務和銀行資訊。

> [AZURE.IMPORTANT]如果未填寫稅務和銀行帳戶資訊，則您無法在預備環境中適當測試優惠，也無法將優惠推送到生產環境。

如果您稍後想要更新稅務和銀行資訊，您可以前往第 3 節＜在發佈入口網站中註冊您的帳戶＞，之後再使用發佈入口網站中的連結返回此處。

### 新增稅務和銀行資訊
 如果您想要發佈商業供應項目供採購，則還需要新增付款和稅務資訊，然後在賣方儀表板中提交以供驗證。如果您只要發佈免費供應項目 (或 BYOL 供應項目)，則不需要加入這項資訊。您可以稍後再新增，但請花一些時間驗證稅務資訊。如果您知道將會提供商業供應項目供採購，建議您儘快新增。

1. 使用您的 Microsoft 帳戶登入[賣方儀表板](http://sellerdashboard.microsoft.com)。

2. 按一下 [帳戶] 索引標籤，然後按一下 [付款與稅務]。

3. 按一下 [新增付款和稅務資訊]。

4. 在 [選擇付款方式] 頁面的 [新增付款方法] 下，按一下 [銀行帳戶] 或 [PayPal]。

> [AZURE.IMPORTANT]如果您商業供應項目可供客戶在 Marketplace 購買，則這是您將收到購物付款金額的帳戶。

5. 輸入銀行帳戶或 PayPal 帳戶的詳細資料。

6. 按 [下一步]。

7. 在 [稅務資訊] 頁面上，選取您的永久居住國家或地區，並選取您擁有主要國籍的國家或地區，然後按 [下一步]。按 [下一步]。

8. 輸入稅務詳細資料，然後按 [下一步]。

9. 按一下 [提交]。如果尚未準備好送出稅務資訊以供驗證，您可以按一下 [儲存] 或 [儲存並結束]。驗證稅務資訊需要一些時間，因此建議您儘快提交以供驗證。

> [AZURE.WARNING]如果沒有在賣方儀表板帳戶中填寫稅務和銀行帳戶資訊，則您無法將商業供應項目推送到生產環境。

## 3\.在發佈入口網站中註冊帳戶
Azure 發佈入口網站可用來發佈和管理您的優惠。您可以在發佈入口網站中找到一些有用的資訊，以指引您完成優惠建立程序。

> [AZURE.WARNING]這裡必須使用賣方儀表板註冊中使用的同一個公司 Microsoft 帳戶。建立主要發行者帳戶之後，可以加入其他使用者來提供協助。

1.	開啟新的 Chrome Incognito 或 Internet Explorer InPrivate 瀏覽工作階段，確定您未登入個人帳戶。
2.	移至 [http://publish.windowsazure.com](http://publish.windowsazure.com)。
3.	 使用您的公司註冊 Microsoft 帳戶登入 (亦即 marketplace@example.com))，您也可以視需要加入共同管理員。
4.	閱讀並接受發佈者合約的條款 (第一次登入發佈入口網站)，到此就完成。

  >[AZURE.TIP]如需參與原則的說明，請參閱 [Azure 網站](http://azure.microsoft.com/support/legal/marketplace/participation-policies/)。

  > 如果在完成賣方註冊時發生問題，請記錄支援票證，如下所示：
  1. 連絡[支援人員](http://go.microsoft.com/fwlink?LinkId=272975)
  2. 選擇 [賣方儀表板註冊和您的帳戶]
  3. 選擇 [註冊開發人員帳戶]
  4. 選擇連絡**方法**

### 「銷售來源」國家/地區

> [AZURE.WARNING]
如果要在 Microsoft Azure Marketplace 上銷售服務，您的註冊實體必須是其中一個經過核准的「銷售來源」國家/地區。這項限制是基於付款和稅務理由。我們正積極在不久的將來擴充這份國家/地區清單，所以敬請密切注意。如需完整清單，請參閱本文件的第 1 節 b 項 ([http://go.microsoft.com/fwlink/?LinkID=526833](http://go.microsoft.com/fwlink/?LinkID=526833))。




## 後續步驟
現在已建立並註冊您的帳戶，請按一下您想要發佈至 Azure Marketplace 的構件類型 (虛擬機器、開發人員服務、資料服務或解決方案範本)。請參閱下列其中一份文件，了解如何發佈您自己的供應項目：

|| 虛擬機器映像 |開發人員服務 | 資料服務 | 解決方案範本 |
|----|-----|-----|-----|-----|
|**步驟 2：建立您的供應項目** |[一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)| [一般非技術性必要條件](marketplace-publishing-pre-requisites.md)|
|| [VM 映像技術性必要條件][link-single-vm-prereq] | 開發人員服務技術性必要條件 | 資料服務技術性必要條件 | [解決方案範本技術性必要條件](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [VM 映像發佈指南][link-single-vm] | 開發人員服務發佈指南 | 資料服務發佈指南 | [解決方案範本發佈指南](marketplace-publishing-solution-template-creation.md) |
|| [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] | [Azure Marketplace 行銷內容指南][link-pushstaging] |

## 另請參閱
- [使用者入門：如何將優惠發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/zh-TW/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=Nov15_HO4-->