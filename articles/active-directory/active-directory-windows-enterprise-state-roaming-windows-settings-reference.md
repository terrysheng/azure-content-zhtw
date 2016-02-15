<properties
	pageTitle="Windows 10 漫遊設定參考 | Microsoft Azure"
	description="在 Windows 10 中進行漫遊或備份的所有設定的完整清單。"
	services="active-directory"
    keywords="企業狀態漫遊, windows 雲端"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Windows 10 漫遊設定參考

以下是在 Windows 10 中進行漫遊或備份的所有設定的完整清單。

##裝置和端點
請參閱下表以取得 Windows 10 中同步處理、備份及還原架構支援的裝置和帳戶類型的摘要。

| 帳戶類型和作業 | 桌上型 | 行動 |
|----------------------------|---------|--------|
|Azure Active Directory：同步處理| 是 | 否 |
|Azure Active Directory：備份/還原| 否 | 否 |
|Microsoft 帳戶：同步處理|是 | 是 |
|Microsoft 帳戶：備份/還原| 否 | 是 |


##什麼是備份？
根據預設，Windows 設定一般會同步處理，但是某些設定只能備份，例如裝置上已安裝應用程式的清單。如果使用者使用 [設定] 應用程式在裝置上停用同步處理，通常可以同步處理的應用程式資料會變成只能備份。備份資料在新裝置第一次執行期間，只能透過還原作業存取。備份可以透過裝置設定停用，並且能透過使用者的 OneDrive 帳戶管理及刪除。

## Windows 設定概觀
下列設定群組可供使用者啟用/停用 Windows 10 裝置上的設定同步處理。

- 佈景主題：桌面背景、使用者圖格、工作列位置等 
- Internet Explorer 設定：瀏覽歷程記錄、輸入的 URL、我的最愛等 
- 密碼：[Windows 認證保險箱](https://technet.microsoft.com/library/jj554668.aspx)，包括 Wi-Fi 設定檔 
- 語言喜好設定：拼字檢查字典、系統語言設定 
- 輕鬆存取：朗讀程式、螢幕小鍵盤、放大鏡 
- 其他 Windows 設定：請參閱 Windows 設定詳細資料

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)
 
## Windows 設定詳細資料
在下表中，[設定群組] 資料行中的 [其他] 項目是指您可以移至 [設定] > [帳戶] > [同步處理您的設定] > [其他 Windows 設定] 停用的設定。

[設定群組] 資料行中的 [內部] 項目是指只能從應用程式本身的同步處理停用，或藉由使用行動裝置管理 (MDM) 或群組原則設定停用的設定和應用程式。不會漫遊的設定或同步處理不屬於一個群組。


