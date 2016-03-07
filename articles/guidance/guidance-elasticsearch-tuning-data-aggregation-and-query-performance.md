<properties
   pageTitle="利用 Azure 上的 Elasticsearch 調整資料彙總與 qQuery 的效能 | Microsoft Azure"
   description="摘要說明 Elasticsearch 查詢和搜尋最佳化的考慮事項。"
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# 利用 Azure 上的 Elasticsearch 調整資料彙總與 qQuery 的效能

本文是[系列文章的其中一篇](guidance-elasticsearch.md)。

使用 Elasticsearch 的主要原因是要支援經由資料的搜尋。使用者應該能夠快速找到他們要尋找的資訊。此外，系統必須能夠讓使用者針對資料問問題、尋找相互關係、得出結論，以利商務決策；就是這個過程讓資料變成資訊。

本文摘要說明在您決定讓系統查詢和搜尋獲得最佳效能的最佳方式時，可以考慮的選項。

所有效能建議大多取決於適用於您情況的案例、您編製索引的資料量、以及應用程式和使用者查詢您的資料的速率。您應該使用自己的資料和工作負載來評估特定案例的優點，仔細測試任何組態或編製索引結構變更的結果。為了這個目的，這份文件也將說明使用不同組態來實作一個特定案例時所執行的一些基準。您可以直接採用我們的方法來評估您系統的效能。

## 索引和查詢的效能考量

本章節描述在設計需要支援快速查詢和搜尋的索引時，您應考慮的一些共通因素。

### 在索引中儲存多個類型

Elasticsearch 索引可以包含多個類型。最好避免使用這種方法，而是為每個類型建立個別索引。請考慮下列幾點：

- 不同類型可能指定不同分析器，如果在索引層級執行查詢而不是在類型層級執行，則不一定了解 Elasticsearch 應該使用哪個分析器。詳細資訊請參閱[避免類型錯誤](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas)。

- 包含多個類型的索引分區可能會比包含單一類型的索引更大。分區愈大，Elasticsearch 執行查詢時需要進行更多工作來篩選資料。

- 如果不同類型的資料量相差很多，一個類型的資訊可能會分散到許多分區而變得稀疏，以致於降低擷取這項資料的搜尋的效率。

![](./media/guidance-elasticsearch/query-performance1.png)

***圖 1.類型共用索引的效果***

圖 1 說明這種案例。在圖的上半部，類型 A 和類型 B 的文件中共用相同的索引。類型 A 的文件比類型 B 多很多。針對類型 A 的搜尋會查詢所有的四個分區。圖的下半部顯示為每個類型建立個別索引的效果。在此情況下，搜尋類型 A 只需要存取兩個分區。

小分區可能比大分區更平均分佈，讓 Elasticsearch 更容易在節點之間分散負載。

不同類型可能會有不同的保留期間。封存與現用資料共用分區的舊資料可能有困難。

不過在某些情況下，不同類型共用索引可以很有效率，前提是：

- 會定期在相同索引中進行跨類型的搜尋。

- 每個類型只有少量的文件；在此情況下，個別為每個類型維護一個分區的負擔可能會相當大。

### 最佳化索引類型

Elasticsearch 索引包含用來填入它的原始 JSON 文件。這項資訊會放在每個索引項目的 [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) 欄位。這項資料不可搜尋，但預設會由 *get* 和 *search* 要求傳回。不過，此欄位會產生額外負荷並佔用儲存空間，讓分區更大，增加執行的 I/O 量。您可以停用個類型別的 *\_source* 欄位：

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

停用此欄位也會失去執行下列作業的能力：

- 使用 *update* API 更新索引中的資料。
- 執行可傳回強調顯示資料的搜尋。
- 直接從一個 Elasticsearch 索引重新編製另一個索引。
- 變更對應或分析設定。
- 藉由檢視原始文件來偵錯查詢。
 
### 為資料重新編製索引

