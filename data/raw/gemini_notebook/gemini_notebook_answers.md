## RAG Q&A

### q1 (direct)

* **Question:** What does the `--input_fp16_nodes` parameter do in the ATC tool?
* **Answer:** The `--input_fp16_nodes` parameter specifies the name of the input node(s) with input data of type FP16 (for example, `"node_name1;node_name2"`, where each node is enclosed in double quotation marks and separated by semicolons). This parameter is optional (default value: `N/A`), is mutually exclusive with `--insert_op_conf`, and can be used in conjunction with `--is_input_adjust_hw_layout` (which, when set to `true`, allows `input_fp16_nodes` to accept `NC1HWC0` tensors of type FP16).
* **Source Document Title:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`)

### q2 (direct)

* **Question:** What are the main hardware differences between IT21DMDA and IT21VDMB board versions of the Atlas 200 DK?
* **Answer:** According to Table 1-1 (*PCB specification comparisons*), Section 4.4, and Section 4.6.4 of the white paper, the main hardware differences between `IT21DMDA` (Old PCB) and `IT21VDMB` (New PCB) are:
  * **Power input:** `IT21DMDA` accepts **5 V to 28 V**, whereas `IT21VDMB` accepts **12 V**.
  * **MIPI-CSI port:** `IT21DMDA` has a **22-pin** port without cables, whereas `IT21VDMB` has a **51-pin** port with cables.
  * **Number of GPIOs:** `IT21DMDA` provides **8** GPIO pins (pins 26, 27, and 28 are NC pins), whereas `IT21VDMB` provides **11** independent GPIO pins.
* **Source Document Title:** `Huawei Atlas 200 DK developer kit Technical White Paper (Model 3000)` (`WP_DOC`)

### q3 (direct)

* **Question:** What causes the error 'CheckPartitionSpace partition space check failed' during Atlas 200 DK driver upgrade?
* **Answer:** This error occurs during the execution of `./minirc_install_phase1.sh` because an unallocated space of **512 MB** is not reserved on the SD card of the Atlas 200 DK for the Driver upgrade, which may be caused by using an older card creation script. You can verify the unallocated space as the `root` user by running `fdisk -l` to compare total available sectors against allocated sectors, and resolve the issue by connecting the SD card to the Ubuntu server over USB and resizing the partition (for example, using GParted) to reserve the required 512 MB of unallocated space.
* **Source Document Title:** `Atlas 200 DK Environment Deployment Guide` (`ENV_DOC`)

### q4 (direct)

* **Question:** How is the NC1HWC0 5D tensor format structured and what does C0 represent for FP16?
* **Answer:** To improve data access efficiency in the Ascend AI Software Stack, tensor data is stored in the Huawei-defined 5D format `NC1HWC0`:
  * **Dimensions:** `N` represents the batch size, `H` is the height, `W` is the width, `C0` is the size of the cube / matrix computing unit in the AI Core (closely related to the microarchitecture), and `C1 = (C + C0 - 1) / C0` (if the division is not exact, the last data segment is padded with zeros to align with `C0`).
  * **Value of C0:** For **FP16**, `C0` is **16** (and for **INT8**, `C0` is **32**), and `C0` elements must be stored contiguously in memory.
  * **Conversion from NHWC:** The `NHWC` data is split along the `C` dimension into `C1` pieces of `NHWC0`, which are then arranged contiguously in memory to form `NC1HWC0`.
* **Source Document Titles:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`), `TBE Custom Operator Development Guide (CLI)` (`TBE_DOC`)

### q5 (multi)

* **Question:** Which CSC matrix coefficients are used in AIPP to convert YUV420SP_U8 to RGB888_U8 for JPEG images?
* **Answer:** In the AIPP configuration (`aipp_op` with `input_format : YUV420SP_U8`, `csc_switch : true`, and `rbuv_swap_switch : false`), the Color Space Conversion (CSC) matrix coefficients are:
  * **For JPEG images** (with `input_bias_0 : 0`, `input_bias_1 : 128`, `input_bias_2 : 128`): `matrix_r0c0 : 256`, `matrix_r0c1 : 0`, `matrix_r0c2 : 359`, `matrix_r1c0 : 256`, `matrix_r1c1 : -88`, `matrix_r1c2 : -183`, `matrix_r2c0 : 256`, `matrix_r2c1 : 454`, `matrix_r2c2 : 0`.
  * **For BT.601 Narrow** (with `input_bias_0 : 16`, `input_bias_1 : 128`, `input_bias_2 : 128`): `matrix_r0c0 : 298`, `matrix_r0c1 : 0`, `matrix_r0c2 : 409`, `matrix_r1c0 : 298`, `matrix_r1c1 : -100`, `matrix_r1c2 : -208`, `matrix_r2c0 : 298`, `matrix_r2c1 : 516`, `matrix_r2c2 : 0`.
