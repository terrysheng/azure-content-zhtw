<properties 
	pageTitle="如何設定媒體服務的電腦 - Azure" 
	description="了解將 Media Services SDK for .NET 用於媒體服務的必要條件。同時了解如何建立 Visual Studio 應用程式。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">設定電腦進行媒體服務開發</span></h1> 

本節包含使用 Media Services SDK for .NET 進行媒體服務開發的一般必要條件。也會讓開發人員了解如何建立 Visual Studio 應用程式來進行媒體服務 SDK 開發。 

### 必要條件

-   新的或現有 Azure 訂用帳戶中的媒體服務帳戶。請參閱[如何建立媒體服務帳戶](media-services-create-account.md)主題。
-   作業系統：Windows 7、Windows 2008 R2 或 Windows 8。
-   .NET Framework 4。
-   Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 (Professional、Premium、Ultimate 或 Express)。 
-   使用 [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) 封裝來安裝 Azure SDK for .NET。下一節顯示如何使用 **Nuget** 來安裝 Azure SDK。
  
<h2><a name="setup-account"></a><span class="short header">設定媒體服務的 Azure 帳戶</span></h2>

若要設定媒體服務帳戶，請使用 Azure 管理入口網站 (建議)。請參閱主題[如何建立媒體服務帳戶][]。在管理入口網站中建立帳戶之後，就可以準備設定電腦來進行媒體服務開發。 

### 在 Visual Studio 中建立應用程式

本節說明如何在 Visual Studio 中建立專案並設定來進行媒體服務開發。  在此案例中，專案是 C# Windows 主控台應用程式，但這裡顯示的設定步驟也適用於可以為媒體服務應用程式建立的其他專案類型 (例如，Windows Forms 應用程式或 ASP.NET Web 應用程式)。

   1. 建立新的 C# **主控台應用程式**   (在 Visual Studio 2013、Visual Studio 2012 或 Visual Studio 2010 SP1 中)。輸入 [**名稱**]、[**位置**] 和 [**方案名稱**]，然後按一下 [**確定**]。
   2. 確定將目標 Framework 設定為 .NET Framework 4。若要這麼做，請在 Visual Studio 專案上按一下滑鼠右鍵並選取 [屬性]。在 [應用程式] 索引標籤中，為目標架構設定 .NET Framework 4。   
   3. 加入 **System.Configuration** 組件的參考。若要新增 **System.Configuration** 的參考，請在 [**方案總管**] 中，以滑鼠右鍵按一下 [**參考**] 節點，然後選取 [**新增參考...**]。在 [**管理參考**] 對話方塊中，選取 [**System.Configuration**]，然後按一下 [**確定**]。
   4. 使用 [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) 套件，將參考新增至 **Azure SDK for .NET.**(所有其他相依組件也會安裝)。  

	若要使用 Nuget 加入參考，請執行下列動作。在 Visual Studio [**主要功能表**] 中，依序選取 [**工具**] -> [**Library Package Manager**] -> [**Package Manager Console**]。在主控台視窗中輸入 **Install-Package windowsazure.mediaservices** 然後按 **Enter** 鍵。
   4. 在 Program.cs 檔案的開頭，使用下列程式碼來覆寫現有的 using 陳述式。

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

現在，您可以開始開發媒體服務應用程式。    
   
<h2><a name="setup-account"></a><span class="short header">連線到媒體服務</span></h2> 

在媒體服務程式設計中，幾乎一切動作都需要參考伺服器內容物件。伺服器內容可讓您以程式設計方式存取所有媒體服務程式設計物件。

若要取得伺服器內容的參考，請建立內容類型的新執行個體，如下列程式碼範例所示。將媒體服務帳戶名稱和帳戶金鑰 (在帳戶設定過程中取得) 傳給建構函式。 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


通常最好定義 **CloudMediaContext** 類型的模組層級變數，以保存伺服器內容的參考。如需詳細資訊，請參閱[使用 Media Services SDK for .NET 連接到媒體服務](http://msdn.microsoft.com/library/azure/jj129571.aspx)。

本主題的其餘程式碼範例使用一個名為 **_context** 的變數來參考伺服器內容。 

<h2>後續步驟</h2>
現在，您已設定電腦並建立 Visual Studio 方案來進行媒體服務程式設計，請移至[如何建立加密資產和上傳至儲存體][]主題。
[如何建立媒體服務帳戶]: ../media-services-create-account/
[如何建立加密資產和上傳至儲存體]: ../media-services-create-encrypted-asset-upload-storage/


<!--HONumber=42-->
