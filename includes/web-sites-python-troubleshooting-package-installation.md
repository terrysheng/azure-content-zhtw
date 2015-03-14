執行於 Azure 時，有些封裝不會使用 pip 進行安裝。這有可能只是封裝無法在 Python 套件索引上使用。也有可能是必須要有編譯器 (編譯器無法在執行 Azure 網站的機器上使用)。

在本節中，我們將探討如何處理這個問題。

### 要求輔助工具

如果封裝的安裝需要編譯器，您應該嘗試向封裝擁有者要求為封裝啟用輔助工具。

隨著 [Microsoft Visual C++ Compiler for Python 2.7][] 近期的推出，現在已較容易建立具有 Python 2.7 之原生程式碼的封裝。

### 建立輔助工具 (需要 Windows)

注意：使用此選項時，請務必使用與 Azure 網站上使用的平台/架構/版本 (Windows/32 位元/2.7 或 3.4) 相符的 Python 環境來編譯封裝。

如果封裝因需要編譯器而無法安裝，您可以在本機電腦上安裝編譯器，並建立封裝的輔助工具 (後續會納入您的儲存機制中)。

Mac/Linux 使用者：如果您沒有 Windows 電腦的存取權，請參閱[建立執行 Windows 的虛擬機器][]，以了解如何在 Azure 上建立 VM。您可以使用它來建立輔助工具、將其新增至儲存機制，並視需要捨棄 VM。 

針對 Python 2.7，您可以安裝 [Microsoft Visual C++ Compiler for Python 2.7][]。

針對 Python 3.4，您可以安裝 [Microsoft Visual C++ 2010 Express][]。

若要建立輔助工具，您將需要輔助工具封裝：

    env\scripts\pip install wheel

您將使用  `pip wheel` 來編譯相依性：

    env\scripts\pip wheel azure==0.8.4

這會在 \wheelhouse 資料夾中建立 .whl 檔案。將 \wheelhouse 資料夾和輔助工具檔案新增至您的儲存機制。

編輯您的 requirements.txt，在頂端新增 `--find-links` 選項。這會指示 pip 在進入 python 封裝索引之前，必須先在本機資料夾中找出完全相符的項目。

    --find-links wheelhouse
    azure==0.8.4
			
如果您想要在 \wheelhouse 資料夾中納入所有相依性，而且完全不使用 python 封裝索引，您可以在 requirements.txt 頂端新增 `--no-index`，以強制 pip 忽略封裝索引。

    --no-index

### 自訂安裝

您可以自訂部署指令碼，以使用替代的安裝程式 (例如 easy\_install) 在虛擬環境中安裝封裝。請查看 deploy.cmd，以檢視已註解化的範例。請確定這類封裝未列在 requirements.txt 中，以防止 pip 加以安裝。

將下列內容新增至部署指令碼：

    env\scripts\easy_install somepackage

您也可以使用 easy\_install 從 exe 安裝程式 (有些與 zip 相容，所以 easy\_install 加以支援) 進行安裝。將安裝程式新增至您的儲存機制，然後傳遞可執行檔的路徑，以叫用 easy\_install。

將下列內容新增至部署指令碼：

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### 在儲存機制中納入虛擬環境 (需要 Windows)

注意：使用此選項時，請務必使用與 Azure 網站上使用的平台/架構/版本 (Windows/32 位元/2.7 或 3.4) 相符的虛擬環境。

如果您在儲存機制中納入虛擬環境，將可藉由建立空檔案防止部署指令碼在 Azure 上執行虛擬環境管理：

    .skipPythonDeployment

建議您刪除網站上現有的虛擬環境，以防止虛擬環境在受到自動管理時出現殘留檔案。


[建立執行 Windows 的虛擬機器]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http:://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
<!--HONumber=42-->
