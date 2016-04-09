#### 若要在主機上安裝 MPIO

1. 在 Windows Server 主機上開啟 [伺服器管理員]。根據預設，伺服器管理員會在 Administrators 群組成員登入執行 Windows Server 2012 R2 或 Windows Server 2012 的電腦時啟動。如果尚未開啟 [伺服器管理員]，請按一下 [開始] > [伺服器管理員]。

	![伺服器管理員](./media/storsimple-install-mpio-windows-server/IC740997.png)

2. 按一下 [伺服器管理員] > [儀表板] > [新增角色及功能]。這樣會啟動 [新增角色及功能] 精靈。

	![新增角色及功能精靈 1](./media/storsimple-install-mpio-windows-server/IC740998.png)

3. 在 [新增角色及功能] 精靈中，執行下列動作：

	- 在 [在您開始前] 頁面上，按 [下一步]。
	- 在 [選取安裝類型] 頁面上，接受 [角色型或功能型安裝] 的預設值。按 [下一步]。
	
		![新增角色及功能精靈 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
	- 在 [選取目的地伺服器] 頁面上，選擇 [從伺服器集區選取伺服器]。應該會自動探索主機伺服器。按 [下一步]。
	- 在 [選取伺服器角色] 頁面上，按 [下一步]。
	- 在 [選取功能] 頁面上，選取 [多重路徑 I/O]，然後按一下 [下一步]。
	
		![新增角色及功能精靈 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
	- 在 [確認安裝選項] 頁面上，確認選取項目，然後選取 [需要時自動重新啟動目的地伺服器]，如下所示。按一下 [Install]。
	
		![新增角色及功能精靈 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
	- 完成安裝時，您將會收到通知。按一下 [關閉] 即可關閉精靈。
	
		![新增角色及功能精靈 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

<!---HONumber=AcomDC_0323_2016-->