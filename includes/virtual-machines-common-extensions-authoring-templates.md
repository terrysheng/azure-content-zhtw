## Azure 資源管理員範本概觀

Azure Resource Manager 範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。如需 Azure Resource Manager 範本的詳細概觀，請參閱下文：

[資源群組概觀](../resource-group-overview.md)

## VM 擴充功能的範例範本程式碼片段
若要將 VM 擴充功能部署到 Azure Resource Manager 範本中，您必須以宣告方式在範本中指定擴充功能組態。以下是用來指定延伸模組組態的格式。

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

您可以在以上程式碼中看到，延伸模組範本包含兩個主要部分：

1. 擴充功能名稱、發行者和版本
2. 延伸模組組態。

## 識別任何擴充功能的發行者、類型和 typeHandlerVersion

Azure VM 擴充功能是由 Microsoft 和受信任的第 3 方發行者所發佈，每個擴充功能會依其發行者、類型和 typeHandlerVersion 進行唯一識別。其判斷方式如下：

<!---HONumber=AcomDC_0323_2016-->