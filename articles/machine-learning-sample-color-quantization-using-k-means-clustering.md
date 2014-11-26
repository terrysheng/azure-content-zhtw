<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning Sample: Color quantization using K-Means clustering | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning 範例：使用 K-Means 叢集的色彩量化

*您可以在 <b>[範例]</b> 索引標籤下的 <b>[實驗]</b> 區段中，找到與 ML Studio 中的這個模型相關聯的範例實驗。實驗名稱為：*

    Sample Experiment - Color Based Image Compression using K-Means Clustering - Development

## 問題說明

[色彩量化][色彩量化]是減少影像中相異色彩數量 (因而加以壓縮) 的程序。一般的目的是要盡可能保留影像的色彩呈現，同時減少色彩數量，無論是基於記憶體限制還是壓縮。

## 資料

在此範例實驗中，我們假設任何給定的 24 位元 RGB 影像都具有 256 x 256 x 256 種可能的色彩。當然，我們可以根據這些濃度值建置標準色彩分佈圖。但另一種方法是明確量化影像，並將色彩數*降低*至例如 16 或 64。如此，所建立的空間將會小得多，並且改善雜訊和變異的情形。為此，我們將像素資料 (每個像素作為一個資料集資料列) 傳至 K-Means 叢集模組。

## 模型

建立的模型如下列影像所示：

![Model][Model]

我們在 5 個不同的分支中執行 K=10 到 500 的 K-Means 叢集。首先我們計算叢集，然後將叢集彙總為其像素色彩的平均值 (使用 R 指令碼)。
最後，我們將每個像素與彙總的叢集色彩產生關聯，並以 CSV 格式將新影像送出。同時，我們也使用原始影像計算新像素色彩的根均方差異，並將其顯示在 R 繪圖中 (使用「執行 R 指令碼」)。

## 結果

我們對不同數量的叢集 (色彩) 測試了其結果，如下方的實驗模型所示。如圖所示，叢集愈多，所建立的影像品質就愈高，且壓縮愈低：


<table>
<tr><th>原始</th>
<td><img alt="Original" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg"></td>
</tr>
<tr><th>K=10</th>
<td><img alt="K=10" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png"></td>
</tr>
<tr><th>K=20</th>
<td><img alt="K=20" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png"></td>
</tr>
<tr><th>K=50</th>
<td><img alt="K=50" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png"></td>
</tr>
<tr><th>K=100</th>
<td><img alt="K=100" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png"></td>
</tr>
<tr><th>K=500</th>
<td><img alt="K=500" src="./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png"></td>
</tr>
</table>
我們也使用原始影像色彩的根均方差異測量了精確性，這可以從「執行 R 指令碼」模組的第二個輸出埠看出：

![Output of Execute R Script module][Output of Execute R Script module]

如圖所示，色彩叢集愈多，就有愈多色彩符合原始影像 (品質愈高)。

## 將影像轉換為 CSV 和反向操作的程式碼

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

  [色彩量化]: http://en.wikipedia.org/wiki/Color_quantization "色彩量化"
  [Model]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
  [Output of Execute R Script module]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png
