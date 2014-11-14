<properties urlDisplayName="Service Management API" pageTitle="如何使用 VM 的服務管理 API - Azure" metaKeywords="" description="了解如何使用 Linux 虛擬機器的 Azure 服務管理 API。" metaCanonical="" services="virtual-machines" documentationCenter="" title="如何使用服務管理 API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# 如何使用服務管理 API

## 初始化

若要從 NodeJS 叫用 Azure IaaS 服務管理 API，請使用 `azure` 模組。

    var azure = require('azure');

首先，請建立 ServiceManagementService 的執行個體。所有的 API 呼叫都將使用此物件。訂閱 ID、認證和其他連線選項都會在此時建立。若要管理多個訂閱，請建立多個 ServiceManagementService。

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid 是必要字串。此項目應為所要存取之帳戶的訂閱 ID。
-   Auth 是選用物件，會指定要用於此帳戶的私密金鑰和公開憑證。

    -   keyfile - 具有私密金鑰之 .pem 檔案的檔案路徑。若已指定 keyvalue，則會忽略此項目。
    -   keyvalue - 私密金鑰儲存於 .pem 檔案中的實際值。
    -   certfile - 具有公開憑證之 .pem 檔案的檔案路徑。若已指定 cervalue，則會忽略此項目。
    -   cervalue - 公開憑證儲存於 .pem 檔案中的實際值。
    -   若未指定前述值，則會讀取並使用處理環境變數值 `CLIENT_AUTH_KEYFILE` 和 `CLIENT_AUTH_CERTFILE`。若未設定這些變數值，則會嘗試使用下列檔案的預設值：priv.pem 和 pub.pem。
    -   如果無法載入私密金鑰和公開憑證，則會擲回錯誤。
-   Options 屬於選用物件，可用來控制用戶端所使用的屬性。

    -   host - Azure 管理伺服器的主機名稱。若未指定，將會使用預設主機。
    -   apiversion - 要在 HTTP 標頭中使用的版本字串。若未指定，將會使用預設版本。
    -   serializetype - 可以是 XML 或 JSON。若未指定，將會使用預設序列化。

您可以選擇性地使用通道 Proxy，使 HTTPS 要求可以通過 Proxy。IaasClient 建立時，會處理環境變數 `HTTPS_PROXY`。如果此變數設為有效的 URL，則會由此 URL 剖析主機名稱和連接埠，並在後續的要求中將其用來識別 Proxy。

        iaasClient.SetProxyUrl(proxyurl)

您可以呼叫 SetProxyUrl，以明確設定 Proxy 主機和連接埠。其效用與設定 `HTTPS_PROXY` 環境變數相同，且會覆寫環境設定。

## 回呼

所有的 API 都具有必要的回呼引數。要求完成時，會藉由呼叫傳入回呼中的函數來表示。

    callback(rsp)

-   回呼具有屬於 response 物件的單一參數.
-   isSuccessful - true 或 false
-   statusCode - 來自回應的 HTTP 狀態碼
-   response - 剖析為 javascript 物件的回應。若 isSuccessful 為 true，則加以設定。
-   error - 包含錯誤資訊的 javascript 物件，若 isSuccessful 為 false，則加以設定。
-   body - 回應的實際本文 (字串格式)
-   headers - 回應的實際 HTTP 標頭
-   reqopts - 用以發出要求的節點 Node HTTP 要求選項。

請注意，在某些情況下，完成只是代表要求已被接受。在此情況下，請使用 **GetOperationStatus** 取得最終狀態。

## API

**iaasClient.GetOperationStatus(requested, callback)**

-   許多管理呼叫都會在作業完成之前傳回。這些呼叫會傳回值「202 已接受」，並在 ms-request-id HTPP 標頭中發出要求。若要輪詢要求是否已完成，請使用此 API，並傳入要求的值。
-   回呼是必要項目。

**iaasClient.GetOSImage(imagename, callback)**

-   imagename 是映像的必要字串名稱。
-   回呼是必要項目。
-   如果成功，response 物件將會包含指定映像的屬性。

**iaasClient.ListOSImages(callback)**

-   回呼是必要項目。
-   如果成功，response 物件將會包含映像物件的陣列。

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imageName 是映像的必要字串名稱。
-   mediaLink 是要使用之 mediaLink 的必要字串名稱。
-   imageOptions 是選用物件。它可包含下列屬性

    -   類別
    -   Label - 若未設定，會預設為 imageName。
    -   位置
    -   RoleSize
-   回呼是必要項目。(若未設定 imageOptions，這可能會是第三個參數。)
-   如果成功，response 物件將會包含已建立之映像的屬性。

**iaasClient.ListHostedServices(callback)**

