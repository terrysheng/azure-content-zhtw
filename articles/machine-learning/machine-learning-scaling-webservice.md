<properties
   pageTitle="調整 Web 服務 | Microsoft Azure"
   description="了解如何藉由增加並行要求和新增新端點的方式調整 Web 服務。"
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="azure 機器學習服務, web 服務, 作業化, 調整, 端點, 並行要求"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/27/2015"
   ms.author="neerajkh"/>

# 調整 Web 服務

## 增加並行要求

根據預設，系統將每個發佈的 Web 服務設定為支援 20 個並行要求。您可以透過 [Azure 管理入口網站](https://manage.windowsazure.com/) 增加至 200 個並行要求，如下圖所示。

移至 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下左邊的機器學習服務圖示，選取用於發佈 Web 服務的工作空間，按一下所需的 Web 服務，選取要增加並行要求的端點，然後按一下 [設定]。使用滑桿增加並行要求，然後按一下面板下方的 [儲存]。

若要增加並行要求，請參閱 [調整 API 端點](machine-learning-scaling-endpoints.md)。

   ![機器學習服務，調整端點。][1]

## 在相同 Web 服務新增新的端點

調整 Web 服務的規模大小是常見的工作，目的是支援 200 個以上的並行要求、透過多個端點提高可用性、或是為不同 Web 服務的取用者提供不同端點。使用者為同一 Web 服務新增額外的端點，即可增加其規模大小。使用者可以透過 [Azure 管理入口網站](https://manage.windowsazure.com/)新增額外的端點，如下圖所示：

移至 [Azure 管理入口網站](https://manage.windowsazure.com/)，按一下左邊的機器學習服務圖示，選取用於發佈 Web 服務的工作空間，按一下所需的 Web 服務，按一下面板下方的 [新增端點]，然後提供新端點的名稱、說明及所需的並行要求。

若要新增新端點，請參閱[建立端點](machine-learning-create-endpoint.md)。

   ![機器學習服務，新增端點。][2]

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png

<!---HONumber=Nov15_HO1-->