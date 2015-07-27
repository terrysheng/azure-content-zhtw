<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
   <th align="left" valign="middle">上限</th>
</tr>
<tr>
   <td valign="middle"><p>每一雲端服務的<a href="http://azure.microsoft.com/documentation/services/virtual-machines/">虛擬機器</a><sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一雲端服務的輸入端點<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>在服務管理 (而不是資源管理員) 中建立的虛擬機器會自動儲存在雲端服務中。您可以將更多虛擬機器加入至該雲端服務以獲得負載平衡和可用性。請參閱[如何連接虛擬機器與虛擬網路或雲端服務](../virtual-machines/cloud-services-connect-virtual-machine.md)。

<sup>2</sup>輸入端點可從虛擬機器的雲端服務外部通訊某個虛擬機器。在相同雲端服務或虛擬網路中的虛擬機器可自動彼此通訊。請參閱[如何設定虛擬機器的端點](../virtual-machines/virtual-machines-set-up-endpoints.md)。

<!---HONumber=July15_HO3-->