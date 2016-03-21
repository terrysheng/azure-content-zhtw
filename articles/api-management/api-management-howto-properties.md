<properties 
	pageTitle="如何在 Azure API 管理原則中使用屬性" 
	description="了解如何在 Azure API 管理原則中使用屬性。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2016" 
	ms.author="sdanie"/>


# 如何在 Azure API 管理原則中使用屬性

API 管理原則是系統的強大功能，可讓發行者透過設定來變更 API 的行為。原則是陳述式的集合，會因 API 的要求或回應循序執行。原則陳述可以使用引述的文字、值、原則運算式及屬性來建構。

每個 API 管理服務執行個體都有服務執行個體全域適用的之鍵/值組的屬性集合。這個屬性可用來管理所有 API 組態及原則的常數字串值。每個屬性都有下列屬性。


| 屬性 | 類型 | 說明 |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| 名稱 | 字串 | 屬性的名稱。只能包含字母、數字、句點、破折號和底線字元。 |
| 值 | 字串 | 屬性的值。不能是空白或只由空白字元組成。 |
| Secret | 布林值 | 決定該值是否為密碼且是否應該加密。 |
| 標記 | 字串陣列 | 若有提供選用的標記，則可用來篩選屬性清單。 |

屬性是在發行者入口網站上的 [屬性] 索引標籤中設定。在以下範例中，設定了三個屬性。

![屬性][api-management-properties]

屬性值可以包含常值字串及[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)。下表顯示之前的三個範例屬性和其屬性。`ExpressionProperty` 的值是會傳回包含目前日期與時間之字串的運算式。`ContosoHeaderValue` 屬性已標記為密碼，所以未顯示其值。

| 名稱 | 值 | Secret | 標記 |
|--------------------|----------------------------|--------|---------|
| ContosoHeader | TrackingId | False | Contoso |
| ContosoHeaderValue | •••••••••••••••••••••• | True | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | False | |

## 使用屬性

若要在原則中使用屬性，請將屬性名稱放在雙大括號 (如 `{{ContosoHeader}}`) 內，如以下範例所示。

	<set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

在此範例中，`ContosoHeader` 是做為 `set-header` 原則中標頭的名稱，且 `ContosoHeaderValue` 是用來做為該標頭的值。當此原則在對 API 管理閘道提出要求或回應管理閘道期間被評估時，`{{ContosoHeader}}` 和 `{{ContosoHeaderValue}}` 會被其各自的屬性值取代。

如前一個範例所示，屬性可以做為完整的屬性或元素值使用，但它們也可以插入部分的常值文字運算式或與之結合，如以下範例所示：`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

屬性也可以包含原則運算式。以下範例使用 `ExpressionProperty`。

	<set-header name="CustomHeader" exists-action="override">
		<value>{{ExpressionProperty}}</value>
	</set-header>

當評估此原則時，`{{ExpressionProperty}}` 會替代為其值 `@(DateTime.Now.ToString())`。因為該值是原則運算式，所以會評估運算式，且原則會繼續執行。

您可以在開發人員入口網站測試此項目，方法是呼叫在範圍內有包含屬性支援則的作業。在下列範例中，已使用前兩個含屬性的範例 `set-header` 原則呼叫作業。請注意，該回應中包含兩個已使用含屬性原則所設定的自訂標頭。

![開發人員入口網站][api-management-send-results]

如果您查看包含之前兩個含屬性之範例原則的呼叫的 [API 檢查器追蹤](api-management-howto-api-inspector.md)，會看到兩個已插入屬性值的 `set-header` 原則，以及含原則運算式之屬性的原則運算式評估。

![API 檢查器追蹤][api-management-api-inspector-trace]

請注意，雖然屬性值可以包含原則運算式，但屬性值不能包含其他屬性。如果文字包含做為屬性值的屬性參照 (例如 `Property value text {{MyProperty}}`)，該屬性參照不會被取代，且將會包含做為屬性值的一部分。

## 建立屬性

若要建立屬性，請按一下 [屬性] 索引標籤 上的 [新增屬性]。

![新增屬性][api-management-properties-add-property-menu]

[名稱] 和 [值] 是必要值。如果此屬性值是密碼，請選取 [這是密碼] 核取方塊。輸入一或多個選擇性標籤來協助組織您的屬性，然後按一下 [儲存]。

![新增屬性][api-management-properties-add-property]

儲存新屬性之後，[搜尋屬性] 文字方塊會填入新屬性的名稱並且顯示新屬性。若要顯示所有屬性，請清除 [搜尋屬性] 文字方塊並按 Enter。

![屬性][api-management-properties-property-saved]

如需使用 REST API 建立屬性的詳細資訊，請參閱[使用 REST API 建立屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put)。

## 編輯屬性

若要編輯屬性，請按一下屬性旁邊的 [編輯] 來編輯。

![編輯屬性][api-management-properties-edit]

進行想要的變更，然後按一下 [儲存]。如果您變更屬性名稱，任何參照該屬性的原則會自動更新以使用新的名稱。

![編輯屬性][api-management-properties-edit-property]

如需使用 REST API 編輯屬性的詳細資訊，請參閱[使用 REST API 編輯屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch)。

## 刪除屬性

若要刪除屬性，請按一下屬性旁邊的 [刪除] 來刪除。

![刪除屬性][api-management-properties-delete]

按一下 [Yes, delete it] 加以確認。

![Confirm delete][api-management-delete-confirm]

>[AZURE.IMPORTANT] 如有任何原則參照該屬性，則您必須將該屬性從所有使用它的原則中移除，才能成功刪除該屬性。

如需使用 REST API 刪除屬性的詳細資訊，請參閱[使用 REST API 刪除屬性](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete)。

## 搜尋與篩選屬性

[屬性] 索引標籤包括可協助您管理屬性的搜尋與篩選功能。若要按照屬性名稱篩選屬性清單，請在 [搜尋屬性] 文字方塊中輸入搜尋字詞。若要顯示所有屬性，請清除 [搜尋屬性] 文字方塊並按一下 Enter。

![搜尋][api-management-properties-search]

若要按照標籤值篩選屬性清單，請在 [依標籤篩選] 文字方塊中輸入一或多個標籤。若要顯示所有屬性，請清除 [依標籤篩選] 文字方塊並按 Enter。

![篩選器][api-management-properties-filter]

## 後續步驟

-	深入了解原則的使用方式
	-	[API 管理中的原則](api-management-howto-policies.md)
	-	[原則參考文件](https://msdn.microsoft.com/library/azure/dn894081.aspx)
	-	[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)


[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

<!---HONumber=AcomDC_0309_2016-->