| Settings | 桌上型 | 行動 | 群組 |
|----------------------------------|---------|---------|-------|
| **帳戶**：帳戶圖片 | sync |X |佈景主題 |
| **帳戶**：其他帳戶設定 |X |X | |
| **進階行動寬頻**：網際網路連接共用網路名稱 (透過藍牙啟用行動 Wi-Fi 作用區的自動探索)|sync |sync |密碼 |
|**應用程式資料**：個別應用程式可以同步處理資料|同步處理備份 | 同步處理備份|內部 |
|**應用程式清單**：已安裝應用程式的清單 |X |backup |其他 |
|**藍牙**：所有藍牙設定 |X |X | |
|**命令提示字元**：所有命令提示字元設定 |sync| |X |其他
|**Cortana**：開啟或關閉 |X |X | |
|**Cortana**：在鎖定畫面上啟用 Cortana |X |X | |
|**Cortana**：使用者名稱 |sync |sync |內部|
|**Cortana**: 大聲閱讀 SMS |X |sync |內部|
|**Cortana**：安全的搜尋 |X |sync |內部|
|**Cortana**：尋找班機及其他資訊|X |sync |內部|
|**認證**：認證保險箱 |sync |sync |password|
|**日期、時間和區域**：自動時間 (網際網路時間同步處理) |sync |sync |語言|
|**日期、時間和區域**：24 小時制時鐘|sync |sync |語言|
|**日期、時間和區域**：日期和時間|sync |X |語言|
|**日期、時間和區域**：時區 | |X |語言|
|**日期、時間和區域**：日光節約時間|sync |X |語言|
|**日期、時間和區域**：國家/區域 |sync |X |語言|
|**日期、時間和區域**：週的第一天 |sync |X |語言|
|**日期、時間和區域**：區域格式 (地區設定) |sync |X |語言|
|**日期、時間和區域**：簡短日期 |sync |X |語言|
|**日期、時間和區域**：完整日期 |sync |X |語言|
|**日期、時間和區域**：簡短時間 |sync |X |語言|
|**日期、時間和區域**：完整時間 |sync |X |語言|
|**桌面個人化**：桌面主題 (背景、系統色彩、預設系統音效、螢幕保護裝置) |sync |X |佈景主題|
|**桌面個人化**：投影片放映底色圖案 |sync |X |佈景主題|
|**桌面個人化**：工作列設定 (位置、自動隱藏等) |sync |X |佈景主題|
|**桌面個人化**：開始畫面版面配置 |sync |backup ||
|**裝置**：您已連接的共用印表機 |sync | X |其他 |
|**Edge 瀏覽器**：閱讀清單 |sync |sync |內部|
|**Edge 瀏覽器**：我的最愛 |sync |sync |內部|
|**Edge 瀏覽器**：所有其他 Edge 設定|X |X ||
|**高對比**：開啟或關閉 |sync |sync |輕鬆存取|
|**高對比**：佈景主題設定|sync |X ||輕鬆存取|
|**Internet Explorer**：開啟索引標籤 (URL 和標題)|sync |sync |Internet Explorer|
|**Internet Explorer**：閱讀清單|sync |sync |Internet Explorer|
|**Internet Explorer**：輸入的 URL|sync |sync |Internet Explorer|
|**Internet Explorer**：瀏覽歷程記錄|sync |sync |Internet Explorer|
|**Internet Explorer**：我的最愛|sync |sync |Internet Explorer|
|**Internet Explorer**：排除的 URL|sync |sync |Internet Explorer|
|**Internet Explorer**：首頁|sync |sync |Internet Explorer|
|**Internet Explorer**：網域建議|sync |sync |Internet Explorer|
|**鍵盤**：使用者可以開啟/關閉螢幕小鍵盤|sync |X |輕鬆存取|
|**鍵盤**：開啟相黏鍵 (預設為關閉)|sync |X |輕鬆存取|
|**鍵盤**：開啟篩選鍵 (預設為關閉)|sync |X |輕鬆存取|
|**鍵盤**：開啟切換鍵 (預設為關閉)|sync |X |輕鬆存取|
|**Internet Explorer**：網域語言：中文 (CHS) QWERTY - 啟用自我學習|sync |X |語言|
|**語言**：CHS QWERTY - 啟用動態 c&idate 排名|sync |X |語言|
|**語言**：CHS QWERTY - 字元集簡體中文|sync |X |語言|
|**語言**：CHS QWERTY - 字元集繁體中文|sync |X |語言|
|**語言**：CHS QWERTY - 模糊拼音|sync |sync |語言|
|**語言**：CHS QWERTY - 模糊配對|sync |sync |語言|
|**語言**：CHS QWERTY - 完整拼音||sync |X |語言|
|**語言**：CHS QWERTY - 雙拼音|sync |X |語言|
|**語言**：CHS QWERTY - 閱讀自動更正|sync |X |語言|
|**語言**：CHS QWERTY - C/E 切換鍵，shift 鍵|sync |X |語言|
|**語言**：CHS QWERTY - C/E 切換鍵，Ctrl 鍵|sync |X |語言|
|**語言**：CHS WUBI - 單一字元輸入模式 |sync |X |語言|
|**語言**：CHS WUBI - 顯示 c&idate 的剩餘編碼 |sync |X |語言|
|**語言**：CHS WUBI - 4-coding 無效時發出嗶聲|sync |X |語言|
|**語言**：CHS 注音符號 - 包括 CJK Ext-A|sync |X |語言|
|**語言**：日文輸入法 - 預測輸入和自訂文字|sync |sync |語言|
|**語言**：韓文 (KOR) 輸入法|X |X |語言|
|**語言**：手寫辨識|X |X |語言|
|**語言**：語言設定檔|sync |backup |語言|
|**語言**：拼字檢查 - 自動校正和反白顯示拼字錯誤|sync |backup |語言|
|**語言**：鍵盤的清單|sync |backup |語言|
|**鎖定畫面**：所有鎖定畫面設定|X |X ||
|**放大鏡**：開啟或關閉 (主切換)|X |backup |輕鬆存取|
|**放大鏡**：開啟或關閉反轉色彩 (預設為關閉)|sync |X |輕鬆存取|
|**放大鏡**：追蹤 - 跟隨鍵盤焦點|sync |X |輕鬆存取|
|**放大鏡**：追蹤 - 跟隨滑鼠游標|sync |X |輕鬆存取|
|**放大鏡**：當使用者登入時啟動 (預設為關閉)|sync |X |輕鬆存取|
|**滑鼠**：變更滑鼠游標的大小|sync |X |其他|
|**滑鼠**：變更滑鼠游標的色彩|sync |X |其他|
|**滑鼠**：所有其他設定|X |X ||
|**朗讀程式**：快速啟動|sync |X |輕鬆存取|
|**朗讀程式**：使用者可以變更朗讀程式朗讀音調|sync |X |輕鬆存取|
|**朗讀程式**：使用者可以開啟或關閉朗讀程式對於常用項目的閱讀提示 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：使用者可以開啟或關閉是否聽到輸入的字元 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：使用者可以開啟或關閉是否聽到輸入的文字 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：在朗讀程式之後有插入游標 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：啟用朗讀程式的游標視覺反白顯示 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：播放音訊提示 (預設為開啟)|sync |X |輕鬆存取|
|**朗讀程式**：舉起手指時啟用觸控式鍵盤上的按鍵 (預設為關閉)|sync |sync |輕鬆存取|
|**輕鬆存取**：設定閃爍游標的寬度|sync |X |輕鬆存取|
|**輕鬆存取**：移除背景影像 (預設為關閉)|sync |X |輕鬆存取|
|**電源與睡眠**：所有設定|X |X ||
|**開始畫面個人化**：系統色彩|sync |sync |佈景主題|
|**輸入**：拼字檢查字典|sync |backup |語言|
|**輸入**：自動校正拼錯文字|sync |backup |語言|
|**輸入**：醒目提示拼錯的單字|sync |backup |語言|
|**輸入**：在我輸入時顯示文字建議|sync |backup |語言|
|**輸入**：在我選擇文字建議後加上空格|sync |backup |語言|
|**輸入**：在我點兩下空格鍵後加上句號|sync |backup |語言|
|**輸入**：每個句子的第一個字母大寫|sync |backup |語言|
|**輸入**：在我點兩下 Shift 鍵時全部使用大寫字母|sync |backup |語言|
|**輸入**：在我輸入時播放按鍵音|sync |backup |語言|
|**輸入**：觸控式鍵盤的個人化資料|sync |backup |語言|
|**Wi-Fi**：Wi-Fi 設定檔 (僅限 WPA)|sync |sync |密碼|


## 相關主題
- [企業狀態漫遊概觀](active-directory-windows-enterprise-state-roaming-overview.md)
- [在 Azure Active Directory 中啟用企業狀態漫遊](active-directory-windows-enterprise-state-roaming-enable.md)
- [設定和資料漫遊常見問題集](active-directory-windows-enterprise-state-roaming-faqs.md)
- [設定同步處理的群組原則和 MDM 設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)






  

<!---HONumber=AcomDC_0204_2016-->