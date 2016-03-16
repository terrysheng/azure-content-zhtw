資源|預設限制|上限
---|---|---
[每一部署的 Web 背景工作角色](../articles/cloud-services/fundamentals-application-models.md#tellmecs)<sup>1</sup>|25|25
每一部署的[執行個體輸入端點](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint)|25|25
每一部署的[輸入端點](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint)|25|25
每一部署的[內部端點](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint)|25|25

<sup>1</sup>具有背景工作角色的每個雲端服務均可有兩個部署，一個供生產環境使用，另一個供接移環境使用。另請注意，此限制是指個別角色的數目 (組態)，而不是每一角色的執行個體數目 (調整)。

<!---HONumber=AcomDC_0211_2016-->