<properties 
   pageTitle="使用 Visual Studio 存取 Azure 私人雲端"
   description="了解如何使用 Visual Studio 存取私人雲端資源。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/13/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 存取 Azure 私人雲端

##概觀

根據預設，Visual Studio 支援 Azure 公用雲端 REST 端點。不過，如果您打算搭配使用 Visual Studio 與 Azure 私人雲端，這可能會是個問題。您可以使用憑證來設定 Visual Studio 存取 Azure 私人雲端 REST 端點。您可以透過 Azure 發佈設定檔取得這些憑證。

## 存取 Visual Studio 中的 Azure 私人雲端

1. 在私人雲端的管理入口網站中下載發佈設定檔，或連絡您的系統管理員以取得發佈設定檔。在 Azure 的公用版本上，下載此檔案的連結是 [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/)。(下載檔案的副檔名應該是 .publishsettings)。

1. 在 Visual Studio 的 [伺服器總管] 中選擇 [Azure] 節點，並在捷徑功能表上選擇 [管理訂用帳戶] 命令。

    ![管理訂用帳戶命令](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. 在 [管理 Microsoft Azure 訂用帳戶] 對話方塊中，選擇 [憑證] 索引標籤，然後選擇 [匯入] 按鈕。

    ![正在匯入 Azure 憑證](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. 在 [匯入 Microsoft Azure 訂用帳戶] 對話方塊中，瀏覽至儲存發佈設定檔案的資料夾並選擇檔案，然後選擇 [匯入] 按鈕。這會將發佈設定檔中的憑證匯入到 Visual Studio。您現在應該能夠與私人雲端資源互動。

    ![正在匯入發佈設定](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## 後續步驟

[從 Visual Studio 發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[如何：下載和匯入發佈設定和訂用帳戶資訊](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

<!---HONumber=AcomDC_1217_2015-->