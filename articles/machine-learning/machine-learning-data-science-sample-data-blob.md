<properties 
	pageTitle="Azure blob 儲存體中的取樣資料 | Microsoft Azure" 
	description="Azure Blob 儲存體中的取樣資料" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/07/2016" 
	ms.author="sunliangms;fashah;garye;bradsev" />

#<a name="heading"></a>Azure blob 儲存體中的取樣資料

## 簡介

本文件說明為儲存於 Azure blob 儲存體中的資料進行取樣，您可以利用程式設計方式下載它，然後使用 Python 程式碼範例來對它進行取樣。執行此動作的步驟如下：

**為何要對您的資料進行取樣？** 如果您規劃分析的資料集很龐大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。這有助於資料了解、探索和功能工程。它在 Cortana 分析程序中扮演的角色是能夠快速建立資料處理函式與機器學習服務模型的原型。

以下**功能表**會連結至說明如何從各種不同儲存體環境進行資料取樣的主題。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

此取樣工作是 [Cortana Analytics Process (CAP) (Cortana Analytics 程序 (CAP))](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一個步驟。


## 下載和降低取樣資料
1. 從下列 Python 程式碼範例中，使用 Blob 服務，從 Azure Blob 儲存體下載資料。 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. 從上述下載的檔案中將資料讀取至 Pandas 資料框架。

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. 使用 `numpy` 的 `random.choice` 以進行降低取樣資料，如下所示：

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	現在您可以使用上述具有 1% 樣本的資料框架，進行進一步探索和功能產生。

##<a name="heading"></a>將資料上傳並將其讀入 Azure Machine Learning

您可以使用下列程式碼範例，對資料進行向下取樣，並直接在 Azure ML 中使用它：

1. 將資料框架寫入本機檔案

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 使用下列程式碼範例，將本機檔案上傳至 Azure Blob：

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. 使用 Azure ML [讀取器](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)讀取 Azure Blob 的資料，如下圖所示：
 
![讀取器 Blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0211_2016-->