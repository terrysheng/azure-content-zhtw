您可以使用負載平衡器來為您在 Azure 中的工作負載提供高可用性。Azure 負載平衡器是 Layer-4 (TCP、UDP) 類型負載平衡器，可將連入流量分散在負載平衡器集合中定義之雲端服務或虛擬機器集中狀況良好的服務執行個體。
 
您可以設定負載平衡器。

- 對虛擬機器 (VM) 的連入網際網路流量進行負載平衡。我們將此案例中的負載平衡器稱為[網際網路面向的負載平衡器](../articles/load-balancer/load-balancer-internet-overview.md)。
- 虛擬網路 (VNet) 中 VM 之間、雲端服務中的 VM，或內部部署電腦與跨內部部署虛擬網路中的 VM 之間的負載平衡流量。我們將此案例中的負載平衡器稱為[內部負載平衡器 (ILB)](../articles/load-balancer/load-balancer-internal-overview.md)。
- 	將外部流量轉送到特定的 VM 執行個體。

<!---HONumber=AcomDC_0224_2016-->