<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning 範例：航班誤點預測 | Azure" description="開發模型預測排定乘客航班將延遲 15 分鐘以上的範例 Azure 機器學習實驗。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning 範例：航班誤點預測

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-datasets


## 問題說明  ##

使用過去的準點率和天候資料，預測預定的客機航班是否會誤點超過 15 分鐘。 

## 資料 ##

**美國交通部的 TranStats 資料收集中的客機航班準點績效：**  [http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)  

資料集的涵蓋範圍為 2013 年的 4 月到 10 月。在上傳至 AzureML Studio 前：  

- 資料集已經過篩選，而涵蓋美國大陸 70 個最繁忙的機場。  
- 已選取下列資料行：'Year'、'Month'、'DayofMonth'、'DayOfWeek'、'Carrier'、'OriginAirportID'、'DestAirportID'、'CRSDepTime'、'DepDelay'、'DepDel15'、'CRSArrTime'、'ArrDelay'、'ArrDel15'、'Cancelled'  
- 取消的航班已標示為誤點達 15 分鐘以上。  
- 已篩選掉更改路徑的航班。  

**NOAA 提供的每小時起降天候觀測值：** http://cdo.ncdc.noaa.gov/qclcd_ascii/  

天候資料包含與每個機場相關聯的天候觀測站在 2013 年 4 月到 10 月間收集到的觀測值。在上傳至 ML Studio 前： 

- 天候觀測站識別碼已對應至相對應的機場識別碼。 
- 已篩選掉與 70 個最繁忙的機場沒有關聯的天候觀測站  
- Date 資料行已分割為個別的 Year、Month 和 Day 資料行  
- 已選取下列資料行：'AirportID'、'Year','Month','Day'、'Time'、'TimeZone'、'SkyCondition'、'Visibility'、'WeatherType'、'DryBulbFarenheit'、'DryBulbCelsius'、  'WetBulbFarenheit'、'WetBulbCelsius'、  'DewPointFarenheit'、'DewPointCelsius'、'RelativeHumidity'、'WindSpeed'、  'WindDirection'、'ValueForWindCharacter'、'StationPressure'、  'PressureTendency'、'PressureChange'、'SeaLevelPressure'、  'RecordType'、'HourlyPrecip'、'Altimeter'  

## 模型 ##

**航班資料前處理**  

首先是可能成為目標漏洞的資料行：DepDelay、DepDel15、ArrDelay、Cancelled 會從資料集中排除。  

'DayOfWeek'、'OriginAirportID' 和 'DestAirportID' 等資料行代表類別屬性。但這些都是整數值資料行，因此最初會剖析為連續數值，��必須使用中繼資料編輯器轉換成類別形式。  

我們必須以預定的起飛時間作為其中一個聯結索引鍵，以聯結航班記錄與每小時天候記錄。為此，CSRDepTime 資料行必須捨入為最接近的前一小時。  

**天候資料前處理**  

首先會排除含有高比例遺漏值的資料行。這些資料行包括  

- 所有字串值資料行  
- ValueForWindCharacter、WetBulbFarenheit、WetBulbCelsius、PressureTendency、PressureChange、SeaLevelPressure、StationPressure  

接著會對其餘資料行套用「遺漏值清除程式」。  

天候觀測時間會四捨五入至最接近的下一個整點時間，以便與預定航班起飛時間等值聯結。請注意，預定航班時間與天候觀測時間的進位方向是相反的。這是為了確保模型只會使用在過去與航班時間有關的期間收集到的天候觀測值。  

**聯結資料集**

航班記錄會與航班起點和終點的天候資料相聯結。  

請注意，報告的資料會採用當地時間，但起點與終點有可能位於不同時區。因此必須要調整時區差異，方法是從預定起飛時間和天候觀測時間中減去時區資料行。  

**準備訓練和驗證範例**  

在建立訓練和驗證範例時，會將資料分割為 4 到 9 月的記錄以供訓練之用，並將 10 月的記錄用於驗證。接著會從資料集中移除年和月資料行。 

此外，訓練資料會以等高分級進行量化，並將相同的分級套用至驗證資料。  

**模型訓練和驗證** 

根據訓練範例，訓練雙類別促進式決策樹模型。模型會使用 10 倍隨機參數掃描分析，進行理想 AUC 的最佳化。為方便比較，雙類別羅吉斯迴歸模型也會以相同的方式進行最佳化。  

接著會根據驗證集為已訓練的模型評分，並使用 [評估模型] 模組比對分析模型的品質。 

**後處理** 

機場識別碼會與一般人可讀的機場名稱和為至相聯結，使結果易於分析。 

## 結果 ##

根據驗證集，促進式決策樹模型獲得 0.697 的 AUC，略優於羅吉斯迴歸模型的 AUC 0.675。 



## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - 航班誤點預測 - 開發**

## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：**儲存的資料集**之下的模組調色板。

###機場代碼資料集
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../includes/machine-learning-sample-dataset-airport-codes.md)]

###航班誤點資料
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../includes/machine-learning-sample-dataset-flight-delays-data.md)]

###天氣資料集
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../includes/machine-learning-sample-dataset-weather-dataset.md)]

