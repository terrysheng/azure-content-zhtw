<properties
	pageTitle="Azure 資源管理員原則 | Microsoft Azure"
	description="描述如何使用 Azure 資源管理員原則來防止在不同範圍 (如訂用帳戶、資源群組或個別資源) 的違規。"
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/10/2015"
	ms.author="gauravbh;tomfitz"/>

# 使用原則來管理資源和控制存取

Azure 資源管理員現在可讓您透過自訂原則來控制存取。您可以透過原則來防止組織中的使用者違反管理組織資源所需的慣例。

建立描述您想要明確拒絕之動作或資源的原則定義。在所需範圍內指派那些原則定義，例如訂用帳戶、資源群組或是個別的資源。

在本文中，我們將說明您可用來建立原則的原則定義語言的基本結構。然後我們將說明如何可以在不同範圍套用這些原則，並在最後示範如何透過 REST API 達成此目的的一些範例。

原則目前以預覽版提供。

## 它和 RBAC 有什麼不同？

原則和角色型存取控制之間有幾個關鍵的差異，但首先您必須了解原則是和 RBAC 一起運作的。若要使用原則，使用者必須經由 RBAC 通過驗證。不同於 RBAC，原則是個預設允許和明確拒絕的系統。

RBAC 著重於**使用者**在不同範圍內可執行的動作。例如，若特定使用者被加入所需範圍內之資源群組的參與者角色中，該使用者便能對該資源群組做出變更。

原則著重於各種範圍內的**資源**動作。例如，您能夠透過原則控制可以佈建的資源類型，或限制資源可以佈建的位置。

## 常見案例

一個常見的案例是為退款用途要求部門標記。組織可能只想在有相關聯的適當成本中心時允許作業，否則將拒絕要求。這會幫助它們向適當的成本中心對所執行之作業收費。

另一個常見案例是組織可能會想要控制建立資源的位置。或者它們可能會想要透過僅允許佈建特定類型的資源，來控制對資源的存取。

同樣地，組織可以控制服務類別或為資源強制執行所需的命名慣例。

使用原則可輕易地達成這些案例，如下所述。

## 原則定義結構

原則定義是使用 JSON 建立。它包含定義動作和效果的一或多個條件/邏輯運算子，可讓您得知當滿足條件時會發生的效果。

基本上，原則包含下列：

**條件/邏輯運算子：**它包含一組可透過一組邏輯運算子操作的條件。

**效果：**這說明當滿足條件時會發生的效果 – 拒絕或稽核。稽核效果會發出警告事件服務記錄檔。例如，系統管理員可以建立原則，如果有任何人建立大型 VM，然後稍後檢閱記錄檔，則此原則會引發稽核。

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## 邏輯運算子

以下列出支援的邏輯運算子以及語法：

| 運算子名稱 | 語法 |
| :------------- | :------------- |
| Not | "not" : {&lt;條件或運算子 &gt;} |
| 和 | "allOf" : [ {&lt;條件 1&gt;},{&lt;條件 2&gt;}] |
| 或 | "anyOf" : [ {&lt;條件 1&gt;},{&lt;條件 2&gt;}] |

不支援巢狀條件。

## 條件

條件將評估**欄位**或**來源**是否符合特定準則。以下列出支援的條件名稱以及語法：

| 條件名稱 | 語法 |
| :------------- | :------------- |
| Equals | "equals" : "&lt;值&gt;" |
| Like | "like" : "&lt;值&gt;" |
| Contains | "contains" : "&lt;值&gt;"|
| 在 | "in" : [ "&lt;值 1&gt;","&lt;值 2&gt;" ]|
| ContainsKey | "containsKey" : "&lt;機碼名稱&gt;" |


## 欄位和來源

透過使用欄位和來源形成條件。欄位代表資源要求裝載中的屬性來源代表要求本身的特性。

支援下列欄位和來源：

欄位：**name**、**kind**、**type**、**location**、**tags**、**tags.***。

