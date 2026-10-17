## Huawei Atlas 200 DK developer kit

## Technical White Paper (Model 3000)

## Trademarks and Permissions

**NOTICE:**

The purchased products, services and features are stipulated by the contract made between Huawei and the customer. All or part of the products, services and features described in this document may not be within the purchase scope or the usage scope. Unless otherwise specified in the contract, all statements, information, and recommendations in this document are provided "AS IS" without warranties, guarantees or representations of any kind, either express or implied.

The information in this document is subject to change without notice. Every effort has been made in the preparation of this document to ensure accuracy of the contents, but all statements, information, and recommendations in this document do not constitute a warranty of any kind, express or implied.

Address:

Website:

Huawei Industrial Base Bantian, Longgang Shenzhen 518129 People's Republic of China

[https://e.huawei.com](https://e.huawei.com/)

## Purpose

This document describes the system design, features, and specifications of the Atlas 200 DK developer kit (model 3000).

## Intended Audience

This document is intended for:

- Huawei presales engineers
- Channel partner presales engineers
- Enterprise presales engineers

## Symbol Conventions

The symbols that may be found in this document are defined as follows.

| Symbol   | Description                                                                                                                                                                                                                              |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | Indicates a hazard with a high level of risk which, if not avoided, will result in death or serious injury.                                                                                                                              |
|          | Indicates a hazard with a medium level of risk which, if not avoided, could result in death or serious injury.                                                                                                                           |
|          | Indicates a hazard with a low level of risk which, if not avoided, could result in minor or moderate injury.                                                                                                                             |
|          | Indicates a potentially hazardous situation which, if not avoided, could result in equipment damage, data loss, performance deterioration, or unanticipated results. NOTICE is used to address practices not related to personal injury. |

## About This Document

| Symbol   | Description                                                                                                                                                                  |
|----------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | Supplements the important information in the main text. NOTE is used to address information not related to personal injury, equipment damage, and environment deterioration. |

## Change history

|   Issue | Date       | Description                                                                                                                                                                                                                                              |
|---------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|      08 | 2021-03-16 | This issue is the eighth official release. Updated the encoding and decoding capability of the AI processor in 3.1 Specifications.                                                                                                                       |
|      07 | 2020-11-26 | This issue is the seventh official release. ● Changed the product name from Atlas 200 Developer Kit to Atlas 200 DK developer kit (model 3000). ● Optimized descriptions in section 4.3 microSD Card Port.                                               |
|      06 | 2020-08-28 | This issue is the sixth official release. Optimized 3.1 Specifications.                                                                                                                                                                                  |
|      05 | 2020-08-12 | This issue is the fifth official release. Added the description of the differences between the old and new control PCBs. The following sections are involved: ● 1.1 Overview ● 3.1 Specifications ● 4.4 Power Port and Reset Button ● 4.5 MIPI-CSI Ports |
|      04 | 2020-07-07 | This issue is the fourth official release. Updated 5 Certifications.                                                                                                                                                                                     |
|      03 | 2020-05-30 | This issue is the third official release.                                                                                                                                                                                                                |
|      02 | 2019-07-29 | This issue is the second official release.                                                                                                                                                                                                               |
|      01 | 2019-05-10 | This issue is the first official release.                                                                                                                                                                                                                |

## Contents

| About This Document................................................................................................................ ii                                                                                                                                                                                                                                                                            |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1 Product Introduction...............................................................................................................1                                                                                                                                                                                                                                                                            |
| 1.1 Overview.................................................................................................................................................................................... 1                                                                                                                                                                                                                |
| 1.2 Appearance................................................................................................................................................................................ 2                                                                                                                                                                                                                  |
| 1.3 System Architecture................................................................................................................................................................4                                                                                                                                                                                                                          |
| 2 Product Features......................................................................................................................6                                                                                                                                                                                                                                                                         |
| 2.1 Performance.............................................................................................................................................................................. 6                                                                                                                                                                                                                   |
| 2.2 Maintainability......................................................................................................................................................................... 6                                                                                                                                                                                                                    |
| 3 Product Specifications............................................................................................................ 7                                                                                                                                                                                                                                                                            |
| 3.1 Specifications............................................................................................................................................................................ 7                                                                                                                                                                                                                  |
| 3.2 Environmental Specifications.............................................................................................................................................. 9                                                                                                                                                                                                                                  |
| 4 Ports..........................................................................................................................................10                                                                                                                                                                                                                                                               |
| 4.1 GE Port......................................................................................................................................................................................10                                                                                                                                                                                                               |
| 4.2 USB Port................................................................................................................................................................................... 10                                                                                                                                                                                                                |
| 4.3 microSD Card Port................................................................................................................................................................ 10                                                                                                                                                                                                                          |
| 4.4 Power Port and Reset Button........................................................................................................................................... 11                                                                                                                                                                                                                                     |
| 4.5 MIPI-CSI Ports........................................................................................................................................................................ 11                                                                                                                                                                                                                     |
| 4.6 40-Pin Extended Port........................................................................................................................................................... 13                                                                                                                                                                                                                            |
| 4.6.1 UART...................................................................................................................................................................................... 14                                                                                                                                                                                                               |
| 4.6.2 SPI........................................................................................................................................................................................... 14 4.6.3 I 2 C........................................................................................................................................................................................... 14 |
| 4.6.4 GPIO.......................................................................................................................................................................................                                                                                                                                                                                                                 |
| 14                                                                                                                                                                                                                                                                                                                                                                                                                |
| 4.7 LED Status Indicators...........................................................................................................................................................14                                                                                                                                                                                                                            |
| 5 Certifications.......................................................................................................................... 18                                                                                                                                                                                                                                                                     |
| 6 Warranty................................................................................................................................. 20 A Appendix................................................................................................................................. 21                                                                                                                     |
| A.1 SN.............................................................................................................................................................................................. 21 22                                                                                                                                                                                                        |
| A.2 Acronyms and Abbreviations............................................................................................................................................                                                                                                                                                                                                                                        |

# 1 Product Introduction

## 1.1 Overview

The Atlas 200 DK developer kit (model 3000) is a developer board that uses the Atlas 200 AI accelerator module (model 3000). It opens interfaces for developers to use the Atlas 200 AI accelerator module (model 3000) easily. The Atlas 200 DK developer kit (model 3000) is ideal for research and development in fields such as safe city, drones, robots, and video servers.

The Atlas 200 AI accelerator module (model 3000) is a high-performance AI compute module powered by the Ascend 310 AI Processor. It performs image and video analysis and inference and has been widely used in intelligent surveillance, robots, drones, and video servers.

**NOTE:**

The Ascend 310 AI Processor is a chip designed for image recognition, video processing, inference computing, and machine learning. It features high performance and low power consumption. The Ascend 310 AI Processor has two built-in AI cores, supports 128-bit LPDDR4X, and delivers computing power of up to 22 TOPS (INT8).

The Atlas 200 DK developer kit (model 3000) provides two types of printed circuit boards (PCBs): IT21DMDA (old PCB) and IT21VDMB (new PCB). Table 1-1 shows the differences between the two types of PCBs. The first eight digits of the product name indicate the PCB model. For details, see A.1 SN.

Table 1-1 PCB specification comparisons

| Item        | IT21DMDA (Old PCB)   | IT21VDMB (New PCB)   | Reference                       |
|-------------|----------------------|----------------------|---------------------------------|
| Power input | 5 V to 28 V          | 12 V                 | 4.4 Power Port and Reset Button |
| MIPI-CSI port   | 22-pin, without cables | 51-pin, with cables  | 4.5 MIPI-CSI Ports |
| Number of GPIOs | 8                      | 11                   | 4.6.4 GPIO         |

## 1.2 Appearance

Figure 1-1 shows the appearance of the Atlas 200 DK developer kit (model 3000).

Figure 1-1 Appearance

**NOTE:**

There are two types of labels for the power port. The figure is for reference only.

Figure 1-2 Dimensions (unit: mm)

Figure 1-3 Ports

|   1 | Power indicator   |   2 | Power port        |
|-----|-------------------|-----|-------------------|
|   3 | USB               |   4 | microSD card slot |
|   5 | Network port      |   6 | Reset button      |

## 1.3 System Architecture

The Atlas 200 DK developer kit (model 3000) consists of the Atlas 200 AI accelerator module (model 3000), audio/video interface chip (Hi3559C), and LAN switch or PHY. Figure 1-4 and Figure 1-5 show the system architectures of the Atlas 200 DK developer kit (model 3000).

Figure 1-4 Block diagram when the PCB is IT21DMDA

Figure 1-5 Block diagram when the PCB is IT21VDMB

# 2 Product Features

## 2.1 Performance

- Provides up to 22 TOPS INT8 computing power.
- Supports 2-channel camera inputs, 2-channel ISP, and HDR10.
- Supports 1000 Mbit/s Ethernet to provide high-speed network connections, delivering strong computing capabilities.
- Provides a universal 40-pin expansion connector (reserved), facilitating product prototype design.

## 2.2 Maintainability

- Supports online upgrade, facilitating routine maintenance.
- Obtains the device information such as temperature and voltage status inband and out-of-band, simplifying management.

# 3 Product Specifications

## 3.1 Specifications

Table 3-1 Hardware specifications

| Item             | Specification                                                                                                                 |
|------------------|-------------------------------------------------------------------------------------------------------------------------------|
| AI processor     | Ascend 310 AI Processor ● 2 x DaVinci AI cores ● 8 x A55 ARM cores (maximum frequency: 1.6 GHz)                              |
| AI compute power | ● Half precision (FP16): 4/8/11 TFLOPS ● Integer precision (INT8): 8/16/22 TOPS                                               |
| Memory           | ● Type: LPDDR4X ● Bit width: 128 bits/64 bits ● Capacity: 8 GB/4 GB ● Rate: 3200 Mbit/s ● Error checking and correcting (ECC) |
| Encoding/ Decoding capability | ● H.264/H.265 decoder, 20-channel 1080p (1920 x 1080) 25 FPS, YUV420 ● H.264/H.265 decoder, 16-channel 1080p (1920 x 1080) 30 FPS, YUV420 ● H.264/H.265 decoder, 2-channel 4K (3840 x 2160) 60 FPS, YUV420 ● H.264/H.265 encoder, 1-channel 1080p (1920 x 1080) 30 FPS, YUV420 ● JPEG decoding at 1080p (1920 x 1080) 256 FPS and encoding at 1080p (1920 x 1080) 64 FPS, up to 8192 x 4320 resolution ● PNG decoding at 1080p (1920 x 1080) 24 FPS, up to 4096 x 2160 resolution |
| Storage                       | 1 x microSD card, which supports SD 3.0 and provides a maximum rate of SDR50 and a maximum capacity of 2 TB                                                                                                                                                                                                                                                                                                                                                                       |
| Port                          | ● Network port - 1 x GE RJ-45 port ● USB port - 1 x USB 3.0 Type-C port, which can be used only to connect a slave device and compatible with USB 2.0 ● Other ports - 1 x 40-pin I/O connector - 2 x MIPI connectors - 2 x onboard microphones                                                                                                                                                                                                                                    |
| Power supply                  | ● If the PCB is IT21VDMB, the input voltage is 12 V. ● If the PCB is IT21DMDA, the input voltage is 5 V to 28 V. 12 V 3 A adapter is configured by default.                                                                                                                                                                                                                                                                                                                       |
| Power consumption             | Typical power consumption: 20 W                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Dimensions (H x W x D)        | 137.8 mm x 93.0 mm x 32.9 mm                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Net weight                    | 234 g                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |

## 3.2 Environmental Specifications

Table 3-2 Environment specifications

| Item                                     | Specification                                                                                                                                                                 |
|------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Operating temperature                    | 0°C to 35°C (32°F to 95°F)                                                                                                                                                    |
| Storage temperature                      | 0°C to 85°C (32°F to 185°F)                                                                                                                                                   |
| Operating humidity (RH, non- condensing) | 5% to 90%                                                                                                                                                                     |
| Storage humidity (RH, non- condensing)   | 5% to 95%                                                                                                                                                                     |
| Maximum altitude                         | 3000 m (9842.4 ft.) For altitudes above 900 m (2952.72 ft.), the highest operating temperature decreases by 1°C (1.8°F) for every increase of 300 m (984.24 ft.) in altitude. |

- 4.3 microSD Card Port
- 4.6 40-Pin Extended Port

# 4 Ports

## 4.1 GE Port

The Atlas 200 DK developer kit (model 3000) provides an external 10/100/1000M Base-T interface, using the RJ45 connector and connecting to a network with a common network cable.

## 4.2 USB Port

The Atlas 200 DK developer kit (model 3000) provides a Type-C USB port that is compatible with USB 3.0 (SuperSpeed), USB 2.0 (HighSpeed), and USB 1.1 (FullSpeed) communication protocols. This port can be used only in device mode and does not support the master mode. It is used to connect to the debugging host for loading and debugging.

## 4.3 microSD Card Port

The Atlas 200 DK developer kit (model 3000) provides a microSD card port that supports SD 3.0 and is backward compatible with SD 2.0. You are advised to use a standard microSD card of SD 3.0. The minimum capacity is 8 GB and the maximum capacity is 2 TB.

**NOTE:**

- The microSD card is based on the flash memory. NAND flash memory is now commonly used in the industry. It uses electrons on the floating gate to store data. However, electrons frequently passing through the floating gate can weaken the gate's ability to store electrons and eventually make the gate unable to store electrons. This problem is common to NAND flash memory. To prevent failures of NAND flash memory, accurately assess the amount of service data to be written.
- For details about the application scenarios of Micro SD cards, see the SD Card Technical White Paper.

## 4.4 Power Port and Reset Button

The power supply port of the Atlas 200 DK developer kit (model 3000) uses a common DC connector, and the power supply must be equal to or higher than 36 W. If the power supply is lower than 36 W, the transient power supply may be insufficient, causing system exceptions.

The RST button is used to reset the system. When the system is abnormal, you can press this button to reset the system.

- If the PCB is IT21VDMB, the input voltage is 12 V.
- If the PCB is IT21DMDA, the input voltage is 5 V to 28 V.

## 4.5 MIPI-CSI Ports

The Atlas 200 DK developer kit (model 3000) has two MIPI-CSI ports, and the definitions of the two ports are the same.

- Table 4-1 defines the pins of a MIPI-CSI port when the PCB is IT21VDMB.
- Table 4-2 defines the pins of a MIPI-CSI port when the PCB is IT21DMDA.

Table 4-1 Pin definition of the camera port (PCB IT21DMDA)

|   Pin | Name      |   Pin | Name      |
|-------|-----------|-------|-----------|
|     1 | GND       |     2 | CAM_DN0   |
|     3 | CAM_DP0   |     4 | GND       |
|     5 | CAM_DN1   |     6 | CAM_DP1   |
|     7 | GND       |     8 | CAM_CN    |
|     9 | CAM_CP    |    10 | GND       |
|    11 | NC        |    12 | NC        |
|    13 | GND       |    14 | NC        |
|    15 | NC        |    16 | GND       |
|    17 | CAM_GPIO0 |    18 | CAM_GPIO1 |
|    19 | GND       |    20 | SCL0      |
|    21 | SDA0   |    22 | VCC3V3 |

Table 4-2 Pin definition of the camera port (PCB IT21VDMB)

|   Pin | Name      |   Pin | Name      |
|-------|-----------|-------|-----------|
|     1 | NC        |     2 | GND       |
|     3 | NC        |     4 | NC        |
|     5 | GND       |     6 | NC        |
|     7 | NC        |     8 | GND       |
|     9 | NC        |    10 | NC        |
|    11 | GND       |    12 | NC        |
|    13 | NC        |    14 | GND       |
|    15 | NC        |    16 | NC        |
|    17 | GND       |    18 | NC        |
|    19 | NC        |    20 | GND       |
|    21 | NC        |    22 | CAM_DN0   |
|    23 | GND       |    24 | CAM_DP0   |
|    25 | NC        |    26 | GND       |
|    27 | NC        |    28 | CAM_DN1   |
|    29 | GND       |    30 | CAM_DP1   |
|    31 | NC        |    32 | GND       |
|    33 | NC        |    34 | CAM_CN    |
|    35 | GND       |    36 | CAM_CP    |
|    37 | NC        |    38 | GND       |
|    39 | GND       |    40 | NC        |
|    41 | GND       |    42 | CAM_GPIO0 |
|    43 | NC        |    44 | NC        |
|    45 | CAM_GPIO1 |    46 | SCL0      |
|    47 | SDA0      |    48 | NC        |
|    49 | NC        |    50 | VCC3V3    |
|    51 | VCC3V3 | -     | -      |

## 4.6 40-Pin Extended Port

Table 4-3 Pin definition

|   Pin | Name       | Level   |   Pin | Name     | Level   |
|-------|------------|---------|-------|----------|---------|
|     1 | +3.3 V      | 3.3 V    |     2 | +5.0 V    | 5 V      |
|     3 | I 2 C2-SDA | 3.3 V    |     4 | +5.0 V    | 5 V      |
|     5 | I 2 C2-SCL | 3.3 V    |     6 | GND      | -       |
|     7 | GPIO0      | 3.3 V    |     8 | TXD0     | 3.3 V    |
|     9 | GND        | -       |    10 | RXD0     | 3.3 V    |
|    11 | GPIO1      | 3.3 V    |    12 | NC       | -       |
|    13 | NC         | -       |    14 | GND      | -       |
|    15 | GPIO2      | 3.3 V    |    16 | TXD1     | 3.3 V    |
|    17 | +3.3 V      | 3.3 V    |    18 | RXD1     | 3.3 V    |
|    19 | SPI-MOSI   | 3.3 V    |    20 | GND      | -       |
|    21 | SPI-MISO   | 3.3 V    |    22 | NC       | -       |
|    23 | SPI-CLK    | 3.3 V    |    24 | SPI-CS   | 3.3 V    |
|    25 | GND        | -       |    26 | GPIO10   | 3.3 V    |
|    27 | GPIO8      | 3.3 V    |    28 | GPIO9    | 3.3 V    |
|    29 | GPIO3      | 3.3 V    |    30 | GND      | -       |
|    31 | GPIO4      | 3.3 V    |    32 | NC       | -       |
|    33 | GPIO5      | 3.3 V    |    34 | GND      | -       |
|    35 | GPIO6      | 3.3 V    |    36 | +1.8 V    | 1.8 V    |
|    37 | GPIO7      | 3.3 V    |    38 | TXD-3559 | 3.3 V    |
|    39 | GND        | -       |    40 | RXD-3559 | 3.3 V    |

### 4.6.1 UART

### 4.6.2 SPI

### 4.6.3 I 2 C

### 4.6.4 GPIO

**NOTE:**

- The NC pin has no connection on the board.
- The maximum output current of 1.8 V is 500 mA, the maximum output current of 3.3 V is 500 mA, and the maximum output current of 5 V is 1 A.

UART 0 corresponds to pin 8 and pin 10. It is the default debug console of Ascend 310. Its baud rate is 115200 bit/s.

UART 1 corresponds to pin 16 and pin 18. It is used for expansion and communication with other modules.

UART-Hi3559 corresponds to pin 38 and pin 40. It is used for debugging the Hi3559 chip over the MIPI-CSI. Its baud rate is 115200 bit/s.

Figure 4-1 Debugging serial port

The SPI-CS0, SPI-CLK, SPI-MISO and SPI-MOSI four-wire SPI interfaces can connect to various sensors, but support only the master mode.

The I 2 C2-SCL and I 2 C2-SDA form the I 2 C2 interface, which can be used to connect external sensors and communicate with other modules. The maximum rate is 400 kHz.

**NOTE:**

The I 2 C1 interface is an intra-board interface and is not open to external systems.

- As output pins, GPIO0, GPIO1, and GPIO2 must be connected to external pullup resistors to increase driving capabilities. It is recommended that the value of pull-up resistance is 1-10 kilohms.
- The PCB IT21VDMB provides 11 independent GPIO pins.
- The PCB is IT21DMDA, which has eight GPIO pins. Pins 26, 27, and 28 are NC pins.

## 4.7 LED Status Indicators

There are four LED indicators on the Atlas 200 DK developer kit (model 3000) developer board. See Figure 4-2 and Figure 4-3.

Figure 4-2 LED positions when the mainboard is IT21DMDA

Figure 4-3 LED positions when the mainboard is IT21VDMB

**NOTE:**

The silkscreen of the indicators is MINI_LED2, MINI_LED1, 3559_ACT, and 3559_VEDIO from left to right, corresponding to the indicators from left to right.

Table 4-4 Indicator status 1

| MINI_LE D2   | MINI_LE D1   | Developer Board Status of the Atlas 200 DK developer kit (model 3000)          | Important Notes                                                                                  |
|--------------|--------------|--------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------|
| Off          | Off          | The developer board of the Atlas 200 DK developer kit (model 3000) is started. | You can power off or restart the developer board of the Atlas 200 DK developer kit (model 3000). |
| Off          | On           | Ascend 310 is started.                                                         | You can power off or restart the developer board of the Atlas 200 DK developer kit (model 3000) except during version upgrade.                                                                                                                                                         |
| Blink        | Blink        | The firmware is being upgraded.                                                | ● Do not power off or restart the developer board of the Atlas 200 DK developer kit (model 3000). Otherwise, the firmware upgrade may be incomplete and the board may be damaged. ● The firmware upgrade process is a part of the version upgrade. The upgrade takes about 15 minutes. |
| On           | On           | The developer board of the Atlas 200 DK developer kit (model 3000) is started. | You can power off or restart the developer board of the Atlas 200 DK developer kit (model 3000).                                                                                                                                                                                       |

Table 4-5 Indicator status 2

| 3559_A CT   | 3559_V EDIO   | Developer Board Status of the Atlas 200 DK developer kit (model 3000)   | Important Notes   |
|-------------|---------------|-------------------------------------------------------------------------|-------------------|
| Off         | Off           | The Hi3559C system is not started.                                      | None              |
| Off         | On            | The Hi3559C system is being started.                                    | None              |
| On          | On            | The startup process of the Hi3559C system is complete.                  | None              |

# 5 Certifications

Table 5-1 Certifications

|   No. | Country/Region   | Certification   | Standard                                                                                                                                                                                                                                                                                                                                                                                              |
|-------|------------------|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     1 | Europe           | CE              | Safety: ● IEC 60950-1:2005(2nd Edition)+A1:2009 +A2:2013 ● EN 60950-1:2006+A11: 2009+A1:2010+A12 :2011+A2:2013 EMC: ● EN 55032:2012/AC: 2013 ● CISPR 32:2012 ● EN 55032:2015 ● CISPR 32:2015 ● EN 55024:2010 ● CISPR 24:2010 ● EN 55024:2010+A1:201 5 ● CISPR 24:2010+A1:2015 ● ETSI EN 300 386 V1.6.1:2012 ● ETSI EN 300 386 V2.1.1:2016 ● EN 61000-3-2:2014 ● EN 61000-3-3:2013 ● EN 61000-6-2:2005 |
|       |                  |                                    | ● EN 61000-6-4:2006+A1 :2010                                                                                                    |
| 2     | Europe           | RoHS                               | EN 50581: 2012                                                                                                                  |
| 3     | Japan            | VCCI                               | VCCI 32-1                                                                                                                       |
| 4     | China            | CCC                                | ● GB 4943.1-2011 ● GB/T 9254-2008 (Class A) ● YD/T 993-1998                                                                     |
| 5     | -                | Multi-country commodity inspection | See the product certification.                                                                                                  |
| 6     | America          | NRTL                               | ● UL 60950-1:2007 Ed.2+R:14Oct2014 ● CSA C22.2#60950-1:200 7 Ed.2 +A1;A2 ● UL 62368-1:2014 Ed.2 ● CSA C22.2#62368-1:201 4 Ed.2] |
| 7     | -                | International CB                   | ● IEC 60950-1:2005, AMD1:2009, AMD2:2013 ● IEC 62368-1:2014 (Second Edition)                                                    |
| 8     | America          | FCC                                | FCC CFR47 Part 15 Subpart B:2018                                                                                                |

For details, see the Maintenance &amp; Warranty.

# 6 Warranty

# A Appendix

# A Appendix

## A.1 SN

A Appendix

The serial number (SN) on the label plate uniquely identifies a device. The SN is required when you contact Huawei technical support.

Table A-1 SN description

|   No. | Description                                                                                                |
|-------|------------------------------------------------------------------------------------------------------------|
|     1 | Serial number (two digits). The value is fixed to 21.                                                      |
|     2 | Material identification code (eight characters), that is, processing code.                                 |
|     3 | Vendor code (two characters). The code 10 indicates Huawei, and other values indicate outsourcing vendors. |
|     4 | Year and month (two digits). ● The first digit indicates the year. - Digits 1 to 9 indicate years 2001 to 2009, respectively. - Letters A to H indicate years 2010 to 2017, respectively. - Letters J to N indicate years 2018 to 2022, respectively. - Letters P to Y indicate years 2023 to 2032, respectively. NOTE The years from 2010 are represented by upper-case letters excluding I, O, and Z because the three letters are similar to the digits 1, 0, and 2. ● The second digit indicates the month. - Digits 1 to 9 indicate January to September, respectively. - Letters A to C indicate October to December, respectively. |
|     5 | Serial number (six digits).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|     6 | RoHS compliance (one character). Y indicates RoHS compliant.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     7 | Internal model, that is, product name.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |

## A.2 Acronyms and Abbreviations

| A     |                                      |
|-------|--------------------------------------|
| AI    | Artificial Intelligence              |
| B     |                                      |
| BTB   | Board to Board Connector             |
| C     |                                      |
| CAN   | Controller Area Network              |
| D     |                                      |
| DK    | Developer Kit                        |
| F     |                                      |
| FLOPS | Floating-point Operations Per Second |
| H     |                                      |
| HDR   | High Dynamic Range                   |
| I     |                                      |
| I 2 C | Inter-integrated Circuit             |

ISP

L

LAN

Image Signal Processing

Local Area Network

S

SPI

Serial Peripheral Interface

T

TFLOPS

teraFLOPS

TOPS

Tera Operations Per Second

U

USB

Universal Serial Bus

UART

Universal Asynchronous Receiver/ Transmitter