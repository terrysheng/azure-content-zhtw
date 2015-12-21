<properties 
   pageTitle="在 Azure App Service 的 Logic Apps 中追蹤 B2B 訊息 | Microsoft Azure" 
   description="本主題說明 B2B 處理的追蹤" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="12/07/2015"
   ms.author="rajram"/>


# 追蹤 B2B 訊息

## B2B 追蹤資訊
B2B 通訊牽涉到交易夥伴之間的訊息處理。關聯性定義為兩個交易夥伴之間的協議。建立通訊後，必須以適當方式監視通訊是否如預期般執行。

我們已經實作了下列 B2B 案例的訊息追蹤：AS2、EDIFACT 和 X12。

## AS2
在您建立 AS2 API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後選取 [追蹤]。在此處，您可以檢視和篩選所有 AS2 追蹤資訊：

![][1]

## EDIFACT
在您建立 EDIFACT API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後選取 [追蹤]。在此處，您可以檢視和篩選所有 EDIFACT 追蹤資訊。此外，您可以檢視交換層級、群組層級和交易集層級資料，全在單一檢視畫面中。

如果批次在相關聯的交易夥伴管理 API 應用程式中建立為 EDIFACT 協議的一部分，則 [批次] 區段會將這些批次全部列出。您可以選取批次來查看作用中的訊息 (如果有的話)，以及已完成的資訊：

![][2]

## X12
在您建立 X12 API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後選取 [追蹤]。在此處，您可以檢視和篩選所有 X12 追蹤資訊。此外，您可以檢視交換層級、群組層級和交易集層級資料，全在單一檢視畫面中。

如果批次在相關聯的交易夥伴管理 API 應用程式中建立為 X12 協議的一部分，則 [批次] 區段會將這些批次全部列出。您可以選取批次來查看作用中的訊息 (如果有的話)，以及已完成批次的資訊。

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!-------HONumber=AcomDC_1210_2015--->