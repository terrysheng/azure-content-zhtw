<properties
   pageTitle="如何使用 Azure PowerShell 命令來建立空白的雲端服務容器 | Microsoft Azure"
   description="本文說明如何使用 PowerShell 指令碼建立雲端服務容器，以及執行雲端服務相關管理作業"
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="paulyuk"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="01/13/2015"
   ms.author="cawa"/>

# 使用 Azure PowerShell 命令來建立空白的雲端服務容器
這篇文章說明如何使用 Azure PowerShell Cmdlet 快速建立雲端服務容器。請依照下列步驟執行：

1. 從 [Azure PowerShell 下載](http://aka.ms/webpi-azps)頁面安裝 Microsoft Azure PowerShell Cmdlet。
2. 開啟 PowerShell 命令提示字元。
3. 使用 [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) 登入。

    > [AZURE.NOTE] 如需安裝 Azure PowerShell Cmdlet 及連接至您的 Azure 訂用帳戶的進一步指示，請參閱[如何安裝及設定 Azure PowerShell](../powershell-install-configure.md)。

4. 使用 **New-AzureService** Cmdlet 來建立空白的 Azure 雲端服務容器。

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. 遵循此範例以叫用 Cmdlet：```
New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
```

如需有關建立 Azure 雲端服務的詳細資訊，請執行：```
Get-help New-AzureService
```

### 後續步驟

 * 若要管理雲端服務部署，請參閱 [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx)、[Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx) 及 [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx)命令。另請參閱[如何設定雲端服務](cloud-services-how-to-configure.md)以取得進一步的資訊。

 * 若要將雲端服務專案發佈至 Azure，請參閱[在 Azure 中持續提供雲端服務](cloud-services-dotnet-continuous-delivery.md)中的 **PublishCloudService.ps1** 程式碼範例

<!---HONumber=AcomDC_0204_2016-->