<properties 
	pageTitle="Azure App Service：調整 App Service 應用程式" 
	description="了解在 App Service 中調整應用程式的優缺點。" 
	keywords="App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
	services="app-service" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="byvinyal"/>
	
# Azure App Service：調整 App Service 應用程式

Azure App Service 可裝載[大規模](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)的應用程式；不過，調整應用程式是十分複雜的問題，沒有「一體適用」的解決方案。若要正確調整您的應用程式，下列 3 項要點可協助您順利進行：

1. 了解您的應用程式架構和其缺點。
	* 您的應用程式是否可設定狀態？ 沒有狀態？
	* 您的應用程式有哪些元件？
		* 應用程式的瓶頸在哪裡？ 
	* 當負載套用至您的應用程式時，會最先中斷哪個項目？
2. 了解預期的負載和效能需求。
	* 應用程式需要為一千個使用者或一百萬個使用者提供服務？
	* 流量是來自單一地區或全球？
	* 是否有季節性變化及流量尖峰？ 
	* 應用程式回應速度如何？ 1 秒？ 1 毫秒？
3. 了解及正確運用裝載您的應用程式的平台。
	* 為了達到我的規模的目標，應該利用哪些功能？
	
本節將有助您了解所有因素，並協助您衍生可利用必要的 App Service 功能的策略，以達到延展性目標。

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]

<!---HONumber=AcomDC_0128_2016-->