<properties 
	pageTitle="使用 Azure 媒體服務 .NET SDK 建立篩選器" 
	description="本主題說明如何建立篩選器，讓您的用戶端可以使用篩選器來串流特定的資料流區段。媒體服務會建立動態資訊清單來完成此選擇性資料流。" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede,cenkdin" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
  ms.date="02/03/2016"
	ms.author="juliako"/>


#使用 Azure 媒體服務 .NET SDK 建立篩選器

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)

從 2.11 版開始，媒體服務可讓您為資產定義篩選器。這些篩選器是伺服器端規則，可讓您的客戶選擇執行如下的動作：只播放一段視訊 (而非播放完整視訊)，或只指定您客戶裝置可以處理的一部分音訊和視訊轉譯 (而非與該資產相關的所有轉譯)。透過在您客戶要求下建立的**動態資訊清單**可達成對資訊進行這樣的篩選，藉此根據指定的篩選器來串流視訊。

如需篩選器與動態資訊清單的詳細資訊，請參閱[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)。

本主題說明如何使用媒體服務 .NET SDK 建立、更新與刪除篩選器。


請注意，如果您更新篩選器，則資料流端點最多需要 2 分鐘的時間來重新整理規則。如果內容是使用此篩選器提供的 (並快取在 Proxy 與 CDN 快取中)，則更新此篩選器會造成播放程式失敗。建議在更新篩選器之後清除快取。如果這個選項無法執行，請考慮使用不同的篩選器。

##用於建立篩選器的類型

建立篩選器時會使用下列類型：

- **IStreamingFilter**。此類型是基於下列的 REST API [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx)
- **IStreamingAssetFilter**。此類型是基於下列的 REST API [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx)
- **PresentationTimeRange**。此類型是基於下列的 REST API [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx)
- **FilterTrackSelectStatement** 和 **IFilterTrackPropertyCondition**。這些類型是基於下列的 REST API [FilterTrackSelect 和 FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx)


##建立/更新/讀取/刪除全域篩選器

下列程式碼示範如何使用.NET 來建立、更新、讀取和刪除資產篩選器。
	
	string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
	            
	List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
	
	FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
	filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatements.Add(filterTrackSelectStatement);
	
	// Create
	IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
	
	// Update
	filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
	filter.Update();
	
	// Read
	var filterUpdated = _context.Filters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);

	// Delete
	filter.Delete();


##建立/更新/讀取/刪除資產篩選器

下列程式碼示範如何使用.NET 來建立、更新、讀取和刪除資產篩選器。

	
	string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
	var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
	
	    
	// Create
	IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
	                                    new PresentationTimeRange(), 
	                                    new List<FilterTrackSelectStatement>());
	
	// Update
	filter.PresentationTimeRange = 
	        new PresentationTimeRange(start: 6000000000, end: 72000000000);
	
	filter.Update();
	
	// Read
	asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
	var filterUpdated = asset.AssetFilters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);
	
	// Delete
	filterUpdated.Delete();
	



##建置使用篩選器的資料流 URL

如需如何發佈與傳遞資產的相關資訊，請參閱[將內容傳遞給客戶概觀](media-services-deliver-content-overview.md)。


下列範例顯示如何將篩選器新增至資料流 URL。

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##另請參閱 

[動態資訊清單概觀](media-services-dynamic-manifest-overview.md)
 

<!---HONumber=AcomDC_0211_2016-->