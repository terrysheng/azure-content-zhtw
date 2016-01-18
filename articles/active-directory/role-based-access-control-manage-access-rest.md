<properties
	pageTitle="使用 REST API 管理角色型存取控制"
	description="使用 REST API 管理角色型存取控制"
	services="active-directory"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="rest-api"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/04/2016"
	ms.author="inhenk"/>

# 使用 REST API 管理角色型存取控制

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## 列出所有角色指派

列出所有在指定的範圍和子範圍內的角色指派。

若要列出角色指派，您必須具有*範圍內*的 `Microsoft.Authorization/roleAssignments/read` 作業存取權。所有內建的角色都會獲得這項作業的存取權。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version={api-version}&filter={filter}` |

使用 2015-07-01 取代 *{api-version}*。

使用您想要套用以篩選角色指派清單的條件取代 *{filter}*。支援下列條件：

| | 篩選器 |
|-----------------------------------------------------------------------------------------------------------------|--------|
| 若僅要列出指定範圍的角色指派，不包括子範圍內的角色指派。 | `atScope()` <br/> 例如 <br/> `&filter=atScope()` |
| 若僅要列出特定使用者、群組或應用程式的角色指派 | `principalId%20eq%20'{objectId}'` <br/> 使用使用者、群組或服務主體的 Azure AD objectId 取代 *{objectId}*。<br/> 例如 <br/> `&filter=principalId%20eq%20'3a477f6a-6739-4b93-84aa-3be3f8c8e7c2'` |
| 若僅要列出特定使用者的角色指派，包括指派給群組的使用者，而使用者為該群組的成員 | `assignedTo('{objectId}')` <br/> 使用使用者的 Azure AD objectId 取代 *{objectId}*。<br/> 例如 <br/> `&filter=assignedTo('3a477f6a-6739-4b93-84aa-3be3f8c8e7c2')` |

使用您要列出角色指派的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "value": [
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "principalId": "2f9d4375-cbf1-48e8-83c9-2a0be4cb33fb",
        "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
        "createdOn": "2015-10-08T07:28:24.3905077Z",
        "updatedOn": "2015-10-08T07:28:24.3905077Z",
        "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
        "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/baa6e199-ad19-4667-b768-623fde31aedd",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "baa6e199-ad19-4667-b768-623fde31aedd"
    }
  ],
  "nextLink": null
}

```

## 取得角色指派的相關資訊

取得由角色指派識別碼所指定的單一角色指派相關資訊。

若要取得角色指派的相關資訊，您必須具有 `Microsoft.Authorization/roleAssignments/read` 作業存取權。所有內建的角色都會獲得這項作業的存取權。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用角色指派的 GUID 識別碼取代 *{role-assignment-id}*。

使用您要列出角色指派的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "principalId": "672f1afa-526a-4ef6-819c-975c7cd79022",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "createdOn": "2015-10-05T08:36:26.4014813Z",
    "updatedOn": "2015-10-05T08:36:26.4014813Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleAssignments/196965ae-6088-4121-a92a-f1e33fdcc73e",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "196965ae-6088-4121-a92a-f1e33fdcc73e"
}

```

## 建立角色指派

在指定範圍中建立指定主體授與指定角色的角色指派。

若要建立角色指派，您必須具有 `Microsoft.Authorization/roleAssignments/write` 作業存取權。內建角色中，*只有擁有者和使用者存取系統管理員會獲得這項作業的存取權*。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| PUT | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用新的 GUID 取代 *{role-assignment-id}*。這將會用做新角色指派的 GUID 識別碼。

使用您要建立角色指派的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

當您在父範圍內建立角色指派時，所有的子範圍會繼承相同的角色指派。

對於要求本文，提供下列格式的值：

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b"
  }
}

```

| 元素名稱 | 必要 | 類型 | 說明 |
|------------------|----------|--------|-------------------------------------------------------------------------------------------------------------|
| roleDefinitionId | 是 | String | 要指派角色的識別碼。識別碼的格式是：<br/> `{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id-guid}` |
| principalId | 是 | String | 角色要指派至 Azure AD 主體的 objectId (使用者、群組或服務主體)。 |

### Response

狀態碼：201

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-16T00:27:19.6447515Z",
    "updatedOn": "2015-12-16T00:27:19.6447515Z",
    "createdBy": null,
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/2e9e86c8-0e91-4958-b21f-20f51f27bab2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "2e9e86c8-0e91-4958-b21f-20f51f27bab2"
}

