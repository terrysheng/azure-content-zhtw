<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

# 疑難排解指南：建立 Azure 機器學習工作區並進行連線

本指南針對一些在設定 Azure ML 工作區時常發生的問題，提供解決方案。

## 工作區擁有者

當您建立新的 Azure ML 工作區時，您在 [工作區擁有者] 欄位中輸入的識別碼必須是有效的 Microsoft 帳戶 (之前稱為 Windows Live ID)，例如 <john-contoso@live.com> 或 <john-contoso@hotmail.com>。不可以使用非 Microsoft 帳戶，例如您的公司電子郵件帳戶。若要建立免費 Microsoft 帳戶，請前往 [www.live.com][]。

## 允許區域

Azure ML 目前在美國中南部區域提供公開預覽版本。如果您的訂用帳戶不包括美國中南部，您可能會看見「您沒有允許區域中的訂用帳戶。允許區域：美國中南部。」的錯誤訊息。

若要解決此問題，請從 Azure 入口網站選取 [連絡 Microsoft 支援] (如下所示)，然後在 [支援類型] 中選擇 [計費]，來將此區域加入您的訂用帳戶中。Azure ML 以後會新增更多區域。

![連絡 Microsoft 支援][]

## 儲存體帳戶

Azure ML 服務需要儲存體帳戶來儲存資料。您可以使用美國中南部中現有的儲存體帳戶，或是在建立新的 ML 工作區時建立新的儲存體帳戶 (如果您有建立新儲存體帳戶的配額的話)。若要查看您是否可以建立新的儲存體帳戶，請從 Azure 入口網站前往 [設定] 中的 [使用量]。

![建立工作區][]

新的 ML 工作區建立完成後，您可以使用指定作為工作區擁有者的 Microsoft 帳戶登入 ML Studio。如果出現「找不到工作區」的錯誤訊息 (如下方螢幕擷取畫面所示)，請執行下列步驟來修正此問題。

![找不到工作區][]

1.  清除瀏覽器的 cookie。

	如果您是使用 Internet Explorer，請按一下右上角的 [工具] 按鈕，然後選取 [網際網路選項\]。

    ![網際網路選項][]

    在 [一般] 索引標籤下，按一下 [刪除]

    ![一般索引標籤][]

    在 [刪除瀏覽歷程記錄] 對話方塊中，確定已選取 [Cookie 與網站資料]，然後按一下 [刪除]。

    ![刪除 cookie][]

2.  清除 cookie 後，請重新啟動瀏覽器，然後前往 [][]<https://studio.azureml.net></a>。出現輸入使用者名稱和密碼的提示時，請輸入指定作為工作區擁有者的同一個 Microsoft 帳戶。

我們的目標是要讓 Azure ML 能夠順利流暢地執行。請將任何建議或問題貼在下方或 [Azure ML forum][] 中，以協助我們為您提供更好的服務。

  [www.live.com]: http://www.live.com
  [連絡 Microsoft 支援]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
  [建立工作區]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
  [找不到工作區]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
  [網際網路選項]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
  [一般索引標籤]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
  [刪除 cookie]: ./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
  []: https://studio.azureml.net
  [Azure ML forum]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning
