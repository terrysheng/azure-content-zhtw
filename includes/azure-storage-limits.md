<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">資源</th>
   <th align="left" valign="middle">預設限制</th>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 TB</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 容器、資料表或佇列的大小上限</p></td>
   <td valign="middle"><p>500 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限</p></td>
   <td valign="middle"><p>唯一的限制是 500 TB 儲存體帳戶容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用的大小上限</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>檔案共用中的檔案數目上限</p></td>
   <td valign="middle"><p>檔案共用的唯一限制是 5 TB 總容量</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永續性磁碟最大 8 KB IOPS (基本層虛擬機器)</p></td>
   <td valign="middle"><p>300<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一永續性磁碟最大 8 KB IOPS (標準層虛擬機器)</p></td>
   <td valign="middle"><p>500<sup>1</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的總要求率 (假設 1KB 物件大小)</p></td>
   <td valign="middle"><p>每秒實體或每秒訊息最多 20,000 個 IOPS</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一 Blob 的目標輸送量</p></td>
   <td valign="middle"><p>每秒最多 60 MB，或每秒最多 500 個要求</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一佇列的目標輸送量 (1 KB 訊息)</p></td>
   <td valign="middle"><p>每秒最多 2000 個訊息</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一資料表分割的目標輸送量 (1 KB 實體)</p></td>
   <td valign="middle"><p>每秒最多 2000 個實體</p></td>
</tr>
<tr>
   <td valign="middle"><p>單一檔案共用的目標輸送量 (預覽)</p></td>
   <td valign="middle"><p>每秒最多 60 MB</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限<sup>2</sup> (美國地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 10 Gbps，LRS 為 20 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限<sup>2</sup> (美國地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 20 Gbps，LRS 為 30 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸入上限<sup>2</sup> (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 5 Gbps，LRS 為 10 Gbps</p></td>
</tr>
<tr>
   <td valign="middle"><p>每一儲存體帳戶的輸出上限<sup>2</sup> (歐洲和亞洲地區)</p></td>
   <td valign="middle"><p>如果啟用 GRS<sup>3</sup>，則為 10 Gbps，LRS 為 15 Gbps</p></td>
</tr>
</table>

<sup>1</sup>儲存體帳戶的總要求率限制為 20,000 個 IOPS。如果虛擬機器會利用每一磁碟的最大 IOPS，那麼，為了避免可能的節流，請確定所有的虛擬機器的 VHD 的 IOPS 總數不超過儲存體帳戶限制 (20,000 個 IOPS)。

您可以根據交易限制大致計算單一儲存體帳戶所支援高度使用的磁碟數目。例如，針對基本層 VM，高度使用的磁碟數目上限為約 66 (每一磁碟 20,000/300 IOPS)，而針對標準層 VM，它大約為 40 (每一磁碟 20,000/500 IOPS)。不過，請注意，儲存體帳戶可支援更大的磁碟數目，只要不是全部在同一時間高度使用。

<sup>2</sup>\*輸入\*是指傳送至某個儲存體帳戶的所有資料 (要求)。*輸出*是指從某個儲存體帳戶接收的所有資料 (回應)。

<sup>3</sup>GRS 是指異地備援儲存體，而 LRS 則是指本地備援儲存體。

<!---HONumber=July15_HO5-->