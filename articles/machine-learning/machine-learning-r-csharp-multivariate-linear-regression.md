<properties 
	pageTitle="多變量線性迴歸 | Azure" 
	description="多變量線性迴歸" 
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
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#多變量線性迴歸   
 

 
假設您有一個資料集，並想要根據其他獨立變數，快速地預測每個個別項目 (i) 的相依變數 y。 
線性迴歸是用於此類預測的普遍統計技術。這裡假設相依變數 y 是連續值。    

一個簡單的案例是研究人員嘗試根據身高 (x) 來預測某人 (y) 的體重。一個較為進階的案例是研究人員擁有個別的額外資訊 (例如體重、性別、種族)，並嘗試預測某人的體重。此 [Web 服務]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) 會將資料套入線性迴歸模型，並為資料中的每個觀察輸出預測值。

>雖然使用者可以取用此 Web 服務 (例如可能可以透過行動應用程式、網站，或甚至在本機電腦上)，但也可以將 Web 服務視為一個範例，以了解如何透過 Azure ML 建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure ML Studio 內的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將它發佈為 Web 服務。然後可以將此 Web 服務發佈至 Azure Marketplace，並在 Web 服務作者沒有設定基礎結構的情況下，由世界各地的使用者和裝置取用。  

#使用 Web 服務  
此 web 服務會根據所有觀察的獨立變數來提供相依變數的預測值。Web 服務需要使用者透過以逗號 (,) 分隔資料列並以分號 (;) 分隔資料行的字串方式輸入他們的資料。Web 服務一次需要 1 個資料列，且第一個資料行要是相依變數。範例資料集看起來可能像這樣：

![Sample data][1]

沒有相依變數的觀察應輸入 "NA" 作為 y 值。上述資料集的資料輸入字串如下："10;5;2.18;1;6.6;5.3;2.10.7;5;5.22;3;4.12;2;1,NA;3;4"。輸出會是根據獨立變數的每個資料列預估值。 

>在 Microsoft Azure Marketplace 託管的這項服務是一個 OData 服務。您可以透過 POST 或 GET 方法進行呼叫。 

有多種方式能以自動的方式取用此服務 (範例應用程式[如下](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx ))。

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
>此 web 服務是使用 Azure ML 建立的。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。


已在 Azure ML 中建立新的空白實驗，並已提取兩個 [Execute R Scripts] 到工作區。此 web 服務會使用基礎 R 指令碼來執行 Azure Machine Learning 實驗。這項實驗有 2 個部分，亦即：結構描述定義，定型模型 + 計分。  第一個模組會定義輸入資料集的預期結構，其中第一個變數是相依變數，而其餘變數是獨立變數。第二個模組會將輸入資料套入一般線性迴歸模型。  
  
![Experiment flow][3]

####模組 1：
 
####結構描述定義  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####模組 2：
####LM 模型建構   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))
	maml.mapOutputPort("out");  
 
##限制
這是一個非常簡單的多元線性迴歸 Web 服務案例。從上面的範例程式碼可以看出，未實作錯誤攔截，且由於建立此 Web 服務時，此服務僅輸入數值，因此服務會假設所有項目都是連續變數 (不允許類別功能)。此外，由於 Web 服務呼叫的要求/回應本質，以及每次呼叫 Web 服務時就會調整模型的事實，此服務目前只能處理有限的資料大小。 

##常見問題集
如需使用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[此處](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq)。

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png

<!--HONumber=46--> 
 