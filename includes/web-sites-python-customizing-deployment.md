**如果這兩個條件都成立**，Azure 會決定您的應用程式將使用 Python：

- 根資料夾中的 requirements.txt 檔案
- 根資料夾中的任何 .py 檔案或指定 python 的 runtime.txt

在這種情況下，它會使用 Python 特定部署指令碼，執行檔案的標準同步處理，以及其他 Python 作業，例如：

- 自動管理虛擬環境
- 使用 pip 安裝列示於 requirements.txt 中的封裝
- 根據選取的 Python 版本建立適當的 web.config。
- 收集 Django 應用程式的靜態檔案

您可以控制某些方面的預設部署步驟，而不需要自訂指令碼。

如果您想略過所有的 Python 特定部署步驟，您可以建立下列空白檔案：

    \.skipPythonDeployment

如果您想要略過 Django 應用程式的靜態檔案集合：

    \.skipDjango 

若想加強控制部署，您可以建立下列檔案，以覆寫預設的部署指令碼：

    \.deployment
    \deploy.cmd

您可以使用 [Azure 命令列介面][]來建立檔案。從您的專案資料夾使用此命令：

    azure site deploymentscript --python

當這些檔案不存在時，Azure 會建立暫存的部署指令碼，並加以執行。它與您使用上述命令建立的指令碼完全相同。

[Azure 命令列介面]: http://azure.microsoft.com/downloads/
<!--HONumber=42-->