索引可用的分區數目最終會決定索引的容量。您可以初步 (明智的) 推測將需要多少分區，但您永遠應該優先考慮您的文件重新編製索引策略。在許多情況下，隨著資料增加而重新編製索引無可避免；為了要得到最佳搜尋結果，您可能不希望一開始就分配大量分區給索引，而是希望隨著資料量擴大而配置新的分區。在其他情況下，您可能需要為更特定的情形重新編製索引 － 單純是您對資料量成長的估計不正確。

> [AZURE.NOTE] 快速過時的資料不需要重新編製索引。在此情況下，應用程式可能會為每一段時間建立新的索引。效能記錄檔、儲存在每天最新索引中的稽核資料都是例子。

有效率地重新編製索引牽涉到從舊的資料建立新的索引，然後再移除舊索引。如果索引很大，此程序很花費時間，而且您需要確保資料在這段期間仍可搜尋。基於這個理由，您應該建立 [每個索引的別名](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html)，且查詢應該透過這些別名擷取資料。重新編製索引時，保留指向舊索引的別名，然後在重新編製索引完成後，將別名轉向參考新索引。這個方法也可用於存取時間型資料 (每天會建立新的索引)；若要存取目前的資料，使用建立新索引時變換的別名。

### 管理對應

Elasticsearch 使用對應來決定如何解譯文件中每個欄位出現的資料。每個類型都有它自己的對應，有效地定義該類型的結構描述。Elasticsearch 使用此資訊來產生一個類型的文件中，每個欄位的反向索引。在任何文件中，每個欄位都有一個資料類型 (例如 *string*、*date* 或 *long*) 和一個值。第一次建立索引後，您可以指定索引的對應，或是讓 Elasticsearch 能夠在類型中加入文件後加以推斷。不過，請考慮下列幾點：

- 動態產生的對應可能會導致錯誤，取決於文件加入索引時的欄位解譯方式。例如，文件 1 包含保存數字的欄位 A，造成 Elasticsearch 加入指定這個欄位是 *long* 的對應。如果後續加入的文件中欄位 A 包含非數值資料，對應將失敗。在此案例中，加入第一個文件時，欄位 A 可能已被解譯為字串。在建立索引時指定此對應，可協助您避免這類問題。

- 設計您的文件，以避免產生過多的對應，因為這可能會在執行搜尋時大幅增加負擔，消耗大量記憶體，並且造成查詢找不到尋找資料。對共用相同類型的文件中的欄位採用一致的命名慣例。例如，請勿在不同的文件中使用像「first\_name」、「FirstName」、「名字」等欄位名稱，請在每份文件中使用相同的欄位名稱。此外，請勿嘗試使用值做為索引鍵 (這在資料欄系列的資料庫中是常見的方法，但會造成效率不彰和 Elasticsearch 失敗)。 如需詳細資訊，請參閱[對應遽增](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion)。

- 在適當的地方使用 *not\_analyzed* 避免 Token 化。例如，如果文件包含名為 *data* 的字串欄位，用於保存值 "ABC-DEF"，則您可能會嘗試搜尋所有符合這個值的文件，如下所示：

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

不過，這項搜尋將無法傳回預期的結果，因為索引字串 "ABC DEF" 時它被 Token 化時；它會有效地被連字號分成 ABC 和 DEF 兩個 Token。這項功能設計來支援全文搜尋，但如果您想要讓字串被解譯為單一不可分割的項目，在將文件加入索引時應該停用 Token 化。您可以像這樣使用對應：

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

如需詳細資訊，請參閱[尋找實際值](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text)。

### 使用文件值

