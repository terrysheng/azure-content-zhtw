<properties 
	pageTitle="Machine Learning Recommendations API 的快速入門指南 |Azure" 
	description="Azure Machine Learning Recommendations - 快速入門手冊" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="jaymathe"/> 

# Machine Learning Recommendations API 的快速入門指南

本文件說明如何將裝載您的服務或應用程式才能使用 Azure ML Recommendations。 

##內容

* 一般概觀	
* 限制	        
* 整合	        
  * 驗證	
  *	服務 URI	    
  *	API 版本	    
  *	建立模型	
  *	匯入目錄資料	
  *	匯入使用資料	
     * 上傳檔案	
	 * 使用資料擷取	
* 建立建議模型	
* 取得組建狀態	
* 取得建議	
* 更新模型	
* 法律	

 
##一般概觀

若要使用 Azure ML Recommendations，您需要執行下列步驟：

* 建立模型 - 模型是使用資料、目錄資料和建議模型的容器。
* 匯入目錄資料 - 這是選擇性步驟。目錄包含項目的中繼資料資訊。如果您未上傳目錄資料，則 Recommendations 服務將從使用資料隱含了解您的目錄。
* 匯入使用資料 - 上傳使用資料的方式有 2 種 (使用其中一種或兩種皆用)：
	* 上傳包含使用資料的檔案。
	* 傳送資料擷取事件。
	通常您會上傳使用檔案以便建立初始建議模型 (啟動程序)，並在系統使用資料擷取格式蒐集足夠的資料之前使用它。
* 建立建議模型 - 這是非同步作業，建議系統會接受所有使用資料並建立建議模型。視資料大小和組建組態參數而定，這項作業可能需要數分鐘或數小時的時間。在觸發您將取得組建 ID 的組建時，請在開始取用建議之前，使用它來檢查建置程序何時結束。 
* 建議取用 - 取得特定項目或項目清單的建議。

上述所有步驟都是透過 Azure ML Recommendations API 完成

##限制

* 每一訂閱的模型數上限： 10
* 一個目錄可保留的項目數上限： 100,000
* 保留的使用點數上限是 ~5000000。如果將上傳或報告新的項目，則會刪除最舊的項目。
* 可以利用 POST 傳送 (例如：匯入目錄資料、匯入使用資料) 的資料大小上限為 200 MB
* 不在作用中的建議模型組建的每秒交易數是 ~2TPS；只有作用中的建議模型組建可以保留高達 20TPS

##整合

###驗證
請遵循與驗證相關的 Microsoft Azure Marketplace 指導方針。Marketplace 可支援基本或 OAuth 驗證方法。

