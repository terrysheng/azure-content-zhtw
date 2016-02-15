<properties
   pageTitle="Azure 資源管理員的流量管理員支援預覽 | Microsoft Azure"
   description="在預覽中使用適用於流量管理員的 PowerShell 與 Azure 資源管理員 (ARM)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="joaoma" />

# Azure 資源管理員的 Azure 流量管理員支援預覽
Azure 資源管理員 (ARM) 是 Azure 中新的服務管理架構。Azure 流量管理員設定檔現在可使用以 Azure 資源管理員為基礎的 API 和工具來管理 。若要深入了解 Azure 資源管理員，請參閱[使用資源群組管理您的 Azure 資源](../azure-preview-portal-using-resource-groups.md)。

## 資源模型

Azure 流量管理員使用名為「流量管理員設定檔」的設定集合進行設定。這包含 DNS 設定、流量路由設定、端點監視設定，以及流量將路由之目標服務端點的清單。

在 ARM 中，每個流量管理員設定檔是以一個 ARM 資源來表示 (類型為 ‘TrafficManagerProfiles’，由 ‘Microsoft.Network’ 資源提供者管理)。在 REST API 層級中，每個設定檔的 URI 如下：

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## 與 Azure 流量管理員服務管理 API 的比較

使用 ARM 來設定流量管理員設定檔，即可存取和 Azure 服務管理 (ASM) API 相同的一組流量管理員功能，但下面列出的「預覽限制」除外。

不過，儘管功能維持相同，但部分術語已經變更：

- 「負載平衡方法」用來決定當流量管理員回應特定的 DNS 要求時，選擇要將流量路由至哪一個端點的方式，已重新命名為「流量路由方法」。

- 「循環配置資源」流量路由方法已重新命名為「加權」。

- 「容錯移轉」流量路由方法已重新命名為 「優先」。

## 限制
支援 Azure 流量管理員的 ARM 中目前有一些限制：

- 使用現有的 (非 ARM) Azure 服務管理 (ASM) API、工具和「傳統」入口網站所建立的流量管理員設定檔無法透過 ARM 使用，反之亦然。目前不支援將設定檔從 ASM 移轉至 ARM API，但可以藉由先刪除再重新建立設定檔來達到此目的。

- 「巢狀」流量管理員端點是透過 ARM API、ARM PowerShell 及 ARM-mode Azure CLI 來支援。Azure 入口網站 (也是使用 ARM API) 目前不支援它們。

- 類型「AzureEndpoints」的流量管理員端點在參考 Web 應用程式時，只能參考預設 (生產) [Web 應用程式位置](../app-service-web/web-sites-staged-publishing.md)。目前尚未支援自訂位置。而為了因應這個問題，您可以使用「ExternalEndpoints」類型來設定自訂位置。

## 設定 Azure PowerShell

這些指示使用 Microsoft Azure PowerShell，並且需要使用下列步驟來設定。

若為非 PowerShell 使用者或非 Windows 使用者，則可透過 Azure CLI 執行類似的作業。可以透過 Azure 入口網站取得所有作業 (除了管理「巢狀」流量管理員設定檔之外)。

### 步驟 1
安裝最新的 Azure PowerShell (可從 Azure 下載頁面取得)。

### 步驟 2
登入您的 Azure 帳戶。

	PS C:\> Login-AzureRmAccount

系統會提示使用您的認證進行驗證。

### 步驟 3
選擇要使用哪一個 Azure 訂用帳戶。

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

若要查看可用訂用帳戶的清單，請使用 ‘Get-AzureRmSubscription’ Cmdlet。

### 步驟 4

流量管理員服務由 Microsoft.Network 資源提供者管理。您的 Azure 訂用帳戶必須先註冊以使用此資源提供者，才能透過 ARM 使用流量管理員。每個訂用帳戶只需執行一次此作業。

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### 步驟 5
建立資源群組 (如果是使用現有的資源群組，請略過此步驟)

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

Azure 資源管理員需要所有的資源群組指定一個位置。這用來作為該資源群組中資源的預設位置。然而，因為流量管理員設定檔的所有資源是全域而非區域，資源群組位置的選擇不會影響 Azure 流量管理員。

## 建立流量管理員設定檔

若要建立流量管理員設定檔，請使用 New-AzureRmTrafficManagerProfile Cmdlet：

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

參數如下所示：

