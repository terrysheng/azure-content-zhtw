<properties
	pageTitle="雲端服務配置失敗的疑難排解 | Microsoft Azure"
	description="疑難排解在 Azure 中部署雲端服務時發生的配置失敗"
	services="azure-service-management, cloud-services"
	documentationCenter=""
	authors="kenazk"
	manager="drewm"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="cloud-services"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="kenazk"/>



# 疑難排解在 Azure 中部署雲端服務時發生的配置失敗

## 摘要
當您部署執行個體至雲端服務或加入新的 Web 或背景工作角色執行個體時，Microsoft Azure 會配置計算資源。執行這些作業時，即使尚未達到 Azure 訂用帳戶限制，也可能偶爾發生錯誤。本文說明一些常見配置失敗的原因，並建議可能的補救方法。規劃服務的部署時，本資訊可能也很有用。

如果您對於本文章內容有任何需要進一步協助之處，您可以連絡 [MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專加。或者，您也可以提出 Azure 支援事件。請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [取得支援]。

### 背景 – 配置的運作方式
Azure 資料中心的伺服器分割成叢集。在多個叢集中嘗試新的雲端服務配置要求。當第一個執行個體部署至雲端服務 (在預備或生產環境) 後，該雲端服務會釘選至某個叢集。雲端服務的任何進一步的部署會發生在相同的叢集中。在本文中，這種情況稱為「釘選到叢集」。下圖 1 說明在多個叢集中嘗試一般配置的情況。圖 2 說明釘選到叢集 2 來配置的情況，因為現有的雲端服務 CS\_1 裝載於此處。

![配置圖表](./media/cloud-services-allocation-failure/Allocation1.png)

### 配置失敗的原因
當配置要求已釘選到叢集時，由於可用的資源集區僅限於某個叢集，很可能找不到可用的資源。此外，如果配置要求已釘選到叢集，但該叢集不支援您所要求的資源類型，即使叢集有可用的資源，您的要求仍會失敗。下圖 3 說明由於唯一候選叢集沒有可用的資源，導致已釘選的配置失敗的情況。圖 4 說明因唯一候選叢集不支援所要求的 VM 大小 (雖然叢集有可用的資源)，而導致已釘選的配置失敗的情況。

![釘選配置失敗](./media/cloud-services-allocation-failure/Allocation2.png)

## 雲端服務配置失敗的疑難排解
### 錯誤訊息
您可能會看到下列錯誤訊息：

	"Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### 常見問題
以下是造成配置要求釘選到單一叢集的常見配置案例。

- 部署至預備位置 - 如果某個雲端服務在任一位置含有部署，則整個雲端服務都會釘選到特定的叢集。這表示如果某個部署已存在生產位置，則新的預備部署只能配置在與生產位置相同的叢集中。如果叢集逼近容量上限，則要求可能會失敗。 
 
- 調整 - 將新的執行個體加入現有的雲端服務必須配置到相同叢集中。小型的調整要求通常可配置，但並不盡然。如果叢集逼近容量上限，則要求可能會失敗。
	
- 同質群組 - 部署到空的雲端服務的新部署可經由該區域中任一叢集的網狀架構配置，除非雲端服務已釘選到某個同質群組。將在相同的叢集中嘗試部署到相同的同質群組。如果叢集逼近容量上限，則要求可能會失敗。
	
- 同質群組 vNet - 較舊的虛擬網路是繫結至同質群組而不是區域，而這些虛擬網路中的雲端服務會釘選到該同質群組叢集。將在釘選的叢集中嘗試部署到這種類型的虛擬網路。如果叢集逼近容量上限，則要求可能會失敗。

## 解決方案

1. 重新部署到新的雲端服務 - 此解決方案可能是最成功的，因為它可讓平台從該區域的所有叢集中選擇。
	
	- 將工作負載部署到新的雲端服務  
	
	- 更新 CNAME 或 A 記錄，以將流量指向新的雲端服務
		
	- 一旦流向舊網站的流量為零，您就可以刪除舊的雲端服務。此解決方案不需要停機。

2. 刪除生產和預備位置 - 這個解決方案將會保留現有的 DNS 名稱，但會造成您的應用程式的停機時間。
	
	- 刪除現有雲端服務的生產和預備位置，讓雲端服務是空白的，然後 
	
	- 在現有的雲端服務中建立新的部署。這會重新嘗試在區域中的所有叢集上配置。請確定雲端服務未繫結至同質群組。

3. 保留的 IP - 此方案將會保留現有的 IP 位址，但是會導致您的應用程式的停機時間。
	
	- 使用 Powershell 為您現有的部署建立 ReservedIP 

	```
	New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
	```
		
	- 請遵循上述的 #2，務必在服務的 CSCFG 中指定新 ReservedIP。

4. 移除新部署中的同質群組 - 不再建議使用同質群組。請遵循上述 #1 的步驟以部署新的雲端服務。請確定雲端服務不在同質群組中。

5. 轉換至區域虛擬網路 - 請參閱[如何從同質群組移轉至區域虛擬網路 (VNet)]( https://azure.microsoft.com/documentation/articles/virtual-networks-migrate-to-regional-vnet/)。

## 其他資源
### 連絡 Azure 客戶支援

如果本文未解決您的 Azure 問題，請瀏覽 [MSDN 和堆疊溢位](https://azure.microsoft.com/support/forums/)上的 Azure 論壇。也可以就您的問題提出 Azure 支援事件。請前往 [Azure 支援](https://azure.microsoft.com/support/options/)網站並按一下 [取得支援]。如需關於使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

<!----HONumber=AcomDC_0128_2016-->