<properties pageTitle="異常登入活動" description="包含由我們的機器學習演算識別為異常登入的報告。"" services="active-directory" documentationCenter="" authors="kenhoff" manager="ilanas" editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="kenhoff"/>

# 異常的登入活動

| 說明 | 報告位置 |
| :-------------     | :-------        |
| <p>這份報表包含由我們的機器學習演算法識別為「異常」的登入。將登入嘗試標示為異常的原因包括非預期的登入位置、當日時間與位置，或這些原因的組合。這可能表示駭客已嘗試使用此帳戶進行登入。機器學習演算法會將事件歸類為「異常」或「可疑」，其中「可疑」表示安全性缺口的可能性較高。</p><p>這份報告的結果將顯示這些登入，以及與每次登入相關聯的分類、位置和時間戳記。</p><p>如果我們在 30 天或更少的天數內遇到 10 個或更多異常的登入事件，我們會傳送電子郵件通知給全域管理員。請務必將 aad-alerts-noreply@mail.windowsazure.com 納入安全寄件者清單中。</p> | 目錄 > 報告索引標籤 |

<!---HONumber=July15_HO3-->