###服務 URI 
每個 Azure ML Recommendations API 的服務根 URI [在此。](https://api.datamarket.azure.com/amla/recommendations/v1/)

完整服務 URI 是使用 OData 規格的元素來表示。  

###API 版本
每個 API 呼叫最後會有名為 apiVersion 的查詢參數 (應設為 1.0)

###建立模型
建立「建立模型」的要求：

![Table1][1]

回應：

HTTP 狀態碼： 200

OData XML
	
	feed/entry/content/properties/id - contains the model id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
	  <subtitle type="text">Create A New Model</subtitle>
	  <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'</id>
	  <rights type="text" />	
	  <updated>2014-10-05T06:35:21Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'" />
	  <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/CreateModel?modelName='MyFirstModel'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">a658c626-2baa-43a7-ac98-f6ee26120a12</d:Id>
        <d:Name m:type="Edm.String">MyFirstModel</d:Name>
        <d:Date m:type="Edm.String">10/5/2014 6:35:19 AM</d:Date>
        <d:Status m:type="Edm.String">Created</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:UserName>
        <d:Description m:type="Edm.String"></d:Description>
      </m:properties>
    </content>
	  </entry>
	</feed>


###匯入目錄資料

如果您將數個目錄檔案上傳至具有多個呼叫的相同模型，則我們只會插入新的目錄項目。現有的項目會保留原始值。

![Table2][2]

回應：

HTTP 狀態碼： 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	  <title type="text" />
  		<subtitle type="text">Import catalog file</subtitle>
  		<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T06:58:04Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">ImportCatalogFileEntity2</title>
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='catalog10_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">4</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
      </m:properties>
    </content>
 	 </entry>
	</feed>


###匯入使用資料

####上傳檔案
本節示範如何使用檔案上傳使用資料。您可以利用使用資料呼叫此 API 數次。所有使用資料都會針對所有呼叫進行儲存。

![Table3a][3]
![Table3b][4]

回應：

HTTP 狀態碼： 200

OData XML	

	Feed\entry\content\properties\LineCount - number of lines accepted
	Feed\entry\content\properties\ErrorCount - number of lines that were not inserted due to an error
	Feed\entry\content\properties\FileId - the file identifier


	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Add bulk usage data (usage file)</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T07:21:44Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&filename='ImplicitMatrix10_Guid_small.txt'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
  	</entry>
	</feed>


####使用資料擷取
本節示範如何將事件即時傳送到 Azure ML Recommendations (通常是從您的網站)。

![Table4][5]

Request body

	您要傳送的每個事件的事件資料項目。您應該為相同的使用者或瀏覽器工作階段在 SessionId 欄位中傳送相同的 ID。


	Example of Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event> 


	Example of Recommendation Click:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RecommendationClick</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>


	Example of Adding to shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Removing from shop cart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>RemoveShopCart</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
    </EventData>
  	</EventData>
	</Event>

	Example of Purchase:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">   
	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>   
	<SessionId>11112222</SessionId>   
	<EventData>     
	<EventData>       
		<Name>Purchase</Name> 
		<PurchaseItems>
			<PurchaseItems>
				<ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>       
				<Count>3</Count>
			</PurchaseItems>
		</PurchaseItems>
	</EventData>     
	</EventData> 
	</Event>

	Example of sending 2 events "Click" and "AddShopCart:
	<Event xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  	<ModelId>2779c063-48fb-46c1-bae3-74acddc8c1d1</ModelId>
  	<SessionId>11112222</SessionId>
  	<EventData>
    <EventData>
      <Name>Click</Name>
      <ItemId>21BF8088-B6C0-4509-870C-E1C7AC78304A</ItemId>
      <ItemName>itemName</ItemName>
      <ItemDescription>item description</ItemDescription>
      <ItemCategory>category</ItemCategory>
    </EventData>
    <EventData>
      <Name>AddShopCart</Name>
      <ItemId>552A1940-21E4-4399-82BB-594B46D7ED54</ItemId>
    </EventData>
  	</EventData>
	</Event>

回應：
HTTP 狀態碼： 200

###建立建議模型

![Table5][6]

回應：

HTTP 狀態碼： 200

	OData XML	
	This is an asynchronous API. You will get a Build Id as a response. To know when the build has ended, you should call the "Get Builds Status of a Model" API and locate this build Id in the response. Note that a build can take from minutes to hours depending on the size of the data.
	You cannot consume recommendations till the build ends.

	Valid build status:

	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Id - contains the build id

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Build a Model with RequestBody</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T08:56:34Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First build'&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&userDescription='First%20build'&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">1000272</d:Id>
        <d:UserName m:type="Edm.String"></d:UserName>
        <d:ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:ModelId>
        <d:ModelName m:type="Edm.String">docTest</d:ModelName>
        <d:Type m:type="Edm.String">Recommendation</d:Type>
        <d:CreationTime m:type="Edm.String">2014-10-05T08:56:31.893</d:CreationTime>
        <d:Progress_BuildId m:type="Edm.String">1000272</d:Progress_BuildId>
        <d:Progress_ModelId m:type="Edm.String">9559872f-7a53-4076-a3c7-19d9385c1265</d:Progress_ModelId>
        <d:Progress_UserName m:type="Edm.String">5-4058-ab36-1fe254f05102@dm.com</d:Progress_UserName>
        <d:Progress_IsExecutionStarted m:type="Edm.String">false</d:Progress_IsExecutionStarted>
        <d:Progress_IsExecutionEnded m:type="Edm.String">false</d:Progress_IsExecutionEnded>
        <d:Progress_Percent m:type="Edm.String">0</d:Progress_Percent>
        <d:Progress_StartTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_StartTime>
        <d:Progress_EndTime m:type="Edm.String">0001-01-01T00:00:00</d:Progress_EndTime>
        <d:Progress_UpdateDateUTC m:type="Edm.String"></d:Progress_UpdateDateUTC>
        <d:Status m:type="Edm.String">Queued</d:Status>
        <d:Key1 m:type="Edm.String">UseFeaturesInModel</d:Key1>
        <d:Value1 m:type="Edm.String">False</d:Value1>
      </m:properties>
    </content>
  	</entry>
	</feed>

###取得模型的組建狀態
![Table6][7]

回應：

HTTP 狀態碼： 200

	OData XML	Valid build status:
	* Create - model was created
	* Queued - model build was triggered and it is queued
	* Building - the model is being build
	* Success - the build ended successfully
	* Error - the build ended with a failure
	* Cancelled - build was canceled
	* Cancelling - build is being cancelled

	Feed\entry\content\properties\Status -contains the build status

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
	<title type="text" />
	<subtitle type="text">Get builds status of a model</subtitle>
	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'</id>
	<rights type="text" />
	<updated>2014-11-05T17:51:10Z</updated>
	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'" />
	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetBuildsStatusEntity</title>
    <updated>2014-11-05T17:51:10Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&onlyLastBuild=False&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:UserName m:type="Edm.String">b-434e-b2c9-84935664ff20@dm.com</d:UserName>
        <d:ModelName m:type="Edm.String">ModelName</d:ModelName>
        <d:ModelId m:type="Edm.String">1d20c34f-dca1-4eac-8e5d-f299e4e4ad66</d:ModelId>
        <d:IsDeployed m:type="Edm.String">true</d:IsDeployed>
        <d:BuildId m:type="Edm.String">1000272</d:BuildId>
        <d:Status m:type="Edm.String">Success</d:Status>
        <d:StatusMessage m:type="Edm.String"></d:StatusMessage>
        <d:Progress m:type="Edm.String">0</d:Progress>
        <d:StartTime m:type="Edm.String">2014-11-02T13:43:51</d:StartTime>
        <d:EndTime m:type="Edm.String">2014-11-02T13:45:10</d:EndTime>
        <d:ExecutionTime m:type="Edm.String">00:01:19</d:ExecutionTime>
        <d:IsExecutionStarted m:type="Edm.String">false</d:IsExecutionStarted>
        <d:ProgressStep m:type="Edm.String"></d:ProgressStep>
      </m:properties>
     </content>
    </entry>
    </feed>


###取得建議
![Table7][8]

HTTP 狀態碼： 200

	OData XML	The response includes one entry per recommended item, each entry has the following data:
	* Feed\entry\content\properties\Id - The recommended item id
	* Feed\entry\content\properties\Name - The name of the item 
	* Feed\entry\content\properties\Rating - The rating of the recommendation, higher number means higher confidence
	* Feed\entry\content\properties\Reasoning - the recommendation reasoning
	The example response below includes 10 recommended items:

	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
 	 <subtitle type="text">Get Recommendation</subtitle>
 	 <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'</id>
  	<rights type="text" />
  	<updated>2014-10-05T12:28:48Z</updated>
  	<link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'" />
  	<entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=0&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">159</d:Id>
        <d:Name m:type="Edm.String">159</d:Name>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '159'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=1&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">52</d:Id>
        <d:Name m:type="Edm.String">52</d:Name>
        <d:Rating m:type="Edm.Double">0.539588900748721</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '52'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=2&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">35</d:Id>
        <d:Name m:type="Edm.String">35</d:Name>
        <d:Rating m:type="Edm.Double">0.53842946443853</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '35'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=3&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">124</d:Id>
        <d:Name m:type="Edm.String">124</d:Name>
        <d:Rating m:type="Edm.Double">0.536712832792886</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '124'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=4&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">120</d:Id>
        <d:Name m:type="Edm.String">120</d:Name>
        <d:Rating m:type="Edm.Double">0.533673023762878</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '120'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=5&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">96</d:Id>
        <d:Name m:type="Edm.String">96</d:Name>
        <d:Rating m:type="Edm.Double">0.532544826370521</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '96'</d:Reasoning>
      </m:properties>
    </content>
  	</entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=6&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">69</d:Id>
        <d:Name m:type="Edm.String">69</d:Name>
        <d:Rating m:type="Edm.Double">0.531678607847759</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '69'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=7&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">172</d:Id>
        <d:Name m:type="Edm.String">172</d:Name>
        <d:Rating m:type="Edm.Double">0.530957821375951</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '172'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=8&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">155</d:Id>
        <d:Name m:type="Edm.String">155</d:Name>
        <d:Rating m:type="Edm.Double">0.529093541481333</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '155'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
 	 <entry>
    <id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations-preview/v1/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&itemIds='1003'&numberOfResults=10&includeMetadata=false&apiVersion='1.0'&$skip=9&$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">32</d:Id>
        <d:Name m:type="Edm.String">32</d:Name>
        <d:Rating m:type="Edm.Double">0.528917978168322</d:Rating>
        <d:Reasoning m:type="Edm.String">People who like '1003' also like '32'</d:Reasoning>
      </m:properties>
    </content>
 	 </entry>
	</feed>

###更新模型
您可以更新模型描述或使用中組建 ID。
使用中組建 Id - 每個模型的每個組建都有「組建 Id」。使用中「組建 Id」是每個新模型的第一個成功組建。一旦您有使用中的組建 Id，而且您進行相同模型的其他建置，您可以視需要將它明確地設定為預設組建 Id。當您取用建議時，如果您未指定想要使用的組建 Id，將會自動使用預設值。

此機制可讓您在生產環境中有建議模型後建立新模型，並先加以測試，再提升至生產環境。

![Table8][9]

回應：

HTTP 狀態碼： 200

	OData XML
	<feed xmlns:base="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
  	<title type="text" />
  	<subtitle type="text">Update an Existing Model</subtitle>
  	<id>https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'</id>
  	<rights type="text" />
 	 <updated>2014-10-05T10:27:17Z</updated>
 	 <link rel="self" href="https://api.datamarket.azure.com/Data.ashx/amla/recommendations/v1/UpdateModel?id='9559872f-7a53-4076-a3c7-19d9385c1265'&apiVersion='1.0'" />
	</feed>

##法律
這份文件係依 「現狀」提供。本文件中說明的資訊與畫面 (包括 URL 及其他網際網路網站參考資料) 如有變更， 恕不另行通知。 
此處描述的一些範例僅供說明之用，純屬虛構。並未影射或關聯任何真實人事物。 
本文件未提供給您任何 Microsoft 產品中任何智慧財產的任何法定權利。您可以複製並使用這份文件，供內部參考之用。 
(c) 2014 Microsoft.著作權所有，並保留一切權利。 


[1]: ./media/machine-learning-recommendation-api-quick-start-guide/Table01.png
[2]: ./media/machine-learning-recommendation-api-quick-start-guide/Table02.png
[3]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03a.png
[4]: ./media/machine-learning-recommendation-api-quick-start-guide/Table03b.png
[5]: ./media/machine-learning-recommendation-api-quick-start-guide/Table04.png
[6]: ./media/machine-learning-recommendation-api-quick-start-guide/Table05.png
[7]: ./media/machine-learning-recommendation-api-quick-start-guide/Table06.png
[8]: ./media/machine-learning-recommendation-api-quick-start-guide/Table07.png
[9]: ./media/machine-learning-recommendation-api-quick-start-guide/Table08.png


<!--HONumber=46--> 
 