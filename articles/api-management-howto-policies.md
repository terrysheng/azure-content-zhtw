<properties pageTitle="Policies in Azure API Management" metaKeywords="" description="Learn how to create, edit, and configure policies in API Management." metaCanonical="" services="" documentationCenter="API Management" title="Policies in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API 管理中的原則

在 Azure API 管理 (預覽) 中，原則是系統中一項強大的功能，可讓發行者透過組態來變更 API 的行為。原則是「陳述式」的集合，會在 API 的要求或回應上循序執行。常見的「陳述式」包括從 XML 至 JSON 的格式轉換，以及利用呼叫速率限制來限制開發人員傳入的呼叫數量。還有許多現成的原則可用。

如需原則陳述式及其設定的完整清單，請參閱[原則參考文件][]。

原則是在位於 API 取用者與受管理 API 之間的 Proxy 內套用。Proxy 會接收所有要求，然後通常原封不動地轉送至基礎 API。不過，原則可以套用至輸入要求和輸出要求。

## 如何設定原則

原則可以整體設定，或在[產品][]、[API][] 或[操作][]的範圍上設定。若要設定原則，請瀏覽至發行者入口網站的原則編輯器。

![Policies menu][]

原則編輯器由三個主要區段組成：原則範圍 (上)、供編輯原則的原則定義 (左) 和陳述式清單 (右)：

![Policies editor][]

若要開始設定原則，您必須先選取要套用原則的範圍。以下的螢幕擷取畫面中選取 [15 Day Free Trial] 產品。請注意，原則名稱旁邊的正方形符號表示此層級上已套用原則。

![Scope][]

因為已套用原則，定義檢視中會顯示組態。

![設定][]

原則最初會以唯讀顯示。為了編輯定義，請按一下 [設定原則] 動作。

![Edit][]

原則定義是一份簡單的 XML 文件，描述一連串輸入和輸出陳述式。可直接在定義視窗中編輯 XML。右邊提供陳述式的清單，而適用於目前範圍的陳述式會啟用並反白，如以上螢幕擷取畫面中的 [限制呼叫速率] 陳述式所示。

按一下已啟用的陳述式會在定義檢視中的游標位置上加入適當的 XML。

[原則參考文件][]中提供原則陳述式及其設定的完整清單。

例如，若要加入新的陳述式以限制只有指定的 IP 位址才能傳入要求，請將游標移至 "inbound" XML 元素的內容之內，然後按一下 [Restrict caller IPs] 陳述式。

![Restriction policies][]

這樣會將 XML 片段加入至 "inbound" 元素，以提供如何設定陳述式的指引。

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

若要限制輸入要求，只接受來自 IP 位址 1.2.3.4 的要求，請如下修改 XML：

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Save][]

完成設定原則的陳述式時，按一下 [儲存]，變更會立即傳播至 API 管理 Proxy。

# 了解原則組態

原則是針對要求和回應而依序執行的一連串陳述式。組態會適當地劃分成輸入 (要求) 和輸出 (原則)，如下列組態所示。

    <policies>
        <inbound>
            <!-- statements to be applied to the request go here -->
        </inbound>
        <outboud>
            <!-- statements to be applied to the response go here -->
        <outbound>
    </policies>

因為原則可以在不同層級上指定 (全域、產品、api 和操作)，所以組態可讓您相對於父系原則，指定此定義的陳述式的執行順序。

例如，如果您在全域層級有一個原則，還有一個為 API 設定的原則，則每當使用該特定的 API 時 - 兩個原則都會套用。API 管理可透過 base 元素來指定組合式原則陳述式的固定順序。

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

在上述的原則定義範例中，cross-domain 陳述式會在任何更高的原則之前執行，而這些原則後面又接著 find-and-replace 原則。

注意：全域原則沒有更高的原則，*base* 元素永遠是「無作業」，否則沒有作用。

  [原則參考文件]: ../api-management-policy-reference
  [產品]: ../api-management-howto-add-products
  [API]: ../api-management-howto-add-products/#add-apis
  [操作]: ../api-management-howto-add-operations
  [Policies menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
  [Policies editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
  [Scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
  [設定]: ./media/api-management-howto-policies/api-management-policies-configure.png
  [Edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
  [Restriction policies]: ./media/api-management-howto-policies/api-management-policies-restrict.png
  [Save]: ./media/api-management-howto-policies/api-management-policies-save.png