來源：**action**。

如需有關動作的詳細資訊，請參閱 [RBAC - 內建角色](active-directory/role-based-access-built-in-roles.md)。

## 原則定義範例

現在讓我們看一下如何定義原則，以達成以上所列的案例。

### 退款：要求部門標記

以下原則會拒絕沒有包含 "costCenter" 索引鍵標記的所有要求。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### 地理區域法規遵循：確保資源位置

下列範例顯示的原則將會拒絕位置不是北歐或西歐的所有要求。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 服務策展：選取服務目錄

下列範例示範如何使用來源。它顯示只允許類型 Microsoft.Resources/*、Microsoft.Compute/*、Microsoft.Storage/*、Microsoft.Network/* 的服務上的動作。任何其他項目將會遭到拒絕。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 命名慣例

下列範例示範如何使用 "like" 條件所支援的萬用字元。條件指出如果名稱符合所述模式 (namePrefix*nameSuffix)，則拒絕要求。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

## 原則指派

原則可以套用在不同的範圍，如訂用帳戶、資源群組和個別資源。原則會由所有子資源繼承。所以，如果原則套用至資源群組，它將適用於該資源群組中的所有資源。

## 建立原則

本節提供如何使用 REST API 建立原則的詳細資料。

### 使用 REST API 建立原則定義

您可以使用[適用於原則定義的 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx) 來建立原則。REST API 可讓您建立和刪除原則定義，以及取得現有定義的相關資訊。

若要建立新原則，請執行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

使用如下的要求內文：

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


原則定義可以定義為如上所示的其中一個範例。針對 api-version，請使用 *2015-10-01-preview*。如需範例與詳細資料，請參閱[適用於原則定義的 REST API](https://msdn.microsoft.com/library/azure/mt588471.aspx)。

### 使用 PowerShell 建立原則定義

您可以使用 New-AzureRmPolicyDefinition Cmdlet 建立新的原則定義，如下所示。下面範例會建立一個原則，只允許北歐和西歐中的資源。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{	"if" : {
    	    			    "not" : {
    	      			    	"field" : "location",
    	      			    		"in" : ["northeurope" , "westeurope"]
    	    			    	}
    	    		          },
    	      		    		"then" : {
    	    			    		"effect" : "deny"
    	      			    		}
    	    		    	}'    		

執行的輸出會儲存 $policy 物件中，以便稍後可在指派原則期間使用它。針對原則參數，也可以提供包含原則之.json 檔案的路徑，而不是指定內嵌原則，如下所示。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain 	regions" -Policy "path-to-policy-json-on-disk"


## 套用原則

### 使用 REST API 的原則指派

您可以透過[適用於原則指派的 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)，在所需範圍內套用原則定義。REST API 可讓您建立和刪除原則指派，以及取得現有指派的相關資訊。

若要建立新的原則指派，請執行：

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} 是原則指派的名稱。針對 api-version，請使用 *2015-10-01-preview*。

使用如下的要求內文：

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

如需範例與詳細資料，請參閱[適用於原則指派的 REST API](https://msdn.microsoft.com/library/azure/mt588466.aspx)。

### 使用 PowerShell 指派原則

您可以使用 New-AzureRmPolicyAssignment Cmdlet 將上面透過 PowerShell 建立的原則套用至所需的範圍，如下所示：

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
以下 $policy 是由於執行 New-AzureRmPolicyDefinition Cmdlet 而傳回的原則物件，如下所示。此處的範圍是您指定之資源群組的名稱。

如果想要移除上述原則指派，您可以執行如下動作：

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

您可以分別透過 Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition 和 Remove-AzureRmPolicyDefinition Cmdlet 取得、變更或移除原則定義。

同樣地，您可以分別透過 Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment 和 Remove-AzureRmPolicyAssignment 取得、變更或移除原則指派。

<!---HONumber=Nov15_HO3-->