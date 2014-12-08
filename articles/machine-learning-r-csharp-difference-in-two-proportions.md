<properties title="Difference in Proportions Test" pageTitle="比例差異測試 | Azure" description="Difference in Proportions Test" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#比例差異測試




兩個比例有統計上的差異嗎？假設使用者想要比較兩部電影，以判斷其中一部電影與另一部電影比較起來是否有明顯較高比例的「讚」。在大型範例中，在 0.50 和 0.51 比例之間可能會有統計上的顯著差異，但在小型範例中，可能沒有足夠的資料可以判斷這些比例是否真有差異。 

此 [Web 服務]( https://datamarket.azure.com/dataset/aml_labs/prop_test) 會根據使用者針對 2 個比較群組輸入的的成功次數和總試驗次數，來進行兩個比例的差異假設測試。一個案例是從電影比較應用程式內部呼叫這個 Web 服務，根據電影分級讓使用者知道其中一部電影是否比另一部電影受到更多人的喜愛。

>雖然使用者可以取用此 Web 服務 (例如可能可以透過行動應用程式、網站，或甚至在本機電腦上)，但也可以將 Web 服務視為一個範例，以了解如何透過 Azure ML 建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure ML Studio 內的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將它發佈為 Web 服務。然後可以將此 Web 服務發佈至 Azure Marketplace，並在 Web 服務作者沒有設定基礎結構的情況下，由世界各地的使用者和裝置取用。


##使用 Web 服務

這項服務可接受 4 個引數，並執行比例的假設測試。

輸入引數包括：

* Successes1：範例 1 中的成功事件數目
* Successes2：範例 2 中的成功事件數目
* Total1：範例 1 的大小
* Total2：範例 2 的大小

服務的輸出是假設測試的結果，並加上 chi-square 統計資料、df、p-value，範例-1/2 中的比例及信心間隔界限。

>在 Microsoft Azure Marketplace 託管的這項服務是一個 OData 服務。您可以透過 POST 或 GET 方法進行呼叫。 

有多種方式能以自動的方式取用此服務 (範例應用程式[如下](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx ))。

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

##建立 Web 服務

>此 web 服務是使用 Azure ML 建立的。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

已在 Azure ML 中建立包含兩個 [Execute R Scripts] 的新空白實驗。在第一個模組中會定義資料結構描述，而第二個模組則會使用 R 內的 prop.test 命令來執行 2 個比例的假設測試。 


###實驗流程：

![Experiment flow][2]


####模組 1：
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####模組 2：

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##限制 

這是一個非常簡單的測試範例，可測試 2 個比例中的差異。從上面的範例程式碼可以看出，未實作錯誤攔截，且服務會假設所有變數都是連續的。

##常見問題集
如需使用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[此處](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-marketplace-faq)。

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png
