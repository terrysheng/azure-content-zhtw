<properties
	pageTitle="自訂分析器 (Azure 搜尋服務 REST API 2015-02-28-Preview 版) | Microsoft Azure"
	description="自訂分析器 (Azure 搜尋服務 REST API 2015-02-28-Preview 版)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# 分析器 (Azure 搜尋服務 REST API 2015-02-28-Preview 版)

> [AZURE.NOTE] 自訂分析器的支援目前為預覽。您必須使用 2015-02-28-Preview 版本的 Azure 搜尋服務 REST API，以利用這項功能。請注意，預覽功能目前不會新增至 .NET SDK，因此使用預覽 REST API 是您目前的唯一程式設計選項。

## 概觀

全文檢索搜尋引擎的角色，簡單地說，是以有效率的查詢和擷取的方式處理和儲存文件。概括而言，就是擷取文件中重要的單字、將它們放在索引中，然後使用索引來尋找符合指定查詢的文字的文件。擷取文字的文件和搜尋查詢的程序稱為語彙分析。執行語彙分析的元件稱為分析器。在 Azure 搜尋服務中，您可以從一組[預先定義不限語言分析器](#Analyzers)和[語言特定分析器](https://msdn.microsoft.com/library/azure/dn879793.aspx)中選擇。您也可以定義自己的自訂分析器。自訂分析器可讓您充分控制將文字轉換成可檢索/可搜尋語彙基元的程序它是使用者定義的組態，其中包含單一預先定義 tokenizer 和一或多個權杖篩選器。tokenizer 負責分成權杖，而權杖篩選器則負責修改 tokenizer 所發出的權杖。

自訂分析器啟用的常見案例包括：

- 語音搜尋 - 新增語音篩選器，以根據文字聽起來如何，而不是它的拼法，來啟用搜尋。
- 停用語彙分析 - 使用關鍵字分析器以建立未分析的可搜尋欄位
- 快速前置詞/後置詞搜尋 - 將邊緣 N-gram 權杖篩選器新增至文字的索引前置詞以啟用快速前置詞比對。您可以將其與反向權杖篩選器結合以進行後置詞比對。
- 自訂 Token 化 - 例如，使用空白字元 tokenizer 將句子分成使用空白字元做為分隔符號的權杖
- ASCII 摺疊 - 新增 ASCII 折疊篩選器來標準化搜尋詞彙中的讀音符號，例如 ö 或 ê。

您可以定義多個自訂分析器來變更篩選器的組合，但是每個欄位只能針對索引分析使用一個分析器、針對搜尋分析使用一個分析器。
 
此頁面提供支援的分析器、tokenizers 和權杖篩選器的清單。您也可以找到具有使用範例的索引定義的變更的描述。如需簡介和案例的探索，請參閱 [Azure 搜尋服務中的自訂分析器](link)。如需基礎技術運用在 Azure 搜尋服務實作的詳細背景，請參閱[分析封裝摘要 (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html)。


## 預設分析器

預設分析器是 [Apache Lucene 標準](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)分析器。

## 驗證規則

自訂分析器、tokenizers 和權杖篩選器的名稱必須是唯一的，而且不能與任何預先定義的分析器、tokenizers 或權杖篩選器相同。
 

## 使用自訂分析器的索引定義 

您可以在建立索引時定義自訂分析器。索引定義中的自訂分析元件的語法如下所示。[這裡](#Example)提供完整的範例

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] 您所建立的自訂分析器不會顯示在 Azure 入口網站。新增自訂分析器的唯一方法是透過程式碼，它會在定義索引時呼叫 REST API。

##自訂分析器、tokenizers 和權杖篩選器的索引屬性 

此區段會指定分析器、tokenizers 和索引定義的權杖篩選區段的組態屬性。

###分析器

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		它只能包含字母、數字、空格、連字號或底線，只能以英數字元開始和結束，並且限制為 128 個字元。
      </td>
    </tr>
    <tr>
      <td>
        <b>類型</b>
      </td>
      <td>
		有效值為「#Microsoft.Azure.Search.CustomAnalyzer」或支援的分析器清單的分析器名稱。請參閱以下 [分析器](#Analyzers) 資料表中的 <b>analyzer_type</b> 資料行。
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (僅對於 #Microsoft.Azure.Search.CustomAnalyzer 有效)
      </td>
      <td>
		必要。必須是以下 [Tokenizers](#Tokenizers) 資料表中列出的其中一個預先定義 tokenizers，或是索引定義中定義的任何自訂 tokenizers。
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b> (僅對於 #Microsoft.Azure.Search.CustomAnalyzer 有效)
      </td>
      <td>
		所有權杖篩選器是 [權杖篩選器](#TokenFilters) 資料表中其中一個預先定義權杖篩選器，或是索引定義中定義的任何自訂權杖篩選器。
      </td>
	</tr>
    <tr>
      <td>
        <b>選項</b>
      </td>
      <td>
		必須是預先定義 (非自訂) 分析器的 [有效選項](#Analyzers)。
      </td>
	</tr>
  </tbody>
</table>

###Tokenizers

tokenizer 會將連續文字分割成權杖的序列，例如將句子分成多個文字。您可以指定每個自訂分析器僅一個 tokenizer。如果您需要一個以上的 tokenizer，您可以建立多個自訂分析器，並且在索引結構描述中逐欄位指派它們。自訂分析器可以使用預設或自訂選項來使用預先定義的 tokenizer。

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		它只能包含字母、數字、空格、連字號或底線，只能以英數字元開始和結束，並且限制為 128 個字元。
      </td>
    </tr>
    <tr>
      <td>
        <b>類型</b>
      </td>
      <td>
		支援 tokenizers 清單中的 Tokenizer 名稱。請參閱以下 [Tokenizers](#Tokenizers) 資料表中的 <b>tokenizer_type</b> 資料行。
      </td>
	</tr>
     <tr>
      <td>
        <b>選項</b>
      </td>
      <td>
		必須是指定 tokenizer 類型的 [有效選項](#Tokenizers)。
      </td>
	</tr>
  </tbody>
</table>

###權杖篩選器

權杖篩選器是用來篩選出或修改 tokenizer 所產生的權杖。例如，您可以指定小寫篩選器，將所有字元轉換成小寫。您可以在自訂分析器中具有多個權杖篩選器。權杖篩選器會以所列的順序執行。

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		它只能包含字母、數字、空格、連字號或底線，只能以英數字元開始和結束，並且限制為 128 個字元。
      </td>
    </tr>
    <tr>
      <td>
        <b>類型</b>
      </td>
      <td>
		支援權杖篩選器清單中的權杖篩選器名稱。請參閱以下 [權杖篩選器](#TokenFilters) 資料表中的 <b>token_filter_type</b> 資料行。
      </td>
	</tr>
     <tr>
      <td>
        <b>選項</b>
      </td>
      <td>
		必須是指定權杖篩選器類型的 [有效選項](#TokenFilters)。
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##索引定義範例

此索引定義範例包括使用自訂分析器「my\_analyzer」的一個欄位，它會輪流使用自訂的標準 tokenzier「my\_standard\_tokenizer」和兩個權杖篩選器：小寫和自訂 asciifolding 篩選器「my\_asciifolding」。

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##分析器

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>說明</td>
		<td>選項</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>將欄位的整個內容視為單一權杖。這對於如郵遞區號、識別碼和某些產品名稱的資料很有用</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>透過規則運算式模式彈性地將文字分成字詞</td>
	  <td>
		- 小寫 - 類型: bool - 字詞是否應該為小寫，預設為 true - [pattern](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - 類型：字串 - 規則運算式模式以比對權杖分隔符號，預設值：\w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 類型：字串 - 規則運算式旗標，預設值：空白字串 - 停用字詞 - 類型：字串陣列 - 停用字詞清單，預設值：空白清單
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>在非字母位置分割文字並且將它們轉換成小寫</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>具有 [snowball 詞幹篩選器] 的標準分析器 (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		語言 - 類型：字串 - 允許值：丹麥文、荷蘭文、英文、芬蘭文、法文、德文、匈牙利文、義大利文、挪威文、葡萄牙文、俄文、西班牙文、瑞典文 - 停用字詞 - 類型：字串陣列 - 停用字詞的清單，預設值：空白清單
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>標準 Lucene 分析器 - 由標準 tokenizer、小寫字母篩選器和停用篩選器組成</td>
	  <td>
		- maxTokenLength - 類型：int - 權杖長度上限，預設值：255。超過長度上限的權限會進行分割。- 停用字詞 - 類型：字串陣列 - 停用字詞清單，預設值：空白清單
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>具有 Ascii 摺疊篩選器的標準分析器</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>在非字母位置分割文字，套用小寫字母和停用字詞權杖篩選器</td>
	  <td>
		- 停用字詞 - 類型：字串陣列 - 停用字詞清單，預設值：空白清單
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>使用空白字元 tokenizer 的分析器。</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Tokenizers

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>說明</td>
		<td>選項</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>以文法為基礎的 tokenizer，適合用來處理大部分歐洲語言文件</td>
	  <td>
		- maxTokenLength - 類型：int - 權杖長度上限，預設值：255。超過長度上限的權杖會進行分割。
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>會從邊緣將輸入 Token 化成指定大小的 n-gram</td>
	  <td>
		- minGram - 類型：int - 預設值：1 - maxGram - 類型：int - 預設值：2 - tokenChars - 類型：字串陣列 - 保留在權杖的的字元類別，允許值：字母、數字、空白字元、標點符號、符號
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>將整個輸入發出為單一權杖</td>
	  <td>
		- bufferSize - 類型：int - 讀取緩衝區大小，預設值：256
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>在非字母位置分割文字</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>在非字母位置分割文字並且將它們轉換成小寫</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>將輸入 Token 化成指定大小的 n-gram</td>
	  <td>
		- minGram - 類型：int - 預設值：1 - maxGram - 類型：int - 預設值：2 - tokenChars - 類型：字串陣列 - 保留在權杖的的字元類別，允許值：字母、數字、空白字元、標點符號、符號
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>路徑類似階層的 Tokenizer</td>
	  <td>
		- 分隔符號 - 類型：字串 - 預設值：'/' - 取代 - 類型：字串 - 如果設定，會取代分隔符號字元，預設值：分隔符號 - bufferSize - 類型：int - 預設值：1024 - 反向 - 類型：bool - 如果為 true，會以反向順序產生權杖，預設值：true - 掠過- 類型：bool - 要略過的初始權杖，預設值：0
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>此 tokenizer 使用 regex 模式比對來建構不同的權杖</td>
	  <td>
		 - 模式 - 類型：字串 - 規則運算式模式，預設值：\w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 類型：字串 - 規則運算式旗標，預設值：空白字串 - 群組 - 類型：int - 哪一個群組要擷取到權杖，預設值：-1 (分割)
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>遵循 [Unicode Text Segmentation rules](http://unicode.org/reports/tr29/) 細分文字</td>
	  <td>
		 - maxTokenLength - 類型：int - 權杖長度上限，預設值為 255。超過長度上限的權杖會進行分割。
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>將 url 和電子郵件 Token 化為一個權杖</td>
	  <td>
		- maxTokenLength - 類型：int - 權杖長度上限，預設值為 255。超過長度上限的權杖會進行分割。
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>在空白字元分割文字</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##權杖篩選器

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>說明</td>
		<td>選項</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>去除所有格符號 (包括所有格符號本身) 之後的所有字元</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>去除所有格符號之後的所有字元</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>將不是前 127 個 ASCII 字元 (「基本拉丁文」Unicode 區塊) 的英文字母、數字和符號 Unicode 字元轉換為其 ASCII 對等項目 (如果有的話)</td>
	  <td>
		- preserveOriginal - 類型：bool - 如果為 true，原始權杖仍會保留，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>從 StandardTokenizer 產生的 CJK 詞彙的表單雙字母組合</td>
	  <td>
		- ignoreScripts - 類型：bool - 要略過的指令碼，允許值：han、平假名、片假名、韓文。預設值：空白清單 - outputUnigrams 類型：bool - 如果您一律想要輸出單字母和雙字母組合，則設為 true，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>將 CJK 寬度差異正規化。摺疊全形 ASCII 變種成相等的基本拉丁文以及半形片假名變種成相等的假名</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>移除英文所有格和縮寫的點</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>針對編制索引時經常發生的詞彙建構雙字母組合。仍然會對單一詞彙編製索引，使用雙字母組合重疊。</td>
	  <td>
		- commonWords - 類型：字串陣列 - 常用文字組合，預設值：空白清單 - ignoreCase - 類型：bool - 如果為 true，一般文字比對不區分大小寫，預設值：false - queryMode - 類型：bool - 產生雙字母組合，然後移除常用文字與單一詞彙，後面接著常用文字，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>分隔符號之前的字元是「權杖」，之後是裝載。例如，如果分隔符號是 '|'，則對於「hello|world」字串，「hello」是權杖，「world」為裝載。您也可以包含編碼器以適當的方式轉換裝載 (從字元到位元組)</td>
	  <td>
		- 分隔符號 - 類型：char - 預設值：'|' - 編碼 - 類型：字串 - 允許值：int、float、身分識別。預設值：float
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>分解許多日耳曼語言中找到的複合字</td>
	  <td>
		- wordList - 類型：字串陣列 - 要比對的文字清單，預設值：空白清單 - minWordSize - 類型：int - 只會處理超過此長度的文字，預設值：5 - minSubwordSize - 類型：int - 只會輸出超過這個長度的子文字，預設值：2 - maxSubwordSize - 類型：int - 只會輸出短於這個長度的子文字，預設值：15 - onlyLongestMatch - 類型：bool - 僅將最長相符子文字加入至輸出，預設值：false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>從輸入權杖的前面或後面的開頭產生指定大小的 n-gram</td>
	  <td>
		- minGram - 類型：int - 預設值：1 - maxGram - 類型：int - 預設值：2 - 側邊 - 類型：字串 - 指定應該從輸入的哪一邊產生 n-gram。允許值：前面、後面
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>移除元音省略。例如，「l'avion」(平面) 會轉換為「avion」(平面)。</td>
	  <td>
		- 文章 - 類型：字串陣列 - 要移除的一組文章，預設值：空白清單
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>根據 [German2 snowball algorithm] (http://snowball.tartarus.org/algorithms/german2/stemmer.html) 的啟發學習法將德文字元正規化</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>正規化印度文中的文字以移除拼字變體中的一些差異</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>將印度語中文字的 Unicode 表示法正規化</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>權杖篩選器，只會保留權杖與指定的文字清單中包含的文字</td>
	  <td>
		- keepWords - 類型：字串陣列 - 要保留的文字清單，預設值：空白清單 - keepWordsCase - 類型：bool - 如果為 true，會先讓所有文字成為小寫，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>保留權杖，其類型會出現在指定的允許類型清單</td>
	  <td>
		- 類型 - 類型：字串陣列 - 要保留的類型清單
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>將詞彙標示為關鍵字</td>
	  <td>
		- 關鍵字 - 類型：字串陣列 - 要標示為關鍵字的文字清單，預設值：空白清單 - ignoreCase - 類型：bool - 如果為 true，會先讓所有文字成為小寫，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>發出每個傳入權杖兩次，一次為關鍵字、一次為非關鍵字</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>英文的高效能 kstem 篩選器</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>移除太長或太短的文字</td>
	  <td>
		- 下限 - 類型：int - 數目下限，預設值：0 - 上限 - 類型：int - 數目上限，預設值：整數值上限
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>編制索引時限制權杖的數目</td>
	  <td>
		- maxTokenCount - 類型：int - 要產生的權杖數目上限，預設值：1 - consumeAllTokens - 類型：bool - 即使達到 maxTokenCount，是否必須取用輸入的所有權杖，預設值：false
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>正規化權杖文字為小寫</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>產生指定大小的 n-gram</td>
	  <td>
		- minGram - 類型：int - 預設值：1 - maxGram - 類型：int - 預設值：2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>使用 Java regex 發出多個權杖 - 一或多個模式中的每個擷取群組一個權杖</td>
	  <td>
		- 模式 - 類型：字串陣列 - 要針對每個權杖比對的模式清單 - preserveOriginal - 類型：bool - 設為 true，即使其中一種模式相符時，傳回原始權杖
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>適用於波斯文的正規化</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>建立語音比對的權杖。</td>
	  <td>
		- 編碼器 - 類型：字串 - 要使用的語音編碼器，允許值：metaphone、doublemetaphone、soundex、refinedsoundex、caverphone1、caverphone2、cologne、nysiis、koelnerphonetik、haasephonetik、beidermorse。預設值：metaphone - 取代 - 類型：bool - 如果編碼的權杖應該取代原始權杖則為 true，如果應該將它們加入為同義字，則為 false，預設值：true
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>如同每個 [Porter stemming algorithm](http://tartarus.org/~martin/PorterStemmer/) 轉換權杖串流</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>反向權杖字串</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>將可互換斯堪的納維亞字元的使用正規化</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>摺疊斯堪的納維亞字元 åÅäæÄÆ->a and öÖøØ->o。它也會判別雙母音 aa、ae、ao、oe 和 oo 的使用，僅保留第一個。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>將權杖的組合建立為單一權杖</td>
	  <td>
		- maxShingleSize - 類型：int - 預設值：2 - minShingleSize - 類型：int - 預設值：2 - outputUnigrams - 類型：bool - 如果為 true，輸出串流將會包含輸入權杖 (單字母)，以及 shingles，預設值：true - outputUnigramsIfNoShingles - 類型：bool - 如果為 true，當沒有 shingles 可供使用時，覆寫 outputUnigrams==false 的行為，預設值：false - tokenSeparator - 類型：字串 - 加入相鄰權杖以形成 shingle 時使用的字串，預設值：" " - filterToken - 類型：字串 - 針對沒有權杖的每個位置要插入的字串，預設值："_"
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Snowaball 詞幹權杖篩選器</td>
	  <td>
		- 語言 - 類型：字串 - 允許值：亞美尼亞文、巴斯克文、卡達隆尼亞文、丹麥文、荷蘭文、英文、芬蘭文、法文、德文、德文2、匈牙利文、義大利文、Kp、Lovins、挪威文、Porter、葡萄牙文、羅馬尼亞文、俄文、西班牙文、瑞典文、土耳其文
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>將索拉尼文文字的 Unicode 表示法正規化</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>stemmer</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>語言特定詞幹篩選器</td>
	  <td>
		- 語言 - 類型：字串 - 允許值：- [阿拉伯文](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [亞美尼亞文](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [巴斯克文](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [巴西文](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [保加利亞文](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [捷克文](http://portal.acm.org/citation.cfm?id=1598600) - [丹麥文](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [荷蘭文](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [英文](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [芬蘭文](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [法文](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [德文](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [印度文](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [匈牙利文](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [印尼文](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [愛爾蘭文](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [義大利文](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [索拉尼文](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [拉脫維亞文](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [挪威文](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [葡萄牙文](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [羅馬尼亞文](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [俄文](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [西班牙文](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [瑞典文](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [土耳其文](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>任何字典詞幹詞彙都會標示為關鍵字，讓它們不會以向下鏈結的詞幹分析器做為詞幹。必須放在任何詞幹篩選器之前。</td>
	  <td>
		- 規則 - 類型：字串陣列 - 下列格式的詞幹規則：「word => stem」，例如「ran => run」，預設值：空白清單
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>從權杖串流移除停用字詞</td>
	  <td>
		- 停用字詞 - 類型：字串陣列 - 停用字詞的清單，預設值：空白清單。- stopwords_list - 類型：字串 - 預先定義的停用字詞清單，允許值：_arabic_、_armenian_、_basque_、_brazilian_、_bulgarian_、_catalan_、_czech_、_danish_、_dutch_、_english_、_finnish_、_french_、_galician_、_german_、_greek_、_hindi_、_hungarian_、_indonesian_、_irish_、_italian_、_latvian_、_norwegian_、_persian_、_portuguese_、_romanian_、_russian_、_sorani_、_spanish_、_swedish_、_thai_、_turkish_，預設值：_english_ - ignoreCase - 類型：bool - 如果為 true，會先讓所有文字成為小寫，預設值：false - removeTrailing - 類型：bool - 如果為 true，如果它是停用字詞則略過最後搜尋的詞彙，預設值：true
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>在權杖串流中比對單一或多重文字同義字</td>
	  <td>
		- 同義字 - 類型：字串陣列 - 下列兩種格式其中之一的同義字清單：- incredible, unbelievable, fabulous => amazing - => 符號左側的所有詞彙將會被其右側的所有詞彙取代 - incredible, unbelievable, fabulous, amazing - 以逗號分隔的對等文字的清單。設定展開選項以變更解譯此清單的方式 - ignoreCase - 類型：bool - 大小寫摺疊輸入以進行比對，預設值：false - 展開 - 類型：bool - 如果為 true，同義字清單中所有的文字 (如果未使用 => 標記法) 會對應到另一個。下列清單：incredible, unbelievable, fabulous, amazing 相當於：incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing - 如果為 false，下列清單：incredible, unbelievable, fabulous, amazing 相當於：incredible, unbelievable, fabulous, amazing => incredible   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>修剪權杖的開頭和結尾空白字元</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>將詞彙截斷至特定長度</td>
	  <td>
		- 長度 - 類型：int - 必要選項
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>篩選出具有與前一個權杖相同文字的權杖</td>
	  <td>
		- onlyOnSamePosition - 類型：bool - 如果設定，只在相同位置移除重複項目，預設值：true
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>正規化權杖文字為大寫</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>將文字分割成子文字並且在子文字群組上執行選擇性轉換</td>
	  <td>
		- generateWordParts - 類型：bool - 產生部分的文字，例如"AzureSearch" -> "Azure" "Search"，預設值：true - generateNumberParts - 類型：bool - 產生數字子文字，預設值：true - catenateWords - 類型：bool - 讓文字部分的執行最大值鏈結起來，例如"Azure-Search" -> "AzureSearch"，預設值：false - catenateNumbers - 類型：bool - 讓數字部分的執行最大值鏈結起來，例如"1-2" -> "12"，預設值：false - catenateAll - 類型：bool - 讓所有子文字部分鏈結起來，例如 "Azure-Search-1" -> "AzureSearch1"，預設值：false - splitOnCaseChange - 類型：bool - 如果為 true，在 caseChange 分割文字，例如"AzureSearch" -> "Azure" "Search"，預設值：true - preserveOriginal - 保留原始文字並且加入至子文字清單，預設值：false - splitOnNumerics - 類型：bool - 如果為 true，在數字分割，例如 "Azure1Search" -> "Azure" "8" "Search"，預設值：true - stemEnglishPossessive - 類型：bool - 對每個子文字移除結尾 "'s"，預設值：true - protectedWords - 類型：字串陣列 - 保護不被分隔的權杖，預設值：空白清單
	  </td>	  
    </tr>
  </tbody>
</table>

**另請參閱** MSDN 上的 [Azure 搜尋服務 REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)、MSDN 上的 <br/>[建立索引 (Azure 搜尋服務 API)](http://msdn.microsoft.com/library/azure/dn798941.aspx)<br/>

<!---HONumber=AcomDC_0224_2016-->