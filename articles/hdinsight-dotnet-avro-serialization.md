<properties  linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Avro.NET Serialization" pageTitle="Serialize Data with Avro.NET | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro.NET to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize Data with Avro.NET " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

# 使用 Avro.NET 將資料序列化

## 概觀

本主題將說明如何使用 Microsoft Avro Library (Avro.NET) 將物件和其他資料結構序列化為位元組串流，以將其保存在記憶體、資料庫或檔案中。它會使用 JSON 實作 Apache Avro 資料序列化系統，以定義語言無從驗證的結構描述，而在必須使用還原序列化機制將物件復原時，提供可由多種語言 (目前有 C、C++、C#、Java、PHP、Python 和 Ruby) 處理的壓縮二進位資料交換格式。

Apache Avro 還原序列化格式廣泛運用在 Hadoop 環境中，包括 Microsoft HDInsight 在內。如需此格式的詳細資訊，請參閱「Apache Avro 規格」。

Microsoft HDInsight Avro.NET 程式庫支援兩種物件序列化方式：使用反映和使用一般記錄

## 必要條件

Microsoft .NET Library for Avro 會隨 NuGet 封裝而提供。從 Visual Studio 安裝：選取 **專案** 索引標籤 -> **管理 NGet 封裝...**、在 **線上搜尋** 方塊中搜尋 "Avro"，然後按一下 **Microsoft .NET Library for Avro** 旁邊的 **安裝** 按鈕。Newtonsoft.Json (>= .5.0.5) 相依性也會自動下載

### 大綱

案例：

* [使用反映進行序列化](#Scenario1)

* [使用 Avro 物件容器進行序列化](#Scenario2)

* [使用物件容器檔案進行序列化和使用反映進行序列化](#Scenario3)

* [使用物件容器檔案進行序列化和使用一般記錄進行序列化](#Scenario4)

* [使用物件容器檔案和自訂壓縮轉碼器進行序列化](#Scenario5)

<h2> <a name="Scenario1">使用反映進行序列化 </a></h2>


下列範例會依序將一個類別和一個結構序列化、還原序列化，最後與初始執行個體相比較，以確認身分識別。類型的 JSON 結構描述會由 Microsoft Avro Library 自動建置，並將資料壓縮屬性納入考量。Microsoft Avro Library 會使用[資料壓縮解析程式][1]識別要序列化的欄位。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set represented as an object using Reflection
    //No explicit schema definition is required - schema of serialized objects is automatically built
    public void SerializeDeserializeObjectUsingReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
    //for serializing only properties attributed with DataContract/DateMember
    var avroSerializer = AvroSerializer.Create<SensorData>();
    
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    //Create a data set using sample Class and struct 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };
    
    //Serialize the data to the specified stream
    avroSerializer.Serialize(buffer, expected);
    
      
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Deserialize data from the stream and cast it to the same type used for serialization
    var actual = avroSerializer.Deserialize(buffer);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify that deserialized data matches the original one
    bool isEqual = this.Equal(expected, actual);
    
    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);
    
    }
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    
      
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Reflection
    Sample.SerializeDeserializeObjectUsingReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario2"></a>使用 Avro 物件容器進行序列化</h2>


以 .NET 類別代表資料的方式不一定能一體適用。資料的結構描述有可能是動態的，例如，如果資料以逗號分隔值檔案 (CSV) 的格式表示，且事先不知道結構描述，而必須轉換為 Avro 格式，就是如此。在這種情況下，我們必須以明確指定的 JSON 結構描述建立特殊類別 (AvroRecord) 的執行個體，並為欄位填入必要資料，然後加以序列化。下列範例將說明如何定義結構描述、建立對應的記錄、為其填入資料，然後加以序列化和還原序列化。最後會比較初始物件和還原序列化的物件，以確認身分識別。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serialize and deserialize sample data set using Generic Record.
    //Generic Record is a special class with the schema explicitly defined in JSON.
    //All serialized data should be mapped to the fields of Generic Record,
    //which in turn will be then serialized.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a Memory Stream buffer
    using (var stream = new MemoryStream())
    {
    //Create a generic record to represent the data
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;
    
    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };
    
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Serialize the data
    serializer.Serialize(stream, expected);
    
    stream.Seek(0, SeekOrigin.Begin);
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Deserialize the data into a generic record
    dynamic actual = serializer.Deserialize(stream);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization to memory using Generic Record
    Sample.SerializeDeserializeObjectUsingGenericRecords();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output: 
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario3"></a>使用物件容器檔案進行序列化和使用反映進行序列化</h2>


此範例與範例 1 相類似：將一個類別和結構序列化，並儲存在位於範例應用程式現行目錄中的「物件容器」檔案內。接下來會讀取此檔案、將資料還原序列化，最後與初始執行個體比較，以確認身分識別。「物件容器」檔案中的資料會使用 deflate (Link?) 壓縮轉碼器進行壓縮。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with Deflate codec
    public void SerializeDeserializeUsingObjectContainersReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will be compressed using Deflate codec
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    bool isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\".File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\".File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario4"></a>使用物件容器檔案進行序列化和使用一般記錄進行序列化</h2>


此範例與範例 2 相類似：使用 AvroRecord 和明確定義的 JSON 結構描述，將範例資料集序列化。序列化的資料會儲存在位於範例應用程式現行目錄中的新「物件容器」檔案內。接下來會讀取此檔案、將資料還原序列化，最後與初始執行個體比較，以確認身分識別。

「物件容器」檔案中的資料不會壓縮 (使用 Null 壓縮轉碼器)。

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Generic Record and Avro Object Container Files
    //Serialized data is not compressed
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleGenericRecordNullCodec.avro";
    
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Define the schema in JSON
    const string Schema = @"{
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
    ""fields"":
    [
    { 
    ""name"":""Location"", 
    ""type"":
    {
    ""type"":""record"",
    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
    ""fields"":
    [
    { ""name"":""Floor"", ""type"":""int"" },
    { ""name"":""Room"", ""type"":""int"" }
    ]
    }
    },
    { ""name"":""Value"", ""type"":""bytes"" }
    ]
    }";
    
    //Create a generic serializer based on the schema
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Create a generic record to represent the data
    var testData = new List<AvroRecord>();
    
    dynamic expected1 = new AvroRecord(rootSchema);
    dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location1.Floor = 1;
    location1.Room = 243;
    expected1.Location = location1;
    expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
    testData.Add(expected1);
    
    dynamic expected2 = new AvroRecord(rootSchema);
    dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location2.Floor = 1;
    location2.Room = 244;
    expected2.Location = location2;
    expected2.Value = new byte[] { 6, 7, 8, 9 };
    testData.Add(expected2);
    
    //Serializing and saving data to file
    //Create a MemoryStream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Data will not be compressed (Null compression codec)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(streamWriter.Write);
    }
    }
    
    Console.WriteLine("Saving serialized data to file...");
    
    //Save stream to file
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    }
    
    //Reading and deserializng the data
    //Create a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Finally, verify the results
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
    int count = 1;
    foreach (var pair in pairs)
    {
    bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
    isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
    isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }
    
    //
    //Helper methods
    //
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\".File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\".File does not exist", path);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Generic Record to Avro Object Container File
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

