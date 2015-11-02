<properties 
	pageTitle="Azure 雲端服務 - 服務定義和服務組態 - XML 憑證" 
	description="了解如何在服務定義檔和組態檔中設定憑證。" 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015"
	ms.author="adegeo"/>



# 設定憑證的服務定義與組態

執行 Web 或背景工作角色的虛擬機器可以擁有相關聯的憑證。上傳憑證到入口網站之後，您需要設定憑證的服務定義 (.csdef) 和服務組態 (.cscfg) 檔案。

虛擬機器可在憑證安裝好之後存取憑證的私密金鑰。因此，您可以使用更高的權限來限制處理序的存取權。

## 服務定義範例

以下範例是在服務定義中定義的憑證。

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### 權限
權限 (`permisionLevel` 屬性) 可設為下列其中之一：

| 權限值 | 說明 |
| ----------------  | ----------- |
| limitedOrElevated | **(預設值)** 所有角色處理序都可以存取私密金鑰。 |
| elevated | 只有較高權限的處理序可以存取私密金鑰。|

## 服務組態範例

以下範例是在服務組態中定義的憑證。

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**請注意**相符的 `name` 屬性。

## 後續步驟
檢閱[服務定義 XML](https://msdn.microsoft.com/library/azure/ee758711.aspx) 結構描述和[服務組態 XML](https://msdn.microsoft.com/library/azure/ee758710.aspx) 結構描述。

<!---HONumber=Oct15_HO4-->