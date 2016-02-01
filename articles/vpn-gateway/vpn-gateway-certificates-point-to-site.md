<properties 
   pageTitle="使用 makecert 建立點對站 VPN 閘道交叉部署設定的自我簽署憑證 |Microsoft Azure"
   description="本文包含使用 makecert 在 Windows 10 上建立自我簽署根憑證的步驟。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/15/2016"
   ms.author="cherylmc" />

# 使用點對站設定的自我簽署根憑證

下列工作將協助您使用 makecert 建立根憑證，並從根憑證產生用戶端憑證。下列步驟專為在 Windows 10 上使用 makecert 所撰寫。

## 建立自我簽署根憑證

Makecert 是建立自我簽署根憑證的其中一種方式。下列步驟將逐步引導您使用 makecert 建立自我簽署根憑證。這些並非部署模型特定的步驟。它們同樣適用於資源管理員和傳統部署模型。

### 建立自我簽署根憑證

1. 從執行 Windows 10 的電腦下載並安裝[適用於 Windows 10 的 Windows 軟體開發套件 (SDK)](https://dev.windows.com/zh-TW/downloads/windows-10-sdk)。

2. 安裝之後，您可以在下列路徑中找到 makecert.exe 公用程式：C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>。
		
	範例：
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. 接下來，建立並安裝您的電腦上的個人憑證存放區中的根憑證。以下範例會建立您稍後將上傳的對應 .cer 檔案。以系統管理員身分執行下列命令，其中 RootCertificateName 是您想要用於憑證的名稱。

	請注意：如果執行以下範例不做任何變更，結果將是根憑證和對應檔案 RootCertificateName.cer。您可以在執行命令所在的目錄中找到 .cer 檔案。憑證會位於您的憑證 - 目前使用者\\個人\\憑證。

    	makecert -sky exchange -r -n "CN=RootCertificateName" -pe -a sha1 -len 2048 -ss My "RootCertificateName.cer"

	>[AZURE.NOTE]因為您已經建立會產生用戶端憑證的根憑證，您可能會想要將此憑證及其私密金鑰匯出，並將它儲存到可復原的安全位置。

## 產生、匯出及安裝用戶端憑證

您可以從自我簽署根憑證產生用戶端憑證，然後匯出及安裝用戶端憑證。下列並非部署模型特定的步驟。它們同樣適用於資源管理員和傳統部署模型。

### 從自我簽署根憑證產生用戶端憑證

下列步驟將引導您進行從自我簽署根憑證產生用戶端憑證的其中一種方法。您可以從相同根憑證產生多個用戶端憑證。然後每個用戶端憑證可以匯出及安裝在用戶端電腦上。

1. 在用來建立自我簽署根憑證的相同電腦上，以系統管理員身分開啟命令提示字元。

2. 將目錄切換至您要儲存用戶端憑證檔案的位置。RootCertificateName 是指您產生的自我簽署根憑證。如果您執行以下範例 (但將 RootCertificateName 變更為您的根憑證名稱)，就會在個人憑證存放區中產生一個名為 "ClientCertificateName" 的用戶端憑證。

3. 輸入以下命令：

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "RootCertificateName" -is my -a sha1

4. 所有憑證都會儲存在電腦上的憑證 - 目前使用者\\個人\\憑證存放區中。您可以視需要根據這個程序來產生許多用戶端憑證。

### 匯出用戶端憑證

1. 若要匯出用戶端憑證，請使用 certmgr.msc。以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。這樣會開啟 [憑證匯出精靈]。

2. 在精靈中，按 [下一步]，然後選取 [是，匯出私密金鑰]，然後按 [下一步]。

3. 在 [匯出檔案格式] 頁面上，您可以保留選取預設值。然後按 [下一步]。
 
4. 在 [安全性] 頁面上，您必須保護私密金鑰。如果您選擇要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。然後按 [下一步]。

5. 在 [要匯出的檔案] 中，**瀏覽**到您要匯出憑證的位置。對於 [檔案名稱]，為憑證檔案命名。然後按 [下一步]。

6. 按一下 [完成] 以匯出憑證。

### 安裝用戶端憑證

您想要使用點對站連線與虛擬網路連線的每個用戶端，都必須安裝用戶端憑證。此憑證是必要的 VPN 設定封裝以外的項目。下列步驟將引導您手動安裝用戶端憑證。

1. 找出 .pfx 檔案並複製到用戶端電腦。在用戶端電腦上按兩下 .pfx 檔案以安裝。將 [存放區位置] 保留為 [目前使用者]，然後按 [下一步]。

2. 在 [匯入檔案] 頁面上，請勿進行任何變更。按 [下一步]。

3. 在 [私密金鑰保護] 頁面上，如果您已使用則輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。

4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。

5. 按一下 [完成]。在憑證安裝的 [安全性警告] 上，按一下 [是]。現在已成功匯入憑證。

## 後續步驟

繼續使用您的點對站設定。

- 對於「資源管理員」部署模型步驟，請參閱 [Configure a Point-to-Site connection to a virtual network using PowerShell (使用 PowerShell 設定虛擬網路的點對站連線)](vpn-gateway-howto-point-to-site-rm-ps.md)。 
- 如需「傳統」部署模型步驟，請參閱[設定 VNet 的點對站 VPN 連線](vpn-gateway-point-to-site-create.md)。

<!---HONumber=AcomDC_0121_2016-->