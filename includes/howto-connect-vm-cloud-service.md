<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#如何連接至雲端服務中的虛擬機器

當您建立虛擬機器時，系統會自動建立雲端服務，包含此虛擬機器。您可以在相同雲端服務內建立多部虛擬機器，以便虛擬機器彼此通訊。 

> [AZURE.NOTE] 當 VM 位於相同雲端服務時，您也可以平衡其負載並管理其可用性，但兩者都需要執行其他步驟。如需詳細資料，請參閱[虛擬機器負載平衡](../../articles/load-balance-virtual-machines/) 和[管理虛擬機器的可用性](../../articles/manage-availability-virtual-machines/). 

首先，您需要使用新的雲端服務建立虛擬機器。接著在相同的雲端服務中建立其他虛擬機器。這麼做可'連接'這些虛擬機器。 

1. 使用[如何建立自訂虛擬機器]中的步驟來建立第一部虛擬機器(../../articles/virtual-machines-create-custom/).

2. 遵循相同的基本程序來建立其他虛擬機器，但您將其加入至雲端服務，而非建立雲端服務時例外。例如，如果您建立了名為  *EndpointTest* 的雲端服務，請選擇該服務。下圖顯示：

	![將虛擬機器新增至現有的雲端服務](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. 完成此頁面上的其餘欄位，然後按一下核取記號以建立已連線的虛擬機器。

#資源

建立虛擬機器後，最好要新增資料磁碟，您的服務和工作負載才有地方可存放資料。執行下列其中一項：

[如何將資料磁碟連接至 Linux 虛擬機器](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[如何將資料磁碟附加至 Windows 虛擬機器](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=42-->
