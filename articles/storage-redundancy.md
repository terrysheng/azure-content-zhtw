
<properties 
  pageTitle="Azure 儲存體備援選項 | Microsoft Azure" 
  description="Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性。了解 Azure 儲存體帳戶的複寫選項。"
  services="storage" 
  documentationCenter="" 
  authors="tamram" 
  manager="adinah" 
  editor=""/>

<tags 
  ms.service="storage" 
  ms.workload="storage" 
  ms.tgt_pltfrm="na" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.date="03/20/2015" 
  ms.author="tamram"/>

# Azure 儲存體備援選項



Microsoft Azure 儲存體帳戶中的資料一律會進行複寫以確保持久性及高可用性，即使在面對暫時性的硬體故障時，仍可滿足 [Azure 儲存體 SLA](http://azure.microsoft.com/support/legal/sla/)。如需 Azure 儲存體持久性的架構詳細資訊，請參閱 [Azure 儲存體 SOSP 文件](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。

建立儲存體帳戶時，您必須選取下列其中一個複寫選項：  

- **本機備援儲存體 (LRS)。**本機備援儲存體會維護三份資料複本。LRS 會在單一區域的單一設備中複寫三次。LRS 會保護資料免於一般的硬體故障，但無法保護資料免於單一設備的故障。
  
使用 LRS 可享有折扣費率。如需最高的持久性，建議您採用異地備援儲存體，如下所述。


- **區域備援儲存體 (ZRS)。**區域備援儲存體會維護三份資料複本。ZRS 會在二至三個設備 (可以是在單一區域或跨兩個區域) 中複寫三次，提供比 LRS 還要高的持久性。ZRS 可確保您的資料在單一區域中的持久性。
ZRS 提供高於 LRS 等級的持久性；不過，如需最高的持久性，建議您採用異地備援儲存體，如下所述。  

	> [AZURE.NOTE] ZRS 目前僅適用於區塊 Blob。  
	> 建立儲存體帳戶並選取區域備援複寫後，您無法轉換為採用任何其他類型的複寫，反之亦然。
 


- **異地備援儲存體 (GRS)**。建立儲存體帳戶時，依預設會啟用異地備援儲存體。GRS 可維護六個資料複本。有了 GRS，您的資料會在主要區域內複寫三次，並在與主要區域相距甚遠的次要區域內複寫三次，提供最高等級的持久性。在主要區域發生問題時，Azure 儲存體將會容錯移轉至次要區域。GRS 可確保兩個不同區域中的資料持續性。


- **讀取權限異地備援儲存體 (RA-GRS)**。讀取權限異地備援儲存體會將您的資料複寫到次要地理位置，並同時提供次要位置中資料的讀取權限。讀取權限異地備援儲存體可讓您在主要或次要任一位置無法使用的情況下，從另外一個位置存取資料。

	> [AZURE.IMPORTANT] 您可以在儲存體帳戶建立之後變更資料的複寫方式，但請注意，如果是從 LRS 切換到 GRS 或 RA-GRS，則您可能需要支付額外的單次資料傳輸成本。如果您在建立帳戶時選擇 GRS，則您之後將無法切換至其他類型的複寫，反之亦然。
 

下表提供 LRS、ZRS、GRS 和 RA-GRS 之間差異的快速概觀，而後續各節將詳細說明每種複寫類型。


|複寫策略|LRS|ZRS|GRS|RA-GRS 
|--------------------|---|---|---|------
|可跨多個設備複寫資料|否|可|可|可|
|可從次要位置及主要位置讀取資料|否|否|否|可
|可在不同節點上維護的資料複本數目|3|3|6|6
 

如需儲存體帳戶複寫的定價資訊，請參閱 [儲存體定價詳細資料](http://azure.microsoft.com/pricing/details/storage/)。

## 本機備援儲存體 (LRS)

本機備援儲存體會在您建立儲存體帳戶的區域中複寫資料。若要達到最高持久性，針對儲存體帳戶資料所提出的每個要求都會複寫三次。這三個複本會各自位於不同的容錯網域和升級網域中。故障網域 (FD) 是一組代表故障實體單元的節點，並且可被視為屬於相同實體機架的節點。升級網域 (UD) 是一組在服務升級 (首展) 過程中一起升級的節點。這三個複本會分散到 UD 和 FD 之間，以確保即使硬體故障而影響單一機架，以及首展期間升級節點時，資料仍然可以使用。只有當要求已寫入這三個複本時，系統才會成功傳回該要求。

雖然建議大部分的應用程式使用異地備援儲存體，但在某些情況下本機備援儲存體可能反而有利：  

- LRS 比 GRS 便宜，而且提供更高的輸送量。如果您的應用程式儲存可以輕鬆重新建構的資料，則您可能會選擇使用 LRS。

- 由於資料控管需求，某些應用程式限制只能在單一區域中複寫資料。

- 如果您的應用程式有它自己的異地複寫策略，則它可能不需要 GRS。


## 區域備援儲存體 (ZRS)

區域備援儲存體會在單一區域或兩個區域內的二或三個設備中複寫資料，提供比 LRS 更高的持久性。如果您的儲存體帳戶已啟用 ZRS，則即使其中一個設備發生故障，您的資料仍會是永久性。


>[AZURE.NOTE]  ZRS 目前僅適用於區塊 Blob。請注意，建立儲存體帳戶並選取異地備援複寫後，您無法轉換為採用任何其他類型的複寫，反之亦然。


## 異地備援儲存體 (GRS)


異地備援儲存體會將資料複寫到與主要區域距離數百英哩的次要區域。如果您的儲存體帳戶已啟用 GRS，即使發生主要區域因全區中斷或嚴重損壞而無法復原的情況，您的資料仍會是永久性。 

在已啟用 GRS 的儲存體帳戶中，更新會先交付到主要區域，並在此複寫三次。然後更新會複寫到跨不同容錯網域和升級網域的次要區域，並同樣在此複寫三次。

 
> [AZURE.NOTE] 使用 GRS 時，寫入資料的要求會以非同步方式複寫到次要區域。請務必注意，選擇 GRS 並不會影響對主要區域提出要求的延遲。不過，由於非同步方式複寫會涉及到延遲，在發生地區性災難的情況下，如果資料無法從主要區域復原，則很有可能會遺失尚未複寫到次要區域的變更。
 
建立儲存體帳戶時，您可以為帳戶選取主要區域。次要區域會視主要區域而定，且無法變更。下表顯示主要和次要區域配對：

|主要              |次要        
| ---------------     |----------------
|美國中北部     |美國中南部
|美國中南部     |美國中北部
|美國東部         |美國西部        
|美國西部         |美國東部         
|美國東部 2      |美國中部      
|美國中部         |美國東部 2       
|北歐                |西歐     
|西歐                |北歐    
|東南亞             |東亞       
|東亞                |東南亞 
|中國東部         |中國北部     
|中國北部         |中國東部      
|日本東部         |日本西部      
|日本西部         |日本東部      
|巴西南部         |美國中南部
|澳洲東部         |澳洲東南部
|澳洲東南部      |澳洲東部  

 
## 讀取權限異地備援儲存體 (RA-GRS)

除了 GRS 所提供的跨兩個區域複寫以外，讀取權限異地備援儲存體會藉由提供次要位置資料的唯讀權限，來最大化儲存體帳戶的可用性。如果發生在主要區域中無法使用資料的情況，您的應用程式可以從次要區域讀取資料。

啟用次要區域資料的唯讀權限時，除了儲存體帳戶的主要端點以外，您的資料還可以在次要端點中使用。次要端點與主要端點類似，但會在帳戶名稱中附加尾碼 `-secondary`。例如，如果 Blob 服務的主要端點是 `myaccount.blob.core.windows.net`，則次要端點會是 `myaccount-secondary.blob.core.windows.net`。主要和次要端點會有相同的儲存體帳戶存取金鑰。

## 後續步驟

- [Azure 儲存體延展性和效能目標](storage-scalability-targets.md) 
- [Microsoft Azure 儲存體備援選項和讀取權限異地備援儲存體](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [使用 RA-GRS 的 Microsoft Azure 儲存體模擬器 3.1](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/08/microsoft-azure-storage-emulator-3-1-with-ra-grs.aspx)
- [Azure 儲存體 SOSP 文件](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

<!--HONumber=52-->