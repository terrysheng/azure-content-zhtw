<properties
   pageTitle="進入維護模式"
   description="說明如何使 StorSimple 裝置進入維護模式。"
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

### 進入維護模式

1. 在序列主控台功能表中，選擇選項 1 [使用完整存取權登入]。

2. 輸入密碼。預設密碼為 **Password1**。

3. 在命令提示字元中，輸入：

    **Enter-HcsMaintenanceMode**

4. 您將會看到警告訊息，告知您維護模式將中斷所有 I/O 要求並提供與管理入口網站的連線，而系統將提示您進行確認。輸入 **Y** 以進入維護模式。

    這兩個控制站都將重新啟動。完成重新啟動時，會出現另一個訊息，指出裝置處於維護模式。

<!--HONumber=52-->
