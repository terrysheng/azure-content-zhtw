1. 在入口網站中，移至 [新增]，然後移至 [瀏覽]。從清單中選取 [虛擬網路閘道]。
2. 按一下 [新增]。
3. 為您的閘道命名。這與為閘道子網路命名不同。這是閘道物件的名稱。 
4. 在 [虛擬網路] 中，選取您要連線到此閘道的 VNet。
5. 在 VNet 的設定中，針對 [公用 IP 位址] 值，為您的公用 IP 位址建立一個名稱。請注意，這並不是要求提供 IP 位址。IP 位址將會以動態方式指派。這是將對其指派位址之 IP 位址物件的名稱。 
6. 對於 [VPN 類型]，可以選擇原則式或路由式。請務必選取組態案例支援，且 (如果您的組態需要) 您計畫使用的 VPN 閘道裝置也支援的 VPN 閘道器類型。
7. 對於 [資源類型]，請選擇 [選取現有的]，並選擇您的 VNet 所在的資源群組，除非您的組態需要不同的選擇。
8. 對於 [位置]，請確定顯示的是您的資源群組和 VNet 存在的位置。
9. 按一下 [建立]。您將會在儀表板上看到 [部署虛擬網路閘道] 磚。建立閘道需要一些時間。有許多工作在背景執行。預計需要 15 分鐘以上。您可能需要重新整理入口網站頁面，才能看到完成的狀態。
10. 建立閘道之後，您可以查看入口網站中的虛擬網路，來檢視已指派給閘道的 IP 位址。閘道將會顯示為已連線的裝置。您可以檢視指派給閘道的名稱和 IP 位址。

<!---HONumber=AcomDC_0114_2016-->