```

## 刪除角色指派

刪除指定範圍內的角色指派。

若要刪除角色指派，您必須具有 `Microsoft.Authorization/roleAssignments/delete` 作業存取權。內建角色中，*只有擁有者和使用者存取系統管理員會獲得這項作業的存取權*。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| 刪除 | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用角色指派 ID 的 GUID 取代 *{role-assignment-id}*。

使用您要建立角色指派的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "properties": {
    "roleDefinitionId": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
    "principalId": "5ac84765-1c8c-4994-94b2-629461bd191b",
    "scope": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND",
    "createdOn": "2015-12-17T23:21:40.8921564Z",
    "updatedOn": "2015-12-17T23:21:40.8921564Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network/providers/Microsoft.Network/virtualNetworks/EASTUS-VNET-01/subnets/Devices-Engineering-ProjectRND/providers/Microsoft.Authorization/roleAssignments/5eec22ee-ea5c-431e-8f41-82c560706fd2",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "5eec22ee-ea5c-431e-8f41-82c560706fd2"
}

```

## 列出所有角色

列出所有在指定的範圍內可指派的角色。

若要列出角色，您必須具有 `Microsoft.Authorization/roleDefinitions/read` 作業存取權。所有內建的角色都會獲得這項作業的存取權。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version={api-version}&filter={filter}` |

使用 2015-07-01 取代 *{api-version}*。

使用您想要套用以篩選角色清單的條件取代 *{filter}*。支援下列條件：

| | 篩選器 |
|--------------------------------------------------------------------------------------------|--------|
| 若要列出所有在指定的範圍及任何其子範圍內可指派的角色。 | `atScopeAndBelow()` <br/> 例如 <br/> `&filter=atScopeAndBelow()` |
| 若要使用確切的顯示名稱搜尋角色。 | `roleName%20eq%20'{role-display-name}'` <br/> 使用角色確切顯示名稱的 URL 編碼型式取代 *{role-display-name}*。<br/> 例如 <br/> `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

使用您要列出角色的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

### 取得角色的相關資訊

取得由角色定義識別碼所指定的單一角色相關資訊。若要取得使用其顯示名稱的單一角色相關資訊，請參閱列出所有角色和 RoleName 篩選條件。

若要取得角色的相關資訊，您必須具有 `Microsoft.Authorization/roleDefinitions/read` 作業存取權。所有內建的角色都會獲得這項作業的存取權。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用角色定義的 GUID 識別碼取代 *{role-definition-id}*。

