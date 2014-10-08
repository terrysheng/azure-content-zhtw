<properties title="SharePoint Server Farm" pageTitle="SharePoint Server Farm" description="Describes the new SharePoint Server Farm feature available in the Azure Preview Portal" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="josephd"></tags>

# SharePoint Server 伺服器陣列

使用 SharePoint Server 伺服器陣列時，Microsoft Azure 預覽入口網站會自動為您建立預先設定的 SharePoint Server 2013 伺服器陣列。當您在開發和測試環境中需要基本或高可用性 SharePoint 伺服器陣列時，或是您要評估將 SharePoint Server 2013 做為組織的共同作業方案時，這將可為您省下許多時間。

基本 SharePoint 伺服器陣列由下列組態中的三個虛擬機器組成：

![sharepointfarm][]

此伺服器陣列組態可用於 SharePoint 應用程式開發的簡化設定，或用於您第一次的 SharePoint 2013 評估。

高可用性 SharePoint 伺服器陣列由下列組態中的九個虛擬機器組成：

![sharepointfarm][1]

此伺服器陣列組態可用來測試較高的用戶端負載、外部 SharePoint 網站的高可用性，以及 SharePoint 伺服器陣列的 SQL Server AlwaysOn。此組態也可用於高可用性環境中的 SharePoint 應用程式開發。

如需這兩種伺服器陣列的組態詳細資料，請參閱 [SharePoint Server 伺服器陣列組態詳細資料][]。

這些伺服器陣列具有預先設定的端點，可讓已驗證的 Web 流量 (TCP 連接埠 80) 透過連接網際網路的用戶端電腦輸送到 SharePoint Web 伺服器。此端點連接到預先設定的團隊網站。若要存取此團隊網站，您必須先取得 SharePoint 伺服器陣列 Web 伺服器的網域名稱：

1.  在 Azure 預覽入口網站中，按一下 [瀏覽]，然後按一下 [虛擬機器]。
2.  在虛擬機器清單中，按一下您的 Web 伺服器名稱 (結尾為 SP、WEB1 或 WEB2)。
3.  在您的 Web 伺服器虛擬機器的窗格中，按一下 [屬性]。
4.  記下 [網域名稱] 中的 FQDN。
5.  從您的網際網路瀏覽器，存取 URL **http://[FQDN]**。以網域名稱 spfarm1-web1-contoso.cloudpapp.net 為例，其 URL 為 **<http://spfarm1-web1-contoso.cloudpapp.net>**。
6.  出現提示時，輸入您在建立伺服器陣列時所指定的使用者帳戶認證。

在「管理中心 SharePoint」網站中，您可以設定「我的網站」、SharePoint 應用程式和其他功能。如需詳細資訊，請參閱[設定 SharePoint 2013][]。若要存取「管理中心 SharePoint」網站，您必須先取得其外部連接埠號碼。

1.  在 Azure 預覽入口網站中，按一下 [瀏覽]，然後按一下 [虛擬機器]。
2.  在虛擬機器清單中，按一下基本組態的 SharePoint 伺服器名稱 (結尾為 SP)，或是可高用性組態的應用程式伺服器名稱 (結尾為 APP1 或 APP2)。
3.  在虛擬機器的窗格中，按一下 [屬性]。
4.  記下 [網域名稱] 中的 FQDN。
5.  關閉 [屬性] 窗格。
6.  在虛擬機器的窗格中，向下捲動至 [端點] 的清單。
7.  記下端點 SPCentralAdmin 的連接埠號碼。
8.  從您的網際網路瀏覽器，存取 URL **http://[FQDN]:[port number]**。以網域名稱 spfarm1-app1-contoso.cloudpapp.net 和外部連接埠號碼 54398 為例，URL 會是 **<http://spfarm1-app1-contoso.cloudpapp.net:54398>**。
9.  出現提示時，輸入您在建立伺服器陣列時所指定的使用者帳戶認證。

注意：

-   Azure 預覽入口網站會在您的訂閱內建立這些虛擬機器。
-   Azure 預覽入口網站會在僅限雲端、具有網際網路對向網站空間的虛擬網路中，同時建立這兩種伺服器陣列。並沒有站對站 VPN 連線會連回您的組織網路。
-   您可以透過遠桌面連線來管理這些伺服器。

## 逐步設定

若要使用 SharePoint 伺服器陣列建立您的 SharePoint 伺服器陣列，請執行下列動作：

1.  在 [Microsoft Azure 預覽入口網站][]中，按一下 [虛擬機器] \> [SharePoint Server 伺服器陣列]。
2.  在 [建立 SharePoint 伺服器陣列] 窗格中，輸入資源群組的名稱。
3.  為您伺服器陣列中每個虛擬機器的本機系統管理員帳戶，輸入使用者名稱和密碼。選擇難以猜測的名稱和密碼。
4.  如果您需要高可用性伺服器陣列，請按一下 [啟用高可用性]。
5.  若要設定網域控制站，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、樹系根網域名稱 (預設值為 contoso.com)，和網域控制站的大小 (預設值為 A1)。
6.  若要設定 SQL Server，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、SQL Server 的大小 (預設值為 A5)、資料庫存取帳戶名稱和密碼 (預設值是使用系統管理員帳戶)，以及 SQL Server 服務帳戶名稱 (預設值為 sqlservice) 和密碼 (預設值是使用與系統管理員帳戶相同的密碼)。
7.  若要設定 SharePoint 伺服器，請按一下箭頭。您可以指定主機名稱首碼 (預設值為資源群組名稱)、SharePoint 伺服器的大小 (預設值為 A23)、SharePoint 使用者帳戶 (預設值為 sp\_setup) 和密碼、SharePoint 伺服器陣列帳戶名稱 (預設值為 sp\_farm) 和密碼，以及 SharePoint 伺服器陣列複雜密碼。預設值是將系統管理員密碼用於 SharePoint 使用者帳戶、伺服器陣列帳戶和複雜密碼。
8.  若要設定選用組態 (您的虛擬網路或儲存體帳戶和診斷)，請按一下箭頭。
9.  若要指定訂閱，請按一下箭頭。
10. 若要指定位置 (區域)，請按一下箭頭。
11. 完成之後，請按一下 [建立]。

## Azure 資源管理員

SharePoint Server 伺服器陣列會使用 Azure 資源管理員和指令碼，自動建立這些 SharePoint 伺服器陣列的基礎結構和伺服器組態。如需詳細資訊，請參閱[搭配使用 Windows PowerShell 與資源管理員][]。

  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-azure-preview/SPFarm_HighAvail.png
  [SharePoint Server 伺服器陣列組態詳細資料]: ../virtual-machines-sharepoint-farm-config-azure-preview/

  [設定 SharePoint 2013]: http://technet.microsoft.com/library/ee836142.aspx
  [Microsoft Azure 預覽入口網站]: https://portal.azure.com/
  [搭配使用 Windows PowerShell 與資源管理員]: http://azure.microsoft.com/en-us/documentation/articles/powershell-azure-resource-manager/
