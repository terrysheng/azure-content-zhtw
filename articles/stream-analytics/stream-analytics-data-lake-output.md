<properties
	pageTitle="串流分析 Data Lake Store 輸出 | Microsoft Azure"
	description="在串流分析工作中，設定 Azure Data Lake Store 的驗證和授權"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="03/18/2016"
	ms.author="jeffstok"
/>

# 串流分析 Data Lake Store 輸出

串流分析工作支援數種輸出方法，其中一個是 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)。Azure 資料湖存放區是容納巨量資料分析工作負載的企業級超大規模存放庫。Data Lake Store 可讓您存放任何大小、類型和擷取速度的資料，以便進行運作和探究分析。本文涵蓋串流分析 Azure 傳統入口網站中，Azure Data Lake Store 的授權、設定以及授權更新。

> [AZURE.NOTE] 目前**只有** Azure 傳統入口網站支援建立及設定 Data Lake Store 輸出。

## 授權 Data Lake Store 帳戶

1.  選取 Data Lake Store 作為 Azure 管理入口網站中的輸出時，系統將會提示您授權使用您現有的 Data Lake Store，或要求透過 Azure 傳統入口網站存取 Data Lake Store Preview。

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.jpg)

2.  如果您已經可以存取 Data Lake Store，按一下 [立即授權]，很快就會出現一個頁面，指出 「正在重新導向至授權...」。此頁面將會自動關閉，而且您會看到可讓您設定 Data Lake Store 輸出的頁面。

如果您尚未註冊 Data Lake Store 預覽，可以依照「立即註冊」連結起始要求，或依照[開始使用指示](../data-lake-store/data-lake-store-get-started-portal.md)進行。

## 設定 Data Lake Store 輸出屬性

驗證 Data Lake Store 帳戶之後，您可以設定 Data Lake Store 輸出的屬性。下表是設定 Data Lake Store 輸出的屬性名稱及其描述的清單。

<table>
<tbody>
<tr>
<td><B>屬性名稱</B></td>
<td><B>描述</B></td>
</tr>
<tr>
<td>輸出別名</td>
<td>此為易記名稱，用於在查詢中將查詢輸出指向這個 Data Lake Store。</td>
</tr>
<tr>
<td>Data Lake Store 帳戶</td>
<td>您傳送輸出的儲存體帳戶名稱。您將看到 Data Lake Store 帳戶的下拉式清單，登入入口網站的使用者可存取該下拉式清單。</td>
</tr>
<tr>
<td>路徑前置詞模式 [<I>選用</I>]</td>
<td>用來在指定的 Data Lake Store 帳戶中寫入檔案的檔案路徑。<BR>{date}、{time}<BR>範例 1：folder1/logs/{date}/{time}<BR>範例 2：folder1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [<I>選用</I>]</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [<I>選用</I>]</td>
<td>如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。目前唯一支援的值為 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料的序列化格式。支援 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>若為 CSV 或 JSON 格式，必須指定編碼。UTF-8 是目前唯一支援的編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>僅適用於 CSV 序列化。串流分析可支援多種序列化 CSV 資料常用的分隔符號。支援的值是逗號、分號、空格、索引標籤和分隔號。</td>
</tr>
<tr>
<td>格式</td>
<td>僅適用於 JSON 序列化。分隔的行會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。陣列會指定輸出將會格式化為 JSON 物件的陣列。</td>
</tr>
</tbody>
</table>

## 更新 Data Lake Store 授權

目前有一個限制，即每隔 90 天必須針對 Data Lake Store 輸出的所有工作，以手動方式更新驗證 Token。如果您在建立工作之後或上次驗證過後變更了密碼，您也必須重新驗證您的 Data Lake Store 帳戶。此問題發生時的徵兆就是沒有工作輸出，且作業記錄檔中出現錯誤，表示需要重新授權。

若要解決這個問題，請停止執行中的工作，並移至 Data Lake Store 輸出。按一下 [更新授權] 連結，很快就會出現一個頁面，指出 「正在重新導向至授權...」。此頁面將會自動關閉，而且如果成功，就會指出「已成功更新授權」。接著，您必須按一下頁面底部的 [儲存]，然後可以從上次停止的時間重新開始您的工作繼續，以避免資料遺失。

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.jpg)

<!---HONumber=AcomDC_0323_2016-->