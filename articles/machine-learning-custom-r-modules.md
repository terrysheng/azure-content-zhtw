<properties 
	pageTitle="在 Azure Machine Learning 中撰寫自訂 R 模組 | Azure" 
	description="在 Azure Machine Learning 中撰寫自訂 R 模組的快速入門。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"  
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/04/2015" 
	ms.author="bradsev" />


# 在 Azure Machine Learning 中撰寫自訂 R 模組

本主題概述如何在 Azure Machine Learning 中撰寫自訂 R 模組。它描述什麼是自訂 R 模組，以及使用哪些檔案定義它們。它說明如何在 Machine Learning 工作區中建構這些檔案，並註冊適用於部署的模組。接著，詳細說明用於自訂模組定義中的元素和屬性。此外，也概述如何使用輔助功能和檔案以及多個輸出。 

## 什麼是自訂 R 模組？
自訂模組是一種使用者定義的模組，可上傳至使用者的工作區，並在 Azure Machine Learning 實驗時執行。自訂 R 模組是在 Machine Learning 中執行使用者定義的 R 函數的自訂模組。R 是一種適用於統計運算和圖形的程式設計語言，由實作演算法的統計學家和資料科學家廣泛使用。Machine Learning 語言支援預設設定為在自訂模組中使用 R 語言。

自訂模組在 Azure Machine Learning 中為第一等狀態，也就是說，它們的用法就像其他任何模組一樣。它們可以與已發行實驗中隨附的其他模組一起執行或視覺化。使用者可以控制模組實作的演算法、要使用的輸入與輸出連接埠、模型參數，以及其他多種執行階段行為。


## 自訂 R 模組中的檔案
自訂 R 模組是由至少包含兩個檔案的 .zip 檔案所定義：

* 一個實作此模組所公開的 R 函數的檔案
* 一個描述自訂模組的 XML 定義檔案

其他輔助檔案也可以包含在提供可從自訂模組存取的功能的 .zip 檔案中。以下將討論此選項。

## 快速入門範例
此範例說明如何建構自訂 R 模組所需的檔案、將它們封裝在 zip 檔案中，然後在您的 Machine Learning 工作區中註冊模組。

請考慮自訂 [My Add Rows] 模組的範例，這個模組會修改從兩個資料集 (資料框架) 串連資料列 (觀察) 時所使用的 [Add Rows] 模組的標準實作。[Add Rows] 模組會使用 rbind 演算法，將第二個輸入資料集的資料列附加到第一個輸入資料集的結尾。自訂的  `myAddRows` 函數同樣會接受兩個資料集，但還會接受一個額外的布林交換參數做為輸入。如果交換參數是 **FALSE**，它會傳回與標準實作相同的資料集。但是，如果交換參數是 **TRUE**，它會將第一個輸入資料集的資料列附加到第二個資料集的結尾。實作 [My Add Rows] 模組所公開的 R myAddRows 函數的檔案包含下列 R 程式碼。

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (dataset)
	}

若要公開這個  `myAddRows` 函數做為 Azure Machine Learning 模組，必須建立 XML 定義檔以指定 [My Add Rows] 模組的外觀及運作方式。 

	<Module Version="v0.00.1" type="Public" insync="false" Owner="myName">
	  <GUID>{1CE529D1-B9D2-496F-AB42-8DBA60DE8279}</GUID>
	  <ID>myAddRows</ID>
	  <Name>My Add Rows</Name>	
	  <State>Custom</State>
	  <Description>This is my module description. </Description>
	  <Language Name="R" EntryPointFile="myAddRows.R" EntryPoint="myAddRows" />  
	    <Ports>
	      <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	      </output>
	      <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	      </input>
	      <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	      </input>
	    </Ports>
	    <Arguments>
	      <arg id="swap" display="swap" type="bool" >
	        <Description>Swaps inputs</Description>
	      </arg>
	    </Arguments>
	  <Category>My Category</Category>
	</Module>

 
請注意，XML 檔案中的 ID 元素內容必須完全符合函數名稱。 

將這兩個檔案儲存為  *myAddRows.R* 和  *myAddRows.xml*，然後將它們一起壓縮到  *myAddRows.zip* 檔案中。

若要在 Machine Learning 工作區中註冊這兩個檔案，請移至 Machine Learning Studio 中的工作區，按一下底部的 [**+ 新增**] 按鈕，然後選擇 [**模組 -> 從 ZIP 封裝**]，以上傳新的自訂 [My Add Rows] 模組。

![](http://i.imgur.com/RFJhCls.png)

[My Add Rows] 模組現在已經準備好，可供 Machine Learning 實驗存取。

## XML 定義檔中的元素

### 輸入和輸出
自訂模組的輸入和輸出是在 XML 定義檔的 [連接埠] 區段的子元素中指定。這些輸入和輸出元素的順序會決定使用者所經歷的配置 (UX)。列在 XML 檔案的 [連接埠] 元素中的第一個子系  將是 Machine Learning UX 中最左側的輸入連接埠。輸入和輸出連接埠支援的資料類型如下所示： 

**DataTable**：此類型會當做 data.frame 傳遞至 R 函數。事實上，Machine Learning 支援，且與 DataTable 相容的所有類型 (例如，CSV 檔或 ARFF 檔)   都會自動轉換為 data.frame。 

       <input id="dataset1" display="Input 1" type="DataTable" IsOptional="false">
           <Description>Input Dataset 1</Description>
       </input>

**Zip**：自訂模組可以接受 zip 檔做為輸入。此輸入會解壓縮到您函數的執行目錄中

       <input id="zippedData" display="Zip Input" type="Zip" IsOptional="false">
           <Description>Zip Input for port</Description>
       </input>

與每個輸入和輸出連接埠相關聯的 id 屬性都必須具有唯一值，而且這些值必須符合 R 函數中的具名參數。此外，必須為選擇性輸入的屬性指定預設值。IsOptional 屬性對於 DataTable 和 Zip 類型都是選擇性的，而且預設為 false。此預設值表示輸入類型不是選擇性的。


### 參數
自訂模組的參數是在 XML 定義檔的 [引數] 區段的子元素中指定。如同在 [連接埠] 區段中的子元素，[引數] 區段中的參數順序會定義 UX 中遇到的配置。列出的第一個參數將是第一個函數參數。Machine Learning 所支援的參數類型列示如下。選擇性的屬性必須在 XML 定義中指定其預設值。這些是使用函數時，若未指定參數值所使用的值。每種類型都會指出哪些屬性是選擇性的。


**int** - 整數 (32 位元) 類型的參數。

       <arg id="intValue1" display="My int Param" type="int" IsOptional="false" MinValue="0" MaxValue="100">
           <Description>Integer Parameter 1</Description>
       </arg>

IsOptional、MinValue 和 MaxValue 對於 int 是選擇性的。

**double** - double 類型的參數。

       <arg id="doubleValue1" display="My double Param" type="double" IsOptional="false" min="0.000" max="0.999" default="0.3">
           <Description>Double Parameter 1</Description>
       </arg>
IsOptional、min、max、default 對於 double 是選擇性的。

**bool** - 布林值參數，在 UX 中以核取方塊表示。

       <arg id="boolValue1" display="My boolean Param" type="bool" default="true">
           <Description>Boolean Parameter 1 </Description>
       </arg>

default 對於 bool 是選擇性的。

**string**：標準字串

        <arg id="stringValue1" display="My string Param" type="string" default="default value" IsOptional="true">
           <Description>String Parameter 1</Description>
        </arg>

default 和 IsOptional 對於 string 是選擇性的。

**ColumnPickerFor**：資料行選取參數。此類型在 UX 中會轉譯成資料行選擇器。您從中選擇資料行之 DataTable 的 id 應該取代類型屬性的值中的資料表部分。變數將會當做字串清單，傳遞至您的函數。 

        <arg id="columnSelection1" display="My Column Param" type="ColumnPickerFor:table">
           <Description>My column selector Param 1</Description>		
        </arg>

例如，如果我們擁有 id 為 dataset1 的 DataTable，此類型將包含 

		type="ColumnPickerFor:dataset1" 
                            
**enum:<DropDown Type ID>**：列舉的 (下拉式) 清單。選擇的值會當做字串，傳遞至 R 函數。此類型會要求先在 [引數] 區段內定義有效的列舉值。

       <DropDownType id="myDropDown1">
           <o id="red" display="Red"/>
           <o id="yellow" display="Yellow"/>
           <o id="blue" display="Blue"/>
       </DropDownType>
       <arg id="enum1" display="My Enum Param" default="red" type="enum:myDropDown1">
           <Description>My Enum Param 1</Description>
       </arg>

如同輸入和輸出，每個參數都必須有與其相關聯的 id 值。此外，id 值必須對應到 R 函數中的具名參數。在我們的快速入門範例中，相關聯的 id/參數是  *swap*。

### 語言定義
XML 定義檔中的 Language 元素會定義語言專屬的功能。對於 R 模組，一般我們有 

	<Language Name="R" EntryPointFile="myFunc.R" EntryPoint="myFunc" AddDisplayOutputPort="false"/>

這會指定特定的語言，在其中定義函數的檔案，以及該定義中的進入點。AddDisplayOutputPort 屬性對於 Language 元素是選擇性的。如同 [執行 R 指令碼] 模組 (連結未定)，如果您要新增可以用於視覺化繪圖/圖形的輸出連接埠，請為 AddDisplayOutputPort 標記選擇  *true*，就會顯示額外的輸出連接埠。 

### 輔助功能

有一些此範例未公開但自訂模組撰寫者可以使用的屬性。例如，模組可以有具決定性或不具決定性的行為。提供相同的輸入資料和相同的參數設定時，具決定性的模組將不會執行另一個時間。將會改用快取的結果，並傳播至所有下游的模組。Azure Machine Learning 中具決定性的模組範例是 [Add Rows] 模組。不具決定性的模組範例是 [Reader]。若要讓您的自訂模組不具決定性，請將下列屬性加入至您的定義以變更預設設定：

	<IsDeterministic>false</IsDeterministic>

### 輔助檔案

放在自訂模組 ZIP 檔案中的所有檔案在執行期間都可供使用。如果沒有出現目錄結構，將會保留該檔案。也就是說，檔案的獲得在本機和在 Azure Machine Learning 執行中的運作相同。 

例如，假設您要先移除資料集中包含 NA 的所有資料列以及所有重複的資料列，然後才在 myAddRows 中輸出該資料集，而且您已經撰寫一個在 removeDupNARows.R 檔案中執行該操作的 R 函數：

	removeDupNARows <- function(dataFrame) {
		#Remove Duplicate Rows:
		dataFrame <- unique(dataFrame)
		#Remove Rows with NAs:
		finalDataFrame <- dataFrame[complete.cases(dataFrame),]
		return(finalDataFrame)
	}
您可以使用 myAddRows 函數獲得 removeDupNARows.R 輔助檔案：

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
		source("removeDupNARows.R")
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	dataset <- removeDupNARows(dataset)
	return (dataset)
	}