- Name：流量管理員設定檔資源的 ARM 資源名稱。相同資源群組中的設定檔必須有唯一的名稱。這個名稱是從用於 DNS 查詢的 DNS 名稱分割而來。

- ResourceGroupName：包含設定檔資源的 ARM 資源群組的名稱。

- TrafficRoutingMethod：指定流量路由方法，用來決定哪一個端點會在傳入的 DNS 查詢回應中傳回。可能的值為 ‘Performance’、‘Weighted’ 或 ‘Priority’。

- RelativeDnsName：指定由此流量管理員設定檔所提供的相對 DNS 名稱。這個值會與由 Azure 流量管理員使用的 DNS 網域名稱結合，形成設定檔的完整網域名稱 (FQDN)。例如，‘contoso’ 的值會提供流量管理員設定檔完整的名稱 ‘contoso.trafficmanager.net’。

- TTL：指定 DNS 存留時間 (TTL)，以秒為單位。這會通知本機 DNS 解析程式和 DNS 用戶端快取此流量管理員設定檔所提供之 DNS 回應的時間長度。

- MonitorProtocol：指定要用來監視端點健全狀況的通訊協定。可能的值為 ‘HTTP’ 和 ‘HTTPS’。

- MonitorPort：指定用來監視端點健全狀況的 TCP 連接埠。

- MonitorPath：指定用來探查端點健全狀況之端點網域名稱的相對路徑。