* **Source Document Titles:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`)

### q6 (multi)

* **Question:** What is the typical power consumption and power input voltage range of the Atlas 200 DK?
* **Answer:** According to Sections 1.1, 3.1, and 4.4 of the Technical White Paper:
  * **Typical power consumption:** **20 W**.
  * **External power supply requirement:** The power supply must be **>= 36 W** (a **12 V 3 A** adapter is configured by default; a power supply lower than 36 W may cause transient power insufficiency and system exceptions).
  * **Power input voltage range:** **12 V** if the PCB is `IT21VDMB`, and **5 V to 28 V** if the PCB is `IT21DMDA`.
* **Source Document Title:** `Huawei Atlas 200 DK developer kit Technical White Paper (Model 3000)` (`WP_DOC`)

### q7 (paraphrase)

* **Question:** How do I configure channel mean subtraction and variance normalization in the AIPP configuration file?
* **Answer:** In the AIPP configuration file (`aipp_op`), channel mean subtraction and variance normalization are configured per channel using `mean_chn_0`..`3` (mean value of each channel, such as `mean_chn_0 : 104`, `mean_chn_1 : 117`, `mean_chn_2 : 123`), along with `min_chn_0`..`3` (minimum value of each channel) and `var_reci_chn_0`..`3` (variance / reciprocal factor of each channel), following the rule: `pixel_out = (pixel_in - mean_chn - min_chn) * var_reci_chn`.
* **Source Document Titles:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`)

### q8 (paraphrase)

* **Question:** What should I do if the Atlas board cannot establish connection to the Ubuntu server via USB?
* **Answer:** According to the troubleshooting sections (*What Do I Do If the Atlas 200 DK Cannot Connect to the Ubuntu Server?*), you should perform the following steps:
  * **Check SD card preparation logs and LED indicators:** Verify the SD card partition/preparation logs (e.g., via `fdisk -l` on the Ubuntu server) and ensure the board status indicators (`MINI_LED1` / `MINI_LED2`, LED1/LED2) are normal after power-on.
  * **Check USB connection and virtual NIC IP:** Inspect the USB cable and verify whether the virtual USB NIC exists and has its IP address (e.g., `192.168.1.x` to communicate with the default USB IP `192.168.1.2`) properly configured on the Ubuntu server.
  * **Use alternative connection or UART serial debugging:** Try connecting via a network cable (NIC mode, default IP `192.168.0.2`) or use a 3.3 V USB-to-TTL serial cable (UART) with a serial debugging tool (such as IPOP / PL2303 driver) to inspect the startup logs.

* **Source Document Titles:** `Atlas 200 DK Environment Deployment Guide` (`ENV_DOC`), `Atlas 200 DK User Guide` (`UserGuide2021`, `UserGuide2020`)

### q9 (followup)

* **Context History:**
* **User:** What is the value of C0 for FP16 in the NC1HWC0 5D tensor format?
* **Assistant:** In the NC1HWC0 5D tensor format, C0 represents the size of the cube unit in the AI Core and equals 16 for FP16.
* **Question:** And what is its value for INT8 data types?
* **Answer:** For **INT8** data types in the `NC1HWC0` 5D tensor format, `C0` is **32**.
* **Source Document Titles:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`), `TBE Custom Operator Development Guide (CLI)` (`TBE_DOC`)

### q10 (none)

* **Question:** What is the GPU clock speed of Nvidia RTX 4090?
* **Answer:** This information is not provided in the attached documents. The provided corpus exclusively covers Huawei Ascend / Atlas 200 DK hardware and software documentation.
* **Source Document Title:** `None` (Out of domain)

### q11 (direct)

* **Question:** Why does DMalloc return error code 16847020 (HIAI_GRAPH_MEMORY_POOL_STOPPED) in a single-process multi-graph scenario?
* **Answer:** In a single-process multi-graph scenario on the host, graphs of the same process share a single memory pool maintained by the **first graph**. When the first graph is destroyed, the shared memory pool is stopped, causing `DMalloc` to return error code `16847020` (`HIAI_GRAPH_MEMORY_POOL_STOPPED`) until the first graph is completely destroyed and the second graph becomes the new first graph. This issue can be avoided by not destroying the first graph or by adding a retry mechanism.
* **Source Document Title:** `ApplicationDevelopmentGuide_MindStudio_`

### q12 (direct)

* **Question:** What are the default memory allocations for weights and feature maps during TensorFlow training on Ascend, and which parameters adjust them?
* **Answer:** By default, **5 GB** is allocated for weight memory and **26 GB** is allocated for feature map memory (with a combined limit of **31 GB**). These allocations can be adjusted using the `variable_memory_max_size` and `graph_memory_max_size` parameters in `NPURunConfig` or the session configuration.
* **Source Document Title:** `TensorFlowNetworkModelPortingandTrainingGuide`

### q13 (direct)

* **Question:** In the TBE operator description JSON file inside kernel_meta, what do the magic values RT_DEV_BINARY_MAGIC_ELF and RT_DEV_BINARY_MAGIC_ELF_AICPU indicate?
* **Answer:** In the `kernel_meta` operator description JSON file, `RT_DEV_BINARY_MAGIC_ELF` indicates that the operator running target is the **AI Core** (with a `.o` binary file suffix), whereas `RT_DEV_BINARY_MAGIC_ELF_AICPU` indicates that the operator running target is the **AI CPU** (with a `.so` binary file suffix).
* **Source Document Titles:** `TBE Custom Operator Development Guide (CLI)` (`TBE_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`)

### q14 (direct)

* **Question:** What are the default port numbers and initial login usernames for the Mind Studio GUI and the Profiling tool?
* **Answer:** For the **Mind Studio GUI**, the default port number is **8888** and the initial login username is `MindStudioAdmin` (initial password: `Huawei123@`). For the **Profiling tool GUI**, the default port number is **8099** and the initial login username is `msvpadmin` (initial password: `Admin12#$`).
* **Source Document Title:** `MindStudioInstallationGuideUbuntuX86`