許多查詢與彙總需要搜尋作業納入資料排序。排序必須能夠將一或多個字詞對應到一份文件清單。為了協助此程序，Elasticsearch 可以將用來做為排序鍵的欄位所有值載入記憶體。這項資訊稱為 *fielddata*。其目的是快取在記憶體中的 fielddata 會產生較少的 I/O，而且可能會比重複地從磁碟讀取相同的資料更快。不過，如果欄位有高基數，則將 fielddata 儲存在記憶體中會耗用大量的堆積空間，可能影響執行其他並行作業的能力，或甚至耗盡儲存空間導致 Elasticsearch 失敗。

ElasticSearch 也支援*文件值*，做為另一種方法。文件值類似記憶體中的一個 fielddata 項目，但是儲存在磁碟上，且是在資料儲存在索引時建立 (fielddata 是在查詢執行時動態建立)。 文件值不會耗用堆積空間，因此很適合用在會跨欄位排序或彙總資料的查詢 (這些欄位可能包含非常大量的唯一值)。此外，堆積的壓力降低，有助於抵銷從磁碟擷取資料和從記憶體讀取的效能差異；記憶體回收可能較少發生，其他利用到記憶體的並行作業就比較不會受影響。

您可以使用 *doc\_values* 屬性，個別啟用或停用索引各個屬性的文件值，如下列範例所示：

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Elasticsearch 2.0.0 及之後的版本預設會啟用文件值。

使用文件值的實際影響很可能與您自己的資料和查詢案例極度相關，因此請準備好執行效能測試以確立其實用性。您也應該注意，文件值不適用於分析的字串欄位。如需詳細資訊，請參閱[文件值](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values)。

### 使用用戶端節點

第一個收到要求的節點會處理所有查詢。這個節點會進一步將要求傳送至所有包含被查詢索引之分區的其他節點，並接著累積結果以便傳回回應。如果查詢牽涉到彙總資料或執行複雜的計算，初始節點會負責執行適當的處理。如果您的系統必須支援相對少量的複雜查詢，請考慮建立用戶端節點的集區，以減緩資料節點的負載。反之，如果您的系統必須處理大量的簡單查詢，可直接將這些要求提交至資料節點，然後使用負載平衡器平均分散要求。

### 使用複本減少查詢競爭

大幅提升查詢效能常用的策略是建立每個索引的許多複本。藉著從複本提取資料，可以滿足資料擷取作業。不過，此策略可能嚴重影響資料擷取作業的效能，所以在牽涉到混合工作負載的情況下需要小心使用。此外，只有在複本會分散到各節點，且不會與屬於相同索引的主要分區競用資源時，這項策略才能發揮優點。請記住，可以增加或減少索引的複本數目，以利動態索引。

### 使用分區要求快取

Elasticsearch 可以快取記憶體中每個分區上的查詢所要求的本機資料。如此可以更快速執行存取相同資料的查詢；也可以從記憶體而非從磁碟儲存體擷取資料。當分區重新整理以及資料已變更時，快取中的資料便失效；重新整理的頻率由索引的 *refresh\_interval* 值設定。索引的分區要求快取預設為停用，但您可以啟用它，如下所示：

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

分區要求快取是最適合用於相對靜態的資訊，例如歷史記錄或記錄資料。

## 測試和分析彙總與搜尋效能
本節說明針對不同叢集和索引設定執行一系列測試的結果。每項測試皆從空的索引開始，接著在測試中藉由執行大量插入作業填入索引 (每個作業加入 1000 個文件)。於此同時，重複執行數個用來搜尋特定資料和產生彙總的查詢，重複間隔為 5 秒。測試的目的是要確立資料量對查詢效能的影響。

