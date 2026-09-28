#### **Huawei Atlas 200 DK developer kit**

# **Technical White Paper (Model 3000)**

**Issue** 08 **Date** 2021-03-16

#### **Trademarks and Permissions**

All other trademarks and trade names mentioned in this document are the property of their respective holders.

#### **Notice**

The purchased products, services and features are stipulated by the contract made between Huawei and the customer. All or part of the products, services and features described in this document may not be within the purchase scope or the usage scope. Unless otherwise specified in the contract, all statements, information, and recommendations in this document are provided "AS IS" without warranties, guarantees or representations of any kind, either express or implied.

The information in this document is subject to change without notice. Every effort has been made in the preparation of this document to ensure accuracy of the contents, but all statements, information, and recommendations in this document do not constitute a warranty of any kind, express or implied.

Address: Huawei Industrial Base Bantian, Longgang Shenzhen 518129 People's Republic of China Website: <https://e.huawei.com>

# **About This Document**

# <span id="page-2-0"></span>**Purpose**

This document describes the system design, features, and specifications of the Atlas 200 DK developer kit (model 3000).

# **Intended Audience**

This document is intended for:

- Huawei presales engineers
- Channel partner presales engineers
- Enterprise presales engineers

# **Symbol Conventions**

The symbols that may be found in this document are defined as follows.

# **Change history**

| Issue Date Description                                    |                                       |
|-----------------------------------------------------------|---------------------------------------|
| 08 2021-03-16 This issue is the eighth official release.  |                                       |
| capability of the AI processor in                         | 3.1                                   |
| 07 2020-11-26 This issue is the seventh official release. |                                       |
| ●                                                         | Changed the product name from         |
|                                                           | Atlas 200 Developer Kit to Atlas 200  |
| ●                                                         | Optimized descriptions in section 4.3 |
| 06 2020-08-28 This issue is the sixth official release.   |                                       |
| Optimized                                                 | 3.1 Specifications                    |
| 05 2020-08-12 This issue is the fifth                     | official release.                     |
| ●                                                         | 1.1 Overview                          |
| ●                                                         | 3.1 Specifications                    |
| ●                                                         | 4.4 Power Port and Reset Button       |
| ●                                                         | 4.5 MIPI-CSI Ports                    |
| 04 2020-07-07 This issue is the fourth official release.  |                                       |
| Updated                                                   | 5 Certifications                      |
| 03 2020-05-30 This issue is the third official release.   |                                       |
| 02 2019-07-29 This issue is the second official release.  |                                       |
| 01 2019-05-10 This issue is the first                     | official release.                     |

# **Contents**

| About This Document................................................................................................................                                                                    | ii |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| 1.1 Overview....................................................................................................................................................................................       | 1  |
| 2.1 Performance..............................................................................................................................................................................          | 6  |
| 2.2 Maintainability.........................................................................................................................................................................           | 6  |
| 3 Product Specifications............................................................................................................                                                                   | 7  |
| 3.1 Specifications............................................................................................................................................................................         | 7  |
| 3.2 Environmental Specifications..............................................................................................................................................                         | 9  |
| 4.2 USB Port...................................................................................................................................................................................        | 10 |
| 4.3 microSD Card Port................................................................................................................................................................                  | 10 |
| 4.4 Power Port and Reset Button...........................................................................................................................................                             | 11 |
| 4.5 MIPI-CSI Ports........................................................................................................................................................................             | 11 |
| 4.6 40-Pin Extended Port...........................................................................................................................................................                    | 13 |
| 4.6.1 UART......................................................................................................................................................................................       | 14 |
| 4.6.2 SPI...........................................................................................................................................................................................   | 14 |
| 4.6.3 I 2 C........................................................................................................................................................................................... | 14 |
| 4.6.4 GPIO.......................................................................................................................................................................................      | 14 |
| 5 Certifications..........................................................................................................................                                                             | 18 |
| 6 Warranty.................................................................................................................................                                                            | 20 |
| A Appendix.................................................................................................................................                                                            | 21 |
| A.1 SN..............................................................................................................................................................................................   | 21 |

# <span id="page-5-0"></span>**1 Product Introduction**