然後上傳包含 'myAddRows.R'、'myAddRows.xml' 與 'removeDupNARows.R' 的 zip 檔，做為自訂 R 模組。

### 多個輸出

若要輸出多個支援的資料類型的物件，必須在 XML 定義檔中指定適當的輸出連接埠，而且必須以清單形式傳回物件。輸出物件將從左到右指派給輸出連接埠，以反映物件置於傳回清單中的順序。
 
例如，如果您想要將資料集 Dataset1 和 Dataset2 輸出到輸出連接埠資料集 (從左到右分別輸出 dataset1 和 dataset2)，請在 'myAddRows.xml' 檔案中定義輸出連接埠，如下所示：

	<Ports>
	    <output id="dataset" display="dataset" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset1" display="dataset1" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
		<output id="Dataset2" display="dataset2" type="DataTable">
	        <Description>Combined Data</Description>
	    </output>
	    <input id="Dataset1" display="Dataset1" type="DataTable">
		    <Description>Input dataset 1</Description>
	    </input>
	    <input id="Dataset2" display="Dataset2" type="DataTable">
		    <Description>Input dataset 2</Description>
	    </input>
	</Ports>

然後在清單中，以 'myAddRows.R' 中的正確順序，傳回物件清單：

	myAddRows <- function(Dataset1, Dataset2, swap=FALSE) {
	if (swap) { 
		dataset <- rbind(Dataset2, Dataset1))
	 } else { 
	  	dataset <- rbind(Dataset1, Dataset2)) 
	 } 
	return (list(dataset, Dataset1, Dataset2))
	}

## 執行環境

TBD

## 後續步驟

TBD

<!--HONumber=49-->