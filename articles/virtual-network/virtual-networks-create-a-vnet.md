<properties 
   pageTitle="建立虛擬網路" 
   description="逐步解說輕鬆建立基本虛擬網路的步驟。" 
   services="virtual-network" 
   documentationCenter="" 
   authors="cherylmc" 
   manager="adinah" 
   editor="tysonn"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/20/2015"
   ms.author="cherylmc"/>

# 建立虛擬網路 



當您建立虛擬網路時，您在 VNet 中的服務和 VM 就可以安全地彼此通訊，而不必經過網際網路。建立專用的純雲端虛擬網路是相當快速且簡單的程序。由於純雲端的虛擬網路不適用於跨單位連線，所以您不需取得和設定 VPN 裝置或驗證憑證。 

一旦建立虛擬網路之後，就可以在其中加入新的 VM 和 PaaS 執行個體。請注意，如果您使用管理入口網站來建立 VM，請務必選取 [從組件庫]****，如此就能指定虛擬網路。這很重要，因為您無法在建立 VM 之後加以回復，並將 VM 置於虛擬網路中。

[Azure Note] **使用此程序建立專用的純雲端虛擬網路。**由於建立跨單位組態的情況更複雜，因此，請勿使用此程序來建立後續將連線到內部部署網路的虛擬網路。如果想要在 Azure 和內部部署網路之間建立安全的跨單位連線，請參閱[關於安全的跨單位連線](https://msdn.microsoft.com/library/azure/dn133798.aspx)。

## <a name="CreateyourVNet">建立虛擬網路</a>

1. 登入**管理入口網站**。
2. 按一下螢幕左下角的 [新增]****。在導覽窗格中，按一下 [網路]****，然後按一下 [虛擬網路]****。按一下 [自訂建立]**** 以開始組態精靈。
3. 在 [虛擬網路詳細資料]**** 頁面上，輸入下列資訊，然後按一下右下角的下一步箭頭。如需詳細資料頁面中設定的詳細資訊，請參閱[關於使用管理入口網站設定虛擬網路](https://msdn.microsoft.com/library/azure/jj156074.aspx)的＜虛擬網路詳細資料＞****一節。
	-  **名稱：**為虛擬網路命名。當您部署 VM 和服務時，將會用到此虛擬網路名稱，因此，最好不要將名稱命名得太過複雜。

	-  **位置：**從下拉式清單中，選取所需的區域。您的虛擬網路將會在位於指定區域的 Azure 資料中心加以建立。



4. 不要在 [DNS 伺服器和 VPN 連線能力]**** 頁面上進行任何變更。只需按一下箭號，向前移到下一個頁面即可。根據預設，Azure 會為虛擬網路提供基本名稱解析。相較於基本 Azure 名稱解析可處理的情況來說，您的名稱解析需求很可能更複雜。在此情況下，您稍後可能想要將執行 DNS 的虛擬機器加入虛擬網路。如需 Azure 名稱解析和 DNS 的詳細資訊，請參閱[名稱解析](https://msdn.microsoft.com/library/azure/jj156088.aspx)。 
5. 在 [虛擬網路位址空間]**** 頁面中，輸入您想要針對此 VNet 使用的位址空間。除非您的 VM 需要特定的內部 IP 位址範圍，或者您想要為將接收靜態 DIP 的 VM 建立特定子網路，否則不需在此頁面上進行任何變更。如果您想建立多個子網路，就可以按一下此頁面上的 [加入子網路]**** 來執行這個動作。如需詳細資料頁面中設定的詳細資訊，請參閱[關於使用管理入口網站設定虛擬網路](https://msdn.microsoft.com/library/azure/jj156074.aspx)的＜虛擬網路詳細資料＞****一節。

	-  如需詳細資料頁面中設定的詳細資訊，請參閱[關於使用管理入口網站設定虛擬網路](https://msdn.microsoft.com/library/azure/jj156074.aspx)的＜虛擬網路詳細資料＞****一節。
	-  由於您不會利用跨單位 VPN 組態將此私人虛擬網路連接到內部部署網路，因此不需要在這些設定與您現有的內部部署網路 IP 位址範圍之間進行協調。如果您認為稍後可能想要建立跨單位組態，現在就必須在位址空間與已經存在於本機網站上的範圍進行協調，以避免發生路由問題。稍後變更範圍可能有點複雜，而且通常會導致必須重新部署


6. 按一下 [虛擬網路位址空間] 頁面右下角的勾號，即會開始建立您的虛擬網路。當虛擬網路建立完成時，您可以在管理入口網站的 [網路] 頁面 [狀態] 下看見 [已建立]。
7. 一旦建立虛擬網路之後，就可以部署到 VNet。例如，如果想要將 VM 部署到 VNet，請參閱[將虛擬機器加入至虛擬網路](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)。建立新的 VM 時，請務必選取 [從組件庫]****，以選取您的虛擬網路選項。請注意，如果您已經部署了現有的 VM 和 PaaS 執行個體部署，則無法將它們完全移至新的 VNet。這是因為您已在部署期間，為它們設定了網路組態設定。您必須將它們重新部署到新的 VNet。



## 後續步驟
-  [虛擬網路技術概觀](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

 
-  [將虛擬機器加入至虛擬網路](http://www.windowsazure.com/manage/services/networking/add-a-vm-to-a-virtual-network/)

-  [虛擬網路常見問題集](http://msdn.microsoft.com/library/windowsazure/dn133803.aspx)

-  [使用網路組態檔設定虛擬網路](http://azure.microsoft.com/documentation/articles/virtual-networks-using-network-configuration-file/)

-  [Azure 名稱解析概觀](http://go.microsoft.com/fwlink/?LinkId=248097)
 



<!--HONumber=47-->
 