<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# 使用 .NET 後端行動服務

本文提供如何在 Azure 行動服務中使用定義行動服務的 .NET 後端 Visual Studio 專案的詳細資訊以及範例。本主題分成這些小節：

-   [簡介][簡介]
-   [資料表作業][資料表作業]

    -   [作法：註冊資料表作業][作法：註冊資料表作業]
    -   [作法：覆寫預設回應][作法：覆寫預設回應]
    -   [作法：覆寫執行成功][作法：覆寫執行成功]
    -   [作法：覆寫預設的錯誤處理][作法：覆寫預設的錯誤處理]
    -   [作法：新增自訂參數][作法：新增自訂參數]
    -   [作法：使用資料表使用者][作法：使用資料表使用者]
-   [自訂 API][自訂 API]

    -   [作法：定義自訂 API][作法：定義自訂 API]
    -   [作法：實作 HTTP 方法][作法：實作 HTTP 方法]
    -   [作法：以 XML 傳送及接收資料][作法：以 XML 傳送及接收資料]
    -   [作法：在自訂 API 中使用 user 和 header][作法：在自訂 API 中使用 user 和 header]
    -   [作法：在自訂 API 中定義多個路由][作法：在自訂 API 中定義多個路由]
-   [工作排程器][工作排程器]

    -   [作法：定義排程工作指令碼][工作排程器]
-   [原始檔控制、共用程式碼及協助程式函數][原始檔控制、共用程式碼及協助程式函數]

    -   [作法：載入 Node.js 模組][作法：載入 Node.js 模組]
    -   [作法：使用協助程式函數][作法：使用協助程式函數]
    -   [作法：使用原始檔控制共用程式碼][作法：使用原始檔控制共用程式碼]
    -   [作法：使用應用程式設定][作法：使用應用程式設定]
-   [使用命令列工具][使用命令列工具]
-   [使用資料表][使用資料表]

    -   [作法：從指令碼存取資料表][作法：從指令碼存取資料表]
    -   [作法：執行大量插入][作法：執行大量插入]
    -   [作法：對應 JSON 類型至資料庫類型][作法：對應 JSON 類型至資料庫類型]
    -   [使用 Transact-SQL 存取資料表][使用 Transact-SQL 存取資料表]
-   [偵錯與疑難排解][偵錯與疑難排解]

    -   [作法：將輸出寫入行動服務記錄檔][作法：將輸出寫入行動服務記錄檔]

## <a name="intro"></a>簡介

在 .NET 後端行動服務中，您可以定義自訂業務邏輯的 JavaScript 程式碼，儲存在伺服器上並執行。將此伺服器指令碼指派給下列伺服器功能之一：

-   [指定資料表上的插入、讀取、更新或刪除作業][資料表作業]。
-   [排程的工作][工作排程器]。
-   [在自訂 API 中定義的 HTTP 方法][自訂 API]。

伺服器指令碼中主要函數的特徵依指令碼的使用情境而異。您也可以將常用指令碼定義為 nodes.js 模組，由指令碼共用。如需詳細資訊，請參閱[原始檔控制與共用程式碼][原始檔控制、共用程式碼及協助程式函數]。

如需個別伺服器指令碼物件和函數的說明，請參閱行動服務伺服器指令碼參考。

<!-- Anchors. -->

  [簡介]: #intro
  [資料表作業]: #table-scripts
  [作法：註冊資料表作業]: #register-table-scripts
  [作法：覆寫預設回應]: #override-response
  [作法：覆寫執行成功]: #override-success
  [作法：覆寫預設的錯誤處理]: #override-error
  [作法：新增自訂參數]: #access-headers
  [作法：使用資料表使用者]: #work-with-users
  [自訂 API]: #custom-api
  [作法：定義自訂 API]: #define-custom-api
  [作法：實作 HTTP 方法]: #handle-methods
  [作法：以 XML 傳送及接收資料]: #api-return-xml
  [作法：在自訂 API 中使用 user 和 header]: #get-api-user
  [作法：在自訂 API 中定義多個路由]: #api-routes
  [工作排程器]: #scheduler-scripts
  [原始檔控制、共用程式碼及協助程式函數]: #shared-code
  [作法：載入 Node.js 模組]: #modules-helper-functions
  [作法：使用協助程式函數]: #helper-functions
  [作法：使用原始檔控制共用程式碼]: #shared-code-source-control
  [作法：使用應用程式設定]: #app-settings
  [使用命令列工具]: #command-prompt
  [使用資料表]: #working-with-tables
  [作法：從指令碼存取資料表]: #access-tables
  [作法：執行大量插入]: #bulk-inserts
  [作法：對應 JSON 類型至資料庫類型]: #JSON-types
  [使用 Transact-SQL 存取資料表]: #TSQL
  [偵錯與疑難排解]: #debugging
  [作法：將輸出寫入行動服務記錄檔]: #write-to-logs
