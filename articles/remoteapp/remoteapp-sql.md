<properties
   pageTitle="SQL Azure 搭配 Azure RemoteApp | Microsoft Azure"
   description="了解如何使用 SQL Azure 搭配 Azure RemoteApp。"
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/07/2016"
   ms.author="elizapo"/>

# SQL Azure 搭配 Azure RemoteApp

當客戶選擇透過 Azure RemoteApp 在雲端託管其 Windows 應用程式時，他們通常也想將其資料 (例如 SQL Server) 移轉至雲端，以便進行整體雲端部署。如此一來，任何使用 Azure RemoteApp 的裝置即可隨時存取整個雲端託管解決方案。以下是連結和參考以及可協助您進行此程序的相關指引。

## 移轉 SQL 資料

本文以[將 SQL Server 資料庫移轉到 Azure SQL Database](sql-database-cloud-migrate.md) 開始，說明如何執行這項操作。

## 設定 Azure RemoteApp
在 Azure RemoteApp 中裝載 Windows 應用程式。以下是非常高階的逐步說明：

1.     建立 [Azure RemoteApp 範本 VM](remoteapp-imageoptions.md)。 
2.     在 VM 上安裝必要的應用程式。
3.     設定應用程式，使其連接至 SQL Database 並確認它可運作。
4.     執行 Sysprep 並關閉 VM擷取 VM 作為映像以便用於 Azure。**注意：**您需要確定應用程式能夠透過 sysprep 程序來保留資料庫連線資訊。如果應用程式無法保留資料庫連線資訊，您可以詢問應用程式的廠商，查看如何指定連接字串。
5.     將自訂映像匯入 Azure RemoteApp 程式庫並選取 SQL Azure 部署所在的適當地理位置。 
6.     使用上述範本，在與您的 SQL Azure 部署相同的資料中心部署 RemoteApp 集合，並發佈應用程式。在與您的 SQL Azure 部署相同的資料中心部署 RemoteApp，有助於確保最快速的連線速度和減少延遲。 

## 應用程式和 SQL 組態考量：
使用 Azure SQL 搭配 RemoteApp 時要考慮下列幾點：

了解[如何設定 Azure SQL Database 防火牆](sql-database-firewall-configure.md)。本文的摘要陳述：一開始，防火牆會封鎖對您的 Azure SQL Database 伺服器的所有存取。若要開始使用 Azure SQL Database 伺服器，您必須移至傳統入口網站並指定一或多個伺服器層級防火牆規則，啟用您的 Azure SQL Database 伺服器的存取。使用防火牆規則來指定允許網際網路的哪些 IP 位址範圍，以及 Azure 應用程式是否可以嘗試連接到 Azure SQL Database 伺服器。

此外，當電腦嘗試從網際網路連線到您的資料庫伺服器時，防火牆會根據整組伺服器層級和 (如有必要) 資料庫層級防火牆規則，檢查要求的原始 IP 位址。「如果要求的 IP 位址在伺服器層級防火牆規則中指定的其中一個範圍內，就會允許連線至您的 Azure SQL Database 伺服器。」 因此，我們可以使用 IP 範圍，而不只是個別的來源 IP 位址。

依照[如何：使用 Azure 入口網站在 SQL Database 上進行防火牆設定](sql-database-configure-firewall-settings.md)中的逐步指示執行。當您設定 SQL 防火牆規則時，請提供針對 Azure RemoteApp 集合指定的子網路 IP 範圍。這應該可讓 ARA 伺服器 (即使它們將有動態指派的 IP 位址) 連接到 SQL Databse。

## 疑難排解
如果使用 Azure RemoteApp 中裝載的用戶端應用程式連接到裝載於 Azure 或內部部署的 SQL Database 時速度緩慢，則可能有下列幾個原因。

- 從裝置至 Azure 的網路延遲時間很長。移至最佳且最快的網路連線，以獲得最佳效能。使用 [azurespeed.com](http://azurespeed.com/) 作為一般工具，以測試您的裝置對 Azure 資料中心的延遲。  
- 在 Azure RemoteApp 中裝載的用戶端應用程式承受壓力。選取不同的計費方案 (例如 Premium 計費) 會改善效能。另一個訣竅是監視您的應用程式正在使用的資源：在作用中工作階段期間，執行將啟動 SAS 畫面的 ctrl-alt-end 按鍵序列，選取 [工作管理員] 並觀察您的應用程式的資源使用率。
- SQL Server 承受壓力或未最佳化。遵循 SQL 疑難排解指引。 

<!---HONumber=AcomDC_0114_2016-->