<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
</tr>
<tr>
   <td valign="middle"><p>資料庫大小</p></td>
   <td valign="middle"><p>視效能層級而定 <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>登入</p></td>
   <td valign="middle"><p>視效能層級而定 <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>記憶體使用量</p></td>
   <td valign="middle"><p>16 MB 記憶體授權超過 20 秒</p></td>
</tr>
<tr>
   <td valign="middle"><p>工作階段</p></td>
   <td valign="middle"><p>視效能層級而定 <sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Tempdb 大小</p></td>
   <td valign="middle"><p>5 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>交易持續時間</p></td>
   <td valign="middle"><p>24 小時<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每筆交易鎖定</p></td>
   <td valign="middle"><p>100 萬</p></td>
</tr>
<tr>
   <td valign="middle"><p>每筆交易的大小</p></td>
   <td valign="middle"><p>2 GB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一交易使用的總記錄空間百分比</p></td>
   <td valign="middle"><p>20%</p></td>
</tr>
<tr>
   <td valign="middle"><p>最大並行要求 (背景工作執行緒)</p></td>
   <td valign="middle"><p>視效能層級而定 <sup>1</sup></p></td>
</tr>
</table>

<sup>1</sup>SQL Database 具有效能層，例如基本、標準和進階。標準和進階也分為不同效能層級。如需每一層和服務層級的詳細限制，請參閱 [Azure SQL Database 服務層和效能層級](https://msdn.microsoft.com/library/azure/dn741336.aspx)。

<sup>2</sup>如果交易鎖定基礎系統工作所需的資源，則最大持續時間為 20 秒。

<!---HONumber=July15_HO3-->