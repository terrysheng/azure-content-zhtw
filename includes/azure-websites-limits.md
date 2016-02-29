資源|免費|共用 (預覽)|基本|標準|高階 (預覽)</th>
---|---|---|---|---|---
每個 [App Service 方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的 [Web、Mobile 或 API Apps](../services/app-service/)<sup>1</sup>|10|100|無限制<sup>2</sup>|無限制<sup>2</sup>|無限制<sup>2</sup>
每個 [App Service 方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)的[邏輯應用程式](../services/app-service/)</a><sup>1</sup>|10|10|10|每個核心 20 個|每個核心 20 個
[App Service 計劃](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|每個區域 1 個|每個資源群組 10 個|每個資源群組 10 個|每個資源群組 10 個|每個資源群組 10 個
計算執行個體類型|共用|共用|專用<sup>3</sup>|專用<sup>3</sup>|專用<sup>3</sup></p>
[相應放大](../articles/app-service-web/web-sites-scale.md) (執行個體上限)|1 個共用|1 個共用|3 個專用<sup>3</sup>|10 個專用<sup>3</sup>|50 個專用<sup>3,4</sup>
儲存體<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
CPU 時間 (天)<sup>6</sup>|60 Minuten|240 Minuten|無限制，以標準[費率](../pricing/details/app-service/)付費</a>|無限制，以標準費率付費|無限制，以標準費率付費
記憶體 (1 小時)|每個 App Service 方案 1024 MB|每個應用程式 1024 MB|N/A|N/A|N/A
頻寬|165 MB|無限制，套用[資料傳輸費率](../pricing/details/data-transfers/)|無限制，套用資料傳輸費率|無限制，套用資料傳輸費率|無限制，套用資料傳輸費率
應用程式架構|32 位元|32 位元|32 位元/64 位元|32 位元/64 位元|32 位元/64 位元
每個執行個體的 Web 通訊端<sup>7</sup>|5|35|350|無限|無限
並行[偵錯工具連接數](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) (每個應用程式)|1|1|1|5|5
[搭配 FTP/S 和 SSL 的 azurewebsites.net 子網域](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
[自訂網域](../articles/app-service-web/web-sites-custom-domain-name.md)支援||X|X|X|X
自訂網域 [SSL 支援](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|||無限|無限制，包含 5 個 SNI SSL 和 1 個 IP SSL 連接|無限制，包含 5 個 SNI SSL 和 1 個 IP SSL 連接
整合式負載平衡器||X|X|X|X
[永遠開啟](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[排定的備份](../articles/app-service-web/web-sites-backup.md)||||一天一次|每 5 分鐘一次<sup>8</sup>
[自動調整](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md)<sup>9</sup>|X|X|X|X|X
[Azure 排程器](../services/scheduler/)支援||X|X|X|X
[端點監視](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[預備位置 (預覽)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
每個應用程式的自訂網域</a>||500|500|500|500
SLA||<p>|99\.9%|99\.95%<sup>10</sup>|99\.95%<sup>10</sup>

<sup>1</sup>應用程式和儲存體配額是依每個 App Service 方案為準，除非另有指示。<sup>2</sup>您可以在這些電腦上裝載的實際應用程式數目，會視應用程式的活動、電腦執行個體的大小，及對應的資源使用率而定。<sup>3</sup>專用的執行個體可有不同的大小。如需詳細資訊，請參閱 [App Service 定價](../pricing/details/app-service/)。您可透過提出支援要求，來取得額外的執行個體。<sup>4</sup>使用 App Service 環境時，高階層最多允許 50 個計算執行個體 (受限於可用性) 和 500 GB 的磁碟空間時，否則為 20 個計算執行個體和 250 GB 儲存體。<sup>5</sup>儲存體限制是跨相同 App Service 方案中所有應用程式的目前總大小。您可透過提出支援要求來提高儲存體限制。<sup>6</sup>這些資源都會受到專用執行個體 (執行個體大小和執行個體數目) 上的實體資源限制。<sup>7</sup>如果您將基本層的一個應用程式調整為兩個執行個體，則其中每個執行個體有 350 個並行連線。<sup>8</sup>使用 App Service 環境時，高階層允許將備份間隔下調為每隔 5 分鐘，否則每天為 50 次。<sup>9</sup>在您的 App Service 執行個體中，以背景工作的方式隨選、依照排程或連續執行自訂可執行檔和/或指令碼。若要連續執行 WebJobs，「永遠開啟」是必要選項。若是排程 WebJobs，則 Azure 排程器免費或標準版本是必要項目。可在 App Service 執行個體中執行的 WebJob 數目沒有任何預先定義的限制，實際限制取決於應用程式程式碼嘗試執行的工作。<sup>10</sup>為部署提供的 99.95% 的 SLA，適用於使用專為容錯移轉設定 Azure 流量管理員的多個執行個體。

<!---HONumber=AcomDC_0218_2016-->