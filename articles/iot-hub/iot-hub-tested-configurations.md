<properties
	pageTitle="作業系統平台與硬體相容性 | Microsoft Azure"
	description="摘要說明 IoT 裝置 SDK 與作業系統平台和裝置硬體的相容性。"
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/09/2015"
     ms.author="hegate"/>

# 作業系統平台與裝置 SDK 硬體相容性

本文件說明 SDK 與不同作業系統平台的相容性，以及 [IoT 程式的 Microsoft Azure 認證](#microsoft-azure-certified-for-iot)中包含的特定裝置組態。如果您已經有一個裝置，請查看程式中包含裝置的清單，以尋找裝置的相容性特定資訊。如果您不確定要使用哪一種裝置，請查看[作業系統平台和程式庫](#os-platforms)相容性區段。


## 作業系統平台

Azure IoT 程式庫已在下列作業系統平台上測試：


|Linux/Unix 作業系統平台 | 版本|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2\.1 |

|Windows 作業系統平台 | 版本|
|:---------------|:------------:|
|Windows 桌面| 7、8、10 |
|Windows IoT 核心版| 10 |
|Windows Server| 2012 R2|

|其他平台 | 版本|
|:---------------|:------------:|
|mbed | 2\.0 |
|TI-RTOS | 2\.x |



## C 程式庫

[適用於 C 的 Microsoft Azure IoT 裝置 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) 已經過下列組態測試：

|作業系統平台| 版本|通訊協定|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS、AMQP、MQTT |
|Fedora Linux| 20 | HTTPS、AMQP、MQTT |
|mbed OS| 2\.0 | HTTPS、AMQP |
|TI-RTOS| 2\.x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS、AMQP、MQTT |
|Windows 桌面| 7、8、10 | HTTPS、AMPQ、MQTT |
|Yocto Linux|2\.1 | HTTPS、AMQP|



## Node.js 程式庫

[適用於 Node.js 的 Microsoft Azure IoT 裝置 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) 已經過下列組態測試：


|執行階段| 版本|通訊協定|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS|



## Java 程式庫

[適用於 Java 的 Microsoft Azure IoT 裝置 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) 已經過下列組態測試：

|執行階段| 版本|通訊協定|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.7 | HTTPS、AMQP |
|Java SE (Linux)| 1\.8 | HTTPS、AMQP|

適用於 Java 的 Microsoft Azure IoT 服務 SDK 已經過下列組態測試：

|執行階段| 版本|通訊協定|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS、AMQP |


## CSharp

[適用於 .NET 的 Microsoft Azure IoT 裝置 SDK](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) 已經過下列組態測試：

|作業系統平台| 版本|通訊協定|
|:---------|:----------:|:----------:|
|Windows 桌面| 7、8、10 | HTTPS、AMPQ|
|Windows IoT 核心版|10 | HTTPS|

受管理的代理程式程式碼需要 Microsoft .NET Framework 4.5


## IoT 的 Microsoft Azure 認證

**IoT 的 Microsoft Azure 認證**是連接廣泛的 IoT 生態系統與 Microsoft Azure 的協力廠商程式，讓開發人員和架構設計人員能夠了解相容性案例。具體來說，它提供 OS/裝置組合的一份信任清單，以協助您快速開始使用 IoT 專案 – 無論您是在概念驗證或試驗階段。使用認證的裝置及作業系統組合就可以快速開始使用 IoT 專案，只需要較少的工作與自訂就可以確定裝置與 Azure IoT 套件和 Azure IoT 中心的相容性。

## IoT 裝置的認證

**IoT 裝置的認證**已通過與 Azure IoT SDK 的相容性測試，並且已可在您的 IoT 應用程式中使用。具體而言，我們會根據作業系統平台和程式碼語言來識別相容性。

#### 裝置清單

每個裝置都已經過認證，能在裝置製造商所選擇的作業系統和語言中，與我們的 SDK 搭配使用。例如，BeagleBone Black 能在使用我們的 C、Javascript 和 Java 語言的 Debian 上運作。這表示開發人員能夠在特定裝置上，以這些語言與作業系統的任何組合來建置應用程式。

|裝置| 已測試的作業系統 |語言|
|:---------|:----------|:----------|
|[ADLINK MXE-202i](http://www.adlinktech.com/PD/web/PD_detail.php?cKind=&pid=1589&seq=&id=&sid=&category=Fanless-Embedded-Computer_IoT-Gateway&utm_source=) |Wind River | Javascript|
|[Advantech Co., ARK-2121L](http://www.advantech.com/products/ark-2000_series_embedded_box_pcs/ark-2121l/mod_dd092808-0832-44bc-b38a-945eb7e016bd) |Windows 10 | C#|
|[Advantech Co., ARK-1123C](http://www.advantech.com/products/92d96fda-cdd3-409d-aae5-2e516c0f1b01/ark-1123c/mod_0b91165c-aa8c-485d-8d25-fde6f88f4873) |Windows 10 | C#|
|[Advantech Co., LTD UNO-1372G](http://www.advantech.com/products/gf-bvl2/uno-1372g/mod_8e63b3c9-b606-4725-a1af-94fccb98bb1a) |[Windows 10 | C#|
|[Advantech Co., TREK-674](http://www.advantech.com/products/1-2jsj5t/trek-674/mod_88a737dd-819b-4c8e-8f2e-2bb75b04619b) |Windows 10 | C#|
|[Advantech Co., UTX-3115](http://www.advantech.com/products/bda911fe-28bc-4171-aed3-67f76f6a12c8/utx-3115/mod_fa00d5cd-7d2b-430b-8983-c232bfb9f315) |Windows 10 | C#|
|[Arduino MKR1000](https://www.arduino.cc/en/Main/ArduinoMKR1000) |Arduino IDE | C|
|[Arduino Zero](https://www.arduino.cc/en/Guide/ArduinoZero) |Arduino IDE | C|
|[Arrow DragonBoard 410c](http://partners.arrow.com/campaigns-na/qualcomm/dragonboard-410c/) |Windows 10 IoT 核心版 | C#|
|[BeagleBone Black](http://beagleboard.org/black) | Debian | C、Javascript、Java|
|[BeagleBone Green](http://beagleboard.org/green) |Debian | C、Javascript、Java|
|[Dell Edge Gateway 5000 系列](http://www.dell.com/IoTgateway) |Ubuntu | Java|
|[e-con Systems Ankaa](http://www.e-consystems.com/iMX6-development-board.asp) |Ubuntu | C|
|[內嵌的系統 LogicMachine 系列](http://openrb.com/products/) |自訂 Linux | C|
|[Freescale FRDM K64](http://www.freescale.com/products/arm-processors/kinetis-cortex-m/k-series/k6x-ethernet-mcus/freescale-freedom-development-platform-for-kinetis-k64-k63-and-k24-mcus:FRDM-K64F) |mbed 2.0 | C|
|[HPE Edgeline EL10](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884747.pdf) |Windows 10 | C#|
|[HPE Edgeline EL20](http://www8.hp.com/h20195/v2/GetPDF.aspx/c04884769.pdf) |Windows 10 | C#|
|[Ilevia Eve Raspberry](http://www.ilevia.com/overview/) |Debian | C|
|[Intel Edison](http://www.intel.com/content/www/us/en/do-it-yourself/edison.html) |Yocto | C、Javascript|
|[Minnowboard Max](http://www.minnowboard.org/meet-minnowboard-max/) |Windows 7、8、10 | C#|
|[NEXCOM NISE 50C](http://www.nexcom.com/Products/industrial-computing-solutions/industrial-fanless-computer/atom-compact/fanless-computer-nise-50c) |Windows 10 IoT 核心版 | C#|
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Raspbian | C、Javascript、Java |
|[Raspberry Pi 2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/) | Windows 10 IoT 核心版| C、Javascript、C#|
|[Samsung ARTIK](http://developer.samsung.com/artik) |Fedora | C|
|[SOTEC CloudPlug](http://cloudplug.info/) |YOCTO | C|
|[TI CC3200](http://www.ti.com/product/cc3200) |TI-RTOS 2.x | C|
|[Toradex Apalis iMX6](https://www.toradex.com/computer-on-modules/apalis-arm-family/freescale-imx-6) |Linux Angstrom(Yocto) | Javascript、Java|
|[Toradex Apalis T30](https://www.toradex.com/computer-on-modules/apalis-arm-family/nvidia-tegra-3) |Linux Angstrom(Yocto) | Javascript、Java|
|[Toradex Colibri iMX6](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-imx6) |Linux Angstrom(Yocto) | Javascript、Java|
|[Toradex Colibri T20](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-2) |Linux Angstrom(Yocto) | Java|
|[Toradex Colibri T30](https://www.toradex.com/computer-on-modules/colibri-arm-family/nvidia-tegra-3) |Windows 10 IoT 核心版 | C#|
|[Toradex Colibri VF61](https://www.toradex.com/computer-on-modules/colibri-arm-family/freescale-vybrid-vf6xx) |Linux Angstrom(Yocto) | Javascript、Java|

[開始使用這些裝置](https://azure.microsoft.com/develop/iot/get-started/)或造訪我們的 GitHub [儲存機制](https://github.com/Azure/azure-iot-sdks)並搜尋每種語言的裝置文件。

## 後續步驟

深入了解使用 [IoT 裝置的認證](http://azure.com/iotdev)開發解決方案的相關資訊。

<!---HONumber=AcomDC_0107_2016-->