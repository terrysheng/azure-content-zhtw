1. 使用[從 Azure CLI 連接到 Azure](../articles/xplat-cli-connect.md) 中列出的步驟登入 Azure 訂用帳戶。

2. 請使用下列項目確定您處於服務管理模式中：

        azure config mode asm

3. 找出您想要從可用映像載入的 Linux 映像：

        azure vm image list | grep "Linux"

   在 Windows 命令提示字元視窗中，請使用 find 而不要使用 grep。

4. 從上述清單中透過 Linux 映像使用 `azure vm create` 建立新的虛擬機器。這個步驟會建立新的雲端服務以及新的儲存體帳戶。您也可以利用 `-c` 選項將此虛擬機器連接到現有的雲端服務。它也會建立 SSH 端點以利用 `-e` 選項登入 Linux 虛擬機器。

        ~$ azure vm create "MyTestVM" b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160314-zh-TW-30GB "adminUser" -z "Small" -e -l "West US"
        info:    Executing command vm create
        + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160314-zh-TW-30GB
        Enter VM 'adminUser' password:*********
        Confirm password: *********
        + Looking up cloud service
        info:    cloud service MyTestVM not found.
        + Creating cloud service
        + Retrieving storage accounts
        + Creating a new storage account 'mytestvm1437604756125'
        + Creating VM
        info:    vm create command OK

    >[AZURE.NOTE] 針對 Linux 虛擬機器，您必須提供 `vm create` 中的 `-e` 選項；建立虛擬機器之後就無法啟用 SSH。如需 SSH 的詳細資料，請參閱[如何在 Azure 上搭配使用 SSH 和 Linux](virtual-machines-linux-ssh-from-linux.md)。

    請注意，映像 *b39f27a8b8c64d52b05eac6a62ebad85\_\_Ubuntu-14\_04\_4-LTS-amd64-server-20160314-zh-TW-30GB* 正是在上述步驟中從映像清單中選擇的映像。*MyTestVM* 是新虛擬機器的名稱，而 *adminUser* 是我們將在虛擬機器中用於 SSH 的使用者名稱。您可以根據您的需求來取代這些變數。如需此命令的詳細資料，請造訪[搭配使用 Azure CLI 和 Azure 服務管理](virtual-machines-command-line-tools.md)。

5. 新建立的 Linux 虛擬機器會出現在清單中，前提是：

        azure vm list

6. 您可以使用命令來確認虛擬機器的屬性：

        azure vm show MyTestVM

7. 新建立的虛擬機器已經準備開始使用 `azure vm start` 命令。

如需所有 Azure CLI 虛擬機器命令的詳細資料，請閱讀[搭配使用 Azure CLI 與服務管理 API](../articles/virtual-machines-command-line-tools.md)。

<!---HONumber=AcomDC_0330_2016-->