索引中的每個文件有相同的結構描述。下表摘要說明結構描述中的欄位：

 名稱 | 類型 | 注意事項 |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 組織 | String | 測試會產生 200 個唯一組織。 |
 CustomField1 - CustomField5 |String |共五個字串欄位，設為空字串。|
 DateTimeRecievedUtc |Timestamp |加入文件的日期和時間。|
 Host |String |此欄位設為空字串。|
 HttpMethod |String |此欄位設為下列值之一：POST、GET、PUT。|
 HttpReferrer |String |此欄位設為空字串。|
 HttpRequest |String |此欄位會填入長度為 10 到 200 個字元的隨機文字。|
 HttpUserAgent |String |此欄位設為空字串。|
 HttpVersion |String |此欄位設為空字串。|
 OrganizationName |String |這個欄位設為和 Organization 欄位相同的值。|
 SourceIp |IP |此欄位包含 IP 位址，指出資料的「來源」。 |
 SourceIpAreaCode |Long |此欄位設為 0。|
 SourceIpAsnNr |String |此欄位設為 AS#####。|
 SourceIpBase10 |Long |此欄位設為 500。|
 SourceIpCountryCode |String |此欄位為 2 個字元的國碼。 |
 SourceIpCity |String |此欄位為字串，指明國家/地區的城市。 |
 SourceIpLatitude |兩倍 |此欄位包含隨機值。|
 SourceIpLongitude |兩倍 |此欄位包含隨機值。|
 SourceIpMetroCode |Long |此欄位設為 0。|
 SourceIpPostalCode |String |此欄位設為空字串。|
 SourceLatLong |Geo point |此欄位設為隨機地區點。|
 SourcePort |String |此欄位會填入一個隨機數字的字串表示法。|
 TargetIp |IP |會填入範圍從 0.0.100.100 到 255.9.100.100 的隨機 IP 位址。|
 SourcedFrom |String |此欄位設為 “MonitoringCollector” 字串。|
 TargetPort |String |此欄位會填入一個隨機數字的字串表示法。|
 Rating |String |此欄位會填入 20 個隨機選取的不同字串值的其中一個。|
 UseHumanReadableDateTimes |Boolean |此欄位設為 false。|

下列查詢在測試的每次反覆中整批執行 (斜體為這些查詢在本文件其餘部分使用的名稱)：

- 過去 15 分鐘內輸入多少個有 *Rating* 值的文件 (*依評等的計數*)?

- 過去 15 分鐘內，每 5 分鐘的間隔中加入多少個文件 (*不同時間的計數*)?

- 過去 15 分鐘內，每個國家/地區的每個 *Rating* 值加入多少文件 (*依國家/地區的點擊數*)?

- 過去 15 分鐘內，哪 15 個組織最常有文件加入 (*前 15 名組織*)?

- 過去 15 分鐘內，多少不同組織有文件加入 (*唯一計數組織*)?

- 過去 15 分鐘加入多少個文件 (*點擊總數*)?

- 過去 15 分鐘內，多少不同 *SourceIp* 值有文件加入 (*唯一 IP 計數*)?

執行這些測試是為了了解下列變數的效果：

- **磁碟類型**。測試是在使用標準儲存體 (HDD) 的 D4 VM 組成的 6 個節點叢集上執行，並在使用進階儲存體 (SSD) 的 DS4 VM 組成的 6 節點叢集上重複測試。

- **機器大小 - 向上擴充**。測試是在由 DS3 VM 組成的 6 節點叢集 (*小型*叢集) 上執行，並分別在 DS4 VM 的叢集 (*中型*叢集) 和 DS14 機器的叢集 (*大型*叢集) 上重複測試。下表摘要說明每個 VM SKU 的主要特性：

叢集 |VM SKU |核心數目 |資料磁碟數量 |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
小型 |標準 DS3 |4 |8 |14 |
中型 |標準 DS4 |8 |16 |28 |
大型 |標準 DS14 |16 |32 |112 |

- **文件值**。一開始是以 doc\_values 設為 true 的索引執行測試。再將 doc\_values 設為 false，重複執行選取的測試。

## 效能結果 – 磁碟類型

