<properties
 pageTitle="將工作提交至 Azure 中的 HPC Pack 叢集 | Microsoft Azure"
 description="了解如何設定內部部署電腦，以將工作提交至 Azure 中的 HPC Pack 叢集"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 將 HPC 工作從內部部署電腦提交至 Azure 中的 HPC Pack 叢集

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

本文將說明如何設定執行 Windows 的內部部署用戶端電腦，使其執行 HPC Pack 工作提交工具，透過 HTTPS 與 Azure 中的 HPC Pack 叢集進行通訊。這可讓各種叢集使用者以直接而有彈性的方式將工作提交至雲端架構 HPC Pack 叢集，而無需直接連接到前端節點 VM 執行工作提交工具。

![將工作提交至 Azure 中的叢集][jobsubmit]

## 必要條件

* **在 Azure VM 中部署的 HPC Pack 前端節點** - 您可以使用 [Azure 快速入門範本](https://azure.microsoft.com/zh-TW/documentation/templates/)或 [Azure PowerShell 指令碼](virtual-machines-hpcpack-cluster-powershell-script.md) 等自動化工具來部署前端節點和叢集，或是以內部部署叢集所使用的相同方式在 Azure 中手動部署叢集。您必須要有前端節點的 DNS 名稱和叢集系統管理員的認證，才能完成本文中的步驟。

    如果您以手動方式部署前端節點，請確定已在 VM 中設定 HTTPS 端點。若非如此，請加以設定。請參閱[如何設定虛擬機器的端點](virtual-machines-set-up-endpoints.md)。

* **HPC Pack 安裝媒體** - 最新版 HPC Pack (HPC Pack 2012 R2)的免費安裝套件可從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=328024)取得。請確定您選取的是安裝在前端節點 VM 上的相同 HPC Pack 版本。

