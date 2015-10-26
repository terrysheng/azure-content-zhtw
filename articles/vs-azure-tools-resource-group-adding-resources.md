<properties 
   pageTitle="將資源新增至 Azure 資源群組"
   description="了解如何使用 Visual Studio 將資源加入 Azure 資源群組。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="kempb" />

# 將資源新增至 Azure 資源群組

如果您需要將多個資源新增至資源群組，您可以在 Visual Studio 的 [JSON 大綱] 視窗中執行此作業。

## 將資源新增至資源群組

### 將資源新增至資源群組

1. 在 [方案總管] 中選擇的 JSON 檔案 Azure 資源群組。JSON 檔案會出現在編輯器中，並且 [JSON 大綱] 視窗也會出現在編輯器旁邊。如果您關閉 [JSON 大綱] 視窗，除非您在 JSON 範本檔案 (不是參數檔案) 的內容功能表上選擇 [顯示外框] 命令，否則它將不會自動再次開啟。

    ![Azure 資源群組的 JSON 檔案](./media/vs-azure-tools-resource-group-adding-resources/arm-json-file.png)

1. 在 [JSON 大綱] 視窗中，選擇 [資源] 節點，然後選擇內容功能表上的 [加入新的資源] 命令，或選擇 [JSON 大綱] 視窗頂端的 [加入資源] 按鈕。

    ![將新的資源加入資源群組](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource.png)

1. 在 [加入資源] 對話方塊的資源清單中，選擇您要新增的資源、提供資源所需的資訊，然後選擇 [加入] 按鈕。例如，如果您新增儲存體帳戶，您必須為建立的參數提供基底名稱。
 
    ![[加入資源] 對話方塊](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-dialog.png)

    資源便會加入 [JSON 大綱] 視窗中的資源清單，而代表資源的新 JSON 便會出現在檔案中。也有可能會加入相關的參數及/或變數。


    ![新增至 JSON 檔案的資源](./media/vs-azure-tools-resource-group-adding-resources/arm-add-resource-json.png)

1. 將新的資源部署至 Azure 資源群組。在 [方案總管] 的資源群組專案的內容功能表上，選擇 [部署]，然後選擇資源群組的名稱。

    ![已部署 Azure 資源群組](./media/vs-azure-tools-resource-group-adding-resources/deploy-arm-resource-group.png)

    [部署到資源群組] 對話方塊隨即出現。


1. 選擇 [部署] 按鈕。

1. 如果有必須由您指定的任何參數，[編輯參數] 對話方塊隨即出現。輸入任何必要的值，然後選擇 [儲存] 按鈕。新的資源便會部署至 Azure 資源群組。

## 另請參閱

[建立和部署 Azure 資源群組部署專案](http://go.microsoft.com/fwlink/p/?LinkID=623073)

[Azure 資源管理員 Cmdlet](https://msdn.microsoft.com/library/dn654592.aspx)

[將 Windows PowerShell 與 Azure 資源管理員搭配使用](../powershell-azure-resource-manager/)

[Channel 9 影片：Azure 資源管理員](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B224#fbid=)

<!---HONumber=Oct15_HO3-->