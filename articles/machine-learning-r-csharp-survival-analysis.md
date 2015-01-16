<properties title="Survival Analysis" pageTitle="存活分析 |Azure" description="存活分析" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#存活分析 





在許多情況下，主要評估結果會是感興趣事件的時間。換句話說，會詢問「何時會發生此事件」這個問題。舉例來說，請考慮資料描述在感興趣的事件 (疾病復發、取得博士學位、煞車來令片故障) 發生之前經過的時間 (天、年、里程數等) 的情況下。資料中的每個執行個體代表特定物件 (病患、人員、汽車等)。

此 [Web 服務]( https://datamarket.azure.com/dataset/aml_labs/survivalanalysis)回答了「物件 x 在時間 n 前發生感興趣事件的機率為何？」這個問題。藉由提供存活分析模型，此 Web 服務可讓使用者提供資料來訓練模型並加以測試。此實驗的主旨是要在感興趣的事件發生之前建立經過時間長度的模型。 

>雖然使用者可以取用此 Web 服務 (例如可能可以透過行動應用程式、網站，或甚至在本機電腦上)，但也可以將 Web 服務視為一個範例，以了解如何透過 Azure ML 建立採用 R 程式碼的 Web 服務。只需幾行 R 程式碼並在 Azure ML Studio 內的按鈕上按幾下，就可以建立採用 R 程式碼的實驗，並將它發佈為 Web 服務。然後可以將此 Web 服務發佈至 Azure Marketplace，並在 Web 服務作者沒有設定基礎結構的情況下，由世界各地的使用者和裝置取用。  

##使用 Web 服務

下表中顯示 Web 服務的輸入資料結構描述。必須輸入六個部分的資訊：訓練資料、測試資料、感興趣的時間、「時間」維度的索引、「事件」維度的索引和變數類型 (連續或因素)。訓練資料是以字串表示，其中的資料列是以逗號分隔，而資料行是以分號分隔。資料的功能數目具有彈性。輸入字串中的所有元素都必須為數值。在訓練資料中，「時間」維度表示自研究的起點 (病患收到藥物治療方案、學生開始博士研究、汽車開始發動等) 直到發生感興趣的事件 (病患再度吸食毒品、學生取得博士學位、汽車的煞車來令片故障等) 為止經過的時間單位數 (日、年、里程數等)。「事件」維度指出感興趣的事件是否發生在研究結束時。「事件 = 1」表示感興趣的事件發生於「時間」維度所指出的時間；而「事件 = 0」表示在「時間」維度所指定的時間前尚未發生感興趣的事件。

- trainingdata：字元字串。資料列已以逗號分隔，而資料行是以分號分隔。每個資料列包含「時間」維度、「事件」維度和預測變數。
- testingdata：包含特定物件之預測變數的一列資料。
- time_of_interest：興趣 n 的經過時間
- index_time：「時間」維度的資料行索引 (從 1 開始)
- index_event：「事件」維度的資料行索引 (從 1 開始)
- variable_types：以分號做為分隔符號的字元字串。0 表示連續變數，而 1 表示因子變數。


輸出是在特定時間前發生事件的機率。 

>在 Microsoft Azure Marketplace 託管的這項服務是一個 OData 服務。您可以透過 POST 或 GET 方法進行呼叫。 

以自動化方式取用服務的方法有很多種 (範例應用程式[在此](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx))。 

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


這項測試的解譯如下。假設資料的目標是要建立在收到兩種治療方案之一的病患再次吸食毒品的經過時間模型。Web 服務的輸出如下所示：病患目前 35 歲，先前藥物治療 2 次，採用長期居家治療方案，吸食海洛因和古柯鹼，在第 500 天前再度吸食毒品的機率是 95.64%。

##建立 Web 服務

>此 Web 服務是使用 Azure ML 建立的。如需免費試用版，以及有關建立實驗和[發佈 Web 服務](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/)的簡介影片，請參閱 [azure.com/ml](http://azure.com/ml)。以下是建立 Web 服務之實驗的螢幕擷取畫面，以及實驗內每個模組的範例程式碼。

在 AzureML 中，會建立新的空白實驗，並有兩個「執行 R 指令碼」提取到工作區。資料結構描述是以簡單「執行 R 指令碼」建立的，其定義 Web 服務的輸入資料結構描述。此模組會接著連結至執行主要工作的第二個「執行 R 指令碼」模組。此模組會進行資料前處理、模型建立和預測。在資料前處理步驟中，長字串所表示的輸入資料會轉換成資料框架。在模型建立步驟中，首次安裝外部 R 套件 "survival_2.37-7.zip" 以進行存活分析。然後會在序列資料處理工作之後執行 "coxph" 函數。在 R 文件中可以讀取存活分析的 "coxph" 函數詳細資料。在預測步驟中，會利用 "surfit" 函數在定型模型中提供測試執行個體，而且此測試執行個體的存活曲線會產生成為 "curve" 變數。最後會取得感興趣時間的機率。 

###實驗流程：

![experiment flow][1]

####模組 1：

    #data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1"
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)
    maml.mapOutputPort("sampleInput"); #send data to output port
	
####模組 2：

    #read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")
    # preprocessing trainingdata
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)
    # preprocessing testingdata
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))
    # preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types
    # necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)
    # prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # based on user input, find the event occurance probablity
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }
    #pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##限制

此 Web 服務只能以數值做為功能變數 (資料行)。「事件」資料行只能接受 0 或 1 的值。「時間」資料行必須是正整數。

##常見問題集
如需使用 Web 服務或發佈至 Marketplace 的常見問題集，請參閱[此處](http://azure.microsoft.com/zh-tw/documentation/articles/machine-learning-marketplace-faq)。

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png
