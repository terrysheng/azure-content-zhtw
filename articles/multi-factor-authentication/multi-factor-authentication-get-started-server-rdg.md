<properties 
	pageTitle="使用 RADIUS 的遠端桌面閘道和 Azure Multi-Factor Authentication Server" 
	description="此 Azure Multi-Factor Authentication 頁面協助您部署使用 RADIUS 的遠端桌面 (RD) 閘道器和 Azure Multi-Factor Authentication Server。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# 使用 RADIUS 的遠端桌面閘道和 Azure Multi-Factor Authentication Server

在許多情況下，遠端桌面閘道器會使用本機 NPS 來驗證使用者。本文件說明如何將 RADIUS 要求從遠端桌面閘道器 (透過本機 NPS) 傳送至 Multi-Factor Authentication Server。

Multi-Factor Authentication Server 應該安裝在不同的伺服器，由它代理將 RADIUS 要求傳回到遠端桌面閘道器伺服器上的 NPS。NPS 驗證使用者名稱和密碼之後，它會將回應傳回給 Multi-Factor Authentication Server 來執行第二因素驗證，然後將結果傳回給閘道器。





## 設定 RD 閘道器

RD 閘道器必須設定為將 RADIUS 驗證傳送到 Azure Multi-Factor Authentication Server。一旦 RD 閘道器安裝、設定且運作之後，請移至 RD 閘道器屬性。移至 [RD CAP 存放區] 索引標籤，變更為使用執行 NPS 的中央伺服器，而不是執行 NPS 的本機伺服器。將一個或多個 Multi-Factor Authentication Servers 新增為 RADIUS 伺服器，並指定每一部伺服器的共用密碼。





## 設定 NPS

RD 閘道器使用 NPS 將 RADIUS 要求傳送到 Azure Multi-Factor Authentication。必須變更逾時，以避免 RD 閘道器在多因素驗證完成之前逾時。使用下列程序設定 NPS。

1. 在 NPS 中，展開左欄中的 [RADIUS 用戶端及伺服器] 功能表，然後按一下 [遠端 RADIUS 伺服器群組]。移至 [TS 閘道器伺服器群組] 的屬性。編輯顯示的 RADIUS 伺服器，然後移至 [負載平衡] 索引標籤。將 [要求被丟棄前，無回應的秒數] 和 [伺服器被識別為無法使用時，要求之間的間隔秒數] 變更為 30 至 60 秒。按一下 [驗證/帳戶] 索引標籤，確定指定的 RADIUS 連接埠符合 Multi-Factor Authentication Server 將接聽的連接埠。
2. NPS 也必須設定為接收 Azure Multi-Factor Authentication Server 傳回的 RADIUS 驗證。按一下左功能表中的 [RADIUS 用戶端]。將 Azure Multi-Factor Authentication Server 新增為 RADIUS 用戶端。選擇 [好記的名稱] 並指定共用密碼。
3. 展開左側導覽列中的 [原則] 區段，然後按一下 [連線要求原則]。其中應該包含設定 RD 閘道器時建立的一個連線要求原則，稱為 [TS 閘道器授權原則]。此原則會將 RADIUS 要求轉送到 Multi-Factor Authentication Server。
4. 複製此原則來建立新的原則。在新的原則中，加入條件來比對 [好記的用戶端名稱] 和上面步驟 2 為 Azure Multi-Factor Authentication Server RADIUS 用戶端設定的 [好記的名稱]。將 [驗證提供者] 變更為 [本機電腦]。此原則可確保收到來自 Azure Multi-Factor Authentication Server 的 RADIUS 要求時，就在本機進行驗證，而不會將 RADIUS 要求傳回給 Azure Multi-Factor Authentication Server，導致迴圈狀況。為了避免迴圈狀況，這項新原則的順序必須在轉送到 Multi-Factor Authentication Server 的原始原則「上方」。

## 設定 Azure Multi-Factor Authentication


--------------------------------------------------------------------------------



Azure Multi-Factor Authentication Server 設定為 RD 閘道器和 NPS 之間的 RADIUS Proxy。它應該安裝在 RD 閘道器伺服器之外另一部加入網域的伺服器上。使用下列程序來設定 Azure Multi-Factor Authentication Server。

1. 開啟 Azure Multi-Factor Authentication Server，然後按一下 [RADIUS 驗證] 圖示。核取 [啟用 RADIUS 驗證] 核取方塊。
2. 在 [用戶端] 索引標籤上，確定連接埠符合 NPS 中設定的連接埠，然後按一下 [新增...] 按鈕。新增 RD 閘道器伺服器 IP 位址、應用程式名稱 (選擇性) 和共用密碼。共用密碼在 Azure Multi-Factor Authentication Server 和 RD 閘道器上必須相同。
3. 按一下 [目標] 索引標籤，然後選擇 [RADIUS 伺服器] 選項按鈕。
4. 按一下 [新增...] 按鈕。輸入 NPS 伺服器的 IP 位址、共用密碼和連接埠。除非使用中央 NPS，否則 RADIUS 用戶端和 RADIUS 目標會相同。共用密碼必須符合 NPS 伺服器的 RADIUS 用戶端區段中設定的共用密碼。 

![Radius 驗證](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

<!---HONumber=AcomDC_0224_2016-->