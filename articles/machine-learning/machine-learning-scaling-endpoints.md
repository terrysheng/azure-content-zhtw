<properties 
	pageTitle="調整 API 端點 | Microsoft Azure" 
	description="在 Azure Machine Learning 中調整 Web 服務端點" 
	services="machine-learning"
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="02/25/2016"
	ms.author="himad"/>


# 調整 API 端點

您可以根據符合的端點取用率，來選擇Azure Machine Learning 中 Web 服務端點的節流層級。

若要控制端點上的節流數量，使用 Azure 傳統入口網站上的滑桿將最大同時呼叫數目設定為 20-200


通常在需要低度延遲的情況下，才會使用同步 API。這裡所說的延遲意味著 API 完成一個要求所花費的時間，而未計入任何網路延遲的時間。假設您有一個會延遲 50 毫秒的 API。其節流層級為 [高] 且 [最大同時呼叫數目] 為 20，您必須每秒呼叫這個 API 20 * 1000 / 50 = 400 次，才能完全耗盡可用的容量。再進一步延伸，假設會延遲 50 毫秒，而最大同時呼叫數目為 200 時，將可讓您每秒呼叫 API 4000 次。

如果您打算以超過最大同時呼叫數目 200 可支援的負載來呼叫 API，則應該在相同的 Web 服務上建立多個端點，並將負載隨機分配給全體。

請記住，如果您未以對應的叫用率叫用 API  
，就不適合使用非常高的並行處理計數。如果您將相對低的負載放在為高負載設定的 API，可能會看見延遲有零星的逾時及 (或) 突增情況。

請注意，調整節流設定只會影響同步 API (RRS) 的行為。如果您經常在同步 API 上看到「503 服務無法使用」的回應，才應該調整這些設定。

管理 UI提供自訂並行存取數目，可將端點調整為超出預設的 20 個並行存取數目。

1. 開啟 manage.windowsazure.com
2. 瀏覽到 [機器學習服務] 索引標籤
3. 按一下您的工作區。
4. 瀏覽到含有您端點的 Web 服務 ![瀏覽到 Web 服務](./media/machine-learning-scaling-endpoints/figure-1.png)

5. 按一下該端點，然後按一下 [設定] 索引標籤 ![瀏覽到端點組態](./media/machine-learning-scaling-webservice/machlearn-2.png)


6. 滑動滑桿以提高並行層級並按一下 [儲存]。


 

<!---HONumber=AcomDC_0302_2016-->