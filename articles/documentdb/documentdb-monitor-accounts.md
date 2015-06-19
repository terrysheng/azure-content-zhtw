<properties 
	pageTitle="監視 DocumentDB 帳戶 | Azure" 
	description="了解如何監視 DocumentDB 帳戶的效能度量 (如要求和伺服器錯誤) 和使用方式度量 (如儲存體耗用量)。" 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="mimig"/>

#監視 DocumentDB 帳戶  

您可以在 [Azure 預覽入口網站](https://portal.azure.com/)中監視 DocumentDB 帳戶。每一個 DocumentDB 帳戶都有效能度量 (例如要求和伺服器錯誤) 和使用量度量 (例如儲存體用量) 可供使用。

## <a id="metrics"></a>  作法：檢視 DocumentDB 帳戶的效能度量
1.	在 [Azure 預覽入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]、[**DocumentDB 帳戶**]，然後按一下您想要檢視效能度量的 DocumentDB 帳戶名稱。
2.	在 [**監視**] 透鏡內，依預設可以看到：
	*	當日的要求總數。
	*	當日的每秒平均要求數 
	
	![](./media/documentdb-monitor-accounts/madocdb1.png)


3.	按一下 [**要求總數**] 或 [**每秒平均要求數**] 組件會開啟詳細的 [**度量**] 分頁。
4.	[**度量**] 分頁會顯示您已選取之度量的詳細資料。分頁上方是圖形，圖形下方有一個資料表顯示所選度量的彙總值，例如平均值、最小值和最大值。度量分頁也會顯示已定義的警示清單，且依目前度量分頁上出現的度量來篩選 (因此，如果您有許多警示，只會看到此處顯示相關的警示)。   

	![](./media/documentdb-monitor-accounts/madocdb2.png)


## <a id="custom"></a>自訂 DocumentDB 帳戶的效能度量檢視

1.	若要自訂特定組件中顯示的度量，請以滑鼠右鍵按一下度量組件，然後選取 [**編輯圖表**]。  
	![](./media/documentdb-monitor-accounts/madocdb3.png)

2.	在 [**編輯圖表**] 分頁上，有選項可修改組件中顯示的度量及其時間範圍。  
	![](./media/documentdb-monitor-accounts/madocdb4.png)

3.	若要變更組件中顯示的度量，只需選取或清除可用的效能度量，然後在分頁底部按一下 [**儲存**]。  
4.	若要變更時間範圍，請選擇不同的範圍 (例如，[**前一個小時**])，然後在分頁底部按一下 [**儲存**]。  

	![](./media/documentdb-monitor-accounts/madocdb5.png) 


## <a id="create"></a>建立並排效能度量圖表
Azure Preview 入口網站可讓您建立並排度量圖表。  

1.	首先，在您要複製並修改的圖表上按一下滑鼠右鍵，然後選取 [**自訂**]。 

	![](./media/documentdb-monitor-accounts/madocdb6.png)

2.	在功能表上按一下 [**複製**] 以複製組件，然後按一下 [**完成自訂**]。 

	![](./media/documentdb-monitor-accounts/madocdb7.png)  


現在，您可以將此組件視為其他任何度量組件，並自訂該組件中顯示的度量和時間範圍。如此一來，您可以同時看到兩個不同的度量圖表並排出現。  
	![](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a id="view"></a>檢視 DocumentDB 帳戶的使用量度量
1.	在 [Azure 預覽入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]，再按一下 [**DocumentDB 帳戶**]，然後按一下您想要查看使用量度量的 DocumentDB 帳戶名稱。
2.	在 [**使用量**] 透鏡內，依預設可以檢視下列：
	*	估計的成本。
	*	帳戶內耗用的儲存體。
	*	帳戶可用的儲存體上限。
	*	使用者和權限使用量。
	*	容量單位 (CU) 配置。
	*	附件使用量。

	![](./media/documentdb-monitor-accounts/madocdb9.png)
 
## <a id="setup"></a>設定 DocumentDB 帳戶的效能度量警示
1.	在 [Azure 預覽入口網站](https://portal.azure.com/)中，按一下 [**瀏覽**]，再按一下 [**DocumentDB 帳戶**]，然後按一下您想要設定效能度量警示的 DocumentDB 帳戶名稱。
2.	在 [**作業**] 透鏡內，按一下 [**警示規則**] 組件。  
	![](./media/documentdb-monitor-accounts/madocdb10.png)

3.	在 [警示規則] 分頁中，按一下 [**新增警示**]。  
	![](./media/documentdb-monitor-accounts/madocdb11.png)

4.	在 [**新增警示規則**] 分頁中，指定：
	*	您設定之警示規則的名稱。
	*	新警示規則的描述。
	*	警示規則的度量。
	*	決定警示何時啟動的條件、臨界值和期間。例如，過去 15 分鐘伺服器錯誤計算大於 5。
	*	警示引發時是否傳送電子郵件給服務管理員和共同管理員。
	*	警示通知的其他電子郵件地址。  
	![](./media/documentdb-monitor-accounts/madocdb12.png)

 
## <a id="next"></a>後續步驟
若要深入了解 DocumentDB 容量和效能，請參閱[管理 DocumentDB 容量和效能](documentdb-manage.md)。 

<!--Anchors-->
[如何檢視 DocumentDB 帳戶的效能度量]: #How-to-view-performance-metrics-for-a-DocumentDB-account
[自訂 DocumentDB 帳戶的效能度量檢視]: #Customize-performance-metric-views-for-a-DocumentDB-account
[如何建立並排效能度量圖表]: #How-to-create-side-by-side-performance-metric-charts
[如何檢視 DocumentDB 帳戶的使用量度量]: #How-to-view-usage-metrics-for-a-DocumentDB-account
[如何設定 DocumentDB 帳戶的效能度量警示]: #How-to-setup-performance-metric-alerts-for-a-DocumentDB-account
[後續步驟]: #Next-steps

<!--HONumber=49--> 