Cmdlet 在 Azure 流量管理員中建立流量管理員設定檔，並傳回對應的設定檔物件。此時，設定檔不包含任何端點，請參閱[新增流量管理員端點](#adding-traffic-manager-endpoints)以取得有關如何新增端點到流量管理員設定檔的詳細資料。

## 取得流量管理員設定檔

若要擷取現有的流量管理員設定檔物件，請使用 Get-AzureRmTrafficManagerProfle Cmdlet：

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

此 Cmdlet 會傳回流量管理員設定檔物件。

## 更新流量管理員設定檔 [](#update-traffic-manager-profile)

修改流量管理員設定檔 (例如新增或移除端點，或是修改設定檔設定) 時，請遵循以下的 3 步驟程序：

1.	使用 Get-AzureRmTrafficManagerProfile 擷取設定檔 (或使用 New-AzureRmTrafficManagerProfile 所傳回的設定檔)。

2.	透過新增端點、移除端點、變更端點參數或是變更設定檔參數來修改設定檔。這些變更是離線進行的作業，只會變更代表設定檔的本機物件。

3.	使用 Set-AzureRmTrafficManagerProfile Cmdlet 認可您的變更。這會使用提供的設定檔取代 Azure 流量管理員中現有的設定檔。

您可以變更所有設定檔屬性，但 RelativeDnsName 設定檔除外，此設定檔在建立後便無法變更 (若要變更此值，請先刪除再重新建立此設定檔)。

例如，若要變更 TTL 設定檔：

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## 新增流量管理員端點
流量管理員端點有三種類型：1.Azure 端點：這些端點代表在 Azure 中託管的服務。2.外部端點：這些端點代表在 Azure 之外託管的服務。3.巢狀端點：這些端點可用來建構巢狀的流量管理員設定檔階層，以針對更複雜的應用程式進行進階流量路由設定。目前還不支援透過 ARM API 使用這些端點。

不論是這三種端點中的哪一種，您都可以透過兩種方式來新增端點：1.使用類似[更新流量管理員設定檔](#update-traffic-manager-profile)中所述方式的 3 步驟程序：使用 Get-AzureRmTrafficManagerProfile 取得設定檔物件、使用 Add-AzureRmTrafficManagerEndpointConfig 將物件離線更新以新增端點，以及使用 Set-AzureRmTrafficManagerProfile 將變更上傳至 Azure 流量管理員。這個方法的優點是單一更新就可以變更許多端點。2.使用 New-AzureRmTrafficManagerEndpoint Cmdlet。這會在單一作業中將端點新增至現有流量管理員設定檔。

### 新增 Azure 端點

Azure 端點會參考在 Azure 中託管的其他服務。目前支援的 Azure 端點有 3 種類型：1.Azure Web Apps 2.「傳統」雲端服務 (可包含 PaaS 服務或 IaaS 虛擬機器) 3.ARM Microsoft.Network/publicIpAddress 資源 (可附加至負載平衡器或虛擬機器 NIC)。要注意的是，publicIpAddress 必須已獲指派 DNS 名稱，才能在流量管理員中使用。

不論是上述哪一種，都必須注意以下事項：- 要使用 Add-AzureRmTrafficManagerEndpointConfig 或 New-AzureRmTrafficManagerEndpoint 的 'targetResourceId' 參數來指定服務。- 不要指定 'Target' 和 'EndpointLocation'，因為上面指定的 TargetResourceId 已經隱含這兩項。- 您可以選擇是否指定 'Weight'。只有在設定檔已設定為使用「加權」流量路由方法時才會使用 Weight，否則會予以忽略。如果有指定，其值必須介於 1 到 1000 的範圍內。預設值是 '1'。- 您可以選擇是否指定 'Priority'。只有在設定檔已設定為使用「優先順序」流量路由方法時才會使用 Priority，否則會予以忽略。有效值為 1 到 1000 (越低的值優先順序越高)。如果對某個端點指定值，則所有端點也都必須進行指定。如果省略，則會以端點的提供順序套用預設值 (從 1、2、3 開始，依此類推)。

#### 範例 1：使用 Add-AzureRmTrafficManagerEndpointConfig 新增 Web 應用程式端點
在此範例中，我們會使用 Add-AzureRmTrafficManagerEndpointConfig Cmdlet 建立新的流量管理員設定檔並新增兩個 Web 應用程式端點，然後使用 Set-AzureRmTrafficManagerProfile 對 Azure 流量管理員認可更新的設定檔。

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 範例 2：使用 New-AzureRmTrafficManagerEndpoint 新增「傳統」雲端服務端點
此範例會在流量管理員設定檔中新增「傳統」雲端服務端點。請注意，在此案例中，我們選擇使用設定檔名稱和資源群組名稱來指定設定檔，而不是透過傳遞設定檔物件來指定 (但這兩種方法都有受到支援)。

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### 範例 3：使用 New-AzureRmTrafficManagerEndpoint 新增 publicIpAddress 端點
此範例會在流量管理員設定檔中新增 ARM 公用 IP 位址資源。公用 IP 位址必須設定 DNS 名稱，而且可以繫結至 VM 的 NIC 或繫結至負載平衡器。

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### 新增外部端點
流量管理員使用外部端點將流量導向至在 Azure 之外所託管的服務。和 Azure 端點一樣，使用 Add-AzureRmTrafficManagerEndpointConfig 並後接 Set-AzureRmTrafficManagerProfile 或使用 New-AzureRMTrafficManagerEndpoint 即可新增外部端點。

在指定外部端點時：- 必須使用 'Target' 參數指定端點網域名稱。- 如果使用「效能」流量路由方法，則 'EndpointLocation' 為必要項目，否則為選用項目。此值必須是[有效的 Azure 區域名稱](https://azure.microsoft.com/regions/)。- 和用於 Azure 端點時一樣，'Weight' 和 'Priority' 是選用項目。

#### 範例 1：使用 Add-AzureRmTrafficManagerEndpointConfig 和 Set-AzureRmTrafficManagerProfile 新增外部端點
在此範例中，我們會建立新的流量管理員設定檔、新增兩個外部端點，並認可變更。

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### 範例 2：使用 New-AzureRmTrafficManagerEndpoint 新增外部端點
在此範例中，我們會在現有設定檔 (使用設定檔名稱和資源群組名稱來指定) 新增外部端點。

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### 新增「巢狀」端點

流量管理員可讓您將流量管理員設定檔 (稱為「子設定檔」) 設為另一個流量管理員設定檔 (稱為「父設定檔」) 內的端點。

巢狀流量管理員可讓您建立更有彈性且功能強大的流量路由和容錯移轉配置，以支援更大型且更複雜的部署需求。[這篇部落格文章](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) 提供數個範例。

巢狀端點是在父設定檔中使用特定的端點類型 ('NestedEndpoints') 來設定。在指定巢狀端點時：- 必須使用 'targetResourceId' 參數指定端點端點 (如子設定檔)。- 如果使用 'Performance' 流量路由方法，'targetResourceId' 為必要項目，否則為選用項目。該值必須是[有效的 Azure 區域名稱](http://azure.microsoft.com/regions/)。- 至於 Azure 端點，'Weight' 和 'Priority' 是可省略的。- 'MinChildEndpoints' 參數是可省略的，預設值為 '1'。如果子設定檔中可用的端點數目低於此臨界值，父設定檔會將子設定檔視為「已降級」，並將流量導向其他父設定檔端點。


#### 範例 1：使用 Add-AzureRmTrafficManagerEndpointConfig 和 Set-AzureRmTrafficManagerProfile 新增巢狀端點

在此範例中，我們會建立新的流量管理員子設定檔和父設定檔，在父設定檔中加入子設定檔做為巢狀端點，並認可變更。(為了簡潔起見，我們不會將任何其他端點加入子設定檔或父設定檔，雖然通常必須加入這些端點。)

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### 範例 2：使用 New-AzureRmTrafficManagerEndpoint 新增巢狀端點

在此範例中，我們會對現有的父設定檔 (使用設定檔名稱和資源群組名稱來指定) 加入子設定檔做為巢狀端點。

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## 更新流量管理員端點
有兩種方式可供更新現有流量管理員端點：1.使用 Get-AzureRmTrafficManagerProfile 取得流量管理員設定檔、更新設定檔內的端點屬性，並使用 Set-AzureRmTrafficManagerProfile 認可變更。這個方法的優點是能夠在單一作業中更新多個端點。2.使用 Get-AzureRmTrafficManagerEndpoint 取得流量管理員端點、更新端點屬性，並使用 Set-AzureRmTrafficManagerEndpoint 認可變更。由於不需要在設定檔中編製索引為端點陣列，這個方法會比較簡單。

#### 範例 1：使用 Get-AzureRmTrafficManagerProfile 和 Set-AzureRmTrafficManagerProfile 更新端點
在此範例中，我們將修改現有設定檔中兩個端點的優先順序。

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### 範例 2：使用 Get-AzureRmTrafficManagerEndpoint 和 Set-AzureRmTrafficManagerEndpoint 更新端點
在此範例中，我們將修改現有設定檔中單一端點的加權。

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## 啟用和停用端點和設定檔
流量管理員可允許啟用和停用個別端點，以及允許啟用和停用全部設定檔。透過取得/更新/設定端點或設定檔資源，即可進行這些變更。為了簡化這些常見作業，系統也支援透過專用 Cmdlet 來執行這些作業。

#### 範例 1：啟用和停用流量管理員設定檔
若要啟用流量管理員設定檔，請使用 Enable-AzureRmTrafficManagerProfile。您可以使用設定檔物件 (透過管線傳遞或使用 '-TrafficManagerProfile' 參數) 或藉由直接指定設定檔名稱和資源群組名稱來指定設定檔，如此範例所示。

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

同樣地，若要停用流量管理員設定檔：

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Disable-AzureRmTrafficManagerProfile Cmdlet 會顯示確認提示，但使用 '-Force' 參數即可隱藏此提示。

#### 範例 2：啟用和停用流量管理員端點
若要啟用流量管理員端點，請使用 Enable-AzureRmTrafficManagerEndpoint。您可以使用 TrafficManagerEndpoint 物件 (透過管線傳遞或使用 '-TrafficManagerEndpoint' 參數) 或透過使用端點名稱、端點類型、設定檔名稱和資源群組名稱來指定端點：

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

同樣地，若要停用流量管理員端點：

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

和使用 Disable-AzureRmTrafficManagerProfile 時一樣，Disable-AzureRmTrafficManagerEndpoint Cmdlet 會有確認提示，但使用 '-Force' 參數即可隱藏此提示。

## 停用流量管理員端點
若要刪除流量管理員端點，有一個方法是擷取設定檔物件 (使用 Get-AzureRmTrafficManagerProfile)、更新本機設定檔物件內的端點清單，並認可變更 (使用 Set-AzureRmTrafficManagerProfile)。此方法可一起認可多個端點變更。

移除個別端點的另一種方式是使用 Remove-AzureRmTrafficManagerEndpoint Cmdlet：

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
這個 Cmdlet 會顯示確認提示，除非您使用 '-Force' 參數來隱藏提示。

## 刪除流量管理員設定檔
若要刪除流量管理員設定檔，請使用 Remove-AzureRmTrafficManagerProfile Cmdlet，指定設定檔名稱和資源群組名稱：

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

這個 Cmdlet 會提示進行確認。選擇性的 ’-Force’ 參數可用來隱藏這個提示。要刪除的設定檔也可以使用設定檔物件來指定：

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

也可輸送以下順序：

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## 後續步驟

[流量管理員監視](traffic-manager-monitoring.md)

[流量管理員的效能考量](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0204_2016-->