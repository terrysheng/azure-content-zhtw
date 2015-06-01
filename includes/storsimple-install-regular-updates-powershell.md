<properties
   pageTitle="透過 Windows PowerShell for StorSimple 安裝一般更新"
   description="說明如何使用 StorSimple 更新功能和 Windows PowerShell for StorSimple 來安裝一般更新。"
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
   ms.date="04/27/2015"
   ms.author="v-sharos" />

### 透過 Windows PowerShell for StorSimple 安裝一般更新

1. 開啟裝置序列主控台，然後選取選項 1 [使用完整存取權登入]。輸入密碼。預設密碼為 *Password1*。 

2. 在命令提示字元中，輸入：

    **Get-HcsUpdateAvailability**

    系統將通知您是否有可用的更新，以及更新是否為破壞性或非破壞性更新。

3. 在命令提示字元中，輸入：

    **Start-HcsUpdate**

    開始更新程序。

> [AZURE.IMPORTANT]
>
> - 這個命令只適用於一般更新。您只需在某一個控制站上執行此命令，就會更新這兩個控制站。 
> - 您可能在更新程序期間注意到控制站容錯移轉。不過，容錯移轉並不會影響系統的可用性或運作。

<!--HONumber=52-->
