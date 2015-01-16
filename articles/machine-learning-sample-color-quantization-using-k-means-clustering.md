<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning 範例：使用 K-Means 叢集的色彩量化 | Azure" description="使用不同的 K 均值叢集值進行評估來量化彩色影像的範例 Azure Machine Learning 實驗。" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning 範例：使用 K-Means 叢集的色彩量化

>[AZURE.NOTE]
>在 ML Studio 中可取得與此模型相關聯的[範例實驗]和[範例資料集]。如需詳細資訊，請參閱下列各項。
[範例實驗]: #sample-experiment
[範例資料集]: #sample-dataset


##問題說明

[色彩量化](http://en.wikipedia.org/wiki/Color_quantization "Color quantization")是減少影像中相異色彩數量 (因而加以壓縮) 的程序。一般的目的是要盡可能保留影像的色彩呈現，同時減少色彩數量，無論是基於記憶體限制還是壓縮。 

##資料

在此範例實驗中，我們假設任何給定的 24 位元 RGB 影像都具有 256 x 256 x 256 種可能的色彩。當然，我們可以根據這些濃度值建置標準色彩分佈圖。但另一種方法是明確量化影像，並*減少uce*色彩數目：16 或 64。這會建立實質上更小的空間和 (理想上) 更少雜訊和變異。為此，我們將像素資料 (每個像素作為一個資料集資料列) 傳遞至 K-Means 叢集模組。 

##模型

建立的模型如下列影像所示：

![Model][image1]

我們在 5 個不同的分支中執行 K=10 到 500 的 K-Means 叢集。我們會先計算叢集，然後將叢集彙總至其像素色彩的平均值 (使用 R 指令碼)。 
最後，我們讓每個像素與彙總叢集色彩產生關聯，並以 CSV 格式送出新影像。同時，我們也使用原始影像計算新像素色彩的根均方差異，並將其顯示在 R 繪圖中 (使用「執行 R 指令碼」)。 

##結果

我們對不同數量的叢集 (色彩) 測試了其結果，如下方的實驗模型所示。您可以在下面看到，愈多叢集可建立更高品質且較少壓縮的影像：

||
------------ | ---------
**原始** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


我們也使用原始影像色彩的根均方差異測量了精確性，這可以從「執行 R 指令碼」模組的第二個輸出埠看出：

![Output of Execute R Script module][image3]

如圖所示，色彩叢集愈多，就有愈多色彩符合原始影像 (品質愈高)。 

##將影像轉換為 CSV 和反向操作的程式碼

為了將影像饋送至 ML Studio 中，我們撰寫了簡易的轉換器程式碼，用以將影像檔轉換成 ML Studio 可使用的 csv 格式，以及將其反向轉換為影像的程式碼。您可以自行取用下列程式碼。未來我們也預計要新增可讀取影像的模組。 

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Drawing;
	using System.Drawing.Imaging;
	using System.IO;
	 
	namespace Text2Image
	{
	    class Program
	    {
	        static void img2csv(string img_path)
	        {
	            FileInfo img_info = new FileInfo(img_path);
	            string destination_file_directory = img_info.DirectoryName + "\\";
	            string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".csv";
	 
	            // Read the image
	            Bitmap img = new Bitmap(img_path);
	 
	            // Create the CSV File and write the header values
	            System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
	            file.WriteLine("X,Y,R,G,B");
	 
	            // Write the Pixel values
	            for (int x = 0; x < img.Width; x++)
	                for (int y = 0; y < img.Height; y++)
	                {
	                    string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
	                    file.WriteLine(line);
	                }
	 
	            file.Close();
	        }
	 
	        static void csv2img(string csv_path)
	        {
	            FileInfo csv_info = new FileInfo(csv_path);
	            string destination_file_directory = csv_info.DirectoryName + "\\";
	            string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
	            string destination_file_path = destination_file_directory + destination_file_name + ".png";
	            
	            // Read all the lines in the CSV file
	            string[] lines = System.IO.File.ReadAllLines(csv_path);
	 
	            // set a new bitmap image with the provided width and height in the header
	            string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	            int img_width = Convert.ToInt32(wh[0])+1;
	            int img_height = Convert.ToInt32(wh[1])+1;
	 
	            Bitmap bmp_img = new Bitmap(img_width, img_height);
	 
	            for (int i = 1; i < lines.Length ;i++ )
	            {
	                string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
	                if (values.Length < 3)
	                    continue;
	 
	                int x = Convert.ToInt16(values[0]);
	                int y = Convert.ToInt32(values[1]);
	                int r = Convert.ToInt32(values[2]);
	                int g = Convert.ToInt32(values[3]);
	                int b = Convert.ToInt32(values[4]);
	 
	                bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
	            }
	 
	            bmp_img.Save(destination_file_path);
	        }
	 
	        static void Main(string[] args)
	        {
	            string source_path = args[1];
	            FileInfo source_info = new FileInfo(source_path);
	 
	            if (source_info.Extension == ".csv")
	                csv2img(source_path);
	            else
	                img2csv(source_path);
	        }
	    }
	}


## 範例實驗

在 ML Studio 中可取得與此模型相關聯的下列範例實驗：[**範例**] 索引標籤之下的 [**實驗**] 區段。

> **範例實驗 - 使用 K-Means 叢集的色彩型影像壓縮 - 開發**


## 範例資料集

在 ML Studio 中可取得此實驗所使用的下列範例資料集：[**儲存的資料集**] 之下的模組調色板。

###Bill Gates RGB 影像
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