下表摘要說明分別在 DS4 VM (使用 HDD) 組成的 6 節點叢集上以及在 DS4 VM (使用SSD) 組成的 6 節點叢集上執行工作的回應時間。兩個叢集的 Elasticsearch 組態相同。資料已分散到每個節點的 16 個磁碟上，且每個節點有 14 GB RAM 配置給執行 Elasticsearch 的 JVM；剩餘的記憶體 (也就是 14 GB) 是保留供作業系統使用。每項測試執行 24 小時。選擇這個時間長度，是為了讓資料量增加的效果更加明顯，以及讓系統穩定：

 叢集 |作業/查詢 |平均回應時間 (毫秒)|
 -----------|---------------------------- |--------------------------|
 D4 |擷取 |978 |
 |依評等的計數 |103 |
 |不同時間的計數 |134 |
 |依國家/地區的點擊數 |199 |
 |前 15 名組織 |137 |
 |唯一計數組織 |139 |
 |唯一的 IP 計數 |510 |
 |點擊總數 |89 |
 DS4 |擷取 |511 |
 |依評等的計數 |187 |
 |不同時間的計數 |411 |
 |依國家/地區的點擊數 |402 |
 |前 15 名組織 |307 |
 |唯一計數組織 |320 |
 |唯一的 IP 計數 |841 |
 |點擊總數 |236 |

乍看之下，DS4 叢集執行的查詢似乎比 D4 叢集少，且回應時間加倍 (或較差)。不過這只是表面的結果。下表顯示每個叢集執行的擷取作業數目 (請記住，每個作業會載入 1000 個文件)：

 叢集 | 擷取作業
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

在測試期間，DS4 叢集能夠載入的資料幾乎是 D4 叢集中的兩倍。因此，在分析每個作業的回應時間時，您還必須考慮每個查詢必須掃描多少文件的，以及傳回多少文件。

這些數字不是一成不變的，因為索引中文件的量會持續成長。您不能只是將 503137 除以 264769 (每個叢集執行的擷取作業數目)，將結果乘以 D4 叢集執行每個查詢的平均回應時間，計算出比較數字，因為這樣的計算結果忽略了擷取作業同時執行的 I/O 數量。

相反地，您應該測量測試期間寫入磁碟以及從磁碟讀取的實體資料量。JMeter 測試計劃會為每個節點擷取這項資訊。結果摘要如下：

 叢集 |每項作業平均寫入/讀取位元組|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

這些數字顯示 DS4 叢集大約能夠承受D4 叢集的 1.8 倍 I/O 速率。基於這個結果，除了磁碟的本質外，其他所有資源都相同，則差異必定是來自使用 SSD 而非 HDD。

為了證明這個結論，下圖說明每個叢集在不同時間如何執行 I/O：

![](./media/guidance-elasticsearch/query-performance2.png)

D4 叢集的圖顯示重大變化，特別是在測試的前半段。這可能是因為系統採取節流來降低 I/O 速率。在測試的初始階段，查詢可以快速地執行，因為只有一些資料要分析。因此，D4 叢集中磁碟的運作可能很接近其 IOPS 能力，雖然每個 I/O 作業可能不會傳回許多資料。DS4 叢集能夠支援較高的 IOPS 速率，且不會發生相同程度的節流；I/O 速度比較規則。

為了圖解說明這個理論，下兩張圖顯示磁碟 I/O在不同時間如何封鎖 CPU (圖中的磁碟等候時間是 CPU 等待 I/O 所花費時間的比例)：

![](./media/guidance-elasticsearch/query-performance3.png)

請務必了解，在此測試案例中，I/O 作業封鎖 CPU 有兩個主要原因：

- I/O 子系統無法從磁碟讀取資料或將資料寫入磁碟。

- 主機環境可能會節流 I/O 子系統。配備標準儲存體的 Azure 磁碟有 500 IOPS 的最大輸送量，而配備進階儲存體的磁碟則為 5000 IOPS 的最大輸送量。

在前半段測試期間，D4 叢集花在等候 I/O 的時間，與圖形顯示的 I/O 速率反向密切相關；低 I/O 的期間對應到 CPU 被封鎖的一段長時間。

