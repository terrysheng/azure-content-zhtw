<properties
	pageTitle="使用 PowerShell 管理服務匯流排"
	description="使用 PowerShell 指令碼 (而非 .NET) 管理服務匯流排"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>

# 使用 PowerShell 管理服務匯流排

## 概觀

Microsoft Azure PowerShell 是一種指令碼環境，可讓您在 Azure 中用來控制和自動化工作負載的部署與管理。在此文章中，您將可以學到如何使用 Azure PowerShell 主控台與 PowerShell 來佈建及管理服務匯流排實體，例如命名空間、佇列及事件中樞。

## 必要條件

開始閱讀本文之前，您必須符合下列必要條件：

- Azure 訂閱。Azure 是訂用帳戶型平台。如需
取得訂閱的相關詳細資訊，請參閱[購買選項][azure-purchase-options]、
[成員優惠][azure-member-offers]，或[免費試用][azure-free-trial]。

- 具備 Azure PowerShell 的電腦。如需指示，請參閱[安裝及設定 Azure PowerShell][Powershell-install-configure]。

- 大致了解 PowerShell 指令碼、NuGet 套件及 .NET Framework。

## 包括對服務匯流排之 .NET 組件的參考

可用於管理服務匯流排的 PowerShell Cmdlet 並不多。若要佈建不是透過現有 Cmdlet 公開的
實體，我們將會使用[服務匯流排 NuGet 套件][]來使用服務匯流排的 .NET 用戶端。

首先，我們想要確定我們的指令碼可以找到與 NuGet 套件一起安裝的 **Microsoft.ServiceBus.dll** 組件。為了要有使用彈性，指令碼將會執行這些步驟：

1. 指令碼會判斷它被叫用所在的路徑。
2. 它會遞迴路徑，直到找到名為  `packages` 的資料夾為止。這是安裝 NuGet 套件時建立的資料夾。
3. 它會以遞迴方式搜尋  `packages` 資料夾，以搜尋名為 **Microsoft.ServiceBus.dll** 的組件。
4. 然後它會參照組件，才能在之後使用那些類型。

以下是如何使用 PowerShell 指令碼實作這些步驟的方式：

```powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## 佈建服務匯流排命名空間

使用服務匯流排命名空間時，您可以使用兩個 Cmdlet，而不使用 .NET SDK。具體而言，也就是您可以使用 [Get-AzureSBNamespace][] 和 [New-AzureSBNamespace][]。

在我們的範例中，我們將會在指令碼中建立幾個本機變數 (`$Namespace` 和 `$Location`)。
- `$Namespace` 為我們想要使用之服務匯流排命名空間的名稱。
- `$Location` 會識別我們將在其中佈建命名空間的資料中心。
- `$CurrentNamespace` 會儲存我們抓取 (或建立) 的參照命名空間。

在實際的指令碼中，`$Namespace` 和 `$Location` 可以以參數的方式傳遞。

此部分的指令碼將會

1. 嘗試抓取具有所提供之名稱的服務匯流排命名空間。
2. 如果找到命名空間，它會回報找到的項目。
3. 如果找不到命名空間，它則會建立命名空間，然後抓取新建立的命名空間。

``` powershell

$Namespace = "MyServiceBusNS"
$Location = "West US"

# Query to see if the namespace currently exists
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists or needs to be created
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
}
```

為了佈建其他服務匯流排實體，我們會從 SDK 建立  `NamespaceManager` 物件的執行個體。
我們可以使用 [Get-AzureSBAuthorizationRule][] Cmdlet 來抓取用來提供連線字串的授權規則。我們將會在 `$NamespaceManager` 變數中儲存對  `NamespaceManager` 執行個體的參照。我們稍後將會在指令碼中使用 `$NamespaceManager` 來佈建其他實體。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## 佈建其他服務匯流排實體

為了佈建其他實體 (例如佇列、主題及事件中樞)，我們可以使用[服務匯流排的 .NET API ][dotnet-servicebus-api]。在此文章中，我們將只會著重在事件中樞，但是其他實體的步驟則很類似。此外，在此文章的結尾處會參照更多詳細範例 (包括其他實體)。

我們會在指令碼的這個部分再建立四個本機變數。這些變數將用來具現化  `EventHubDescription`。我們將會執行以下動作：

1. 使用  `NamespaceManager` 物件時，我們將會先查看 `$Path` 所識別的事件中樞是否存在。
2. 如果事件中樞不存在，我們會建立一個  `EventHubDescription` 並將它傳遞給 `NamespaceManager` class `CreateEventHubIfNotExists` 方法。
3. 在我們知道事件中樞可用之後，我們會使用  `ConsumerGroupDescription` 和  `NamespaceManager` 建立取用者群組。

``` powershell

$Path  = "MyEventHub"
$PartitionCount = 12
$MessageRetentionInDays = 7
$UserMetadata = $null
$ConsumerGroupName = "MyConsumerGroup"

# Check if the event hub already exists
if ($NamespaceManager.EventHubExists($Path))
{
    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
}
else
{
    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
    $EventHubDescription.PartitionCount = $PartitionCount
    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
    $EventHubDescription.UserMetadata = $UserMetadata
    $EventHubDescription.Path = $Path
    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
}

# Create the consumer group if it doesn't exist
Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
```

## 後續步驟

此文章會提供您使用PowerShell 佈建服務匯流排實體的基本大綱。您可以使用 .NET 用戶端程式庫執行的任何動作，也都可以使用 PowerShell 指令碼來執行。

這些部落格張貼文章中有更多詳細的範例可用：

- [如何使用 PowerShell 指令碼來建立服務匯流排佇列、主題及訂閱](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [如何使用 PowerShell 指令碼來建立服務匯流排命名空間與事件中樞](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

也有一些現成的指令碼可供下載：
- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[powershell-install-configure]: ../install-configure-powershell/
[服務匯流排 NuGet 套件]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[dotnet-servicebus-api]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!--HONumber=47-->
 