使用您要列出角色指派的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "value": [
    {
      "properties": {
        "roleName": "Virtual Machine Contributor",
        "type": "BuiltInRole",
        "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they\u2019re connected to.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "Microsoft.Authorization/*/read",
              "Microsoft.Compute/availabilitySets/*",
              "Microsoft.Compute/locations/*",
              "Microsoft.Compute/virtualMachines/*",
              "Microsoft.Compute/virtualMachineScaleSets/*",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
              "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
              "Microsoft.Network/loadBalancers/read",
              "Microsoft.Network/locations/*",
              "Microsoft.Network/networkInterfaces/*",
              "Microsoft.Network/networkSecurityGroups/join/action",
              "Microsoft.Network/networkSecurityGroups/read",
              "Microsoft.Network/publicIPAddresses/join/action",
              "Microsoft.Network/publicIPAddresses/read",
              "Microsoft.Network/virtualNetworks/read",
              "Microsoft.Network/virtualNetworks/subnets/join/action",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Storage/storageAccounts/listKeys/action",
              "Microsoft.Storage/storageAccounts/read",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "2015-06-02T00:18:27.3542698Z",
        "updatedOn": "2015-12-08T03:16:55.6170255Z",
        "createdBy": null,
        "updatedBy": null
      },
      "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
      "type": "Microsoft.Authorization/roleDefinitions",
      "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
    }
  ],
  "nextLink": null
}

```

## 建立自訂角色
建立自訂角色。

若要建立自訂角色，您必須在所有其 `AssignableScopes` 上具有 `Microsoft.Authorization/roleDefinitions/write` 作業存取權。內建角色中，*只有擁有者和使用者存取系統管理員會獲得這項作業的存取權*。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| PUT | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用新的 GUID 取代 *{role-definition-id}*。這將會用做新自訂角色的 GUID 識別碼。

使用自訂角色的第一個 *AssignableScope* 取代 *{scope}*。下列範例顯示如何指定不同層級的範圍。

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

對於要求本文，提供下列格式的值：

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 元素名稱 | 必要 | 類型 | 說明 |
|-----------------------------------|----------|----------|------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱 | 是 | String | 自訂角色的 GUID 識別碼。 |
| properties.roleName | 是 | String | 自訂角色的顯示名稱。大小上限為 128 個字元。 |
| properties.description | 否 | String | 自訂角色的說明。大小上限為 1024 個字元。 |
| properties.type | 是 | String | 將此項目設為「CustomRole」。 |
| properties.permissions.actions | 是 | String | 動作字串的陣列會指定自訂角色授與存取權的作業。 |
| properties.permissions.notActions | 否 | String | 動作字串的陣列會指定作業，而該作業會從自訂角色授與存取權的作業中排除。 |
| properties.assignableScopes | 是 | String | 範圍的陣列，在其中自訂角色可以用來管理存取。 |

### Response

狀態碼：201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## 更新自訂角色

修改自訂角色。

若要修改自訂角色，您必須在所有其 `AssignableScopes` 上具有 `Microsoft.Authorization/roleDefinitions/write` 作業存取權。內建角色中，*只有擁有者和使用者存取系統管理員會獲得這項作業的存取權*。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| PUT | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用要更新的自訂角色 GUID 識別碼取代 *{role-definition-id}*。

使用自訂角色的第一個 *AssignableScope* 取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

對於要求本文，提供下列格式的值：

```
{
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "properties": {
    "roleName": "Virtual Machine Operator",
    "description": "Lets you monitor virtual machines and restart them.",
    "type": "CustomRole",
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
  }
}

```

| 元素名稱 | 必要 | 類型 | 說明 |
|-----------------------------------|----------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 名稱 | 是 | String | 要更新的自訂角色 GUID 識別碼。 |
| properties.roleName | 是 | String | 已更新的自訂角色顯示名稱。 |
| properties.description | 否 | String | 已更新的自訂角色說明。 |
| properties.type | 是 | String | 將此項目設為「CustomRole」。 |
| properties.permissions.actions | 是 | String | 動作字串的陣列會指定已更新自訂角色授與存取權的作業。 |
| properties.permissions.notActions | 否 | String | 動作字串的陣列會指定作業，而該作業會從已更新自訂角色授與存取權的作業中排除。 |
| properties.assignableScopes | 是 | String | 範圍的陣列，在其中已更新的自訂角色可以用來管理存取。 |

### Response

狀態碼：201

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-18T00:10:51.4662695Z",
    "updatedOn": "2015-12-18T00:10:51.4662695Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "7c8c8ccd-9838-4e42-b38c-60f0bbe9a9d7"
}

```

## 刪除自訂角色

刪除自訂角色。

若要刪除自訂角色，您必須在所有其 `AssignableScopes` 上具有 `Microsoft.Authorization/roleDefinitions/delete` 作業存取權。內建角色中，只有擁有者和使用者存取系統管理員會獲得這項作業的存取權。如需角色指派和管理 Azure 資源存取的詳細資訊，請參閱 [Azure 角色型存取控制](role-based-access-control-configure.md)。

### 要求

| **方法** | **要求 URI** |
|--------|-------------|
| 刪除 | `https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}?api-version={api-version}` |

使用 2015-07-01 取代 *{api-version}*。

使用要刪除的自訂角色 GUID 角色定義識別碼取代 *{role-definition-id}*。

使用您要刪除角色定義的範圍取代 *{scope}*。下列範例顯示如何指定不同層級的範圍：

| | **範圍** |
|----------------|-------|
| 訂用帳戶 | /subscriptions/{subscription-id} |
| 資源群組 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1 |
| 資源 | /subscriptions/{subscription-id}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1 |

### Response

狀態碼：200

```
{
  "properties": {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole",
    "description": "Lets you monitor virtual machines and restart them.",
    "assignableScopes": [
      "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Network/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/*/read",
          "Microsoft.Support/*",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action"
        ],
        "notActions": []
      }
    ],
    "createdOn": "2015-12-16T00:07:02.9236555Z",
    "updatedOn": "2015-12-16T00:07:02.9236555Z",
    "createdBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e",
    "updatedBy": "877f0ab8-9c5f-420b-bf88-a1c6c7e2643e"
  },
  "id": "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/providers/Microsoft.Authorization/roleDefinitions/0bd62a70-e1b8-4e0b-a7c2-75cab365c95b",
  "type": "Microsoft.Authorization/roleDefinitions",
  "name": "0bd62a70-e1b8-4e0b-a7c2-75cab365c95b"
}

```

<!---HONumber=AcomDC_0107_2016-->