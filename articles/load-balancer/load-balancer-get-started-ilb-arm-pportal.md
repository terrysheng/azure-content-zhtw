<properties 
   pageTitle="開始使用 Azure 入口網站在資源管理員中建立內部負載平衡器 | Microsoft Azure"
   description="了解如何使用 Azure 入口網站在資源管理員中建立內部負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="joaoma" />

# 開始在 Azure 入口網站中建立內部負載平衡器

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## 開始使用 Azure 入口網站建立內部負載平衡器	

若要從 Azure 入口網站建立內部負載平衡器，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 在畫面的左上方，按一下 [新增] > [網路] > [負載平衡器]。
4. 在 [建立負載平衡器] 刀鋒視窗中，輸入負載平衡器的**名稱**。
5. 在 [配置] 中按一下 [內部]。
5. 按一下 [虛擬網路]，然後選取您要建立負載平衡器的虛擬網路。

>[AZURE.NOTE] 如果沒看到想要使用的虛擬網路，請檢查您為負載平衡器使用的**位置**，並據以變更它。

6. 按一下 [子網路]，然後選取您要建立負載平衡器的子網路。
6. 在 [IP 位址指派] 下，按一下 [動態] 或 [靜態]，視您想要讓負載平衡器的 IP 位址固定 (靜態) 或不固定而定。

>[AZURE.NOTE] 如果您選擇使用靜態 IP 位址，您必須提供負載平衡器的位址。
    
7. 在 [資源群組] 下，指定負載平衡器的新資源群組名稱，或按一下 [選取現有]，然後選取現有的資源群組。 
8. 按一下 [建立]。 

## 設定負載平衡規則 

負載平衡器建立之後，瀏覽至負載平衡器資源來設定它。您必須先設定後端位址集區和探查，然後再設定負載平衡規則。

### 步驟 1

設定後端集區︰

1. 在 Azure 入口網站中，按一下 [瀏覽] > [負載平衡器]，然後按一下您先前建立的負載平衡器。
2. 在 [設定] 刀鋒視窗中，按一下 [後端集區]。 
3. 在 [探查] 刀鋒視窗中，按一下 [加入]。
4. 在 [加入後端集區] 刀鋒視窗中，輸入後端集區的**名稱**，然後按一下 [確定]。

### 步驟 2 

設定探查︰
 
1. 在 Azure 入口網站中，按一下 [瀏覽] > [負載平衡器]，然後按一下您先前建立的負載平衡器。
2. 在 [設定] 刀鋒視窗中，按一下 [探查]。 
3. 在 [後端位址集區] 刀鋒視窗中，按一下 [加入]。
4. 在 [加入探查] 刀鋒視窗中，輸入探查的**名稱**。
5. 在 [通訊協定] 下，選取 [HTTP] \(適用於網站) 或 [TCP] \(適用於其他 TCP 型應用程式)。
6. 在 [連接埠] 下，指定用於存取探查的連接埠。
7. 在 [路徑] \(僅適用於 HTTP 探查) 下，指定要用來作為探查的路徑。
4. 在 [間隔] 下，指定探查應用程式的頻率。
5. 在 [狀況不良臨界值] 下，指定在將後端 VM 標示為狀況不良之前，應該失敗的嘗試次數。
5. 按一下 [確定] 以建立探查。

### 步驟 3

設定負載平衡規則：

1. 在 Azure 入口網站中，按一下 [瀏覽] > [負載平衡器]，然後按一下您先前建立的負載平衡器。
2. 在 [設定] 刀鋒視窗中，按一下 [負載平衡規則]。 
3. 在 [負載平衡規則] 刀鋒視窗中，按一下 [加入]。
4. 在 [加入負載平衡規則] 刀鋒視窗中，輸入規則的**名稱**。
5. 在 [通訊協定] 下，選取 [HTTP] \(適用於網站) 或 [TCP] \(適用於其他 TCP 型應用程式)。
6. 在 [連接埠] 下，指定用戶端在負載平衡器中連接的連接埠。
7. 在 [後端連接埠] 下，指定要用於後端集區的連接埠 (負載平衡器連接埠和後端連接埠通常會相同)。
8. 在 [後端集區] 下，選取您先前建立的後端集區。
9. 在 [工作階段持續性] 下，選取要保存工作階段的方式。
10. 在 [閒置逾時 (分鐘)] 下，指定閒置逾時。
11. 在 [浮動 IP (伺服器直接回傳)] 下，按一下 [停用] 或 [啟用]。
12. 按一下 [確定]。
 
## 後續步驟

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0316_2016-->