<h2> <a name="Scenario5"></a>Serialization using object container files with custom compression codec</h2>


[Avro 規格][2]允許使用選用的壓縮轉碼器 (除了 Null 和 Deflate 以外)。下列範例將說明如何使用自訂壓縮轉碼器來處理「物件容器」檔案。它「實際上」並不會實作不同的轉碼器 (例如「Avro 規格」中提到的支援選用轉碼器 Snappy)，而是使用 .NET Framework 4.5 的 [Deflate][3] 實作 (可根據 zlib 提供更理想的壓縮演算法)，這對許多開發人員而言都將有所幫助。

    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of Deflate compression algorithm is used
    // Ensure your C# Project is set up accordingly
    //
    
    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.Diagnostics;
    using System.IO;
    using System.IO.Compression;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;

	#region Defining objects for serialization
    //Sample Class used in serialization samples
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Sample struct used in serialization samples
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }

	# endregion

	#region Defining custom codec based on .NET Framework V.4.5 Deflate
    //Avro.NET Codec class contains two methods 
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //which are the key ones for data compression.
    //To enable a custom codec one needs to implement these methods for the required codec

	#region Defining Compression and Decompression Streams
    //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
    //can not be directly used for Avro because it does not support vital operations like Seek.
    //Thus one needs to implement two classes inherited from Stream
    //(one for compressed and one for decompressed stream)
    //that use Deflate compression and implement all required features 
    internal sealed class CompressionStreamDeflate45 :Stream
    {
    private readonly Stream buffer;
    private DeflateStream compressionStream;
    
    public CompressionStreamDeflate45(Stream buffer)
    {
    Debug.Assert(buffer != null, "Buffer is not allowed to be null.");
    
    this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
    this.buffer = buffer;
    }
    
    public override bool CanRead
    {
    get { return this.buffer.CanRead; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return this.buffer.CanWrite; }
    }
    
    public override void Flush()
    {
    this.compressionStream.Close();
    }
    
    public override long Length
    {
    get { return this.buffer.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.buffer.Position;
    }
    
    set
    {
    this.buffer.Position = value;
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.buffer.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    return this.buffer.Seek(offset, origin);
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    this.compressionStream.Write(buffer, offset, count);
    }
    
    protected override void Dispose(bool disposed)
    {
    base.Dispose(disposed);
    
    if (disposed)
    {
    this.compressionStream.Dispose();
    this.compressionStream = null;
    }
    }
    }
    
    internal sealed class DecompressionStreamDeflate45 :Stream
    {
    private readonly DeflateStream decompressed;
    
    public DecompressionStreamDeflate45(Stream compressed)
    {
    this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
    }
    
    public override bool CanRead
    {
    get { return true; }
    }
    
    public override bool CanSeek
    {
    get { return true; }
    }
    
    public override bool CanWrite
    {
    get { return false; }
    }
    
    public override void Flush()
    {
    this.decompressed.Close();
    }
    
    public override long Length
    {
    get { return this.decompressed.Length; }
    }
    
    public override long Position
    {
    get
    {
    return this.decompressed.Position;
    }
    
    set
    {
    throw new NotSupportedException();
    }
    }
    
    public override int Read(byte[] buffer, int offset, int count)
    {
    return this.decompressed.Read(buffer, offset, count);
    }
    
    public override long Seek(long offset, SeekOrigin origin)
    {
    throw new NotSupportedException();
    }
    
    public override void SetLength(long value)
    {
    throw new NotSupportedException();
    }
    
    public override void Write(byte[] buffer, int offset, int count)
    {
    throw new NotSupportedException();
    }
    
    protected override void Dispose(bool disposing)
    {
    base.Dispose(disposing);
    
    if (disposing)
    {
    this.decompressed.Dispose();
    }
    }
    }

	# endregion

	#region Define Codec
    //Define the actual codec class containing the required methods for manipulating streams:
    //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed)
    //Codec class uses classes for comressed and decompressed streams defined above
    internal sealed class DeflateCodec45 :Codec
    {
    
    //We merely use different IMPLEMENTATION of Deflate, so the CodecName remains "deflate"
    public static readonly string CodecName = "deflate";
    
    public DeflateCodec45()
    : base(CodecName)
    {
    }
    
    public override Stream GetCompressedStreamOver(Stream decompressed)
    {
    if (decompressed == null)
    {
    throw new ArgumentNullException("decompressed");
    }
    
    return new CompressionStreamDeflate45(decompressed);
    }
    
    public override Stream GetDecompressedStreamOver(Stream compressed)
    {
    if (compressed == null)
    {
    throw new ArgumentNullException("compressed");
    }
    
    return new DecompressionStreamDeflate45(compressed);
    }
    }

	# endregion

	#region Define modified Codec Factory
    //Define modified Codec Factory to be used in Reader
    //It will catch the attempt to use "deflate" and provide Custom Codec 
    //For all other cases it will rely on the base class (CodecFactory)
    internal sealed class CodecFactoryDeflate45 :CodecFactory
    {
    
    public override Codec Create(string codecName)
    {
    if (codecName == DeflateCodec45.CodecName)
    return new DeflateCodec45();
    else
    return base.Create(codecName);
    }
    }

	# endregion

	# endregion

	#region Sample Class with demonstration methods
    //This class contains methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {
    
    //Serializes and deserializes sample data set using Reflection and Avro Object Container Files
    //Serialized data is compressed with the Custom compression codec (Deflate of .NET Framework 4.5)
    //
    //This sample uses Memory Stream for all operations related to serialization, deserialization and
    //Object Container manipulation, though File Stream could be easily used.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");
    
    //Path for Avro Object Container File
    string path = "AvroSampleReflectionDeflate45.avro";
    
    //Create a data set using sample Class and struct
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Serializing and saving data to file
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Create a SequentialWriter instance for type SensorData which can serialize a sequence of SensorData objects to stream
    //Here the custom Codec is introduced.For convenience the next commented code line shows how to use built-in Deflate.
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Serialize the data to stream using the sequential writer
    testData.ForEach(writer.Write);
    }
    }
    
    //Save stream to file
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    }
    
    //Reading and deserializing data
    //Creating a Memory Stream buffer
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Reading data from Object Container File
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation.Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Prepare the stream for deserializing the data
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Because of SequentialReader<T> constructor signature an AvroSerializerSettings instance is required
    //when Codec Factory is explicitly specified
    //You may comment the line below if you want to use built-in Deflate (see next comment)
    AvroSerializerSettings settings = new AvroSerializerSettings();
    
    //Create a SequentialReader for type SensorData which will derserialize all serialized objects from the given stream
    //It allows iterating over the deserialized objects because it implements IEnumerable<T> interface
    //Here the custom Codec Factory is introduced.
    //For convenience the next commented code line shows how to use built-in Deflate
    //(no explicit Codec Factory parameter is required in this case).
    //Note, that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
    //in read-write operations
    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;
    
    //Finally, verify that deserialized data matches the original one
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    bool isEqual;
    int count = 1;
    var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
    foreach (var pair in pairs)
    {
    isEqual = this.Equal(pair.expected, pair.actual);
    Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
    count++;
    }
    }
    }
    
    //Delete the file
    RemoveFile(path);
    }

	# endregion

	#region Helper Methods
    
    //Comparing two SensorData objects
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Saving memory stream to a new file with the given path
    private bool WriteFile(MemoryStream InputStream, string path)
    {
    if (!File.Exists(path))
    {
    try
    { 
    using (FileStream fs = File.Create(path))
    {
    InputStream.Seek(0, SeekOrigin.Begin);
    InputStream.CopyTo(fs);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    else
    {
    Console.WriteLine("Can not create file \"{0}\".File already exists", path);
    return false;
    
    }
    }
    
    //Reading a file content using given path to a memory stream
    private bool ReadFile(MemoryStream OutputStream, string path)
    {
    try
    {
    using (FileStream fs = File.Open(path, FileMode.Open))
    {
    fs.CopyTo(OutputStream);
    }
    return true;
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    return false;
    }
    }
    
    //Deleting file using given path
    private void RemoveFile(string path)
    {
    if (File.Exists(path))
    {
    try
    {
    File.Delete(path);
    }
    catch (Exception e)
    {
    Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
    Console.WriteLine(e.Message);
    }
    }
    else
    {
    Console.WriteLine("Can not delete file \"{0}\".File does not exist", path);
    }
    }

	# endregion
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Create an instance of AvroSample Class and invoke methods
    //illustrating different serializing approaches
    AvroSample Sample = new AvroSample();
    
    //Serialization using Reflection to Avro Object Container File using Custom Codec
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.



[1]: http://msdn.microsoft.com/en-us/library/ms731072(v=vs.110).aspx
[2]: http://avro.apache.org/docs/current/spec.html#Required+Codecs
[3]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.110).aspx