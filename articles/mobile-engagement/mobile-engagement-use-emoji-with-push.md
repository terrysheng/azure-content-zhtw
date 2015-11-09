<properties 
	pageTitle="在 Azure Mobile Engagement 內使用 Emoji 表情符號" 
	description="如何在推播通知內使用 Emoji 表情符號"		
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/22/2015" 
	ms.author="piyushjo" />

#在推播通知內使用 Emoji 表情符號

您可以使用幾個簡易的步驟，在推播通知內包含 Emoji 表情符號：

1. 首先您需要尋找要在訊息中傳送的 Emoji。請確定您要選取的 Emoji 可受目標裝置支援，因為裝置製造商需要一些時間才能將新核准的 Emoji 新增至裝置平台。 

2. 在 **Windows** 上 - 您可以瀏覽這個[連結](http://apps.timwhitlock.info/emoji/tables/unicode)，並複製 'Native' 圖示。

	![][7]

3. 在 **Mac** 上 - 您可以在字典應用程式中的 [編輯] -> [表情與符號] 中找到表情符號。

	![][6]

4. 現在，移至 Azure Mobile Engagement 入口網站的 **Reach** 索引標籤。選取您的推播通知類型 (通知、投票等等)。對於此範例，我們選擇公告推播。

5. 指定通知的不同欄位，直到您到達通知的文字。這就是您新增 Emoji 表情符號的位置。您可以選擇將它放在標題、訊息或兩者中。您必須拖放或複製您從上述位置找到的表情符號。

	![][1]

6. 完成通知的其他欄位，然後儲存。

7. 當您執行測試或啟動公告時，您會看到具有指定表情符號的通知。

	![][3] ![][4] ![][5]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/iOS_Emoji.png
[4]: ./media/mobile-engagement-use-emoji-with-push/Android_Emoji.png
[5]: ./media/mobile-engagement-use-emoji-with-push/WindowsPhone_Emoji.png
[6]: ./media/mobile-engagement-use-emoji-with-push/Mac_SelectEmoji.png
[7]: ./media/mobile-engagement-use-emoji-with-push/Windows_SelectEmoji.png

<!---HONumber=Nov15_HO1-->