### q15 (multi)

* **Question:** How is the required output memory size calculated for a 300 x 300 YUV420SP_UV image in DVPP, and which Matrix APIs must be used to allocate and free this memory?
* **Answer:** Due to DVPP alignment rules (width aligned to 16 pixels and height aligned to 2 pixels), a `300 x 300` `YUV420SP_UV` image is rounded up to **304 x 300**. Because each `YUV420SP` pixel requires **1.5** (`3/2`) bytes, the required output memory size is **304 x 300 x 3/2 bytes**. This memory must be allocated and freed in pairs on the device side using `HIAI_DVPP_DMalloc` (or `HIAIMemory::HIAI_DVPP_DMalloc`) and `HIAI_DVPP_DFree` (or `HIAIMemory::HIAI_DVPP_DFree`).
* **Source Document Titles:** `ApplicationDevelopmentGuide_MindStudio_`, `Application Software Development Guide (Models 3000)` (`DEV_DOC`)

### q16 (multi)

* **Question:** Which pins on the Atlas 200 DK must be connected with a jumper cap to prepare an SD card without a card reader, and what are the default IP addresses configured in make_sd_card.py for the USB NIC and network port?
* **Answer:** To prepare an SD card without a card reader (using the Atlas 200 DK as a USB device), power off the board and place a jumper cap or jumper wire over **pin 16** and **pin 18** on the Atlas 200 DK. During card creation (`make_sd_card.py`), the default IP address for the USB virtual NIC (`USB_CARD_DEFAULT_IP`) is **192.168.1.2**, and the default IP address for the physical network port (`NETWORK_CARD_DEFAULT_IP`) is **192.168.0.2**.
* **Source Document Titles:** `Atlas 200 DK Environment Deployment Guide` (`ENV_DOC`), `Atlas 200 DK User Guide` (`UserGuide2020`, `UserGuide2021`)

### q17 (paraphrase)

* **Question:** How should VDEC engines and thread counts be configured in the graph file to decode multiple video streams concurrently without frame order issues?
* **Answer:** In the graph configuration file, configure **multiple VDEC engines** so that each video stream fixedly corresponds to **one engine** (supporting up to **16** video streams per graph), and set `thread_num` to **1** in each VDEC engine segment so that one VDEC engine corresponds to a single thread.
* **Source Document Title:** `ApplicationDevelopmentGuide_MindStudio_`

### q18 (paraphrase)

* **Question:** How can I speed up slow offline model conversion when running on an Arm (AArch64) server?
* **Answer:** On an Arm (`AArch64`) development server, you can speed up offline model conversion by installing `numactl` and binding CPU cores **16-31** using the command `numactl -C 16-31 --localalloc <command>` (with `atc` or `omg`). Additionally, in the ATC tool, you can enable parallel operator compilation via the `TE_PARALLEL_COMPILER` environment variable.
* **Source Document Titles:** `Atlas 200 DK ATC Tool Instructions` (`ATC_DOC`), `Application Software Development Guide (Models 3000)` (`DEV_DOC`)

### q19 (followup)

* **Context History:**
* **User:** How can I check if the Atlas 200 AI accelerator module is a VC or earlier version using the command line?
* **Assistant:** You can run 'cat /proc/cmdline' on the Atlas 200 DK and inspect the boardid parameter. A value of boardid=000 indicates a VC or earlier version.
* **Question:** And what value indicates that it is a VD version?
* **Answer:** When inspecting `/proc/cmdline` (`cat /proc/cmdline`) on the Atlas 200 DK, a value of `boardid=004` (i.e., **004**) indicates that the Atlas 200 AI accelerator module is a **VD** version.
* **Source Document Titles:** `Atlas 200 DK Environment Deployment Guide` (`ENV_DOC`), `Atlas 200 DK User Guide` (`UserGuide2020`, `UserGuide2021`)

### q20 (none)

* **Question:** How many Tensor Cores does the Nvidia H100 Hopper GPU have?
* **Answer:** This information is not provided in the attached documents. The provided corpus only covers Huawei Ascend / Atlas 200 DK documentation and does not contain specifications for Nvidia GPUs.
* **Source Document Title:** `None` (Out of domain)