-   回呼是必要項目。
-   如果成功，response 物件將會包含代管服務物件的陣列。

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   回呼是必要項目。
-   如果成功，response 物件將會包含代管服務的屬性。

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   serviceOptions 是選用物件。它可包含下列屬性

    -   Description - 預設為「服務主機」
    -   Label - 若未設定，會預設為 serviceName。
    -   Location - 建立服務的地區
-   回呼是必要項目。

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   回呼是必要項目。
-   如果成功，response 物件將會包含儲存體存取金鑰。

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   回呼是必要項目。
-   如果成功，response 物件將會包含指定部署的屬性。

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentSlot 是位置的必要字串名稱 (預備或生產)。
-   回呼是必要項目。
-   如果成功，response 物件將會包含位置中各部署的屬性。

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   VMRole 是必要物件，具有要為部署建立之角色的屬性。
-   deployOptions 是選用物件。它可包含下列屬性

    -   DeploymentSlot - 預設為「生產」
    -   Label - 若未設定，會預設為 deploymentName。
    -   UpgradeDomainCount - 無預設值
-   回呼是必要項目。

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   回呼是必要項目。
-   如果成功，response 物件將會包含指定角色的屬性。

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   VMRole 是必要物件，具有要新增至部署之角色的屬性。
-   回呼是必要項目。

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   VMRole 是必要物件，具有要在角色中修改的屬性。
-   回呼是必要項目。

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   回呼是必要項目。

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   Datadisk 是必要物件，用以指定資料磁碟的建立方式。
-   回呼是必要項目。

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   LUN 是可識別資料磁碟的號碼。
-   Datadisk 是必要物件，用以指定資料磁碟的修改方式。
-   回呼是必要項目。

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleName 是角色的必要字串名稱。
-   LUN 是可識別資料磁碟的號碼。
-   回呼是必要項目。

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleInstance 是角色執行個體的必要字串名稱。
-   回呼是必要項目。

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleInstance 是角色執行個體的必要字串名稱。
-   回呼是必要項目。

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName 是代管服務的必要字串名稱。
-   deploymentName 是部署的必要字串名稱。
-   roleInstance 是角色執行個體的必要字串名稱。
-   captOptions 是可定義擷取動作的必要物件。

    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   回呼是必要項目。

## 資料物件

在建立或修改部署、角色或磁碟時，API 會以物件作為輸入。其他 API 在取得或列出作業上會傳回類似的物件。
本節將概述物件屬性。
部署

-   Name - 字串
-   DeploymentSlot -「預備」或「生產」
-   Status - 字串 - 僅限輸出
-   PrivateID - 字串 - 僅限輸出
-   Label - 字串
-   UpgradeDomainCount - 號碼
-   SdkVersion - 字串
-   Locked - true 或 false
-   RollbackAllowed - true 或 false
-   RoleInstance - 物件 - 僅限輸出
-   Role - VMRole 物件
-   InputEndpointList - InputEndpoint 的陣列

**VMRole**

-   RoleName - 字串。建立時的必要項目。
-   RoleSize - 字串。建立時的選用項目。
-   RoleType - 字串。若未在建立中指定，則預設為 'PersistentVMRole'。
-   OSDisk - 物件。建立時的必要項目
-   DataDisks - 物件的陣列。建立時的選用項目。
-   ConfigurationSets - 組態物件的陣列。

**RoleInstance**

-   RoleName - 字串
-   InstanceName - 字串
-   InstanceStatus - 字串
-   InstanceUpgradeDomain - 號碼
-   InstanceFaultDomain - 號碼
-   InstanceSize - 字串
-   IpAddress - 字串

**OSDisk**

-   SourceImageName - 字串。建立時的必要項目
-   DisableWriteCache - true 或 false
-   DiskName - 字串。
-   MediaLink - 字串

**DataDisk**

-   DisableReadCache - true 或 false
-   EnableWriteCache - true 或 false
-   DiskName - 字串
-   MediaLink - 字串
-   LUN - 號碼 (0-15)
-   LogicalDiskSizeInGB - 號碼
-   SourceMediaLink - 字串
-   在建立期間有 3 種方式可指定磁碟 - 依名稱、依媒體或依大小。指定的選項將決定其運作方式。如需詳細資訊，請參閱 RDFE API 文件。

**ProvisioningConfiguration**

-   ConfigurationSetType - 'ProvisioningConfiguration'
-   AdminPassword - 字串
-   MachineName - 字串
-   ResetPasswordOnFirstLogon - true 或 false

**NetworkConfiguration**

-   ConfigurationSetType - 'NetworkConfiguration'
-   InputEndpoints - ExternalEndpoints 的陣列

**InputEndpoint**

-   RoleName
-   Vip
-   連接埠

**ExternalEndpoint**

-   LocalPort
-   名稱
-   連接埠
-   通訊協定

## 範例程式碼

以下 javascript 範例程式碼會建立代管服務和部署，然後輪詢部署的完成狀態。

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
