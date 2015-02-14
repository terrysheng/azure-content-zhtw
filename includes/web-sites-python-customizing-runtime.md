Azure 會依照下列優先順序，決定要用於其虛擬環境中的 Python 版本：

1. 在根資料夾的 runtime.txt 中指定的版本
1. 網站組態中的 Python 設定 (Azure 入口網站上的 [設定] 頁面) 所指定的版本
1. 如果未指定任何上述項目，則會使用預設值 python 2.7

內容的有效值 

    \runtime.txt

是：

- python-2.7
- python-3.4

指定的微版本 (第三位數) 會被忽略。
<!--HONumber=42-->
