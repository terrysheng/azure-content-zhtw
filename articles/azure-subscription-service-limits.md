<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure 訂用帳戶和服務限制、配額與限制" metaKeywords="雲端服務, 虛擬機器, 網站, 虛擬網路, SQL Database, 訂用帳戶, 儲存體" description="提供一般 Azure 訂用帳戶和服務限制的清單及最大值。" metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="jeffreyg" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jroth" />

# Azure 訂閱和服務限制、配額與限制

以下文件說明一些最常見的 Microsoft Azure 限制。請注意，本文件目前未涵蓋所有 Azure 服務。這些限制將隨著時間擴展並更新以涵蓋更多平台。

- [訂用帳戶限制](#subscription)
- [雲端服務限制](#webworkerlimits)
- [虛擬機器限制](#vmlimits)
- [網站限制](#websiteslimits)
- [網路功能限制](#networkinglimits)
- [儲存體限制](#storagelimits)
- [批次預覽版限制](#batchlimits)
- [DocumentDB 預覽版限制](#documentdblimits)
- [SQL Database 限制](#sqldblimits)
- [媒體服務限制](#mediaserviceslimits)
- [服務匯流排限制](#servicebuslimits)
- [Active Directory 限制](#adlimits)

> [WACOM.NOTE] 如果您想要將限制提升到「預設限制」****以上，您可以[免費提出線上客戶支援要求][azurelimitsblogpost]。您無法將限制提升到高於下表中所示的「上限值」****。如果沒有「上限」****欄，則表示指定的資源沒有可調整的限制。

##<a name="subscription"></a>訂閱限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>核心 (每一 <a href="http://msdn.microsoft.com/zh-tw/library/azure/hh531793.aspx">訂用帳戶)</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10,000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/azure/gg456328.aspx">共同管理員</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/storage-whatis-account/">儲存體帳戶</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-what-is/">雲端服務</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">虛擬網路</a> (每一訂用帳戶)<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/jj157100.aspx">區域網路</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>SQL Database 伺服器 (每一訂用帳戶)</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>SQL Database (每一伺服器)</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一訂閱的 DNS 伺服器</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一訂閱的保留 IP</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一訂閱託管的服務憑證</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/azure/jj156085.aspx">同質群組</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/azure-preview-portal-using-resource-groups/">資源群組</a> (每一訂用帳戶)</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/services/batch/">批次預覽</a> 帳戶 (每一地區的每一訂用帳戶)</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>雖然只使用部分核心，在計算核心限制時，仍會將「超小型」執行個體視為一個核心。

<sup>2</sup>每個虛擬網路支援一個單一虛擬網路閘道。

##<a name="webworkerlimits"></a>雲端服務限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/documentation/articles/cloud-services-what-is/">Web/背景工作角色 (每一部署)<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/gg557552.aspx#InstanceInputEndpoint">執行個體輸入端點</a> (每一部署)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/gg557552.aspx#InputEndpoint">輸入端點</a> (每一部署)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/zh-tw/library/gg557552.aspx#InternalEndpoint">內部端點</a> (每一部署)</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>具有 Web/背景工作角色的每個「雲端服務」均可有兩個部署，一個供生產環境使用，另一個供預備環境使用。另請注意，此限制是指個別角色的數目 (組態)，而不是每一角色的執行個體數目 (調整)。 

##<a name="vmlimits"></a>虛擬機器限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/zh-tw/documentation/services/virtual-machines/">虛擬機器</a> (每一雲端服務)<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>輸入端點 (每一雲端服務)<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>當您建立虛擬機器時，系統會自動建立雲端服務，包含此虛擬機器。之後您便可以在那個相同的雲端服務中加入多個虛擬機器。

<sup>2</sup>輸入端點是用來允許對容器雲端服務外部的虛擬機器進行通訊。相同雲端服務內的虛擬機器會自動允許在所有 UDP 和 TCP 通訊埠之間進行通訊，以實現內部通訊。

##<a name="websiteslimits"></a>網站限制

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>網路限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>電腦總數<sup>1</sup> (每一 <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">虛擬網路)</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>虛擬機器或角色執行個體的並行 TCP 連線</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>存取控制清單 (ACL) (每一端點)<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一虛擬網路的區域網路網站</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>電腦總數包括虛擬機器和 Web/背景工作角色執行個體。

<sup>2</sup>每個虛擬網路支援一個單一[虛擬網路閘道][gateway]。

<sup>3</sup>「虛擬機器」的「輸入端點」上支援 ACL。輸入和執行個體輸入端點上支援 Web/背景工作角色。

##<a name="storagelimits"></a>儲存體限制

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源<sup>1</sup></th>
   <th align="left" valign="middle">預設限制</th>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 TB</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 容器、資料表或佇列的大小上限</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限</p></td>
   <td valign="middle"><p>唯一的限制是 500 TB 儲存體帳戶容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用的大小上限</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用中的檔案數目上限</p></td>
   <td valign="middle"><p>檔案共用的唯一限制是 5 TB 總容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永久性磁碟最多 8 KB IOPS (基本層)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永久性磁碟最多 8 KB IOPS (標準層)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的總要求率 (假設 1KB 物件大小)</p></td>
   <td valign="middle"><p>每秒最多 20,000 個實體或訊息</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 的目標輸送量</p></td>
   <td valign="middle"><p>每秒最多 60 MB，或每秒最多 500 個要求</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一佇列的目標輸送量 (1 KB 訊息)</p></td>
   <td valign="middle"><p>每秒最多 2000 個訊息</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一資料表分割的目標輸送量 (1 KB 實體)</p></td>
   <td valign="middle"><p>每秒最多 2000 個實體</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限 (美國地區)</p></td>
   <td valign="middle"><p>如果已啟用 GRS，則為 10 Gbps<sup>3</sup> ，LRS 為 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限 (美國地區)</p></td>
   <td valign="middle"><p>如果已啟用 GRS，則為 20 Gbps<sup>3</sup> ，LRS 為 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限 (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果已啟用 GRS，則為 5 Gbps<sup>3</sup> ，LRS 為 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限 (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果已啟用 GRS，則為 10 Gbps<sup>3</sup> ，LRS 為 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup>如需有關這些限制的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標][storagelimits]。 

<sup>2</sup>對於「基本層」中的虛擬機器，請勿在儲存體帳戶中放入超過 66 個高度使用的 VHD，以避免達到 20,000 總要求率的限制 (20,000/300)。對於標準層中的虛擬機器，請勿在儲存體帳戶中放入超過 40 個高度使用的 VHD (20,000/500)。如需詳細資訊，請參閱 [Azure 的虛擬機器和雲端服務大小][vmsizes]。 

<sup>3</sup>GRS 的全名是 [Geo Redundant Storage][georedundantstorage] (異地備援儲存體)。LRS 的全名是 [Locally Redundant Storage][locallyredundantstorage] (本地備援儲存體)。請注意，GRS 也是本地備援。

##<a name="batchlimits"></a>批次預覽版限制

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>DocumentDB 預覽版限制

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>SQL Database 限制

如需 SQL Database 限制的詳細資訊，請參閱下列主題：

 - [Azure SQL Database 服務層 (版本) ][sqltiers]
 - [Azure SQL Database 服務層和效能層級][sqltiersperflevels]
 - [資料庫輸送量單位 (DTU) 配額][sqlDTU]
 - [SQL Database 資源限制][sqldblimits]

##<a name="mediaserviceslimits"></a>媒體服務限制

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>服務匯流排限制

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Active Directory 限制

針對 Azure Active Directory (AD)，請參閱下列主題：

 - [Azure Active Directory 服務限制和限制][adlimitsandrestrictions]

##<a name="seealso"></a>另請參閱

[了解 Azure 限制和增加][azurelimitsblogpost]

[Azure 的虛擬機器和雲端服務大小][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/zh-tw/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/zh-tw/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/zh-tw/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/zh-tw/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/zh-tw/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/zh-tw/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/zh-tw/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/zh-tw/library/azure/dn764971.aspx

<!--HONumber=35.2-->
