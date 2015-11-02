<properties
   pageTitle="定義 Azure 資源管理員範本中的相依性"
   description="說明如何在部署期間，將某個資源設定為相依於另一個資源。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="mmercuri"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2015"
   ms.author="mmercuri"/>

# 定義 Azure 資源管理員範本中的相依性

對於指定的資源，可以有多個上游相依性和子相依性，這些相依性是您的拓撲成功的關鍵。您可以使用資源的 **dependsOn** 和 **resources** 屬性定義其他資源的相依性。相依性也可以使用 **reference** 函式指定。

    {
        "name": "<name-of-the-resource>",
        "type": "<resource-provider-namespace/resource-type-name>",
        "apiVersion": "<supported-api-version-of-resource>",
        "location": "<location-of-resource>",
        "tags": { <name-value-pairs-for-resource-tagging> },
        "dependsOn": [ <array-of-related-resource-names> ],
        "properties": { <settings-for-the-resource> },
        "resources": { <dependent-resources> }
    }

 也有可以定義資源之間關聯性的資源連結，以及定義資源群組中這些關聯性的支援。

## dependsOn

對於指定的虛擬機器，您可以依賴已經成功佈建的資料庫資源。在其他情況下，您可以依賴要在叢集中安裝的多個節點，然後再使用叢集管理工具部署虛擬機器。

在您的範本中，dependsOn 屬性可讓您為資源定義這個相依性。它的值可以是資源名稱的逗號分隔清單。評估資源與依相依順序部署資源之間的相依性。資源若不互相依賴，則會嘗試平行部署資源。

雖然您可能比較傾向於使用 dependsOn 對應您的資源之間的相依性，但是請務必了解為什麼您要這麼做，因為這可能會影響您部署的效能。例如，如果您這麼做是因為您想要記載資源互連的方式，則 dependsOn 並不是適當的方法。dependsOn 的生命週期只能用於部署，而且不適用於後續部署。一旦部署之後，就沒有任何方法可以查詢這些相依性。使用 dependsOn，表示您冒著影響效能的風險，可能會讓您不小心讓部署引擎轉為使用它可能會有的平行處理原則。若要記載資源之間的關聯性並提供查詢功能，您應該改用[連結資源](resource-group-link-resources.md)。

如果因為參考物件意味著資源的相依性而使用 reference 函式取得資源的表示法，則不需要這個元素。事實上，如果有使用 reference 和 dependsOn 的選項，此指導方針為使用 reference 函式並具備隱含的參考。再次強調，基本理由就是效能。參考定義已知為必要的隱含相依性，因為在範本內會參考這些相依性。相依性的存在表示它們彼此相關，避免再次針對效能最佳化，並避免轉移部署引擎以避免不必要地使用平行處理原則的潛在風險。

如果您需要定義某項資源和透過複製迴圈所建立之資源之間的相依性，可以將 dependsOn 項目設定為迴圈的名稱。例如，請參閱[在 Azure 資源管理員中建立多個資源的執行個體](resource-group-create-multiple.md)。

## 資源

resources 屬性可讓您指定與所定義的資源相關的子資源。子資源僅能定義為 5 個層級的深度。請務必注意，在子資源與父資源之間並不會建立隱含的相依性。如果您需要在父資源之後部署子資源，您必須使用 dependsOn 屬性明確地敘述該相依性。

## reference 函式

reference 函式可讓運算式從其他 JSON 名稱和值組或執行階段資源衍生其值。reference 運算式會隱含地宣告某個資源相依於另一個資源。以下 **propertyPath** 所代表的屬性是選擇性的，如果未指定，則參考資源。

    reference('resourceName').propertyPath

您可以使用此元素或 dependsOn 元素指定相依性，但是您不需要針對相同的相依資源使用兩者。本指導方針是使用隱含的參考以避免不小心讓不必要的 dependsOn 元素防止部署引擎以平行方式進行部署的風險。

若要深入了解，請參閱 [reference 函數](../resource-group-template-functions/#reference)。

## 後續步驟

- 若要了解如何建立 Azure 資源管理員範本，請參閱[撰寫範本](resource-group-authoring-templates.md)。 
- 如需在範本中可用函式的清單，請參閱[範本函式](resource-group-template-functions.md)。

<!---HONumber=Oct15_HO4-->