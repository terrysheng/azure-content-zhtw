<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# 如何在 Azure 網站中建立 PHP 網站

本文說明如何使用 [Azure 管理入口網站][]、[適用於 Mac 和 Linux 的 Azure 命令列工具][]或 [Azure PowerShell Cmdlet][]，在 [Azure 網站][]中建立 PHP 網站。

大致上，在 Azure 網站中建立 PHP 網站和建立*任何*網站並無不同。依預設，所有網站都會啟用 PHP。如需有關設定 PHP (或提供您自己的自訂 PHP 執行階段) 的詳細資訊，請參閱＜[如何在 Azure 網站中設定 PHP][]＞。

下述每一個選項說明如何在共用託管環境中免費建立網站，但 CPU 使用量和頻寬使用量有所限制。如需詳細資訊，請參閱＜[Azure 網站定價][]＞。如需有關如何升級和調整網站的詳細資訊，請參閱[如何調整網站][]。

## 目錄

-   [使用 Azure 管理入口網站來建立網站][]
-   [使用適用於 Mac 和 Linux 的 Azure 命令列工具來建立網站][]
-   [使用 Azure PowerShell Cmdlet 來建立網站][]

## <a name="portal"></a>使用 Azure 管理入口網站來建立 PHP 網站

在 Azure 管理入口網站中建立網站時有三個選項：[快速建立]、[Create with Database] 和 [從組件庫]。以下指示適用於 [快速建立] 選項。如需其他兩個選項的詳細資訊，請參閱＜[建立 PHP-MySQL Azure 網站並使用 Git 部署][]＞和＜[從 Azure 中的組件庫建立 WordPress 網站][]＞。

若要使用 Azure 管理入口網站來建立 PHP 網站，請執行下列動作：

1.  登入 [Azure 管理入口網站][]。
2.  按一下頁面底部的 [新增]，然後依序按一下 [運算]、[網站] 和 [快速建立]。提供網站的 [URL]，並選取網站的 [地區]。最後，按一下 [建立網站]。

    ![Select Quick Create web site][]

## <a name="XplatTools"></a>使用適用於 Mac 和 Linux 的 Azure 命令列工具來建立 PHP 網站

若要使用適用於 Mac 和 Linux 的 Azure 命令列工具來建立 PHP 網站，請執行下列動作：

1.  依照此處的指示來安裝 Azure 命令列工具：[如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具][]。

2.  依照此處的指示來下載和匯入發行設定檔：[如何下載和匯入發行設定][]。

3.  從命令提示字元中執行下列命令：

        azure site create MySiteName

新建立網站的 URL 為 `http://MySiteName.azurewebsites.net`。

請注意，您可以搭配下列任何選項來執行 `azure site create` 命令：

-   `--location [location name]`。此選項可讓您指定要建立網站的資料中心位置 (例如，[美國西部])。如果省略此選項，此命令會提示您選擇位置。
-   `--hostname [custom host name]`。此選項可讓您指定網站的自訂主機名稱。
-   `--git`。此選項可讓您在本機應用程式目錄和網站的資料中心建立 Git 儲存機制，以利用 Git 來發行至網站。請注意，如果本機資料夾已經是 git 儲存機制，此命令會將新的遠端新增至現有的儲存機制，並指向網站資料中心裡的儲存機制。

如需其他選項的相關資訊，請參閱＜[如何建立和管理 Azure 網站][]＞。

## <a name="PowerShell"></a>使用 Azure PowerShell Cmdlet 來建立 PHP 網站

若要使用 Azure PowerShell Cmdlet 來建立 PHP 網站，請執行下列動作：

1.  依照此處的指示來安裝 Azure PowerShell Cmdlet：[開始使用 Azure PowerShell][]。

2.  依照此處的指示來下載和匯入發行設定檔：[作法：匯入發行設定][]。

3.  開啟 PowerShell 命令提示字元並執行下列命令：

        New-AzureWebSite MySiteName

新建立網站的 URL 為 `http://MySiteName.azurewebsites.net`。

請注意，您可以搭配下列任何選項來執行 `New-AzureWebSite` 命令：

-   `-Location [location name]`。此選項可讓您指定要建立網站的資料中心位置 (例如，[美國西部])。如果省略此選項，此命令會提示您選擇位置。
-   `-Hostname [custom host name]`。此選項可讓您指定網站的自訂主機名稱。
-   `-Git`。此選項可讓您在本機應用程式目錄和網站的資料中心建立 Git 儲存機制，以利用 Git 來發行至網站。請注意，如果本機資料夾已經是 git 儲存機制，此命令會將新的遠端新增至現有的儲存機制，並指向網站資料中心裡的儲存機制。

如需其他選項的相關資訊，請參閱＜[作法：建立和管理 Azure 網站][]＞。

## <a name="NextSteps"></a>後續步驟

現在，您已在 Azure 網站中建立 PHP 網站，您可以開始管理、設定、監視、部署至和調整網站。如需詳細資訊，請參閱下列連結：

-   [如何設定網站][]
-   [如何在 Azure 網站中設定 PHP][]
-   [如何管理網站][]
-   [如何監視網站][]
-   [如何調整網站][]
-   [使用 Git 來發行][]

如需完整的教學課程系列，請參閱＜[PHP 開發人員中心 - 教學課程][]＞頁面 (英文)。

  [Azure 管理入口網站]: http://manage.windowsazure.com/
  [適用於 Mac 和 Linux 的 Azure 命令列工具]: /en-us/develop/php/how-to-guides/command-line-tools/
  [Azure PowerShell Cmdlet]: /en-us/develop/php/how-to-guides/powershell-cmdlets/
  [Azure 網站]: /en-us/manage/services/web-sites/
  [如何在 Azure 網站中設定 PHP]: /en-us/develop/php/common-tasks/configure-php-web-site/
  [Azure 網站定價]: http://www.windowsazure.com/en-us/pricing/details/#header-1
  [如何調整網站]: /en-us/manage/services/web-sites/how-to-scale-websites/
  [使用 Azure 管理入口網站來建立網站]: #portal
  [使用適用於 Mac 和 Linux 的 Azure 命令列工具來建立網站]: #XplatTools
  [使用 Azure PowerShell Cmdlet 來建立網站]: #PowerShell
  [建立 PHP-MySQL Azure 網站並使用 Git 部署]: /en-us/develop/php/tutorials/website-w-mysql-and-git/
  [從 Azure 中的組件庫建立 WordPress 網站]: /en-us/develop/php/tutorials/website-from-gallery/
  [Select Quick Create web site]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [如何安裝適用於 Mac 和 Linux 的 Azure 命令列工具]: /en-us/develop/php/how-to-guides/command-line-tools/#Download
  [如何下載和匯入發行設定]: /en-us/develop/php/how-to-guides/command-line-tools/#Account
  [如何建立和管理 Azure 網站]: /en-us/develop/php/how-to-guides/command-line-tools/#WebSites
  [開始使用 Azure PowerShell]: /en-us/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [作法：匯入發行設定]: /en-us/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [作法：建立和管理 Azure 網站]: /en-us/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [如何設定網站]: /en-us/manage/services/web-sites/how-to-configure-websites/
  [如何管理網站]: /en-us/manage/services/web-sites/how-to-manage-websites/
  [如何監視網站]: /en-us/manage/services/web-sites/how-to-monitor-websites/
  [使用 Git 來發行]: /en-us/develop/php/common-tasks/publishing-with-git/
  [PHP 開發人員中心 - 教學課程]: /en-us/develop/php/tutorials/