1.1 Overview [1.2 Appearance](#page-6-0) [1.3 System Architecture](#page-8-0)

# **1.1 Overview**

The Atlas 200 DK developer kit (model 3000) is a developer board that uses the Atlas 200 AI accelerator module (model 3000). It opens interfaces for developers to use the Atlas 200 AI accelerator module (model 3000) easily. The Atlas 200 DK developer kit (model 3000) is ideal for research and development in fields such as safe city, drones, robots, and video servers.

The Atlas 200 AI accelerator module (model 3000) is a high-performance AI compute module powered by the Ascend 310 AI Processor. It performs image and video analysis and inference and has been widely used in intelligent surveillance, robots, drones, and video servers.

#### NO TE

The Ascend 310 AI Processor is a chip designed for image recognition, video processing, inference computing, and machine learning. It features high performance and low power consumption. The Ascend 310 AI Processor has two built-in AI cores, supports 128-bit LPDDR4X, and delivers computing power of up to 22 TOPS (INT8).

The Atlas 200 DK developer kit (model 3000) provides two types of printed circuit boards (PCBs): IT21DMDA (old PCB) and IT21VDMB (new PCB). **Table 1-1** shows the differences between the two types of PCBs. The first eight digits of the product name indicate the PCB model. For details, see **[A.1 SN](#page-25-0)**.

**Table 1-1** PCB specification comparisons

<span id="page-6-0"></span>

| Item            | IT21DMDA (Old   |    |            |
|-----------------|-----------------|----|------------|
| MIPI-CSI port   | 22-pin, without |    |            |
| Number of GPIOs | 8               | 11 | 4.6.4 GPIO |

# **1.2 Appearance**

**Figure 1-1** shows the appearance of the Atlas 200 DK developer kit (model 3000).

**Figure 1-1** Appearance

#### NO TE

There are two types of labels for the power port. The figure is for reference only.

#### **Figure 1-2** Dimensions (unit: mm)

#### <span id="page-8-0"></span>**Figure 1-3** Ports

| 1 | Power indicator | 2 | Power port        |
|---|-----------------|---|-------------------|
| 3 | USB             | 4 | microSD card slot |
| 5 | Network port    | 6 | Reset button      |

# **1.3 System Architecture**

The Atlas 200 DK developer kit (model 3000) consists of the Atlas 200 AI accelerator module (model 3000), audio/video interface chip (Hi3559C), and LAN switch or PHY. **[Figure 1-4](#page-9-0)** and **[Figure 1-5](#page-9-0)** show the system architectures of the Atlas 200 DK developer kit (model 3000).

<span id="page-9-0"></span>**Figure 1-4** Block diagram when the PCB is IT21DMDA

**Figure 1-5** Block diagram when the PCB is IT21VDMB

# **2 Product Features**

<span id="page-10-0"></span>2.1 Performance 2.2 Maintainability

# **2.1 Performance**

- Provides up to 22 TOPS INT8 computing power.
- Supports 2-channel camera inputs, 2-channel ISP, and HDR10.
- Supports 1000 Mbit/s Ethernet to provide high-speed network connections, delivering strong computing capabilities.
- Provides a universal 40-pin expansion connector (reserved), facilitating product prototype design.

# **2.2 Maintainability**

- Supports online upgrade, facilitating routine maintenance.
- Obtains the device information such as temperature and voltage status inband and out-of-band, simplifying management.

# <span id="page-11-0"></span>**3 Product Specifications**

3.1 Specifications [3.2 Environmental Specifications](#page-13-0)

# **3.1 Specifications**

**Table 3-1** Hardware specifications

| Item               | Specification                                  |
|--------------------|------------------------------------------------|
| AI processor       | Ascend 310 AI Processor                        |
| ●                  | 2 x Da Vinci AI cores                          |
| ●                  | 8 x A55 ARM cores (maximum frequency: 1.6 GHz) |
| AI compute power ● | Half precision (FP16): 4/8/11 TFLOPS           |
| ●                  | Integer precision (INT8): 8/16/22 TOPS         |
| Memory ●           | Type: LPDDR4X                                  |
| ●                  | Bit width: 128 bits/64 bits                    |
| ●                  | Capacity: 8 GB/4 GB                            |
| ●                  | Rate: 3200 Mbit/s                              |
| ●                  | Error checking and correcting (ECC)            |

| Item           | Specification                                          |
|----------------|--------------------------------------------------------|
| ●              | H.264/H.265 decoder, 20-channel 1080p (1920 x 1080)    |
| ●              | H.264/H.265 decoder, 16-channel 1080p (1920 x 1080)    |
| ●              | H.264/H.265 decoder, 2-channel 4K (3840 x 2160) 60     |
| ●              | H.264/H.265 encoder, 1-channel 1080p (1920 x 1080)     |
| ●              | JPEG decoding at 1080p (1920 x 1080) 256 FPS and       |
| ●              | PNG decoding at 1080p (1920 x 1080) 24 FPS, up to      |
| Storage        | 1 x microSD card, which supports SD 3.0 and provides a |
| Port ●         | Network port                                           |
|                | – 1 x GE RJ-45 port                                    |
| ●              | USB port                                               |
|                | – 1 x USB 3.0 Type-C port, which can be used only to   |
| ●              | Other ports                                            |
|                | – 1 x 40-pin I/O connector                             |
|                | – 2 x MIPI connectors                                  |
|                | – 2 x onboard microphones                              |
| Power supply ● | If the PCB is IT21VDMB, the input voltage is 12 V.     |
| ●              | If the PCB is IT21DMDA, the input voltage is 5 V to 28 |
| Net weight     | 234 g                                                  |

# <span id="page-13-0"></span>**3.2 Environmental Specifications**

**Table 3-2** Environment specifications

| Item                | Specification                                          |
|---------------------|--------------------------------------------------------|
| Storage temperature | 0°C to 85°C (32°F to 185°F)                            |
| Maximum altitude    | 3000 m (9842.4 ft.) For altitudes above 900 m (2952.72 |

<span id="page-14-0"></span>4.1 GE Port 4.2 USB Port 4.3 microSD Card Port [4.4 Power Port and Reset Button](#page-15-0) [4.5 MIPI-CSI Ports](#page-15-0) [4.6 40-Pin Extended Port](#page-17-0) [4.7 LED Status Indicators](#page-18-0)

# **4.1 GE Port**

The Atlas 200 DK developer kit (model 3000) provides an external 10/100/1000M Base-T interface, using the RJ45 connector and connecting to a network with a common network cable.

# **4.2 USB Port**

The Atlas 200 DK developer kit (model 3000) provides a Type-C USB port that is compatible with USB 3.0 (SuperSpeed), USB 2.0 (HighSpeed), and USB 1.1 (FullSpeed) communication protocols. This port can be used only in device mode and does not support the master mode. It is used to connect to the debugging host for loading and debugging.

# **4.3 microSD Card Port**

The Atlas 200 DK developer kit (model 3000) provides a microSD card port that supports SD 3.0 and is backward compatible with SD 2.0. You are advised to use a standard microSD card of SD 3.0. The minimum capacity is 8 GB and the maximum capacity is 2 TB.

#### <span id="page-15-0"></span>NO TE

- The microSD card is based on the flash memory. NAND flash memory is now commonly used in the industry. It uses electrons on the floating gate to store data. However, electrons frequently passing through the floating gate can weaken the gate's ability to store electrons and eventually make the gate unable to store electrons. This problem is common to NAND flash memory. To prevent failures of NAND flash memory, accurately assess the amount of service data to be written.
- For details about the application scenarios of Micro SD cards, see the **[SD Card Technical](https://e.huawei.com/en/material/datacenter/server/7670f86edf4a4b8ebfacbba337a37771) [White Paper](https://e.huawei.com/en/material/datacenter/server/7670f86edf4a4b8ebfacbba337a37771)**.

# **4.4 Power Port and Reset Button**

The power supply port of the Atlas 200 DK developer kit (model 3000) uses a common DC connector, and the power supply must be equal to or higher than 36 W. If the power supply is lower than 36 W, the transient power supply may be insufficient, causing system exceptions.

The RST button is used to reset the system. When the system is abnormal, you can press this button to reset the system.

- If the PCB is IT21VDMB, the input voltage is 12 V.
- If the PCB is IT21DMDA, the input voltage is 5 V to 28 V.

# **4.5 MIPI-CSI Ports**

The Atlas 200 DK developer kit (model 3000) has two MIPI-CSI ports, and the definitions of the two ports are the same.

- **Table 4-1** defines the pins of a MIPI-CSI port when the PCB is IT21VDMB.
- **[Table 4-2](#page-16-0)** defines the pins of a MIPI-CSI port when the PCB is IT21DMDA.

**Table 4-1** Pin definition of the camera port (PCB IT21DMDA)

| Pin | Name      | Pin | Name      |
|-----|-----------|-----|-----------|
| 1   | GND       | 2   | CAM_DN0   |
| 3   | CAM_DP0   | 4   | GND       |
| 5   | CAM_DN1   | 6   | CAM_DP1   |
| 7   | GND       | 8   | CAM_CN    |
| 9   | CAM_CP    | 10  | GND       |
| 11  | NC        | 12  | NC        |
| 13  | GND       | 14  | NC        |
| 15  | NC        | 16  | GND       |
| 17  | CAM_GPIO0 | 18  | CAM_GPIO1 |
| 19  | GND       | 20  | SCL0      |

<span id="page-16-0"></span>

**Table 4-2** Pin definition of the camera port (PCB IT21VDMB)

| Pin | Name      | Pin | Name      |
|-----|-----------|-----|-----------|
| 1   | NC        | 2   | GND       |
| 3   | NC        | 4   | NC        |
| 5   | GND       | 6   | NC        |
| 7   | NC        | 8   | GND       |
| 9   | NC        | 10  | NC        |
| 11  | GND       | 12  | NC        |
| 13  | NC        | 14  | GND       |
| 15  | NC        | 16  | NC        |
| 17  | GND       | 18  | NC        |
| 19  | NC        | 20  | GND       |
| 21  | NC        | 22  | CAM_DN0   |
| 23  | GND       | 24  | CAM_DP0   |
| 25  | NC        | 26  | GND       |
| 27  | NC        | 28  | CAM_DN1   |
| 29  | GND       | 30  | CAM_DP1   |
| 31  | NC        | 32  | GND       |
| 33  | NC        | 34  | CAM_CN    |
| 35  | GND       | 36  | CAM_CP    |
| 37  | NC        | 38  | GND       |
| 39  | GND       | 40  | NC        |
| 41  | GND       | 42  | CAM_GPIO0 |
| 43  | NC        | 44  | NC        |
| 45  | CAM_GPIO1 | 46  | SCL0      |
| 47  | SDA0      | 48  | NC        |
| 49  | NC        | 50  | VCC3V3    |

<span id="page-17-0"></span>

# **4.6 40-Pin Extended Port**

**Table 4-3** Pin definition

| Pin | Name     | Level | Pin | Name     | Level |
|-----|----------|-------|-----|----------|-------|
| 1   | +3.3 V    | 3.3 V  | 2   | +5.0 V    | 5 V    |
| 3   | I        |       |     |          |       |
|     | 2 C2-SDA | 3.3 V  | 4   | +5.0 V    | 5 V    |
| 5   | I        |       |     |          |       |
|     | 2 C2-SCL | 3.3 V  | 6   | GND      |       |
| 7   | GPIO0    | 3.3 V  | 8   | TXD0     | 3.3 V  |
| 9   | GND      |       | 10  | RXD0     | 3.3 V  |
| 11  | GPIO1    | 3.3 V  | 12  | NC       |       |
| 13  | NC       |       | 14  | GND      |       |
| 15  | GPIO2    | 3.3 V  | 16  | TXD1     | 3.3 V  |
| 17  | +3.3 V    | 3.3 V  | 18  | RXD1     | 3.3 V  |
| 19  | SPI-MOSI | 3.3 V  | 20  | GND      |       |
| 21  | SPI-MISO | 3.3 V  | 22  | NC       |       |
| 23  | SPI-CLK  | 3.3 V  | 24  | SPI-CS   | 3.3 V  |
| 25  | GND      |       | 26  | GPIO10   | 3.3 V  |
| 27  | GPIO8    | 3.3 V  | 28  | GPIO9    | 3.3 V  |
| 29  | GPIO3    | 3.3 V  | 30  | GND      |       |
| 31  | GPIO4    | 3.3 V  | 32  | NC       |       |
| 33  | GPIO5    | 3.3 V  | 34  | GND      |       |
| 35  | GPIO6    | 3.3 V  | 36  | +1.8 V    | 1.8 V  |
| 37  | GPIO7    | 3.3 V  | 38  | TXD-3559 | 3.3 V  |
| 39  | GND      |       | 40  | RXD-3559 | 3.3 V  |

#### NO TE

- The NC pin has no connection on the board.
- The maximum output current of 1.8 V is 500 mA, the maximum output current of 3.3 V is 500 mA, and the maximum output current of 5 V is 1 A.

#### <span id="page-18-0"></span>**4.6.1 UART**

UART 0 corresponds to pin 8 and pin 10. It is the default debug console of Ascend 310. Its baud rate is 115200 bit/s.

UART 1 corresponds to pin 16 and pin 18. It is used for expansion and communication with other modules.

UART-Hi3559 corresponds to pin 38 and pin 40. It is used for debugging the Hi3559 chip over the MIPI-CSI. Its baud rate is 115200 bit/s.

**Figure 4-1** Debugging serial port

#### **4.6.2 SPI**

The SPI-CS0, SPI-CLK, SPI-MISO and SPI-MOSI four-wire SPI interfaces can connect to various sensors, but support only the master mode.

#### **4.6.3 I2C**

The I2C2-SCL and I2C2-SDA form the I2C2 interface, which can be used to connect external sensors and communicate with other modules. The maximum rate is 400 kHz.

#### NO TE

The I2C1 interface is an intra-board interface and is not open to external systems.

#### **4.6.4 GPIO**

- As output pins, GPIO0, GPIO1, and GPIO2 must be connected to external pullup resistors to increase driving capabilities. It is recommended that the value of pull-up resistance is 1–10 kilohms.
  - The PCB IT21VDMB provides 11 independent GPIO pins.
  - The PCB is IT21DMDA, which has eight GPIO pins. Pins 26, 27, and 28 are NC pins.

# **4.7 LED Status Indicators**

There are four LED indicators on the Atlas 200 DK developer kit (model 3000) developer board. See **[Figure 4-2](#page-19-0)** and **[Figure 4-3](#page-20-0)**.

#### **Figure 4-2** LED positions when the mainboard is IT21DMDA

<span id="page-19-0"></span><span id="page-20-0"></span>**Figure 4-3** LED positions when the mainboard is IT21VDMB

#### NO TE

The silkscreen of the indicators is **MINI\_LED2**, **MINI\_LED1**, **3559\_ACT**, and **3559\_VEDIO** from left to right, corresponding to the indicators from left to right.

**Table 4-4** Indicator status 1

**Table 4-5** Indicator status 2

| 3559_A CT | 3559_V EDIO | Developer Board Important Notes Status of the Atlas 200 DK developer kit (model 3000) |
|-----------|-------------|---------------------------------------------------------------------------------------|
| Off       | Off         | The Hi3559C None system is not started.                                               |
| Off       | On          | The Hi3559C None system is being started.                                             |
| On        | On          | The startup None process of the Hi3559C system is complete.                           |

<span id="page-22-0"></span>**Table 5-1** Certifications

| No. Country/Region Certification | Standard          |
|----------------------------------|-------------------|
| 1 Europe CE                      | Safety:           |
| ●                                | IEC               |
| ●                                | EN                |
| ●                                | EN 55032:2012/AC: |
| ●                                | CISPR 32:2012     |
| ●                                | EN 55032:2015     |
| ●                                | CISPR 32:2015     |
| ●                                | EN 55024:2010     |
| ●                                | CISPR 24:2010     |
| ●                                | EN                |
| ●                                | CISPR             |
| ●                                | ETSI EN 300 386   |
| ●                                | ETSI EN 300 386   |
| ●                                | EN 61000-3-2:2014 |
| ●                                | EN 61000-3-3:2013 |
| ●                                | EN 61000-6-2:2005 |

| No. Country/Region Certification | Standard          |
|----------------------------------|-------------------|
| ●                                | EN                |
| 2 Europe RoHS                    | EN 50581: 2012    |
| 3 Japan VCCI                     | VCCI 32-1         |
| 4 China CCC ●                    | GB 4943.1-2011    |
| ●                                | GB/T 9254-2008    |
| ●                                | YD/T 993-1998     |
| 5 Multi-country                  |                   |
| 6 America NRTL ●                 | UL 60950-1:2007   |
| ●                                | CSA               |
| ●                                | UL 62368-1:2014   |
| ●                                | CSA               |
| 7 International CB ●             | IEC 60950-1:2005, |
| ●                                | IEC 62368-1:2014  |
| 8 America FCC                    | FCC CFR47 Part 15 |

<span id="page-24-0"></span>For details, see the **[Maintenance & Warranty](http://support.huawei.com/enterprise/servesolution)**.

# <span id="page-25-0"></span>**A.1 SN**

The serial number (SN) on the label plate uniquely identifies a device. The SN is required when you contact Huawei technical support.

#### **Figure A-1** SN example

**Table A-1** SN description

| No. | Description                                                 |
|-----|-------------------------------------------------------------|
| 1   | Serial number (two digits). The value is fixed to 21.       |
| 2   | Material identification code (eight characters), that is,   |
| 3   | Vendor code (two characters). The code 10 indicates Huawei, |

<span id="page-26-0"></span>

# **A.2 Acronyms and Abbreviations**

| A       |                                      |
|---------|--------------------------------------|
| AI B    | Artificial Intelligence              |
| BTB C   | Board to Board Connector             |
| CAN D   | Controller Area Network              |
| DK F    | Developer Kit                        |
| FLOPS H | Floating-point Operations Per Second |
| HDR I   | High Dynamic Range                   |
| 2 C I   | Inter-integrated Circuit             |

**ISP** Image Signal Processing **L LAN** Local Area Network **S SPI** Serial Peripheral Interface **T TFLOPS** teraFLOPS **TOPS** Tera Operations Per Second **U USB** Universal Serial Bus **UART** Universal Asynchronous Receiver/ Transmitter