這表示系統正在節流 I/O。隨著更多的資料加入叢集，情況跟著改變，在後半段的測試中，尖峰 I/O 等候時間對應至尖峰 I/O 輸送量。此時，CPU 被封鎖，同時執行真正的 I/O。同樣地，DS4 叢集用在等候 I/O 所花費的時間較平均，且每個尖峰在 I/O 效能都有對等的尖峰 (而不是低谷)；這意味著幾乎沒有發生任何節流。

還有一個因素需要考慮。在測試期間，D4 叢集產生 10584 擷取錯誤和 21 查詢錯誤。在 DS4 叢集上的測試沒有產生任何錯誤。

## 效能結果 – 向上擴充

下表摘要說明在中型 (DS4) 和大型 (DS14) 叢集上執行測試的結果。每個 VM 使用 SSD 來保存資料。每項測試執行 24 小時：

| 叢集 |作業/查詢 |要求數目 |平均回應時間 (毫秒)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| 中型 (DS4) |擷取 |503157 |511 |
| |依評等的計數 |6958 |187 |
| |不同時間的計數 |6958 |411 |
| |依國家/地區的點擊數 |6958 |402 |
| |前 15 名組織 |6958 |307 |
| |唯一計數組織 |6956 |320 |
| |唯一的 IP 計數 |6955 |841 |
| |點擊總數 |6958 |236 |
| 大型 (DS14) |擷取 |502714 |511 |
| |依評等的計數 |7041 |201 |
| |不同時間的計數 |7040 |298 |
| |依國家/地區的點擊數 |7039 |363 |
| |前 15 名組織 |7038 |244 |
| |唯一計數組織 |7037 |283 |
| |唯一的 IP 計數 |7037 |681 |
| |點擊總數 |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## 效能結果 – 文件值

在啟用文件值的情況下執行擷取與查詢測試，導致 Elasticsearch 儲存用來排序磁碟上欄位的資料。接著，再停用文件值重複測試，因此 Elasticsearch 會動態建構 fielddata 並在記憶體中快取它。所有測試皆執行 24 小時。
 
 下表比較分別使用 D4、DS4 和 DS14 VM 建立 6 個節點的叢集，在叢集上執行測試的回應時間。

| 叢集 |作業/查詢 |啟用文件值 (毫秒) | 停用文件值 (毫秒) | 差異 % |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |擷取 | 978 | 835 | -15% |
| |依評等的計數 | 103 | 132 | +28% |
| |不同時間的計數 | 134 | 189 | +41% |
| |依國家/地區的點擊數 | 199 | 259 | +30% |
| |前 15 名組織 | 137 | 184 | +34% |
| |唯一計數組織 | 139 | 197 | +42% |
| |唯一的 IP 計數 | 510 | 604 | +18% |
| |點擊總數 | 89 | 134 | +51% |
| DS4 |擷取 | 511 | 581 | +14% |
| |依評等的計數 | 187 | 190 | +2% |
| |不同時間的計數 | 411 | 409 | -0.5% |
| |依國家/地區的點擊數 | 402 | 414 | +3% |
| |前 15 名組織 | 307 | 284 | -7% |
| |唯一計數組織 | 320 | 313 | -2% |
| |唯一的 IP 計數 | 841 | 955 | +14% |
| |點擊總數 | 236 | 281 | +19% |
| DS14 |擷取 | 511 | 571 | +12% |
| |依評等的計數 | 201 | 232 | +15% |
| |不同時間的計數 | 298 | 341 | +14% |
| |依國家/地區的點擊數 | 363 | 457 | +26% |
| |前 15 名組織 | 244 | 338 | +39% |
| |唯一計數組織 | 283 | 350 | +24% |
| |唯一的 IP 計數 | 681 | 909 | +33% |
| |點擊總數 | 200 | 245 | +23% |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->