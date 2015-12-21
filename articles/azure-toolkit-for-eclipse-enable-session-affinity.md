<properties
    pageTitle="使用 Azure Toolkit for Eclipse 啟用工作階段同質"
    description="了解如何使用 Azure Toolkit for Eclipse 來啟用工作階段同質。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# 啟用工作階段同質 #

在 Azure Toolkit for Eclipse 中，您可以為角色啟用 HTTP 工作階段同質 (或「黏性工作階段」)。下圖顯示用來啟用工作階段同質功能的 [負載平衡] 屬性對話方塊：

![][ic719492]

## 為角色啟用工作階段同質 ##

1. 以滑鼠右鍵按一下 Eclipse 專案總管中的角色，然後依序按一下 [Azure] 和 [負載平衡]。
1. 在 [WorkerRole1 的屬性] 的 [負載平衡] 對話方塊中：
    1. 核取 [為這個角色啟用 HTTP 工作階段同質 (黏性工作階段)]。
    1. 針對 [要使用的輸入端點]，選取要使用的輸入端點，例如 **http (public:80, private:8080)**。您的應用程式必須使用這個端點作為其 HTTP 端點。您可以為角色啟用多個端點，但只能選取其中一個端點來支援黏性工作階段。
    1. 重建您的應用程式

啟用後，如果您有多個角色執行個體，來自特定用戶端的 HTTP 要求會繼續由相同的角色執行個體來處理。

Eclipse Toolkit 藉由將稱為應用程式要求路由 (ARR) 的特殊 IIS 模組安裝到每個角色執行個體，來完成這項作業。ARR 會將 HTTP 要求重新路由傳送至適當的角色執行個體。此工具組會自動重新設定選取的端點，因此傳入 HTTP 流量會先路由傳送至 ARR 軟體。此工具組也會建立可設定您的 Java 伺服器接聽的新內部端點。這是 ARR 將 HTTP 流量的端點重新路由傳送至適當角色執行個體時所用的端點。如此一來，多個執行個體部署中的每個角色執行個體可作為其他所有執行個體的反向 Proxy，以啟用黏性工作階段。

## 工作階段同質的相關注意事項 ##

* 工作階段同質不會在計算模擬器中執行。您可以在不干擾建置流程或計算模擬器執行的情況下，將這些設定套用至計算模擬器，但功能本身不會在計算模擬器中執行。
* 啟用工作階段同質會導致 Azure 中的部署所佔用的磁碟空間量增加，因為當您在 Azure 雲端中啟動服務時，會下載額外的軟體並安裝到您的角色執行個體。
* 初始化每個角色的時間會比較長。
* 系統會加入一個內部端點，如上所述作為路由器來重新路由傳送流量。

如需在啟用工作階段同質時如何維護工作階段資料的範例，請參閱[如何使用工作階段同質來維護工作階段資料][]。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中為 Azure 建立 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][]

[如何使用工作階段同質來維護工作階段資料][]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java 開發人員中心]: http://go.microsoft.com/fwlink/?LinkID=699547
[適用於 Eclipse 的 Azure 工具組]: http://go.microsoft.com/fwlink/?LinkID=699529
[在 Eclipse 中為 Azure 建立 Hello World 應用程式]: http://go.microsoft.com/fwlink/?LinkID=699533
[如何使用工作階段同質來維護工作階段資料]: http://go.microsoft.com/fwlink/?LinkID=699539
[安裝 Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png

<!---HONumber=AcomDC_1210_2015-->