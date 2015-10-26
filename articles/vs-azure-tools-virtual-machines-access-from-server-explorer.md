<properties
   pageTitle="從伺服器總管存取 Azure 虛擬機器"
   description="取得如何在 Visual Studio 的 [伺服器總管] 中，檢視建立和管理 Azure 虛擬機器 (VM) 的概觀。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/30/2015"
   ms.author="kempb" />

# 從伺服器總管存取 Azure 虛擬機器

藉由使用 Visual Studio 中的 [伺服器總管]，您可以顯示 Azure 主控的虛擬機器相關資訊。

## 在 [伺服器總管] 中存取 Azure 虛擬機器

如果您有 Azure 主控的虛擬機器，您可以在 [伺服器總管] 中存取它們。您必須先登入您的 Azure 訂用帳戶，才能檢視您的行動服務。若要登入，請在 [伺服器總管] 中開啟 [Azure] 節點的捷徑功能表，並選擇 [連線到 Microsoft Azure]。

### 取得虛擬機器的相關資訊

1. 在 [伺服器總管] 中選擇虛擬機器，然後選擇 F4 鍵以顯示其屬性視窗。

    下表顯示可以使用的屬性，但他們全部都是唯讀。若要加以變更，請使用管理入口網站。

  	|屬性|說明|
  	|---|---|
  	|DNS 名稱|包含虛擬機器網際網路位址的 URL。|
  	|Environment|若是虛擬機器，這個屬性的值一定是 [生產]。|
  	|名稱|虛擬機器的名稱。|
  	|大小|虛擬機器的大小，此值會反映可用的記憶體和磁碟空間數量。如需詳細資訊，請參閱如何：設定虛擬機器大小。|
  	|Status|值包括 [啟動中]、[已啟動]、[停止中]、[已停止] 和 [正在擷取狀態]。如果出現 [正在擷取狀態]，則目前狀態是未知的。這個屬性的值不同於管理入口網站上所使用的值。|
  	|SubscriptionID|Azure 帳戶的訂用帳戶識別碼您可以透過檢視訂用帳戶的屬性，在管理入口網站上顯示這項資訊。|

1. 選擇端點節點，然後檢視 [屬性] 視窗。

1. 下表說明可用的端點屬性，但他們全部都是唯讀。若要新增或編輯虛擬機器的端點，請使用管理入口網站。

  	|屬性|說明|
  	|---|---|
  	|名稱|端點的識別碼|
  	|私人連接埠|網路內部存取應用程式的連接埠。|
  	|通訊協定|此端點的傳輸層所使用的通訊協定：TCP 或 UDP。|
  	|公用連接埠|提供公用存取應用程式使用的通訊埠。|

## 後續步驟

若要深入了解 Visual Studio 中的 Azure 角色使用方式，請參閱[搭配使用遠端桌面與 Azure 角色](vs-azure-tools-remote-desktop-roles.md)。

<!---HONumber=Oct15_HO3-->