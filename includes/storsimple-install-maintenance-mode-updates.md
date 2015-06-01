<properties
   pageTitle="安裝維護模式更新"
   description="說明如何使用 Windows PowerShell for StorSimple 來安裝維護模式更新。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />

<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### 透過 Windows PowerShell for StorSimple 安裝維護模式更新

1. 如果您尚未這樣做，請存取裝置序列主控台並選取選項 1 [使用完整存取權登入]。 

2. 輸入密碼。預設密碼為 **Password1**。

3. 在命令提示字元中，輸入：

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. 若要套用干擾性更新，您需要使裝置進入維護模式。如需相關指示，請參閱[進入維護模式](#to-enter-maintenance-mode)。

5. 當裝置處於維護模式時，在命令提示字元中輸入：

    **Start-HcsUpdate**

6. 系統將提示您進行確認。當您確認更新之後，它們將安裝於您目前正在存取的控制站上。安裝更新之後，將重新啟動控制站。

7. 完成第一個控制站的重新啟動之後，請連接到另一個控制站，並執行步驟 1 到 6。

8. 更新這兩個控制站之後，結束維護模式。如需相關指示，請參閱[結束維護模式](#to-exit-maintenance-mode)。

<!--HONumber=52-->
