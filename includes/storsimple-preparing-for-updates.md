<!--author=jgerend last changed: 03/16/16-->

## 準備更新
您必須在掃描和套用更新之前執行下列步驟：


1. 擷取裝置資料的雲端快照。

2. 請確認您的控制器固定 IP 能夠路由，且可以連線到網際網路。這些固定 IP 將用於裝置的服務更新。您可以從裝置的 Windows PowerShell 介面，在每個控制站上執行下列 Cmdlet 以進行測試：

 	`Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `

	**當固定 IP 能連線到網際網路時測試連線的範例輸出**


		Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

	    Source	  Destination 	IPV4Address      IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200
	    HCSNODE0  bing.com		204.79.197.200

		Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

	    Source	  Destination 	  IPV4Address    IPV6Address
	    ----------------- -----------  -----------
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200
	    HCSNODE0  204.79.197.200  204.79.197.200

在您成功完成這些手動預先檢查之後，您就可以繼續掃描和安裝更新。

<!---HONumber=AcomDC_0323_2016-->