<properties
	pageTitle="Azure 註冊問題疑難排解 | Microsoft Azure"
	description="針對註冊 Azure 時可能發生的問題，對常見的原因進行疑難排解。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/02/2016"
	ms.author="cjiang"/>

# Azure 註冊問題疑難排解
本文針對註冊 Azure 時可能發生的問題，協助您對常見的原因進行疑難排解。

## 我在輸入註冊資訊時出現錯誤。
帳戶個人檔案資訊正確嗎？ 您的個人檔案詳細資料會用來產生帳單，因此請務必輸入正確的資訊。不正確的欄位範例如下︰
- 不正確的名稱或姓氏。
- 無效的電話號碼。
- 無效的地址。
- 使用非標準字元。

建議
- 若要成功註冊，請輸入正確的資訊。

## 在嘗試註冊時，我未在帳戶驗證期間收到簡訊或接聽來電。
- 請確認您的電話號碼可以接收簡訊。
- 再次確認您輸入的電話號碼，包括下拉式功能表中的國碼選項。
- 請確定您的電話可以收到簡訊 (SMS) (如果您使用 [傳送簡訊]) 或接聽來電 (如果您選擇 [撥號給我] 替代方式)。
- 如果您使用行動電話，請確定電話連線能力良好。
- 如果您選擇 [傳送簡訊]，最多等候 4 分鐘，傳訊系統會將文字代碼傳送給您。
- 一旦收到簡訊，請在文字方塊中插入代碼，然後按一下 [驗證] 按鈕以繼續。

建議：
- 如果您的電話未接收到簡訊 (SMS)，請使用 [撥號給我] 替代驗證方法。
- 如果使用簡訊和 [撥號給我] 方法的電話驗證步驟均失敗，請使用另一個電話號碼。
- 您無法使用 VOIP 電話號碼進行電話驗證程序。

附註：您可以在稍後更新[個人檔案資訊](https://account.windowsazure.com/Profile)，以變更您的慣用電話號碼。

## 我的信用卡不被接受。
請確定註冊時使用的付款方法是 Azure 啟用或付款所支援的方法。
- 不接受虛擬和預付的信用卡/轉帳卡。
- 根據帳戶國家/地區而定，接受的信用卡/轉帳卡提供者有所不同。

建議：
- 如需使用信用卡或金融卡之註冊問題的常見原因，請參閱[當我嘗試註冊 Azure 時，我的信用卡不被接受](billing-credit-card-fails-during-azure-sign-up.md)文章。

## 我無法啟用 MSDN、BizSpark、BizSparkPlus 或 MPN 之類的 Azure 權益方案。
確認資格：透過您的權益方案頻道來進行驗證，確認您是否符合所選方案的資格：
- MSDN：
  - 請在您的 [MSDN 帳戶頁面](https://msdn.microsoft.com/subscriptions/manage/default.aspx)驗證自己的資格狀態。
  - 如果您無法驗證自己的狀態，請連絡 [MSDN 訂閱客服中心](https://msdn.microsoft.com/subscriptions/contactus.aspx)
- MPN
  - 登入 [MPN 入口網站](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx)，並驗證您的資格狀態。如有適當的[雲端平台能力](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)，您或許能夠得到額外的好處。
  - 如果您無法驗證自己的狀態，請連絡 [MPN 支援](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)。
- Bizpark：
  - 登入 [BizSpark 入口網站](https://www.microsoft.com/bizspark/default.aspx#start-two)，確認您 BizSpark 和 BizSpark Plus 的資格狀態。如果無法驗證您的狀態，請傳送電子郵件到[連絡 BizSpark 小組](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)以連絡 BizSpark 支援

建議：
- 如果您嘗試啟用新的權益訂用帳戶，而且在註冊體驗期間遇到任何錯誤，請確認您已在 [Azure 訂用帳戶頁面](http://account.windowsazure.com/Subscriptions)完成訂用帳戶設定。可能需要幾分鐘，訂用帳戶才會顯示為作用中。當訂用帳戶啟用之後，您會收到電子郵件。如果您的訂用帳戶狀態持續擱置超過四分鐘，請[連絡 Azure 支援](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以取得協助。

## 我無法啟用新的 Azure in Open 訂用帳戶。
您必須具備有效的 OSA 金鑰與至少一個相關聯的 Azure in Open 權杖，才能啟用新的 Azure in Open 訂用帳戶。

建議：
- 如果您沒有 OSA 金鑰，請連絡 [Microsoft Pinpoint](http://pinpoint.microsoft.com/) 列出的其中一個 Microsoft 合作夥伴。

## 我不能啟用 Azure 免費試用。
您曾經使用過 Azure 訂用帳戶嗎？ Azure 服務條款協議限制任何從未使用過 Azure 的使用者只能啟用免費試用一次。如果您有任何其他類型的 Azure 訂用帳戶，您將無法啟用免費試用。

建議：
- 如果您以前已啟用過 Azure 訂用帳戶，以致於免費試用啟用失敗，請考慮購買「隨用隨付」訂用帳戶。您也可能有資格獲得權益優惠。請在 [Microsoft Azure 優惠詳細資料頁面](https://azure.microsoft.com/support/legal/offer-details/)深入了解，而非 Azure 免費試用方案。請注意，權益方案需要特定的必要條件。

## 仍然無法註冊？
如果仍然發生註冊問題，或您的問題已超出本文的範圍，請[連絡 Azure 支援](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以取得支援。

<!---HONumber=AcomDC_0309_2016-->