* **用戶端電腦** - 您必須要有可執行 HPC Pack 用戶端公用程式的 Windows 或 Windows Server 用戶端電腦 (請參閱[系統需求](https://technet.microsoft.com/library/dn535781.aspx))。如果您只想要使用 HPC Pack Web 入口網站或 REST API 來提交工作，您可以使用自行選擇的用戶端電腦。


## 步驟 1：在前端節點上安裝及設定 Web 元件

若要啟用透過 HTTPS 以 REST 介面將工作提交至叢集的功能，請在 HPC Pack 前端節點上安裝及設定 HPC Pack Web 元件 (如果尚未設定)。您必須先執行 HpcWebComponents.msi 安裝檔案，以安裝 Web 元件。然後，請執行 HPC PowerShell 指令碼 **Set-HPCWebComponents.ps1**，以設定元件。

如需詳細程序，請參閱[安裝 Microsoft HPC Pack Web 元件](http://technet.microsoft.com/library/hh314627.aspx)。

>[AZURE.TIP]如果您使用 [HPC Pack IaaS 部署指令碼](virtual-machines-hpcpack-cluster-powershell-script.md) 之類的自動化方法來建立叢集，您可以選擇性地安裝 Web 元件並將其設定為部署的一部分。

**安裝 Web 元件**

1. 使用叢集系統管理員的認證連接到前端節點 VM。

2. 從 HPC Pack 安裝程式資料夾，在前端節點上執行 HpcWebComponents.msi。

3. 依照精靈中的步驟安裝 Web 元件

**設定 Web 元件**

1. 在前端節點上，以系統管理員身分啟動 HPC PowerShell。

2. 若要將目錄切換至組態指令碼的位置，請輸入下列命令：

    ```
    cd $env:CCP_HOME\bin
    ```
3. 若要設定 REST 介面並啟動 HPC Web 服務，輸入下列命令：

    ```
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. 當系統提示您選取憑證時，請選擇與前端節點的公用 DNS 名稱相對應的憑證 (CN=&lt;*HeadNodeDnsName*&gt;.cloudapp.net)。

    >[AZURE.NOTE]您必須選取此憑證，後續才能工作從內部部署電腦提交至前端節點。請勿選取或設定與 Active Directory 網域中前端節點的電腦名稱相對應的憑證 (例如 CN=*MyHPCHeadNode.HpcAzure.local*)。

5. 若要設定 Web 入口網站以提交工作，請輸入下列命令：

    ```
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. 指令碼完成之後，請輸入下列命令，以停止並重新啟動 HPC 工作排程器服務：

    ```
    net stop hpcscheduler
net start hpcscheduler
    ```

## 步驟 2：在內部部署電腦上安裝 HPC Pack 用戶端公用程式

如果您尚未從 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=328024)將相容版本的 HPC Pack 安裝程式檔案下載到用戶端電腦，並選擇 HPC Pack 用戶端公用程式的安裝選項，請於此時執行。

若要使用 HPC Pack 用戶端工具將工作提交至前端節點 VM，您也必須從前端節點匯出憑證，並將它安裝在用戶端電腦上。憑證須採用 .CER 格式。

**從前端節點匯出憑證**

1. 在前端節點上，將 [憑證] 嵌入式管理單元新增至本機電腦帳戶的 Microsoft 管理主控台。如需新增嵌入式管理單元的步驟，請參閱[將憑證嵌入式管理單元新增至 MMC](https://technet.microsoft.com/library/cc754431.aspx)。

2. 在主控台樹狀目錄中，依序展開 [憑證 - 本機電腦]、[個人]，然後按一下 [憑證]。

3. 找出您在[步驟 1：在前端節點上安裝及設定 Web 元件](#step-1:-install-and-configure-the-web-components-on-the-head-node)中為 HPC Pack Web 元件設定的憑證 (例如，名為 &lt;*HeadNodeDnsName*&gt;.cloudapp.net)。

4. 在憑證上按一下滑鼠右鍵，按一下 [所有工作]，然後按一下 [匯出]。

5. 在 [憑證匯出精靈] 中按 [下一步]，然後確定已選取 [否，不要匯出私密金鑰]。

6. 依照精靈中的其餘步驟，以 DER 編碼的二進位 X.509 (.CER) 格式匯出憑證。


**在用戶端電腦上匯入憑證**


1. 將您從前端節點中匯出的憑證複製到用戶端電腦上的資料夾。

2. 在用戶端電腦上執行 certmgr.msc。

3. 在 [憑證管理員] 中，依序展開 [憑證 - 目前使用者]、[信任的根憑證授權單位]，以滑鼠右鍵按一下 [憑證]，按一下 [所有工作]，然後按一下 [匯入]。

4. 在 [憑證匯入精靈] 中按 [下一步]，然後依照步驟匯入您從前端節點匯出的憑證。



>[AZURE.SECURITY]您可能會看到安全性警告，因為用戶端電腦無法辨識前端節點上的憑證授權單位。基於測試目的，您可以忽略此警告並完成憑證匯入。

## 步驟 3：在叢集上執行測試工作

若要確認您的設定，請使用執行 HPC Pack 用戶端公用程式在內部部署電腦，嘗試在 Azure 中的叢集上執行工作。例如，您可以使用 HPC Pack GUI 工具或命令列命令，將工作提交至叢集。您也可以使用 Web 型入口網站來提交工作。


**在用戶端電腦上執行工作提交命令**


1. 在用戶端電腦上啟動命令提示字元。

2. 輸入範例命令。例如，若要列出叢集上的所有工作，請輸入下列命令

    ```
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    >[AZURE.TIP]在排程器 URL 中請使用前端節點的完整 DNS 名稱，而不是 IP 位址。如果您指定 IP 位址，則會看到如下的錯誤：「伺服器憑證必須具有有效的信任鏈結，或放在受信任的根存放區」。

3. 出現提示時，請輸入使用者名稱 (格式為 &lt;DomainName&gt;&lt;UserName&gt;) 和 HPC 叢集系統管理員或您所設定之其他叢集使用者的密碼。您可以選擇將認證儲存在本機，以供更多工作運用。

    工作清單隨即出現。


**在用戶端電腦上使用 HPC 工作管理員**

1. 如果您先前未將叢集使用者的網域認證儲存在用戶端電腦上，在提交工作時，您可能會在 [認證管理員] 中新增認證。

    a.在用戶端電腦的控制台中，啟動 [認證管理員]。

    b.按一下 [Windows 認證]，然後按一下 [新增一般認證]。

    c.指定網際網路位址 https://&lt;*HeadNodeDnsName*&gt;.cloudapp.net/HpcScheduler，然後提供使用者名稱 (格式為 &lt;DomainName&gt;&lt;UserName&gt;) 和 HPC 叢集系統管理員或您所設定之其他叢集使用者的密碼。

2. 在用戶端電腦上，啟動 [HPC 工作管理員]。

3. 在 [選取前端節點] 對話方塊中，以 https://&lt;*HeadNodeDnsName*&gt;.cloudapp.net 的格式輸入 Azure 中的前端節點的 URL。

    [HPC 工作管理員] 隨即開啟，並顯示前端節點上的工作清單。

**在前端節點上使用 Web 型工作入口網站**

1. 在用戶端電腦上啟動網頁瀏覽器，並輸入下列位址：```
    https://HeadNodeDnsName.cloudapp.net/HpcPortal
    ```
2. 在出現的安全性對話方塊中，輸入 HPC 叢集系統管理員的網域認證。(您也可以在不同的角色中新增其他叢集使用者。如需詳細資訊，請參閱[管理叢集使用者](https://technet.microsoft.com/library/ff919335.aspx)。)

    入口網站會開啟並顯示工作清單檢視。

3. 若要提交會從叢集傳回字串 "Hello World" 的範例工作，請按一下左側導覽列中的 [新增工作]。

4. 在 [新增工作] 頁面的 [來源提交頁面] 下，按一下 **HelloWorld**。工作提交頁面隨即出現。

5. 按一下 [提交]。出現提示時，請提供 HPC 叢集系統管理員的網域認證。工作提交後，工作 ID 會出現在 [我的工作] 頁面上。

6. 若要檢視您所提交之工作的結果，請按一下工作 ID，然後按一下 [檢視工作] 以檢視命令輸出 (在 [輸出] 下)。

## 後續步驟

* 您也可以使用 [HPC Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx) 將工作提交至 Azure 叢集。

* 如果您要從 Linux 用戶端提交叢集，請參閱 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756) 中的 Python 範例。


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-hpcpack-cluster-submit-jobs/jobsubmit.png

<!---HONumber=Oct15_HO3-->