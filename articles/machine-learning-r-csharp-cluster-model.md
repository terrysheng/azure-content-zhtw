<properties title="Cluster Model" pageTitle="叢集模型 | Azure" description="叢集模型" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#叢集模型    



我們可以如何預測多組的信用卡持卡人行為，以降低信用卡發卡機構的壞帳風險？我們可以如何定義多組員工的人格特質，以提高員工工作效能？醫生可以如何根據疾病特性來將病人分類？基本上，叢集分析可以回答所有這些問題。    
   
實際上，叢集分析會根據變數的組合，將一組觀察分成兩個或多個互斥的未知群組。叢集分析的目的是要找出將觀察 (通常是人員或其特性) 分組的系統，其中群組成員會共用共同的屬性。此 [服務](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) 會使用 K-Means 方法 (常用的叢集技術) 來群集任意資料。此 web 服務會採用資料和作為輸入的叢集數目 k，並產生有關每個觀察屬於哪個 k 群組的預測。 

>雖然使用者可以取用此 Web 服務 (例如可能可以透過行動應用程式、網站，或甚至在本機電腦上)，但也可以將 Web 服務視為一個範例，以了解如何透過 Azure ML 建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure ML Studio 內的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將它發佈為 Web 服務。然後可以將此 Web 服務發佈至 Azure Marketplace，並在 Web 服務作者沒有設定基礎結構的情況下，由世界各地的使用者和裝置取用。  

#使用 Web 服務   
此 Web 服務會將資料分成一組 k 群組，並輸出每個資料列的群組指派。Web 服務需要使用者透過以逗號 (,) 分隔資料列並以分號 (;) 分隔資料行的字串方式輸入他們的資料。Web 服務一次需要 1 個資料列。範例資料集看起來可能像這樣：

![Sample data][1]

假設使用者想要將資料分成三個互斥的群組。上述資料集的輸入資料應如下所示：值 = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3"。輸出會是每個資料列的預測群組成員資格。

>在 Microsoft Azure Marketplace 託管的這項服務是一個 OData 服務。您可以透過 POST 或 GET 方法進行呼叫。 

有多種方式能以自動的方式取用此服務 (範例應用程式[如下](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx ))。

###啟動 Web 服務使用的 C# 程式碼：

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}


#建立 Web 服務  
>此 web 服務是使用 Azure ML 建立的。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

已在 Azure ML 中建立新的空白實驗，並已提取兩個 [Execute R Scripts] 到工作區。資料結構描述是採用簡單的 [Execute R Script] 所建立的，接著，資料結構描述會連結到同樣也是採用 [Execute R Script] 建立的叢集模型區段。在用於叢集模型的 [Execute R Script] 中，Web 服務會接著使用 "k-means" 函數 (會預先建置到 Azure ML 的 [Execute R Script]。    
   

     
![Experiment flow][3]

####模組 1： 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	maml.mapOutputPort("mydata");     
	

####模組 2：
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#限制
這是一個非常簡單的群集 Web 服務範例。從上面的範例程式碼可以看出，未實作錯誤攔截，且由於建立此 Web 服務時，此服務僅輸入數值，因此服務會假設所有項目都是連續變數 (不允許類別功能)。此外，由於 Web 服務呼叫的要求/回應本質，以及每次呼叫 Web 服務時就會調整模型的事實，此服務目前只能處理有限的資料大小。 

##常見問題集
如需使用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[此處](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-marketplace-faq)。

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png
