# **Atlas 200 DK V100R020C00 ATC Tool Instructions**

**Issue** 01 **Date** 2021-01-29

# **Trademarks and Permissions**

# **Notice**

The purchased products, services and features are stipulated by the contract made between Huawei and the customer. All or part of the products, services and features described in this document may not be within the purchase scope or the usage scope. Unless otherwise specified in the contract, all statements, information, and recommendations in this document are provided "AS IS" without warranties, guarantees or representations of any kind, either express or implied.

The information in this document is subject to change without notice. Every effort has been made in the preparation of this document to ensure accuracy of the contents, but all statements, information, and recommendations in this document do not constitute a warranty of any kind, express or implied.

# **Contents**

| 1 Introduction..............................................................................................................................                                                   | 1   |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| 4.1 Overview.................................................................................................................................................................................. | 38  |
| 4.3 CSC Configuration................................................................................................................................................................          | 43  |
| 4.4 Cropping and Padding Configuration............................................................................................................................                             | 60  |
| 5 Single-Operator JSON File Configuration.......................................................................                                                                               | 65  |
| 7.1 Caffe Network Model..........................................................................................................................................................              | 73  |
| 7.1.1.1 Overview...........................................................................................................................................................................    | 73  |
| 7.1.1.2 Extended Operator List................................................................................................................................................                 | 74  |
| 7.1.1.4 Caffe Operator Specifications....................................................................................................................................                      | 85  |
| 7.1.1.5 Sample Reference........................................................................................................................................................               | 155 |
| 7.1.1.5.1 Modifying Faster R-CNN Prototxt......................................................................................................................                                | 155 |
| 7.1.1.5.2 Modifying YOLOv3 Prototxt.................................................................................................................................                           | 156 |
| 7.1.1.5.3 Modifying YOLOv2 Prototxt.................................................................................................................................                           | 159 |
| 7.1.1.5.4 Modifying SSD Prototxt.........................................................................................................................................                      | 161 |
| 7.2.1 TensorFlow Operator Specifications.........................................................................................................................                              | 163 |
| 8 FAQs.......................................................................................................................................                                                  | 176 |

[8.2 How Do I Determine the Video Stream Format Standard When I Perform CSC on a Model Using AIPP?](#page-179-0) ......................................................................................................................................................................................................... 176 8.3 What Do I Do If a Network Model Containing an NPU-based Custom Operator Failed to Be Frozen? [......................................................................................................................................................................................................... 177](#page-180-0)

<span id="page-4-0"></span>This document describes how to use the Ascend Tensor Compiler (ATC) to convert models of open-source frameworks (such as Caffe and TensorFlow) and singleoperator .json files into offline models supported by the Ascend AI Processor. During model conversion, operator scheduling optimization, weight data rearrangement, and memory optimization can be implemented, and model preprocessing can be completed without depending on the device.

# **ATC Architecture**

**Figure 1-1** shows the ATC architecture.

**Figure 1-1** ATC architecture

# **ATC Flow**

**[Figure 1-2](#page-5-0)** shows the flow of model conversion with the ATC.

## <span id="page-5-0"></span>**Figure 1-2** ATC flowchart

The flow is described as follows:

- 1. Install the ATC in the development environment and open the ATC from the installation path. For details, see "Preparations" in **[Preparations](#page-36-0)**.
- 2. Upload the model or single-operator .json file to be converted to the development environment. For details, see **[Example \(Converting a Caffe](#page-37-0) [Model into an Offline Model\)](#page-37-0)** or **[Example \(Converting a Single-Operator](#page-39-0) [JSON File into an Offline Model\)](#page-39-0)**.
- 3. Convert the model using the ATC. **[Configure AI preprocessing \(AIPP\)](#page-41-0)** as required.

Ascend AI Processor introduces the artificial intelligence preprocessing (AIPP) module for hardware-based image preprocessing including color space conversion (CSC), image normalization (by subtracting the mean value or multiplying a factor), image cropping (by specifying the crop start and cropping the image to the size required by the neural network), and much more. Images output by the digital vision preprocessing (DVPP) module are YUV420SP images with rounded-up heights and widths. The DVPP module does not output RGB images. Therefore, the AIPP module is introduced to convert the YUV420SP images with rounded-up heights and widths and then crop them to the size required by the model.

# **Key Terms**

- AIPP The Ascend AI Processor introduces the AIPP module for hardware-based image preprocessing including CSC, image normalization (by subtracting the mean value or multiplying a factor), image cropping (by specifying the crop start and cropping the image to the size required by the neural network), and much more.
- YUV420SP It is a lossy image color encoding format, which can be YUV420SP\_UV or YUV420SP\_VU.

- Repository It stores the tuned tiling policy for direct call in operator build.
- Cost model It is an evaluator that evaluates the quality of tiling in the tiling space for selecting the optimal tiling policy.
- Format

In the deep learning framework, n-dimensional data is stored in an ndimensional array. For example, a feature graph of a convolutional neural network is stored in a four-dimensional array. The four dimensions are N, H, W and C, which stand for batch size, height, width, and channels, respectively.

Data can be stored only in linear mode because the dimensions have a fixed order. Different deep learning frameworks store feature maps with different layouts. For example, Caffe uses the layout [Batch, Channels, Height, Width], that is, NCHW, while TensorFlow uses the layout [Batch, Height, Width, Channels], that is, NHWC. Data in TensorFlow is stored in the order of [Batch, Height, Width, Channels], that is, NHWC.

As shown in **Figure 1-3**, an RGB image is used as an example. In the NCHW order, the pixel values of each channel are clustered in sequence as RRRGGGBBB. In the NHWC order, the pixel values are interleaved as RGBRGBRGB.

# **Figure 1-3** NCHW and NHWC

To improve data access efficiency, the tensor data is in the Ascend AI Software Stack is stored in the 5D format NC1HWC0. C0, closely related to the micro architecture, is the size of the cube unit in the AI Core. C0 is **16** for FP16 or **32** for INT8. C0 needs to be stored contiguously. C1 = (C + C0 – 1)/C0. If the result is not an integer, round it up.

The NHWC-to-NC1HWC0 conversion process is as follows:

- a. Split the NHWC data into C1 pieces of NHWC0 along the C dimension.
- b. Arrange the C1 pieces of NHWC0 in the memory contiguously, obtaining NC1HWC0.

The following is an example of converting NHWC to NC1HWC0:

- a. Convert RGB images at the first layer into the NC1HWC0 format by using AIPP.
- b. Rearrange the NC1HWC0 output from intermediate layers of the feature map during data movement.

# <span id="page-7-0"></span>**2 Restrictions and Parameters**

# **Restrictions**

Before model conversion, pay attention to the following restrictions:

- To convert a network model such as Faster R-CNN to an offline model supported by the Ascend AI Processor, you must modify the model file (.prototxt) by referring to **[7.1.1 Prototxt Customization](#page-76-0)**.
- Ensure that the **caffe.proto** file used in the Caffe training network is consistent with the built-in **caffe.proto** in the **atc/include/proto** directory and the customized **custom.proto** file in the **atc/sample/op/caffe** directory in the ATC installation path. Otherwise, you need to integrate the **caffe.proto** file used in the Caffe training network into the **custom.proto** file. When you add the definition of the custom operator to **custom.proto**, ensure that the ID is unique. It is recommended that the ID starts at 1000 in ascending order.
- Only Caffe and TensorFlow models can be converted. For Caffe, the input data type can be fp32, fp16 (by setting the **--input\_fp16\_nodes** argument), or uint8 (by configuring AIPP). For TensorFlow, the input data type can be fp16, fp32, unit8, int32, int64, or bool.
- For a Caffe model, **op name** and **op type** in the model file (.prototxt) must be consistent with those in the weight file (.caffemodel) must be the consistent (case sensitive).
- For a TensorFlow model, only the **FrozenGraphDef** format is supported.
- Inputs with dynamic shapes are not supported, for example, NHWC = [?, ?, ?, 3]. The dimension sizes must be static.
- For a Caffe model, the input data is up to 4-dimensional and operators such as reshape and expanddim do not support 5D output. For a TensorFlow model, the restrictions of each operator apply.
- Except the const operator, the input and output at all layers in the model must meet the condition of **dim! = 0**.
- Only the operators listed in **[7.1.1.4 Caffe Operator Specifications](#page-88-0)** are supported. The defined operator restrictions must be met.
- Only the operators listed in **[7.2.1 TensorFlow Operator Specifications](#page-166-0)** are supported.

# **Parameters**

Before using the ATC to convert models, you must understand the following parameters.

#### NO TICE

- If the parameters queried by running the **atc --help** command are not described in the following table, they are reserved or applicable to other chip versions and therefore can be ignored.
- You can run the **atc** command to convert the model in either of the following formats:
  - 1. **atc param1=value1 param2=value2 ...** (No space is allowed before **value**. Otherwise, it will be truncated, and the value of **param** is empty.)
  - 2. **atc param1 value1 param2 value2 ...**

|          | Description Re                                        |
|----------|-------------------------------------------------------|
| --mode   | Work mode.                                            |
| ●        | 0: Generate an offline model adapted to Ascend AI     |
| ●        | 1: Generate an offline model or .json file.           |
| ●        | 3: Perform precheck only to check the validity of the |
| ●        | 5: Convert a .pbtxt file to a .json file. Currently,  |
| --model  | Path of the original model file.                      |
|          | Yes N/                                                |
| --weight | Path of the weight file.                              |

| Description              | Re                                            |
|--------------------------|-----------------------------------------------|
| --output ●               | For a model under an open-source framework:   |
| example,                 | \$HOME/test/out/caffe_resnet18 or             |
|                          | \$HOME/test/out/tf_resnet18 . The name of the |
| with .om, for example,   | caffe_resnet18.om                             |
| ●                        | For a single-operator .json file:             |
| example,                 | \$HOME/test/out/op_model . The naming         |
| (dateType_format_shape)_ | OutputDescription                             |
|                          | Yes N/                                        |

| Description                          | Re                                       |
|--------------------------------------|------------------------------------------|
| If the graph fails to be parsed with | --mode set to 0 or 1 ,                   |
| or if --mode is set to 3             | to perform pre-check only, this          |
| check_result.json                    | . If not specified, the pre-check result |
| Example: --check_report=             | /home/hisisoc/test/out/                  |
| Help information.                    | No                                       |

| Description                                | Re                     |
|--------------------------------------------|------------------------|
| ("") and separate nodes by semicolons (;). | node_name              |
| the output index. For example,             | node_name1:0 indicates |
| output 0 of the node named                 | node_name1             |

| Description    | Re                                                          |
|----------------|-------------------------------------------------------------|
| network are    | FP16 and NC1HWC0 , respectively. Use this                   |
|                | parameter in conjunction with out_nodes                     |
| Example:       | false,true,false,true                                       |
| atc --model=   | \$HOME/test/resnet18.prototxt --weight= \$HOME/test/        |
| resnet18.      | caffemodel --framework=0 --output= \$HOME / test /out/      |
| If set to true | , out_nodes outputs NC1HWC0 tensors of type                 |
| Example:       | "node_ name1 ;node_ name2 " . Enclose each                  |
| exclusive with | --insert_op_conf                                            |
| network are    | FP16 and NC1HWC0 , respectively. Use this                   |
|                | parameter in conjunction with --input_fp16_nodes            |
| Example:       | false,true,false,true                                       |
| atc --model=   | \$HOME/test/resnet18.prototxt --weight= \$HOME/test/        |
| resnet18.      | caffemodel --framework=0 --output= \$HOME / test /out/      |
|                | If this parameter is set to true , input_fp16_nodes accepts |

| Description             | Re                                                   |
|-------------------------|------------------------------------------------------|
| input_name              | must be the node name in the network                 |
| not fixed, for example, | input_name1:? ,h,w,c , this                          |
| 1. Set ?                | to a fixed value (such as 1, 2, 3...) to convert the |
| 2. Set ? to –1          | and use this parameter in conjunction with           |
|                         | --dynamic_batch_size to set the dynamic batch size.  |
| For details, see the    | --dynamic_batch_size parameter.                      |

| Description                                              | Re                                               |
|----------------------------------------------------------|--------------------------------------------------|
| --json Path and name of the JSON file converted from the |                                                  |
| mode=1                                                   | and --om (as well as --weight for a Caffe        |
| Example:                                                 | \$HOME/test/out/resnet18.json                    |
| ● 0                                                      | : no (default)                                   |
| ● 1                                                      | : yes                                            |
| json                                                     | , --mode=1 , and --om (as well as --weight for a |
| atc --mode=1 --om=                                       | \$HOME/test/resnet18.prototxt --json= \$HOME/    |
| test/out/resnet18.json                                   | --framework=0 --weight= \$HOME/test/             |
| resnet18.                                                | caffemodel --dump_mode=1                         |

| Description                                | Re                                                         |
|--------------------------------------------|------------------------------------------------------------|
| Path of the operator mapping configuration | file. This                                                 |
| ●                                          | FSRDetectionOutput (of the Faster R-CNN network)           |
| ●                                          | SSDDetectionOutput (of the SSD network)                    |
| ●                                          | The path allows only uppercase letters, lowercase letters, |
| ●                                          | The following is an example of the operator mapping        |
|                                            | configuration file:                                        |

| Description   | Re                                                         |
|---------------|------------------------------------------------------------|
| Configuration | file of the preprocessing operator (such as,               |
| ●             | The path allows only uppercase letters, lowercase letters, |
| ●             | The following is an example of the configuration file:     |
| ●             | For details about the AIPP configuration file, see the     |

| Description                             | Re                                                   |
|-----------------------------------------|------------------------------------------------------|
|                                         | caffe_resnet18 --soc_version=\${soc_version} --      |
|                                         | output_type= "conv1:0:FP16 " --out_nodes= "conv1:0 " |
| Single-operator definition              | file, which is used to convert                       |
| configuration of a single operator, see | 5 Single-Operator                                    |
| conjunction with this parameter.        | --output and                                         |
| soc_version                             | are required.                                        |
| ●                                       | --output                                             |
| ●                                       | --soc_version                                        |
| ●                                       | --aicore_num                                         |
| ●                                       | --auto_tune_mode                                     |
| ●                                       | --precision_mode                                     |
| ●                                       | --op_select_implmode                                 |
| ●                                       | --enable_small_channel                               |
| ●                                       | --log                                                |

| Description        |                                                         | Re                                        |
|--------------------|---------------------------------------------------------|-------------------------------------------|
| ●                  | force_fp16 (default): forcibly selects fp16 even if the |                                           |
| ●                  | allow_fp32_to_fp16                                      | : selects fp16 if the operator does       |
| ●                  | must_keep_origin_dtype                                  | : retains the original                    |
| ●                  | allow_mix_precision                                     | : allows mixed precision.                 |
|                    | precision_reduce                                        | in the /aic-\${soc_version}-ops          |
|                    | info.json file in the                                   | \${ install_path }/opp/op_impl/           |
|                    | built-in/ai_core/tbe/config/\${soc_version}             | directory                                 |
|                    | – If set to true                                        | , data precision of the operator is fp16. |
|                    | – If set to false                                       | , data precision of the operator is       |
|                    | – If not set, data precision of the operator is the     |                                           |
| Precision ranking: |                                                         | must_keep_origin_dtype >                  |
| allow_fp32_to_fp16 |                                                         | > allow_mix_precision >                   |

| Description          | Re                                      |
|----------------------|-----------------------------------------|
| Performance ranking: | force_fp16 >=                           |
| allow_mix_precision  | > allow_fp32_to_fp16 >                  |
| ● high_precision     | : precision first                       |
| ● high_performance   | (default): performance first            |
| op_select_implmode   | is selected during running. If only     |
| op_select_implmode   | is set to high_performance . In this    |
| case, the            | --op_select_implmode parameter does not |

| Description           |   | Re                              |
|-----------------------|---|---------------------------------|
| mode specified by the |   | --op_select_implmode parameter. |
| op_select_implmode    |   | parameter. For example,         |
| ●                     | 1 | : disabled                      |
| ●                     | 0 | (default):enabled               |

| Description |                        | Re                                                    |
|-------------|------------------------|-------------------------------------------------------|
| When the    | atc                    | command is used for model conversion,                 |
|             | auto_tune_mode=GA      | or                                                    |
|             | auto_tune_mode="RL,GA" | . Otherwise, Auto                                     |
| Schedule    |                        | is used for tuning during model conversion.           |
| ●           | RL tuning result:      |                                                       |
| – The       |                        | \${install_path_atc}/atc/data/rl/\$                   |
| {           | soc_version            | }/built-in/ directory stores the built-in             |
| – The       |                        | \${install_path_atc}/atc/data/rl/\$                   |
| {           | soc_version            | }/custom/ directory stores the                        |
|             |                        | than the repository in the built-in directory or does |
|             | not exist in           | built-in directory during tuning.                     |

| Description                                                      | Re                                                       |
|------------------------------------------------------------------|----------------------------------------------------------|
| ● If a tuned repository exists in the                            | custom directory and the                                 |
| ● When auto tuning is enabled, memory allocation is needed       |                                                          |
| estimated as follows:                                            | 2 x Number of devices x Input data                       |
| size                                                             | . If the memory size exceeds the size of the ATC running |
| ● During GA tuning, the device resource is exclusively occupied. |                                                          |
| ● l2_optimize                                                    | : enabled.                                               |
| ● off_optimize                                                   | : disabled.                                              |

| Description                                      | Re                                                         |
|--------------------------------------------------|------------------------------------------------------------|
| Path and name of the fusion switch configuration | file.                                                      |
| The following is an example of the configuration | file                                                       |
| (The file name fusion_switch.cfg                 | is used as an example).                                    |
| For details about the configuration              | file, see 6 Fusion                                         |
| Upload the configured                            | fusion_switch.cfg file to any                              |
| the following command,                           | /home/Davinci/ is used as an                               |
| --fusion_switch_file=                            | /home/Davinci/fusion_switch.cfg                            |
| ●                                                | The path and file name can contain uppercase and lowercase |
|                                                  | Yes N/                                                     |

| Description                             | Re                                    |
|-----------------------------------------|---------------------------------------|
| input_shape                             | and is mutually exclusive with        |
| ght,imagesize2_width"                   | . Enclose specified parameters in     |
| The following is an example, where, the | -1 argument for                       |
| --input_shape                           | indicates that the dynamic image size |

| Description                                                      | Re                                            |
|------------------------------------------------------------------|-----------------------------------------------|
| ● If this parameter is specified for model conversion, you need  |                                               |
| to add the                                                       | aclmdlSetDynamicHWSize API before the         |
| aclmdlExecute                                                    | API to set the actual image size before model |
| For details about how to use the                                 | aclmdlSetDynamicHWSize                        |
| API, see                                                         | AscendCL API Reference > Model Loading and    |
| Execution                                                        | in Application Software Development Guide     |
| ● Too large image sizes or too many image size choices will      |                                               |
| ● If the dynamic image size function is enabled, the size of the |                                               |
| ● If you set too large image sizes or too many image size        |                                               |
| advised to run the                                               | swapoff -a command to disable the             |

| Description                                          |                                      | Re                                                |
|------------------------------------------------------|--------------------------------------|---------------------------------------------------|
| --log Level of logs to be displayed during ATC model |                                      |                                                   |
| ●                                                    | debug                                | : generates debug, info, warning, error, and      |
| ●                                                    | info                                 | : generates info, warning, error, and event logs. |
| ●                                                    | warning                              | : generates warning, error, and event logs.       |
| ●                                                    | error                                | : generates error and event logs.                 |
| ●                                                    | null                                 | : logging disabled                                |
| ●                                                    | If the                               | /var/log/npu/conf/slog/slog.conf configuration    |
|                                                      | deployed, the log level specified by | global_level in                                   |
|                                                      | this configuration                   | file is used. If global_level is set to           |
|                                                      | null                                 | , the event-level logs can be displayed.          |
| ●                                                    | If the preceding configuration       | files do not exist on the                         |

<span id="page-36-0"></span>The Ascend 310 chip is capable of accelerating inference under the Caffe and TensorFlow framework models. During model conversion, operator scheduling tuning, weight data rearrangement, quantization compression, and memory usage tuning can be implemented, and model preprocessing can be completed without using devices. After model training is complete, you need to convert the trained model to the model file (.om file) supported by Ascend 310 by using ATC, compile service code, and call APIs provided by ACL to implement service functions.

# **Preparations**

- 1. Obtain the ATC software package. Before using the ATC, install the development kit Ascend-Toolkit
- 2. Set environment variables.
  - a. Mandatory environment variables: (**\${install\_path}** in the following environment variables uses the default installation path /usr/local/ Ascend/ascend-toolkit/latest/xxx-linux\_gccx.x.x of the development kit Ascend-Toolkit as an example.) export PATH=/usr/local/python3.7.5/bin:\${install\_path}/atc/ccec\_compiler/bin:\${install\_path}/atc/ bin:\$PATH export PYTHONPATH=\${install\_path}/atc/python/site-packages/te:\${install\_path}/atc/python/ site-packages/topi:\$PYTHONPATH export LD\_LIBRARY\_PATH=\${install\_path}/atc/lib64:\$LD\_LIBRARY\_PATH export ASCEND\_OPP\_PATH=\${install\_path}/opp
  - b. Set optional environment variables:
    - i. To use the auto tuning function **--auto\_tune\_mode="RL,GA"**, modify the environment variables **PYTHONPATH** and **LD\_LIBRARY\_PATH**: export PYTHONPATH=\${install\_path}/atc/python/site-packages/te:\${install\_path}/atc/ python/site-packages/topi**:\${install\_path}/atc/python/site-packages/auto\_tune.egg/ auto\_tune/:\${install\_path}/atc/python/site-packages/schedule\_search.egg:\$ {install\_path}/opp/op\_impl/built-in/ai\_core/tbe/**:\$PYTHONPATH export LD\_LIBRARY\_PATH=**\${install\_path}/acllib/lib64:**\${install\_path}/atc/ lib64:\$LD\_LIBRARY\_PATH
    - ii. If the slog process exists in the current environment (which can be checked by running the **ps -ef | grep slog** command), logs are generated and recorded in log files by default. To display logs on the screen or redirect logs to files, set the following environment variable:

<span id="page-37-0"></span>export SLOG\_PRINT\_TO\_STDOUT=1

If the slog process does not exist in the current environment, logs are displayed on the screen by default. To redirect logs to a file, set the environment variable **SLOG\_PRINT\_TO\_STDOUT** to **1**.

- iii. If you want to convert only TBE operators using single-operator .json files, set the following environment variable: export ASCEND\_ENGINE\_PATH=\${install\_path}/atc/lib64/plugin/opskernel/libfe.so:\$ {install\_path}/atc/lib64/plugin/opskernel/libge\_local\_engine.so

After the preceding command is executed, if you need to run the command for converting a third-party model to an offline model again, as shown in **Example (Converting a Caffe Model into an Offline Model)**, run the **unset ASCEND\_ENGINE\_PATH** command to make the **ASCEND\_ENGINE\_PATH** environment variable take effect first.

- iv. If the model is large, you can set the following environment variable to enable parallel operator building during model conversion: export TE\_PARALLEL\_COMPILER=xx

The value of **TE\_PARALLEL\_COMPILER** indicates the number of parallel operator building processes. The value must be an integer. The default value is **8**. As long as the value is greater than **0**, parallel building is enabled. In the single-chip scenario, you are advised to set this parameter to (80% \* Host CPU core count). In the multi-chip scenario, you are advised to set this parameter to (80% \* Host CPU core coun/Chip count).

#### NO TE

- Environment variables set by using **export** commands are valid only in the current window. If the environment variable of the ATC installation path has been set in the .bashrc file, you need to manually delete it before running the preceding commands.
- If it takes too long to convert a model in the Arm (AArch64) development environment, fix it by referring to **[8.1 What Do I Do If Model Conversion Takes](#page-179-0) [Too Long When the OS and Architecture Configuration of the Development](#page-179-0) [Environment Is Arm \(AArch64\)?](#page-179-0)** .

# **Example (Converting a Caffe Model into an Offline Model)**

**Step 1** Log in to the development environment as the ATC running user and upload the model file (\*.prototxt) and weight file (\*.caffemodel) to be used for model conversion to any path in the development environment, for example, **\$HOME/ test/**.

**Step 2** Generate a model file (the directory and file arguments in the command are for reference only):

atc --model=\$HOME/test/resnet50.prototxt --weight=\$HOME/test/resnet50.caffemodel --framework=0 - output=\$HOME/test/out/caffe\_resnet50 --soc\_version=Ascend310

**Step 3** If the following message is displayed, the model is successfully converted: ATC run success

After the command is executed successfully, you can view the model file (for example, **caffe\_resnet50.om**) in the path specified by the **output** parameter.

## NO TE

If the user model contains custom operators, develop and deploy custom operators by referring to the TBE Custom Operator Development Guide. During model conversion, the custom operator library will be preferentially looked up than the built-in operator library for operators in the user model.

**Step 4** (Optional) If the output node is specified by using the **--out\_nodes** parameter during model conversion, the output information of the operator will not be available after the model is converted into an .om model. You can run the following command to convert the .om model into a .json file and view the output information:

atc --mode=1 --om=\$HOME/test/caffe\_resnet50.om --json=\$HOME/test/out/resnet.json

In the example shown in **Figure 3-1**, the **--out\_nodes** parameter specifies the res4f operator as the output node. The left part shows the code without specifying the **--out\_nodes** parameter, and the right part shows the code with the **- out\_nodes** parameter specified.

**Figure 3-1** Code with and without the --out\_nodes parameter specified

**----End**

# <span id="page-39-0"></span>**Example (Converting a Single-Operator JSON File into an Offline Model)**

**Step 1** Log in to the development environment as the ATC running user and upload the single-operator JSON file to the **\$HOME/singleop** directory in the development environment. This section uses the single-operator GEMM with format ND as an example. **Step 2** Generate a model file (the directory and file arguments in the command are for reference only): atc --singleop=\$HOME/singleop/gemm.json --output=\$HOME/test/out/op\_model --soc\_version=Ascend310 **Step 3** If the following message is displayed, the model is successfully converted:

ATC run success

After the command is executed successfully, you can view the model file, for example, **0\_GEMM\_1\_2\_16\_16\_1\_2\_16\_16\_1\_2\_16\_16\_1\_2\_1\_2\_1\_2\_16\_16.om**, in the path specified by the **output** parameter.

The naming rule of the generated offline model file is

**SN\_opType\_InputDescription (dataType\_format\_shape)\_OutputDescription (dataType\_format\_shape)**. The enumeration of **dataType** in the .json file is as follows:

typedef enum { ACL\_DT\_UNDEFINED = -1, // Unknown data type (default) ACL\_FLOAT = 0, ACL\_FLOAT16 = 1, ACL\_INT8 = 2, ACL\_INT32 = 3, ACL\_UINT8 = 4, ACL\_INT16 = 6, ACL\_UINT16 = 7, ACL\_UINT32 = 8, ACL\_INT64 = 9, ACL\_UINT64 = 10, ACL\_DOUBLE = 11, ACL\_BOOL = 12, } aclDataType;

**----End**

# **Example (Converting a TensorFlow Model into an Offline Model)**

**Step 1** Log in to the development environment as the ATC running user and upload the .pb model file to any path in the development environment, for example, **\$HOME/test/**.

**Step 2** Generate a model file (the directory and file arguments in the command are for reference only):

- If the original model has a fixed shape, that is, the values of all NHWC parameters in **input\_name** are fixed, the conversion command is as follows: atc --model=\$HOME/test/resnet18\_tensorflow.pb --framework=3 --output=\$HOME/test/out/ tf\_resnet18 --soc\_version=Ascend310
- If the original model has a dynamic shape, for example, **input\_name1:? ,h,w,c**. In this scenario, **--input\_shape** is required, and set **?** to a fixed value as required. The conversion command is as follows: atc --model=\$HOME/test/module\_tensorflow.pb --input\_shape="input\_name1:n,h,w,c" - framework=3 --output=\$HOME/test/out/module\_tf --soc\_version=Ascend310

**Step 3** If the following message is displayed, the model is successfully converted: ATC run success

After the command is executed successfully, you can view the model file (for example, **tf\_resnet18.om**) in the path specified by the **output** parameter.

**----End**

# **Example (Converting a MindSpore Model to an Offline Model)**

**Step 1** Log in to the development environment as the ATC running user and upload the .pb model file to any path in the development environment, for example, **\$HOME/test/**.

**Step 2** Generate a model file (the directory and file arguments in the command are for reference only):

atc --model=\$HOME/test/ReLU.pb --framework=1 --output=\$HOME/test/out/ReLU\_mindspore - soc\_version=Ascend310

**Step 3** If the following message is displayed, the model is successfully converted: ATC run success

After the command is executed successfully, you can view the model file (for example, **ReLU\_mindspore.om**) in the path specified by the **output** parameter.

**----End**

# **4 AIPP Configuration**

<span id="page-41-0"></span>4.1 Overview [4.2 Configuration File Template](#page-42-0) [4.3 CSC Configuration](#page-46-0) [4.4 Cropping and Padding Configuration](#page-63-0) [4.5 AIPP Configuration for a Multi-Input Model](#page-64-0) [4.6 AIPP Verification of the Model Input Size](#page-65-0) [4.7 Parameter Structure for Dynamic AIPP](#page-66-0)

# **4.1 Overview**

The AIPP module is introduced for image preprocessing including CSC (by converting the image format), data normalization (by subtracting the mean value or multiplying a factor), image cropping (by specifying the crop start and cropping the image to the size required by the neural network), and much more.

Static AIPP and dynamic AIPP modes are provided. However, the two modes cannot be both configured at a time.

- Static AIPP: During model conversion, set the AIPP mode to static and set the AIPP parameters. After the model is generated, the AIPP parameter values are saved in the offline model. The same AIPP parameter configurations are used in each model inference phase. If the static AIPP mode is used, batches share the same AIPP parameter configurations.
- Dynamic AIPP: During model conversion, set the AIPP mode to dynamic. The AIPP parameters must be set in the code of the inference engine before each model inference. In this way, different sets of AIPP parameters can be used for different model inference phases. In dynamic AIPP mode, the preprocessing settings vary according to the service requirements in the scenario where, for example, cameras use different normalization parameters or both YUV420 and RGB input formats need to be supported. For details about the APIs for setting dynamic AIPP parameters, see **See Also > ACL API Reference > Model**

<span id="page-42-0"></span>**Loading and Execution > aclmdlSetInputAIPP** in the **[Application Software](https://support.huaweicloud.com/intl/en-us/adevg-A200dk_3000/atlasdevelopment_01_0001.html) [Development Guide](https://support.huaweicloud.com/intl/en-us/adevg-A200dk_3000/atlasdevelopment_01_0001.html)**.

In dynamic AIPP mode, batches use different parameter configurations (such as crop) defined by the dynamic parameter structure. For details about the dynamic parameter structure, see **[4.7 Parameter Structure for Dynamic](#page-66-0) [AIPP](#page-66-0)**.

AIPP supports the following image input formats: **YUV420SP\_U8**, **RGB888\_U8**, **XRGB8888\_U8**, and **YUV400\_U8**.

- For RGB888\_U8, the AIPP output format varies according to the value of **rbuv\_swap\_switch**.
  - If **rbuv\_swap\_switch** in **4.2 Configuration File Template** is set to **false**, the AIPP output format is RGB888\_U8.
  - If **rbuv\_swap\_switch** in **4.2 Configuration File Template** is set to **true**, the AIPP output format is BGR888\_U8.

## NO TICE

- In dynamic AIPP mode, parameters are computed for each inference operation, which is time consuming. Therefore, dynamic AIPP delivers poorer performance than static AIPP.
- When AIPP is enabled, the model input is in **RGB888\_U8** or **BGR888\_U8** format. The two formats correspond to different CSC matrices.
- Pay attention to the following requirements on the input images:
  - If AIPP is enabled for model conversion, the converted model accepts NHWC input only. In this scenario, the data format specified by the **- input\_format** argument in the **atc** command does not take effect.
  - If AIPP is disabled for model conversion, the converted model accepts NCHW input only for inference. Therefore, you need to manually convert the NHWC data to NCHW data.
- AIPP input format mapping:
  - **YUV420SP\_U8** corresponds to the YUV420SP format.
  - **RGB888\_U8** represents the RGB Package format.

# **4.2 Configuration File Template**

Due to the restrictions of hardware processing logic, parameters in the configuration file must be processed in sequence: cropping > CSC > data normalization (by subtracting the mean value and multiplying a factor) > padding. The configuration file is described as follows:

## NO TICE

- 1. AIPP provides the following features: CSC, cropping, mean subtraction, multiplication coefficient, data exchange between channels, and single-line mode. The input images must in the RAW or uint8 data types.
- 2. When this configuration file is used, uncomment the required parameters and set them to appropriate values.
- 3. **The parameter values in the template are default values. If some parameters in your configuration file are not configured, the default values shown in the template will be used during model conversion.**
- 4. **The input\_format parameter is required by static AIPP, while others are optional. If these parameters are not configured, the default values shown in the template will be used during model conversion.**

#Enclose each set of AIPP parameters within **aipp\_op { }**, which is the configuration format of an AIPP operator. Dynamic AIPP allows only one set of AIPP parameters. aipp\_op {

#========================= Global settings (start) ======================================================================================

=====================================================================

# **aipp\_mode** specifies the AIPP mode. This parameter is required.

# Type: enum

# Value range: **dynamic** or **static** (**dynamic** indicates dynamic AIPP, and **static** indicates static AIPP.)

# aipp\_mode:

# **related\_input\_rank** (optional) indicates the AIPP processing start of the inputs. The default value **0** indicates that the processing starts from input 0. If the model has two inputs, to start processing from the

second, set **related\_input\_rank** to **1**.

# Type: int # Value range: >= 0 # related\_input\_rank: 0

#========================= Global settings (end)

======================================================================================

=======================================================================

#=========================Dynamic AIPP settings (start)

======================================================================================

=============================================

# The maximum size of the input image must be configured in dynamic AIPP. (In the dynamic batch

scenario, **N** is set to the maximum in batch size.)

# Type: int

# max\_src\_image\_size: 0

# If the input image format is **YUV400\_U8**, max\_src\_image\_size >= N \* src\_image\_size\_w \* src\_image\_size\_h

\* 1.

# If the input image format is **YUV420SP\_U8**, max\_src\_image\_size >= N \* src\_image\_size\_w \*

src\_image\_size\_h \* 1.5.

# If the input image format is **XRGB8888\_U8**, max\_src\_image\_size >= N \* src\_image\_size\_w \*

src\_image\_size\_h \* 4.

# If the input image format is **RGB888\_U8**, max\_src\_image\_size >= N \* src\_image\_size\_w \* src\_image\_size\_h

\* 3.

# Rotation enable. This parameter is reserved. Rotation is not supported currently.

# Type: bool

# Value: **true** (enabled) or **false** (disabled)

# support\_rotation: false

#========================= Dynamic AIPP settings (end)

======================================================================================

================================================= #========================= Static AIPP settings (start)

======================================================================================

# Input image format # Type: enum # Value range: YUV420SP\_U8, RGB888\_U8, XRGB8888\_U8, YUV400\_U8 # input\_format: # Note: After model conversion is complete, the preceding arguments are displayed as the following enumerated values in the .om model file: 1, 3, 2, 4, receptively. # Width and height of the source image # Type: int32 # Value range: [0, 4096]. For **YUV420SP\_U8** images, the argument must be an even number. # src\_image\_size\_w: 0 # src\_image\_size\_h: 0 Note: Set **src\_image\_size\_w** and **src\_image\_size\_h** based on the actual image width and height. The two can be set to **0** or not configured only when the cropping and padding functions are disabled. In this case, the values of **w** and **h** defined in the network model input are used, with a value range [1,4096]. # Padding value for the C dimension. This field is reserved because the function is not supported currently. # Type: float16 # Value range: [–65504, +65504] # cpadding\_value: 0.0 #========= Crop settings (For the configuration example, see section **AIPP Configuration > Crop/ Padding Configuration**.) ========= # Crop enable # Type: bool # Value: **true** (enabled) or **false** (disabled) # crop: false # Start coordinates of the cropping area. **W** and **H** defined in the network input are used as the size of the cropped image. # Type: int32 # Value range: [0, 4096). For **YUV420SP\_U8** images, the argument must be an even number. # Note: load\_start\_pos\_w < src\_image\_size\_w, load\_start\_pos\_h < src\_image\_size\_h # load\_start\_pos\_w: 0 # load\_start\_pos\_h: 0 # Size of the cropped image # Type: int32 # Value range: [0, 4096], load\_start\_pos\_w + crop\_size\_w <= src\_image\_size\_w, and load\_start\_pos\_h + crop\_size\_h <= src\_image\_size\_h # crop\_size\_w: 0 # crop\_size\_h: 0 Note: If the image cropping function is enabled and padding is not configured, **crop\_size\_w** and **crop\_size\_h** can be set to **0** or not configured. In this case, the cropping width and height (**crop\_size[W|H]**) are obtained from the width and height in the **--input\_shape** of the model file, with a value range [1,4096]. # The following conditions must be met: # If **input\_format = YUV420SP\_U8**, the values of **crop\_size\_w**, **crop\_size\_h**, **load\_start\_pos\_w**, and **load\_start\_pos\_h** must be even numbers. # If **input\_format** is set to other values, there is no restriction on **crop\_size\_w**, **crop\_size\_h**, **load\_start\_pos\_w**, and **load\_start\_pos\_h**. # If cropping is enabled, the following condition must be met: src\_image\_size[W|H] >= crop\_size[W|H] + load\_start\_pos[W|H] #========================= Resize settings ======================== # Resize enable. This parameter is reserved. This function is not supported currently. # Type: bool # Value: **true** (enabled) or **false** (disabled) resize: false # Width and height of the resized image. This parameter is reserved. This function is not supported currently. # Type: int32 # Value range: **resize\_output\_h** ∈ [16,4096] or 0; **resize\_output\_w** ∈ [16, 1920] or 0; **resize\_output\_w** and **resize\_input\_w** ∈ [1/16,16], **resize\_output\_h** and **resize\_input\_h** ∈ [1/16,16] resize\_output\_w: 0 resize\_output\_h: 0 # Note: If the resizing function is enabled and padding is not configured, **resize\_output\_w** and

**resize\_output\_h** can be set to **0** or not configured. In this case, the width and height of the resized image are obtained from the width and height in the **--input\_shape** of the model file, with a value range [16, 4096].

#========= Padding settings (For the configuration example, see section **AIPP Configuration > Crop/ Padding Configuration**.) =========

# Padding enable # Type: bool # Value: **true** (enabled) or **false** (disabled) # padding: false

# Padding values of H and W, configured in static AIPP (**left\_padding\_size**, **right\_padding\_size**, **top\_padding\_size**, and **bottom\_padding\_size** ∈ [0, 32])

# Type: int32 # left\_padding\_size: 0 # right\_padding\_size: 0 # top\_padding\_size: 0 # bottom\_padding\_size: 0

# The output H and W after AIPP padding must be the same as those required by the model. W <= 1080.

#================================ Rotation settings ================================== # Rotation angle. This parameter is reserved. Rotation is not supported currently.

# Type: uint8

# Value range: {0, 1, 2, 3}, where, **0** indicates rotation disabled, **1** indicates 90° clockwise, **2** indicates 180°

clockwise, and **3** indicates 270° clockwise.

# rotation\_angle :0

#========= CSC settings (For the configuration example, see section **AIPP Configuration > CSC Configuration**.) =============

# CSC enable # Type: bool

# Value: **true** (enabled) or **false** (disabled)

# csc\_switch: false

# R/B or U/V channel swap switch

# Type: bool

# Value: **true** (enabled) or **false** (disabled)

# rbuv\_swap\_switch: false

# RGBA->ARGB and YUVA->AYUV swap switch

# Type: bool

# Value: **true** (enabled) or **false** (disabled)

# ax\_swap\_switch: **false**

# Whether to enable the single-line processing mode (only the first line after image cropping). This parameter is reserved. This function is not supported currently.

# Type: bool

# Value: **true** (enabled) or **false** (disabled)

# single\_line\_mode: false

# If the CSC is disabled (**false**), this function does not take effect. # If the input image has 4 channels, channel A or X is ignored.

# YUV2BGR conversion:

# | B | | matrix\_r0c0 matrix\_r0c1 matrix\_r0c2 | | Y - input\_bias\_0 | # | G | = | matrix\_r1c0 matrix\_r1c1 matrix\_r1c2 | | U - input\_bias\_1 | >> 8 # | R | | matrix\_r2c0 matrix\_r2c1 matrix\_r2c2 | | V - input\_bias\_2 |

# BGR2YUV conversion:

# | Y | | matrix\_r0c0 matrix\_r0c1 matrix\_r0c2 | | B | | output\_bias\_0 | # | U | = | matrix\_r1c0 matrix\_r1c1 matrix\_r1c2 | | G | >> 8 + | output\_bias\_1 | # | V | | matrix\_r2c0 matrix\_r2c1 matrix\_r2c2 | | R | | output\_bias\_2 |

# Elements in the 3 x 3 CSC matrix

# Type: int16

# Value range: [–32677, +32676]

# matrix\_r0c0: 298 # matrix\_r0c1: 516 # matrix\_r0c2: 0

<span id="page-46-0"></span># matrix\_r1c0: 298 # matrix\_r1c1: -100 # matrix\_r1c2: -208 # matrix\_r2c0: 298 # matrix\_r2c1: 0 # matrix\_r2c2: 409 # Output bias for RGB2YUV conversion # Type: uint8 # Value range: [0, 255] # output\_bias\_0: 16 # output\_bias\_1: 128 # output\_bias\_2: 128 # Input bias for YUV2RGB conversion # Type: uint8 # Value range: [0, 255] # input\_bias\_0: 16 # input\_bias\_1: 128 # input\_bias\_2: 128 #============================== Mean subtraction and multiplication factor settings ================================= # The computation rules are as follows: # For uint8->uint8, this function is bypassed. # For uint8->fp16: pixel\_out\_chx(i) = [pixel\_in\_chx(i) – mean\_chn\_i – min\_chn\_i] x var\_reci\_chn #Mean value of each channel # Type: uint8 # Value range: [0, 255] # mean\_chn\_0: 0 # mean\_chn\_1: 0 # mean\_chn\_2: 0 # mean\_chn\_3: 0 #Minimum value of each channel # Type: float16 # Value range: [0, 255] # min\_chn\_0: 0.0 # min\_chn\_1: 0.0 # min\_chn\_2: 0.0 # min\_chn\_3: 0.0 # Variance value of each channel # Type: float16 # Value range: [–65504, +65504] # var\_reci\_chn\_0: 1.0 # var\_reci\_chn\_1: 1.0 # var\_reci\_chn\_2: 1.0 # var\_reci\_chn\_3: 1.0 } #========================= Static AIPP settings (end) ====================================================================================== ===============================================

# **4.3 CSC Configuration**

If the input image format is inconsistent with that required by the model, CSC needs to be performed prior to AIPP processing. The value of **matrix\_r\*c\*** is fixed and does not need to be adjusted.

The following typical CSC configuration is provided for images or videos (in various color encoding modes such as YUV420SP\_U8 and RGB888\_U8) input to the model:

- For JPEG image files (such as jpg, jpeg, JPG, and JPEG), you can select from the CSC configurations in the **JPEG** columns in the following tables.
- For the decoded video data, the CSC parameters can be configured according to different color video digital standards (such as BT.601 and BT.709).
  - BT.601 is the standard for standard-definition television (SDTV).
  - BT.709 is the standard for high-definition television (HDTV). The two standards are classified into narrow range (Video Range) and wide range (Full Range) according to their representation range.

The value ranges of narrow and wide are listed in and

, respectively. For details, see **[8.2 How Do I Determine the](#page-179-0) [Video Stream Format Standard When I Perform CSC on a Model Using](#page-179-0) [AIPP?](#page-179-0)**

No change in format occurs in CSC configuration when data is processed by DVPP. If the data prior to DVPP processing is in the BT.709 format, the data input format in AIPP remains the same after DVPP processing.

# **YUV420SP\_U8 to YUV444**

aipp\_op { aipp\_mode: static input\_format : YUV420SP\_U8 csc\_switch : false rbuv\_swap\_switch : false }

# **YUV420SP\_U8 to YVU444**

aipp\_op { aipp\_mode: static input\_format : YUV420SP\_U8 csc\_switch : false rbuv\_swap\_switch : true }

# **YUV420SP\_U8 to RGB**

**Table 4-1** YUV420SP\_U8 to RGB

# **YUV420SP\_U8 to BGR**

**Table 4-2** YUV420SP\_U8 to BGR

# **YUV420SP\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : YUV420SP\_U8 csc\_switch : true rbuv\_swap\_switch : false matrix\_r0c0 : 256 matrix\_r0c1 : 0 matrix\_r0c2 : 0 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 input\_bias\_0 : 0 input\_bias\_1 : 0 input\_bias\_2 : 0 }

# **YVU420SP\_U8 to RGB**

**Table 4-3** YVU420SP\_U8 to RGB

# **YVU420SP\_U8 to BGR**

**Table 4-4** YVU420SP\_U8 to BGR

# **RGB888\_U8 to RGB**

 input\_format : RGB888\_U8 csc\_switch : false rbuv\_swap\_switch : false }

# **RGB888\_U8 to BGR**

aipp\_op { aipp\_mode : static input\_format : RGB888\_U8 csc\_switch : false rbuv\_swap\_switch : true }

# **RGB888\_U8 to YUV444**

**Table 4-5** RGB888\_U8 to YUV444

# **RGB888\_U8 to YVU444**

**Table 4-6** RGB888\_U8 to YVU444

# **RGB888\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : RGB888\_U8 csc\_switch : true rbuv\_swap\_switch : false matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **BGR888\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : RGB888\_U8 csc\_switch : true rbuv\_swap\_switch : true matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **BGR888\_U8 to RGB**

aipp\_op { aipp\_mode: static input\_format : RGB888\_U8 csc\_switch : false rbuv\_swap\_switch : true }

# **BGR888\_U8 to BGR**

aipp\_op { aipp\_mode: static input\_format : RGB888\_U8 csc\_switch : false rbuv\_swap\_switch : false }

# **XRGB8888\_U8 to RGB**

aipp\_op { aipp\_mode: static input\_format : XRGB8888\_U8 csc\_switch : false rbuv\_swap\_switch : false ax\_swap\_switch : true }

# **XRGB8888\_U8 to BGR**

aipp\_op { aipp\_mode : static input\_format : XRGB8888\_U8 csc\_switch : false rbuv\_swap\_switch : true ax\_swap\_switch : true }

# **XRGB8888\_U8 to YUV444**

**Table 4-7** XRGB8888\_U8 to YUV444

# **XRGB8888\_U8 to YVU444**

**Table 4-8** XRGB8888\_U8 to YVU444

# **XRGB8888\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : XRGB8888\_U8 csc\_switch : true rbuv\_swap\_switch : false ax\_swap\_switch : true matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **XBGR8888\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : XRGB8888\_U8 csc\_switch : true rbuv\_swap\_switch : true ax\_swap\_switch : true matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **RGBX8888\_U8 to Gray**

<span id="page-63-0"></span> input\_format : XRGB8888\_U8 csc\_switch : true rbuv\_swap\_switch : false ax\_swap\_switch : false matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **BGRX8888\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : XRGB8888\_U8 csc\_switch : true rbuv\_swap\_switch : true ax\_swap\_switch : false matrix\_r0c0 : 76 matrix\_r0c1 : 150 matrix\_r0c2 : 30 matrix\_r1c0 : 0 matrix\_r1c1 : 0 matrix\_r1c2 : 0 matrix\_r2c0 : 0 matrix\_r2c1 : 0 matrix\_r2c2 : 0 output\_bias\_0 : 0 output\_bias\_1 : 0 output\_bias\_2 : 0 }

# **YUV400\_U8 to Gray**

aipp\_op { aipp\_mode: static input\_format : YUV400\_U8 csc\_switch : false }

# **4.4 Cropping and Padding Configuration**

Currently, AIPP supports image resizing through cropping and padding.

**Figure 4-1** Image resizing

<span id="page-64-0"></span>For YUV420SP\_U8 images, the values of **load\_start\_pos\_w**, **load\_start\_pos\_h**, **crop\_size\_w**, and **crop\_size\_h** must be even numbers. The size of the cropped image must be the same as that defined in the network input.

A configuration example is as follows:

aipp\_op { aipp\_mode: static input\_format : YUV420SP\_U8 src\_image\_size\_w :320 src\_image\_size\_h :240 crop :true load\_start\_pos\_w :10 load\_start\_pos\_h :20 crop\_size\_w :50 crop\_size\_h :60 padding : true left\_padding\_size :10 right\_padding\_size :20 top\_padding\_size :10 bottom\_padding\_size :20 }

# **4.5 AIPP Configuration for a Multi-Input Model**

The AIPP configuration file can define multiple sets of AIPP parameters. Separate AIPP processing is performed for different model inputs. Enclose each set of AIPP parameters within **aipp\_op { }**. The following provides two sample sets of AIPP parameters.

aipp\_op { related\_input\_rank : 0 src\_image\_size\_w : 608 src\_image\_size\_h : 608 crop : false input\_format : YUV420SP\_U8 aipp\_mode: static csc\_switch : true rbuv\_swap\_switch : false matrix\_r0c0 : 298 matrix\_r0c1 : 0 matrix\_r0c2 : 409 matrix\_r1c0 : 298 matrix\_r1c1 : -100 matrix\_r1c2 : -208 matrix\_r2c0 : 298 matrix\_r2c1 : 516 matrix\_r2c2 : 0 input\_bias\_0 : 16 input\_bias\_1 : 128 input\_bias\_2 : 128 mean\_chn\_0 : 104 mean\_chn\_1 : 117 mean\_chn\_2 : 123 } aipp\_op { related\_input\_rank : 1 src\_image\_size\_w : 608 src\_image\_size\_h : 608 crop : false input\_format : YUV420SP\_U8 aipp\_mode: static

<span id="page-65-0"></span> csc\_switch : true rbuv\_swap\_switch : false matrix\_r0c0 : 298 matrix\_r0c1 : 0 matrix\_r0c2 : 409 matrix\_r1c0 : 298 matrix\_r1c1 : -100 matrix\_r1c2 : -208 matrix\_r2c0 : 298 matrix\_r2c1 : 516 matrix\_r2c2 : 0 input\_bias\_0 : 16 input\_bias\_1 : 128 input\_bias\_2 : 128 mean\_chn\_0 : 104 mean\_chn\_1 : 117 mean\_chn\_2 : 123

}

In this example, two sets of AIPP parameters are defined. AIPP processing is performed on the first and second inputs of the model. Dynamic AIPP allows only one set of AIPP parameters.

# **4.6 AIPP Verification of the Model Input Size**

If AIPP is configured, whether static AIPP or dynamic AIPP, the input size (**input\_size**) of the eventually generated offline model is subject to operations such as cropping and padding. Assume that the batch size is **N** (the maximum batch size in the dynamic batch scenario), the input image width is **src\_image\_size\_w**, and the input image height is **src\_image\_size\_h**, the input image size can be calculated in the formulas in **Table 4-9**.

**Table 4-9** input\_size verification formulas

| input_format | input_size                                    |
|--------------|-----------------------------------------------|
| YUV400_U8    | N * src_image_size_w * src_image_size_h * 1   |
| YUV420SP_U8  | N * src_image_size_w * src_image_size_h * 1.5 |
| XRGB8888_U8  | N * src_image_size_w * src_image_size_h * 4   |
| RGB888_U8    | N * src_image_size_w * src_image_size_h * 3   |

For dynamic AIPP, the ATC adds a new model input for passing the AIPP parameters during model conversion. **input\_size** of the new input is calculated as follows:

sizeof(kAippDynamicPara) – sizeof(kAippDynamicBatchPara) + batch\_count \* sizeof(kAippDynamicBatchPara)

#### ● NO TE

For details about the **kAippDynamicPara** and **kAippDynamicBatchPara** parameters, see **[4.7 Parameter Structure for Dynamic AIPP](#page-66-0)**.

# <span id="page-66-0"></span>**4.7 Parameter Structure for Dynamic AIPP**

After the dynamic AIPP file is configured based on the **[4.2 Configuration File](#page-42-0) [Template](#page-42-0)**, the following struct will be constructed automatically based on the dynamic AIPP configuration file. No manual operation is required.

typedef struct tagAippDynamicBatchPara { int8\_t cropSwitch; //crop switch int8\_t scfSwitch; //resize switch int8\_t paddingSwitch; // 0: unable padding, // 1: padding config value,sfr\_filling\_hblank\_ch0 ~ sfr\_filling\_hblank\_ch2 // 2: padding source picture data, single row/collumn copy // 3: padding source picture data, block copy // 4: padding source picture data, mirror copy int8\_t rotateSwitch; //rotate switch, 0: rotation disabled, 1: 90° clockwise, 2: 180° clockwise, 3: 270° clockwise int8\_t reserve[4]; int32\_t cropStartPosW; //the start horizontal position of cropping int32\_t cropStartPosH; //the start vertical position of cropping int32\_t cropSizeW; //crop width int32\_t cropSizeH; //crop height int32\_t scfInputSizeW; //input width of scf int32\_t scfInputSizeH; //input height of scf int32\_t scfOutputSizeW; //output width of scf int32\_t scfOutputSizeH; //output height of scf int32\_t paddingSizeTop; //top padding size int32\_t paddingSizeBottom; //bottom padding size int32\_t paddingSizeLeft; //left padding size int32\_t paddingSizeRight; //right padding size int16\_t dtcPixelMeanChn0; //mean value of channel 0 int16\_t dtcPixelMeanChn1; //mean value of channel 1 int16\_t dtcPixelMeanChn2; //mean value of channel 2 int16\_t dtcPixelMeanChn3; //mean value of channel 3 #ifndef DAVINCI\_TINY uint16\_t dtcPixelMinChn0; //min value of channel 0 uint16\_t dtcPixelMinChn1; //min value of channel 1 uint16\_t dtcPixelMinChn2; //min value of channel 2 uint16\_t dtcPixelMinChn3; //min value of channel 3 uint16\_t dtcPixelVarReciChn0; //sfr\_dtc\_pixel\_variance\_reci\_ch0 uint16\_t dtcPixelVarReciChn1; //sfr\_dtc\_pixel\_variance\_reci\_ch1 uint16\_t dtcPixelVarReciChn2; //sfr\_dtc\_pixel\_variance\_reci\_ch2 uint16\_t dtcPixelVarReciChn3; //sfr\_dtc\_pixel\_variance\_reci\_ch3 int8\_t reserve1[16]; //32B assign, for ub copy #endif }kAippDynamicBatchPara; typedef struct tagAippDynamicPara { uint8\_t inputFormat; //Input format: YUV420SP\_U8, XRGB8888\_U8, or RGB888\_U8 //uint8\_t outDataType; //output data type: CC\_DATA\_HALF,CC\_DATA\_INT8, CC\_DATA\_UINT8 int8\_t cscSwitch; //csc switch int8\_t rbuvSwapSwitch; //rb/ub swap switch int8\_t axSwapSwitch; //RGBA->ARGB, YUVA->AYUV swap switch int8\_t batchNum; //batch parameter number int8\_t reserve1[3]; int32\_t srcImageSizeW; //source image width int32\_t srcImageSizeH; //source image height int16\_t cscMatrixR0C0; //csc\_matrix\_r0\_c0 int16\_t cscMatrixR0C1; //csc\_matrix\_r0\_c1 int16\_t cscMatrixR0C2; //csc\_matrix\_r0\_c2 int16\_t cscMatrixR1C0; //csc\_matrix\_r1\_c0 int16\_t cscMatrixR1C1; //csc\_matrix\_r1\_c1 int16\_t cscMatrixR1C2; //csc\_matrix\_r1\_c2 int16\_t cscMatrixR2C0; //csc\_matrix\_r2\_c0 int16\_t cscMatrixR2C1; //csc\_matrix\_r2\_c1 int16\_t cscMatrixR2C2; //csc\_matrix\_r2\_c2

 int16\_t reserve2[3]; uint8\_t cscOutputBiasR0; //output bias for RGB to YUV, element of row 0, unsigned number uint8\_t cscOutputBiasR1; //output bias for RGB to YUV, element of row 1, unsigned number uint8\_t cscOutputBiasR2; //output bias for RGB to YUV, element of row 2, unsigned number uint8\_t cscInputBiasR0; //input bias for YUV to RGB, element of row 0, unsigned number uint8\_t cscInputBiasR1; //input bias for YUV to RGB, element of row 1, unsigned number uint8\_t cscInputBiasR2; //input bias for YUV to RGB, element of row 2, unsigned number uint8\_t reserve3[2]; int8\_t reserve4[16]; //32B assign, for ub copy kAippDynamicBatchPara aippBatchPara; //allow transfer several batch para. } kAippDynamicPara;

# <span id="page-68-0"></span>**5 Single-Operator JSON File Configuration**

The configuration file can define multiple sets of single-operator JSON configurations, each set including the operator type, operator input and output information, and optional attributes. The following provides some configuration code examples. You can modify the examples as required.

1. If format = ND:

[ {

 "op": "GEMM", "input\_desc": [

{

 "format": "ND", "shape": [16, 16], "type": "float16"

 }, {

 "format": "ND", "shape": [16, 16], "type": "float16"

 }, {

 "format": "ND", "shape": [16, 16], "type": "float16"

 }, {

 "format": "ND", "shape": [], "type": "float16"

 }, {

 "format": "ND", "shape": [], "type": "float16"

 } ],

"output\_desc": [

{

 "format": "ND", "shape": [16, 16], "type": "float16"

 } ], "attr": [ {

 "name": "transpose\_a", "type": "bool", "value": false

 }, { "name": "transpose\_b", "type": "bool", "value": false } ] } ] 2. If format = NCHW: [ { "op": "**Conv2D**", "input\_desc": [ { "format": "NCHW", "shape": [1, 3, 16, 16], "type": "float16" }, { "format": "NCHW", "shape": [3, 3, 3, 3], "type": "float16" } ], "output\_desc": [ { "format": "NCHW", "shape": [1, 3, 16, 16], "type": "float16" } ], "attr": [ { "name": "strides", "type": "list\_int", "value": [1, 1, 1, 1] }, { "name": "pads", "type": "list\_int", "value": [1, 1, 1, 1] }, { "name": "dilations", "type": "list\_int", "value": [1, 1, 1, 1] } ] } ]

# 3. Multiple sets of JSON configurations:

If multiple groups of operators are configured in the JSON file, multiple .om files are generated after model conversion and are displayed in sequence: **0\_xx**, **1\_xx**, and so on. The following configuration file is only an example. Modify it as required.

[ { **"op": "MatMul",** "input\_desc": [ { "format": "ND", "shape": [ 16, 16 ], "type": "float16"  }, ... ], "output\_desc": [ { "format": "ND", "shape": [ 16, 16 ], "type": "float16" } ], "attr": [ { "name": "alpha", "type": "float", "value": 1.0 }, ... ] }, { **"op": "MatMul"**, "input\_desc": [ { "format": "ND", "shape": [ 256, 256 ], "type": "float16" }, ... ], "output\_desc": [ { "format": "ND", "shape": [ 256, 256 ], "type": "float16" } ], "attr": [ { "name": "alpha", "type": "float", "value": 1.0 }, ... ] }

]

The .json file consists of **OpDesc** arrays. The parameters are described as follows:

**Table 5-1** OpDesc parameters

|            | Type       | Description                | Required or |
|------------|------------|----------------------------|-------------|
| op         | string     | Operator type              | Yes         |
| input_desc | TensorDesc |                            |             |
|            |            | Operator input description | Yes         |

|      | Type       | Description                 | Required or |
|------|------------|-----------------------------|-------------|
|      |            | Operator output description | Yes         |
| attr | Attr array | Operator attributes         | Not         |

**Table 5-2** TensorDesc array parameters

| Type          | Description | Required or                    |
|---------------|-------------|--------------------------------|
| format string |             | Tensor format, which is set to |
| ●             | NCHW        |                                |
| ●             | NHWC        |                                |
| ●             | ND          | : any format                   |
| ●             | NC1HWC0     | : the 5D format                |
|               |             | defined by Huawei C0 is        |
|               |             | unit, for example, 16 C1       |
|               | C0          | , that is, C1 = C/C0. When     |
|               | to C0       |                                |
| ●             | FRACTAL_Z   | : format of the                |
| ●             |             | FRACTAL_NZ : fractal format    |
|               | matrix is   | NW1H1H0W0                      |

| Type            | Description Required or         |
|-----------------|---------------------------------|
| type string     | Tensor data type. The           |
| ●               | bool                            |
| ●               | int8                            |
| ●               | uint8                           |
| ●               | int16                           |
| ●               | uint16                          |
| ●               | int32                           |
| ●               | uint32                          |
| ●               | float16                         |
| ●               | float                           |
| ●               | double                          |
| shape int array | Tensor shape, for example, [1,  |
| name string     | Tensor name                     |
|                 | s, ...) , set this parameter to |

**Table 5-3** Attr array parameters

| Type Description                     | Required or                       |
|--------------------------------------|-----------------------------------|
| type string Attribute data type. The |                                   |
| ●                                    | bool                              |
| ●                                    | int                               |
| ●                                    | float                             |
| ●                                    | string                            |
| ●                                    | list_bool                         |
| ●                                    | list_int                          |
| ●                                    | list_float                        |
| ●                                    | list_string                       |
| ●                                    | list_list_int                     |
| value Determined by                  |                                   |
| value varies according to            | type                              |
| ●                                    | bool: true/false                  |
| ●                                    | int: 10                           |
| ●                                    | float: 1.0                        |
| ●                                    | string: "NCHW"                    |
| ●                                    | list_bool: [false, true]          |
| ●                                    | list_int: [1, 224, 224, 3]        |
| ●                                    | list_float: [1.0, 0.0]            |
| ●                                    | list_string: ["str1", "str2"]     |
| ●                                    | list_list_int: [[1, 3, 5, 7], [2, |

# <span id="page-75-0"></span>**6 Fusion Switch Configuration File**

When the model miniaturization tool is used to quantize the original model, it will insert quantization and dequantization operators. When the ATC is used to convert the model, it will fuse the inserted quantization and dequantization operators. In this case, to measure the computation accuracy of the quantized model by comparing the dump files before and after the model quantization, the fusion function must be disabled by using this configuration file.

The configuration file template is as follows. Only the following **pass** rules can be configured. Disable the following rules altogether when using the configuration file.

RequantFusionPass:off // In the quantization scenario, optimize the deployment when the dequantization and quantization patterns are met to improve inference performance. TbeConvDequantVaddReluQuantFusionPass:off // In the quantization scenario, mark consecutive convdequant-vadd-relu-quant nodes with UB fusion to improve inference performance. TbeConvDequantQuantFusionPass:off // In the quantization scenario, mark consecutive convdequant-quant nodes with UB fusion to improve inference performance. TbePool2dQuantFusionPass:off //In the quantization scenario, mark consecutive Pool2d-quant nodes with UB fusion to improve inference performance.

# <span id="page-76-0"></span>**7 Supported Operators**

7.1 Caffe Network Model

[7.2 TensorFlow Network Model](#page-166-0)

# **7.1 Caffe Network Model**

# **7.1.1 Prototxt Customization**

# **7.1.1.1 Overview**

Operators supported by the Ascend AI Processor are classified as follows:

- Standard operators: standard Caffe operators, such as Convolution.
- Extended operators: open-source but non-standard Caffe operators, including:
  - Operators extended based on the Caffe framework, such as ROIPooling in Faster R-CNN and Normalize in SSD.
  - Operators extended based on other deep learning frameworks, such as PassThrough in YOLOv2.

Networks such as Faster R-CNN and SSD include some operator structures not defined in the Caffe framework, such as ROIPooling, Normalize, PSROI Pooling, and Upsample. To support these networks, the Caffe operators are extended for the Ascend AI Processor to reduce developers' workload of operator customization and post-processing. If these extended operators are used in Caffe networks, you need to modify or add the definition of the extension layer in the .prototxt file prior to model conversion.

This chapter provides the rundown of the extended operators supported by the Ascend AI Processor and the instructions of modifying the .prototxt file.

# <span id="page-77-0"></span>**7.1.1.2 Extended Operator List**

**Table 7-1**

| Operator Type  | Description                                      |
|----------------|--------------------------------------------------|
| Reverse        | Reverses the dimensions of a tensor.             |
| ROIPooling     | Performs pooling over feature maps of non       |
| PSROIPooling   | Performs position-sensitive pooling over feature |
| Upsample       | Performs upsampling using pooling mask           |
| Normallize     | Normalizes the input tensor along the channel    |
| Reorg          | Rearranges blocks of spatial data into depth, or |
| Proposal       | Filters bounding boxes (BBoxes) and outputs      |
|                | based on the foreground output of rpn_cls_prob   |
|                | and BBox regression output of rpn_bbox_pred in   |
| ROIAlign       | A regional feature aggregation method that       |
| ShuffleChannel | Permutes data in the channel dimension of the    |
| Yolo (Yolo/    |                                                  |
| PriorBox       | Generates prior boxes based on the input         |

<span id="page-78-0"></span>

# **7.1.1.3 Extended Operator Description**

Extended operators can be implemented in Caffe or other deep learning frameworks. Standard .prototxt definitions are available for operators extended based on the Caffe framework, such as ROIPooling, Normalize, PSROIPolling, and Upsample. For operators extended based on frameworks other than Caffe, define them and their parameters in .prototxt format as follows.

# **Reverse**

Reverses the dimensions of a tensor. For example, reverse from [1, 2, 3] to [3, 2, 1].

Define the operator as follows:

- 1. Add **ReverseParameter** to **LayerParameter**. message LayerParameter { ... optional ReverseParameter reverse\_param = 157; ... }
- 2. Define the data types and attributes of **ReverseParameter**. message ReverseParameter{ repeated int32 axis = 1; }

# **ROIPooling**

The major hurdle for going from image classification to object detection is fixed size input requirement to the network because of the existing fully connected (FC) layers. In object detection, different proposals have different shapes. Therefore, it is necessary to convert all the proposals to a fixed shape as required by FC layers.

Different images produce different feature maps after being processed by the convolution layers. The ROI Pooling layer in the Faster R-CNN network is used to produce feature maps with fixed dimensions from non-uniform inputs.

You need to extend the **caffe.proto** file and define **ROIPoolingParameter** as follows:

- <span id="page-79-0"></span>● **spatial\_scale**: multiplicative spatial scale factor to translate ROI coordinates from their input scale to the scale used when pooling
- **pooled\_h** and **pooled\_w**: height and width of the ROI output feature map
- 1. Add **ROIPoolingParameter** to **LayerParameter**. message LayerParameter { ... optional ROIPoolingParameter roi\_pooling\_param = 161; ... }
- 2. Define the data types and attributes of **ROIPoolingParameter**. message ROIPoolingParameter { required int32 pooled\_h = 1; required int32 pooled\_w = 2; optional float spatial\_scale = 3 [default=0.0625]; optional float spatial\_scale\_h = 4; optional float spatial\_scale\_w = 5; }

Example .prototxt definition of ROIPooling:

layer { name: "roi\_pooling" type: "ROIPooling" bottom: "res4f" bottom: "rois" bottom: "actual\_rois\_num" top: "roi\_pool" roi\_pooling\_param { pooled\_h: 14 pooled\_w: 14 spatial\_scale:0.0625 spatial\_scale\_h:0.0625 spatial\_scale\_w:0.0625 } }

# **PSROIPooling**

Position Sensitive ROI Pooling (PSROIPooling) works in similar way to ROIPooling. However, unlike ROIPooling, the feature map output from PSROIPooling is obtained from different feature map channels, and average pooling (instead of max-pooling) is performed on each divided bin.

PSROIPooling divides the ROI into k \* k bins and outputs a k \* k feature map. The number of output channels for pooling is the same as the number of input channels.

You need to extend the **caffe.proto** file and define **PSROIPoolingParameter** as follows:

- **spatial\_scale**: multiplicative spatial scale factor to translate ROI coordinates from their input scale to the scale used when pooling
- **output\_dim**: number of output channels
- **group\_size**: number of groups to encode position-sensitive score maps, that is, <sup>k</sup>
- 1. Add **PSROIPoolingParameter** to **LayerParameter**.

message LayerParameter {

... optional PSROIPoolingParameter psroi\_pooling\_param = 207; ...

- }
- <span id="page-80-0"></span>2. Define the data types and attributes of **PSROIPoolingParameter**. message PSROIPoolingParameter { required float spatial\_scale = 1; required int32 output\_dim = 2; // output channel number required int32 group\_size = 3; // number of groups to encode position-sensitive score maps }

Example .prototxt definition of PSROIPooling:

layer { name: "psroipooling" type: "PSROIPooling" bottom: "some\_input" bottom: "some\_input" top: "some\_output" psroi\_pooling\_param { spatial\_scale: 0.0625 output\_dim: 21 group\_size: 7 } }

# **Upsample**

The Upsample layer is the reverse of the Pooling layer. Each decoder upsamples the activations generated by the corresponding encoder.

The Upsample layer needs to extend the **caffe.proto** file and define **UpsampleParameter** as follows. The defined parameters include **scale**, which specifies the ratio of the output size to the input size, for example, **2**.

- 1. Add **UpsampleParameter** to **LayerParameter**. message LayerParameter { ... optional UpsampleParameter upsample\_param = 160; ... }
- 2. Define the data types and attributes of **UpsampleParameter**. message UpsampleParameter{ optional float scale = 1[default = 1]; optional int32 stride = 2[default = 2]; optional int32 stride\_h = 3[default = 2]; optional int32 stride\_w = 4[default=2]; }

Example .prototxt definition of Upsample:

layer { name: "layer86-upsample" type: "Upsample" bottom: "some\_input" top: "some\_output" upsample\_param { scale: 1 stride: 2 } }

# **Normallize**

The Normalize layer is a normalization layer in the SSD network, and is mainly used to normalize elements in a space or a channel to the range [0, 1. The

Normalize layer is to output a tensor of a same size for a c\*h\*w three-dimensional tensor. In the formula, Normalize is calculated based on the square root of the sum of squares in the channel direction for each element. The formula is as follows:

where, the cumulative vector of the square sum in the denominator part is the sum of the channel vectors that share the same height and width, as the orange part shown in **Figure 7-1**.

**Figure 7-1** Normalize diagram

After the preceding normalization calculation, the Normalize layer scales each feature map using separate scale factors.

The Normalize layer needs to extend the **caffe.proto** file and define **NormalizeParameter** as follows. The defined parameters are as follows:

- **across\_spatial**: a bool. If **True**, normalizes every channel to 1 x c x h x w. If **False**, normalizes every pixel to 1 x c x 1 x 1.
- **channels\_shared**: a bool. If **True**, the scale parameters are shared across channels. Defaults to **True**.
- **eps**: a small number to avoid division by zero while normalizing.

The mathematical formulation of Normalize is as follows:

Define the operator as follows:

- 1. Add **NormalizeParameter** to **LayerParameter**. message LayerParameter { ... optional NormalizeParameter norm\_param = 206; ... }

# <span id="page-82-0"></span>2. Define the data types and attributes of **NormalizeParameter**.

message NormalizeParameter { optional bool across\_spatial = 1 [default = true]; // Initial value of scale. Default is 1.0 for all optional FillerParameter scale\_filler = 2; // Whether or not scale parameters are shared across channels. optional bool channel\_shared = 3 [default = true]; // Epsilon for not dividing by zero while normalizing variance optional float eps = 4 [default = 1e-10]; }

Example .prototxt definition of Normalize:

layer { name: "normalize\_layer" type: "Normalize" bottom: ""some\_input" top: "some\_output" norm\_param { across\_spatial: false scale\_filler { type: "constant" value: 20 } channel\_shared: false } }

# **Reorg**

The Reorg operator is implemented as a PassThrough operator in Ascend AI Processor, which rearranges blocks of spatial data into depth, or vice versa.

The PassThrough layer is not implemented using the Caffe framework. Therefore, there is no standard definition for this layer. This layer expands the feature map data in the spatial dimension to the channel dimension. The consecutive elements in the channel dimension are still consecutive in the expanded feature map.

Define the operator as follows:

- 1. Add **ReorgParameter** to **LayerParameter**. message LayerParameter { ... optional ReorgParameter reorg\_param = 155; ... }
- 2. Define the data types and attributes of **ReorgParameter**. message ReorgParameter{ optional uint32 stride = 2 [default = 2]; optional bool reverse = 1 [default = false]; }

Example of Reorg .prototxt definition:

layer { bottom: "some\_input" top: "some\_output" name: "reorg" type: "Reorg" reorg\_param { stride: 2 } }

# <span id="page-83-0"></span>**Proposal**

The proposal operator modifies anchors based on foreground of **rpn\_cls\_prob** and the BBox regression of **rpn\_bbox\_pred** to obtain accurate proposals.

It consists of three operators: decoded\_bbox, topk, and nms, as shown in **Figure 7-2**.

**Figure 7-2** Proposal implementation

Define the operator as follows:

# 1. Add **ProposalParameter** to **LayerParameter**.

message LayerParameter {

... optional ProposalParameter proposal\_param = 201; ...

}

# 2. Define the **ProposalParameter** class and attribute parameters.

message ProposalParameter {

 optional float feat\_stride = 1 [default = 16]; optional float base\_size = 2 [default = 16]; optional float min\_size = 3 [default = 16];

 repeated float ratio = 4; repeated float scale = 5;

 optional int32 pre\_nms\_topn = 6 [default = 3000]; optional int32 post\_nms\_topn = 7 [default = 304]; optional float iou\_threshold = 8 [default = 0.7];

optional bool output\_actual\_rois\_num = 9 [default = false];

}

Example .prototxt definition of Proposal:

layer { name: "faster\_rcnn\_proposal" type: "Proposal" //Operator type bottom: "rpn\_cls\_prob\_reshape" bottom: "rpn\_bbox\_pred" bottom: "im\_info" top: "rois" top: "actual\_rois\_num" // Added operator output proposal\_param { feat\_stride: 16 base\_size: 16 min\_size: 16 pre\_nms\_topn: 3000 post\_nms\_topn: 304 iou\_threshold: 0.7

 output\_actual\_rois\_num: true } }

# <span id="page-84-0"></span>**ROIAlign**

ROIAlign is a regional feature aggregation method proposed by Mask-RCNN, which solves the problem of misalignment caused by two quantizations in ROIPooling operation.

The size of the feature map after pooling is **pooled\_w \* pooled\_h**. Each ROI is divided into **sampling\_ratio \* sampling\_ratio** grids of the same size. The grid points are the sampling points. As shown in **Figure 7-3**, the dashed line indicates the feature map, and the solid line indicates the ROI, which is divided into 2 x 2 cells. Assuming that the number of sampling points is 4, it means that four grids are equally divided, each of which takes its center point position. The coordinates of a sampling point are usually floating-point numbers. Therefore, you need to perform bilinear interpolation on the pixel of the sampling point (as shown by the four arrows in **Figure 7-3**) to obtain the value of the pixel. Finally, average the four pixel values as the ROIAlign result.

**Figure 7-3** ROIAlign diagram

# Define the operator as follows:

# 1. Add **ROIAlignParameter** to **LayerParameter**.

message LayerParameter {

...

optional ROIAlignParameter roi\_align\_param = 154;

... }

# 2. Define the data types and attributes of **ROIAlignParameter**.

message ROIAlignParameter { // Pad, kernel size, and stride are all given as a single value for equal // dimensions in height and width or as Y, X pairs. optional uint32 pooled\_h = 1 [default = 0]; // The pooled output height optional uint32 pooled\_w = 2 [default = 0]; // The pooled output width // Multiplicative spatial scale factor to translate ROI coords from their // input scale to the scale used when pooling optional float spatial\_scale = 3 [default = 1]; optional int32 sampling\_ratio = 4 [default = -1];

 optional int32 roi\_end\_mode = 5 [default = 0]; }

You can customize the .prototxt file based on the preceding data types and attributes.

# <span id="page-85-0"></span>**ShuffleChannel**

ShuffleChannel permutes data in the channel dimension of the input.

For example, if **channel = 4** and **group = 2**, ShuffleChannel transposes channel[1] and channel[2].

Define the operator as follows:

- 1. Add **ShuffleChannelParameter** to **LayerParameter**. message LayerParameter { ... optional ShuffleChannelParameter shuffle\_channel\_param = 159; ... }
- 2. Define the data types and attributes of **ShuffleChannelParameter**. message ShuffleChannelParameter{ optional uint32 group = 1[default = 1]; // The number of group }

Example .prototxt definition of ShuffleChannel:

layer { name: "layer\_shuffle" type: "ShuffleChannel" bottom: "some\_input" top: "some\_output" shuffle\_channel\_param { group: 3 } }

# **Yolo**

The YOLO operator is introduced to the YOLOv2 network and is applied only on the YOLOv2 and YOLOv3 networks. It performs sigmoid and softmax operations on input.

- In YOLOv2, there are four scenarios based on the **background** and **softmax** parameters:
  - a. background = false, softmax = true: sigmoid is performed on (x, y) in (x, y, h, w), sigmoid is performed on **b**, and softmax is performed on **classes**.
  - b. background = false, softmax = false: sigmoid is performed on (x, y) in (x, y, h, w), sigmoid is performed on **b**, and sigmoid is performed on **classes**.
  - c. background = true, softmax = false: sigmoid is performed on (x, y) in (x, y, h, w), **b** is ignored, and sigmoid is performed on **classes**.
  - d. background = true, softmax = true: sigmoid is performed on (x, y) in (x, y, h, w), and softmax is performed on **b** and **classes**.

- <span id="page-86-0"></span>● In YOLOv3, there is only one scenario: sigmoid is performed on (x,y) in (x,y,h,w), sigmoid is performed on b, and sigmoid is performed on classes.

The input data format is **Tensor(n, coords+backgroup+classes,l.h,l.w)**, where **n** indicates the number of anchor boxes and **corrds** indicates x, y, w, and h, :

Define the operator as follows:

- 1. Add **YoloParameter** to **LayerParameter**. message LayerParameter { ... optional YoloParameter yolo\_param = 199; ... }
- 2. Define the data types and attributes of **YoloParameter**. message YoloParameter { optional int32 boxes = 1 [default = 3]; optional int32 coords = 2 [default = 4]; optional int32 classes = 3 [default = 80]; optional string yolo\_version = 4 [default = "V3"]; optional bool softmax = 5 [default = false]; optional bool background = 6 [default = false]; optional bool softmaxtree = 7 [default = false];

}

Example .prototxt definition of Yolo:

layer { bottom: "layer82-conv" top: "yolo1\_coords" top: "yolo1\_obj" top: "yolo1\_classes" name: "yolo1" type: "Yolo" yolo\_param { boxes: 3 coords: 4 classes: 80 yolo\_version: "V3" softmax: true background: false } }

# **PriorBox**

The prior box is generated according to the arguments.

The following uses conv7\_2\_mbox\_priorbox as an example. The definition is as follows:

layer{ name:"conv7\_2\_mbox\_priorbox" type:"PriorBox" bottom:"conv7\_2" bottom:"data" top:"conv7\_2\_mbox\_priorbox" prior\_box\_param{ min\_size:162.0 max\_size:213.0 aspect\_ratio:2 aspect\_ratio:3 flip:true clip:false variance:0.1 variance:0.1

 variance:0.2 variance:0.2 img\_size:300 step:64 offset:0.5 } }

- 1. A prior box is generated when the width and height are both **min\_size**.
- 2. If **max\_size** is available, **sqrt(min\_size x max\_size)** is used to determine the width and height of generated boxes (max\_size > min\_size).
- 3. The prior box is generated based on the aspect ratios (1/2 and 1/3 according to the definition).

Therefore, num\_priors\_ = min\_sizes + aspect\_ratios \* min\_size + max\_size

Define the operator as follows:

- 1. Add **PriorBoxParameter** to **LayerParameter**. message LayerParameter { ... optional PriorBoxParameter prior\_box\_param = 203; ... }
- 2. Define the data types and attributes of **PriorBoxParameter**. message PriorBoxParameter { // Encode/decode type. enum CodeType { CORNER = 1; CENTER\_SIZE = 2; CORNER\_SIZE = 3; } // Minimum box size (in pixels). Required! repeated float min\_size = 1; // Maximum box size (in pixels). Required! repeated float max\_size = 2; // Various of aspect ratios. Duplicate ratios will be ignored. // If none is provided, we use default ratio 1. repeated float aspect\_ratio = 3; // If true, will flip each aspect ratio. // For example, if there is aspect ratio "r", // we will generate aspect ratio "1.0/r" as well. optional bool flip = 4 [default = true]; // If true, will clip the prior so that it is within [0, 1] optional bool clip = 5 [default = false]; // Variance for adjusting the prior bboxes. repeated float variance = 6; // By default, we calculate img\_height, img\_width, step\_x, step\_y based on // bottom[0] (feat) and bottom[1] (img). Unless these values are explicitely // provided. // Explicitly provide the img\_size. optional uint32 img\_size = 7; // Either img\_size or img\_h/img\_w should be specified; not both. optional uint32 img\_h = 8; optional uint32 img\_w = 9; // Explicitly provide the step size. optional float step = 10; // Either step or step\_h/step\_w should be specified; not both. optional float step\_h = 11; optional float step\_w = 12; // Offset to the top left corner of each cell. optional float offset = 13 [default = 0.5]; }

<span id="page-88-0"></span>layer { name: "layer\_priorbox" type: "PriorBox" bottom: "some\_input" bottom: "some\_input" top: "some\_output" prior\_box\_param { min\_size: 30.0 max\_size: 60.0 aspect\_ratio: 2 flip: true clip: false variance: 0.1 variance: 0.1 variance: 0.2 variance: 0.2 step: 8 offset: 0.5 } }

# **7.1.1.4 Caffe Operator Specifications**

General restrictions:

- 1. Unless otherwise specified, there is a restriction for the input and output: w <= 4096; h<= 4096

|        |     |    | Doc                           |    | Restriction |
|--------|-----|----|-------------------------------|----|-------------|
|        | int | FA |                               |    |             |
|        |     |    | Kernel width                  |    | padding)    |
|        |     |    |                               | 2. | Filter_h*f  |
|        |     |    |                               | 3. | filter_w    |
|        |     |    |                               |    | ∈ [1,       |
| stride | int | FA |                               |    |             |
|        |     |    | 1). stride_h and stride_w are |    |             |
|        | int | FA |                               |    |             |
|        | int | FA |                               |    |             |
| pad    | int | FA |                               |    |             |
|        |     |    | = 0). pad_h is preferred (if  |    |             |
|        | int | FA |                               |    |             |
|        | int | FA |                               |    |             |
|        | int | FA |                               |    |             |
|        |     |    | pooling. Defaults to false    |    |             |

|      |     |    | Doc                   | Restriction |
|------|-----|----|-----------------------|-------------|
| b    | flo |    |                       |             |
|      |     |    | bias                  | float16     |
| y    | flo |    |                       |             |
|      |     |    | Output tensor         | float16     |
|      | Int | TR |                       |             |
| axis | int | FA |                       |             |
|      |     |    | Axis of InnerProduct. | 1 or 2      |
| x    | flo |    |                       |             |
|      |     |    | Input tensor          | float16     |
| y    | flo |    |                       |             |
|      |     |    | Output tensor         | float16     |

|      |     |    | Doc                             | Restriction |
|------|-----|----|---------------------------------|-------------|
| axis | int | FA |                                 |             |
| x    | flo |    |                                 |             |
|      |     |    | Input                           | float16     |
| y    | flo |    |                                 |             |
|      |     |    | Negative slope (default = 0)    | float16     |
|      |     |    | height, width]. 2 indicates the |             |
|      |     |    | is bg prob and fg probs         |             |

| Doc        | Restriction    |
|------------|----------------|
| False      | (default): not |
| True       | : yes          |
| x flo      |                |
| mean flo   |                |
| Mean value | float16        |
| Variance   | float16        |

|       |     | Doc           | Restriction |
|-------|-----|---------------|-------------|
| scale | flo |               |             |
| y     | flo |               |             |
|       |     | Output tensor | float16     |
| eps   | flo |               |             |

| Doc                         | Restriction        |
|-----------------------------|--------------------|
| rois flo                    |                    |
| [batch, 5-tuple, N], where, | N is a             |
| int FA                      |                    |
| y flo                       |                    |
| int TR                      |                    |
| int TR                      |                    |
| spatial_scale_h             | and                |
| spatial_scale_w             | are specified,     |
| spatial_scale_h             | and                |
| spatial_scale_w             | applies. If        |
| spatial_scale_h             | and                |
| spatial_scale_w             | are not specified, |
| convert spatial_scale       | to                 |
| spatial_scale_h             | and                |
| spatial_scale_w             | in the Caffe       |
| Defaults to                 | 0.0625             |
| Defaults to                 | 0.0625             |

|          | Doc Restriction      |
|----------|----------------------|
| x        | flo                  |
| y        | flo                  |
| Bias INP |                      |
| x        | flo                  |
|          | Input tensor float16 |
| bias     | flo                  |
| y        | flo                  |

| Doc                    | Restriction                      |
|------------------------|----------------------------------|
| Crop INP               |                                  |
| x flo                  |                                  |
| Crops the input tensor | x to the                         |
| shape of               | size                             |
| (1) x                  | : bottom to be cropped, of       |
| (2)                    | size : destination size, of size |
| (3)                    | y : output top, cropped from x   |
| Has the same shape as  | size                             |
| axis                   | determines the dimension         |
| and                    | offsets determines the           |
| dimension size of      | x2 . Example:                    |

|      |     | Doc | Restriction |
|------|-----|-----|-------------|
| y    | flo |     |             |
| axis | An  |     |             |

|       |     | Doc             | Restriction |
|-------|-----|-----------------|-------------|
| x     | flo |                 |             |
| y     | flo |                 |             |
|       |     | (default = 1.0) | The power   |
| scale | flo |                 |             |
| shift | flo |                 |             |
| x     | flo |                 |             |
|       |     | y=tanh(x)       | float16     |

|   |     | Doc | Restriction |
|---|-----|-----|-------------|
| y | flo |     |             |
| x | flo |     |             |

|      |     |    | Doc          | Restriction |
|------|-----|----|--------------|-------------|
| y    | flo |    |              |             |
| axis | int | TR |              |             |
| x1   | flo |    |              |             |
|      |     |    | Input tensor | float16     |

|      |     | Doc                            | Restriction |
|------|-----|--------------------------------|-------------|
| x2   | flo |                                |             |
| y    | flo |                                |             |
|      |     | Output tensor                  | float16     |
|      |     | Controls x2 . (default = True) | True or     |
| eps  | FA  |                                |             |
| x    | flo |                                |             |
|      |     | feature Map                    | float16     |
| rois | flo |                                |             |
| y    | flo |                                |             |

|       |     |    | Doc                             | Restriction |
|-------|-----|----|---------------------------------|-------------|
|       | int | TR |                                 |             |
|       |     |    | Output channels, greater than 0 | Supported.  |
|       | int | TR |                                 |             |
| x     | flo |    |                                 |             |
|       |     |    | Permutes the input.             | float16     |
| y     | flo |    |                                 |             |
| order | lis |    |                                 |             |
| x     | int |    |                                 |             |

| Doc                  | Restriction     |
|----------------------|-----------------|
| dimension count of   | weight . If 1D, |
| channel_shared==True | ; else          |
| y int                |                 |

|       |     |        |     |    | Doc                  | Restriction |
|-------|-----|--------|-----|----|----------------------|-------------|
|       |     | y      | flo |    |                      |             |
|       |     | stride | int | FA |                      |             |
|       |     |        |     |    | Stride (default = 2) | float16     |
| scale | INP |        |     |    |                      |             |
|       |     | x      | flo |    |                      |             |

| Doc                        | Restriction                |
|----------------------------|----------------------------|
| int FA                     |                            |
| (bottom[0]) covered by the | scale                      |
| Set to                     | –1 to cover all axes of    |
| bottom[0] starting from    | axis . Set                 |
| to 0                       | to multiply with a scalar. |
| x flo                      |                            |

|     |     |       |     | Doc | Restriction |
|-----|-----|-------|-----|-----|-------------|
| ELU | INP |       |     |     |             |
|     |     | x     | flo |     |             |
|     |     | y     | flo |     |             |
|     |     | alpha | de  |     |             |

|       |     |   |     | Doc | Restriction |
|-------|-----|---|-----|-----|-------------|
| Slice | INP |   |     |     |             |
|       |     | x | flo |     |             |

|      |     | Doc                                  | Restriction |
|------|-----|--------------------------------------|-------------|
| y    | flo |                                      |             |
|      |     | Alias for axis (default = 1)         | Value       |
| axis | int | Begin axis for slicing (default = 1) | Value       |

|         | Doc Restriction                       |
|---------|---------------------------------------|
| Exp INP |                                       |
| x       | flo                                   |
|         | Computes exponential of x             |
| y       | flo                                   |
| base    | flo                                   |
|         | The base (default = –1.0) > 0 or = –1 |
| scale   | flo                                   |
| shift   | flo                                   |

|      |     |    | Doc                              | Restriction |
|------|-----|----|----------------------------------|-------------|
| y    | flo |    |                                  |             |
| axis | int | FA |                                  |             |
|      |     |    | the end (for example, –1 for the |             |
|      | int | FA |                                  |             |
|      |     |    | the end (for example, –1 for the |             |

| Doc                        | Restriction      |
|----------------------------|------------------|
| int8_t cropSwitch;         | //crop switch    |
| int8_t scfSwitch;          | //resize switch  |
| int8_t paddingSwitch;      | //0: unable      |
| int8_t rotateSwitch;       | //rotate switch, |
| int32_t cropStartPosW;     | //the start      |
| int32_t cropStartPosH;     | //the start      |
| int32_t cropSizeW;         | //crop width     |
| int32_t cropSizeH;         | //crop height    |
| int32_t scfInputSizeW;     | //input width    |
| int32_t scfInputSizeH;     | //input height   |
| int32_t scfOutputSizeW;    | //output         |
| int32_t scfOutputSizeH;    | //output         |
| int32_t paddingSizeTop;    | //top padding    |
| int32_t paddingSizeBottom; | //bottom         |
| int32_t paddingSizeLeft;   | //left padding   |
| int32_t paddingSizeRight;  | //right          |
| int16_t dtcPixelMeanChn0;  | //mean           |
| int16_t dtcPixelMeanChn1;  | //mean           |
| int16_t dtcPixelMeanChn2;  | //mean           |
| int16_t dtcPixelMeanChn3;  | //mean           |
| uint16_t dtcPixelMinChn0;  | //min value      |
| uint16_t dtcPixelMinChn1;  | //min value      |

| Doc                       | Restriction       |
|---------------------------|-------------------|
| uint16_t dtcPixelMinChn2; | //min value       |
| uint16_t dtcPixelMinChn3; | //min value       |
| int8_t reserve1[16];      | //32B assign, for |
| uint8_t inputFormat;      | //input format:   |
| int8_t cscSwitch;         | //csc switch      |
| int8_t rbuvSwapSwitch;    | //rb/ub swap      |
| int8_t axSwapSwitch;      | //RGBA-           |
| int8_t batchNum;          | //batch           |
| int32_t srcImageSizeW;    | //source          |
| int32_t srcImageSizeH;    | //source image    |
| int16_t cscMatrixR0C0;    | //                |
| int16_t cscMatrixR0C1;    | //                |
| int16_t cscMatrixR0C2;    | //                |
| int16_t cscMatrixR1C0;    | //                |
| int16_t cscMatrixR1C1;    | //                |
| int16_t cscMatrixR1C2;    | //                |
| int16_t cscMatrixR2C0;    | //                |
| int16_t cscMatrixR2C1;    | //                |
| int16_t cscMatrixR2C2;    | //                |
| uint8_t cscOutputBiasR0;  | //output Bias     |
| uint8_t cscOutputBiasR1;  | //output Bias     |

| Doc                            | Restriction       |
|--------------------------------|-------------------|
| uint8_t cscOutputBiasR2;       | //output Bias     |
| uint8_t cscInputBiasR0;        | //input Bias for  |
| uint8_t cscInputBiasR1;        | //input Bias for  |
| uint8_t cscInputBiasR2;        | //input Bias for  |
| int8_t reserve4[16];           | //32B assign, for |
| Path of the AIPP configuration | file              |
| rois flo                       |                   |

| Doc                | Restriction |
|--------------------|-------------|
| int TR             |             |
| Height of output   | y           |
| int TR             |             |
| Width of output    | y           |
| int FA             |             |
| score Fl           |             |
| Confidence score   | Float16     |
| Anchor information | Float16     |

|     |     |    | Doc                              | Restriction |
|-----|-----|----|----------------------------------|-------------|
|     | int | FA |                                  |             |
| x   | flo |    |                                  |             |
| img | flo |    |                                  |             |
|     |     |    | the shape are used. img_size nor |             |
|     |     |    | img_h/img_w in attr does not     |             |
| y   | flo |    |                                  |             |
|     |     |    | Output tensor                    | float16     |

|      |     |    | Doc                               | Restriction |
|------|-----|----|-----------------------------------|-------------|
| flip | bo  |    |                                   |             |
|      |     |    | Whether to flip each aspect_ratio |             |
| clip | bo  |    |                                   |             |
|      | int | FA |                                   |             |
|      | int | FA |                                   |             |
| rois | Fl  |    |                                   |             |
|      |     |    | max_rois_num is the maximum       |             |

| Doc                    | Restriction |
|------------------------|-------------|
| dimensions. The value  | 0 indicates |
| (unchanged). The value | –1          |
| y flo                  |             |
| axis int TR            |             |

| Doc   | Restriction                  |
|-------|------------------------------|
| x flo |                              |
| x     | is a variable-length list of |

|      |     |    | Doc                          | Restriction |
|------|-----|----|------------------------------|-------------|
| y    | flo |    |                              |             |
| axis | int | FA |                              |             |
|      | int | FA |                              |             |
|      |     |    | Alias for axis (default = 1) | Does not    |
| x    | Fl  |    |                              |             |

|      |     |        |     |    | Doc                                 | Restriction |
|------|-----|--------|-----|----|-------------------------------------|-------------|
|      |     | y      | Fl  |    |                                     |             |
|      |     | stride | int | FA |                                     |             |
|      |     |        |     |    | If stride and stride_h/stride_w     |             |
|      |     |        |     |    | are both specified, stride applies. |             |
|      |     |        | int | FA |                                     |             |
|      |     |        |     |    | Stride height (default = 2)         | > 1         |
|      |     |        | int | FA |                                     |             |
|      |     |        |     |    | Stride width (default = 2)          | > 1         |
|      |     | scale  | flo |    |                                     |             |
| Yolo | INP |        |     |    |                                     |             |
|      |     | x      | flo |    |                                     |             |

|       |     |    | Doc                              | Restriction |
|-------|-----|----|----------------------------------|-------------|
| boxes | int | FA |                                  |             |
|       | int | FA |                                  |             |
|       | int | FA |                                  |             |
|       |     |    | Softmax enable (default = False) | True or     |
|       |     |    | b and classes (default = False)  |             |

|     |    | Doc                               | Restriction |
|-----|----|-----------------------------------|-------------|
| int | FA |                                   |             |
| int | FA |                                   |             |
|     |    | Number of classes (default = 20)  | Up to 1024  |
|     |    | the threshold in clsProb (default |             |
| int | FA |                                   |             |
| int | FA |                                   |             |

|     |     |       |     |    | Doc                               | Restriction |
|-----|-----|-------|-----|----|-----------------------------------|-------------|
|     |     | boxes | int | FA |                                   |             |
|     |     |       | int | FA |                                   |             |
|     |     |       | int | FA |                                   |             |
|     |     |       |     |    | Number of classes (default = 80)  | Up to 1024  |
|     |     |       |     |    | the threshold in clsProb (default |             |
|     |     |       | int | FA |                                   |             |
|     |     |       | int | FA |                                   |             |
| Log | INP |       |     |    |                                   |             |
|     |     | x     | flo |    |                                   |             |
|     |     |       |     |    | Computes natural logarithm of x   |             |

|         | Doc Restriction             |
|---------|-----------------------------|
| y       | flo                         |
| base    | flo                         |
|         | (default = 1.0) base > 0 or |
| scale   | flo                         |
| shift   | flo                         |
| LRN INP |                             |
| x       | flo                         |
| y       | flo                         |
| alpha   | flo                         |
| beta    | flo                         |

|       |     |    | Doc | Restriction |
|-------|-----|----|-----|-------------|
| y     | flo |    |     |             |
|       | int | FA |     |             |
| axis  | int | FA |     |             |
| coeff | flo |    |     |             |

|       |     |   |     | Doc | Restriction |
|-------|-----|---|-----|-----|-------------|
| Split | INP |   |     |     |             |
|       |     | x | flo |     |             |

|     |     |   |     | Doc | Restriction |
|-----|-----|---|-----|-----|-------------|
|     |     | y | flo |     |             |
| SPP | INP |   |     |     |             |
|     |     | x | flo |     |             |
|     |     | y | flo |     |             |

|      |     |   |     | Doc | Restriction |
|------|-----|---|-----|-----|-------------|
| Tile | INP |   |     |     |             |
|      |     | x | flo |     |             |

|       |     |    | Doc                                | Restriction |
|-------|-----|----|------------------------------------|-------------|
| y     | flo |    |                                    |             |
| axis  | int | FA |                                    |             |
| tiles | int | TR |                                    |             |
|       |     |    | Tiling multiple. For example, if x |             |
|       |     |    | tiles=3, y has shape [n, c, 3 * h, |             |

| Doc                            | Restriction                        |
|--------------------------------|------------------------------------|
| x2 fp                          |                                    |
| adj_x2 = False                 | , x2 has shape                     |
| [batch, ..., M, K]. If         | adj_x2 = True ,                    |
| x2                             | has shape [batch, ..., K, M].      |
| batch, ...                     | must be the same as                |
| those in                       | x1                                 |
| y fp                           |                                    |
| Indicates whether to transpose | x1                                 |
| Defaults to                    | False                              |
| Indicates whether to transpose | x2                                 |
| Defaults to                    | False                              |
| x flo                          |                                    |
| cont flo                       |                                    |
| w_x flo                        |                                    |
| x                              | 's weight, with shape [input_size, |
| bias flo                       |                                    |
| w_h flo                        |                                    |

|     | Doc Restriction                  |
|-----|----------------------------------|
| h_0 | flo                              |
|     | when expose_hidden = True        |
| c_0 | flo                              |
|     | x_static weight, with shape [4 * |
| h   | flo                              |
| h_t | flo                              |
| c_t | flo                              |

|      |     |    | Doc                              | Restriction |
|------|-----|----|----------------------------------|-------------|
| x    | fp  |    |                                  |             |
| axis | int | FA |                                  |             |
|      |     |    | provided, topk is calculated per |             |

| Doc                               | Restriction                      |
|-----------------------------------|----------------------------------|
| ●                                 | If True and there are two TOPS,  |
| ●                                 | If True and there is one TOP,    |
| ●                                 | If False, the following index is |
| topk int FA                       |                                  |
| Defaults to                       | 1 , indicating the top k         |
| top_k                             | in Caffe.                        |
| RNN INP                           |                                  |
| x fp                              |                                  |
| Time change input, (T * N * ...)  | T <= 256                         |
| cont fp                           |                                  |
| Sequence continuity flag, (T * N) | T <= 256                         |

|      | Doc Restriction |
|------|-----------------|
| h_0  | fp              |
| w_xh | fp              |
| w_sh | fp              |
| w_hh | fp              |
| w_ho | fp              |
| o    | fp              |
| h_t  | fp              |

<span id="page-158-0"></span>

# **7.1.1.5 Sample Reference**

This section provides instructions for modifying frequently-used networks.

# **7.1.1.5.1 Modifying Faster R-CNN Prototxt**

# NO TE

The provided code samples must be modified before using them on the network. You need to modify the arguments, for example, the **bottom** and **top** arguments, to match the network in use.

The following uses the Faster R-CNN ResNet-34 model as an example.

# 1. Modify the Proposal operator.

According to **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**, the operator has three inputs and two outputs. Modify the original prototxt accordingly. Change **type** to that defined in the **caffe.proto** file, and add the actual\_rois\_num output node. Add attribute description by referring to the attribute definition in the **caffe.proto** file. **Figure 7-4** shows the modification. The original prototxt is on the left, and the prototxt adapted to the Ascend AI Processor is on the right.

## **Figure 7-4** Prototxt file before and after modification (1)

# A code example is as follows:

layer { name: "faster\_rcnn\_proposal" type: "Proposal" //Operator type <span id="page-159-0"></span>bottom: "rpn\_cls\_prob\_reshape" bottom: "rpn\_bbox\_pred" bottom: "im\_info" top: "rois" top: "actual\_rois\_num" // Added operator output proposal\_param { feat\_stride: 16 base\_size: 16 min\_size: 16 pre\_nms\_topn: 3000 post\_nms\_topn: 304 iou\_threshold: 0.7 output\_actual\_rois\_num: true } }

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

- 2. Add a FSRDetectionOutput operator to the last layer to output the final detection result.

On the Faster R-CNN network, add a post-processing layer

FSRDetectionOutput at the end of the original .prototxt file by referring to **[7.1.1.2 Extended Operator List](#page-77-0)**. The FSRDetectionOutput operator has five inputs and two outputs as described in **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**. Define the data types and the attributes of the operator accordingly.

A code example is as follows:

layer { name: "FSRDetectionOutput\_1" type: "FSRDetectionOutput" bottom: "rois" bottom: "bbox\_pred" bottom: "cls\_prob" bottom: "im\_info" bottom: "actual\_rois\_num" top: "actual\_bbox\_num1" top: "box1" fsrdetectionoutput\_param { num\_classes:3 score\_threshold:0.0 iou\_threshold:0.7 batch\_rois:1 } }

For details about the parameter description, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

# **7.1.1.5.2 Modifying YOLOv3 Prototxt**

## NO TE

All code samples in this section cannot be directly copied to the network model. You need to adjust the parameters to suit your use case. For example, the **bottom** and **top** parameters must match those in the corresponding network model, and the sequence of the bottom and top parameters is fixed.

- 1. Modify the **upsample\_param** attribute of the Upsample operator. Change **scale:2** in the .prototxt file of the original operator to **scale:1 stride:2** by referring to **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**.

**Figure 7-5** shows the comparison. The left part is the native operator prototxt, and the right part is the prototxt adapted to the Ascend AI Processor.

**Figure 7-5** Prototxt file before and after modification (4)

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

#### 2. Add three Yolo operators.

The Yolo and DetectionOutput operators complete the post-processing logic of the feature detection network. According to the original operator .prototxt file, three Yolo operators should be added before adding the YoloV3DetectionOutput operator.

According to **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**, a Yolo operator has one input and three outputs. The code examples of the Yolo operators are provided.

# – Code example of operator 1

layer { bottom: "layer82-conv" top: "yolo1\_coords" top: "yolo1\_obj" top: "yolo1\_classes" name: "yolo1" type: "Yolo" yolo\_param { boxes: 3 coords: 4 classes: 80 yolo\_version: "V3" softmax: true background: false }

}

– Code example of operator 2

layer {

 bottom: "layer94-conv" top: "yolo2\_coords" top: "yolo2\_obj" top: "yolo2\_classes" name: "yolo2" type: "Yolo" yolo\_param { boxes: 3 coords: 4 classes: 80 yolo\_version: "V3" softmax: true background: false

 } }

– Code example of operator 3

layer {

 bottom: "layer106-conv" top: "yolo3\_coords" top: "yolo3\_obj"

 top: "yolo3\_classes" name: "yolo3" type: "Yolo" yolo\_param { boxes: 3 coords: 4 classes: 80 yolo\_version: "V3" softmax: true background: false } }

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

- 3. Add a YoloV3DetectionOutput operator to the last layer.

On the YOLOv3 network, add a post-processing layer YoloV3DetectionOutput to the end of the original .prototxt file by referring to **[7.1.1.2 Extended](#page-77-0) [Operator List](#page-77-0)**. The YoloV3DetectionOutput operator has 10 inputs and two outputs as described in **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**.

layer { name: "detection\_out3" type: "YoloV3DetectionOutput" bottom: "yolo1\_coords" bottom: "yolo2\_coords" bottom: "yolo3\_coords" bottom: "yolo1\_obj" bottom: "yolo2\_obj" bottom: "yolo3\_obj" bottom: "yolo1\_classes" bottom: "yolo2\_classes" bottom: "yolo3\_classes" bottom: "img\_info" top: "box\_out" top: "box\_out\_num" yolov3\_detection\_output\_param { boxes: 3 classes: 80 relative: true obj\_threshold: 0.5 score\_threshold: 0.5 iou\_threshold: 0.45 pre\_nms\_topn: 512 post\_nms\_topn: 1024 biases\_high: 10 biases\_high: 13 biases\_high: 16 biases\_high: 30 biases\_high: 33 biases\_high: 23 biases\_mid: 30 biases\_mid: 61 biases\_mid: 62 biases\_mid: 45 biases\_mid: 59 biases\_mid: 119 biases\_low: 116 biases\_low: 90 biases\_low: 156 biases\_low: 198 biases\_low: 373 biases\_low: 326 }

}

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

### <span id="page-162-0"></span>4. Added the inputs.

The YoloV3DetectionOutput operator has the **img\_info** input. Add **img\_info** to model inputs. **Figure 7-6** shows the comparison. The left part is the native operator prototxt, and the right part is the prototxt adapted to the Ascend AI Processor.

## **Figure 7-6** Prototxt file before and after modification (5)

The following is a code example. **img\_info** has shape [batch, 4-tuple], where the 4-tuple is formatted [netH, netW, scaleH, scaleW]. **netH** and **netW** are H and W of the network model input, and **scaleH** and **scaleW** are H and W of the original image.

input: "img\_info" input\_shape { dim: 1 dim: 4 }

# **7.1.1.5.3 Modifying YOLOv2 Prototxt**

## NO TE

All code samples in this section cannot be directly copied to the network model. You need to adjust the parameters to suit your use case. For example, the **bottom** and **top** parameters must match those in the corresponding network model, and the sequence of the bottom and top parameters is fixed.

# 1. Modify the Region operator.

The Yolo and DetectionOutput operators complete the post-processing logic of the feature detection network. Before adding the YoloV2DetectionOutput operator, replace the Region operator with a Yolo operator.

A Yolo operator has one input and three outputs according to **[7.1.1.4 Caffe](#page-88-0) [Operator Specifications](#page-88-0)**. **Figure 7-7** shows the .prototxt file before and after modification for adapting to Ascend AI Processor.

# **Figure 7-7** Prototxt file before and after modification (2)

# A code example is as follows:

layer { bottom: "layer31-conv"  top: "yolo\_coords" top: "yolo\_obj" top: "yolo\_classes" name: "yolo" type: "Yolo" yolo\_param { boxes: 5 coords: 4 classes: 80 yolo\_version: "V2" softmax: true background: false }

}

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

- 2. Add a YoloV2DetectionOutput operator to the last layer.

On the YOLOv2 network, add a post-processing layer YoloV2DetectionOutput to the end of the original .prototxt file by referring to **[7.1.1.2 Extended](#page-77-0) [Operator List](#page-77-0)**. The YoloV2DetectionOutput operator has four inputs and two outputs as described in **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**.

layer { name: "detection\_out2" type: "YoloV2DetectionOutput" bottom: "yolo\_coords" bottom: "yolo\_obj" bottom: "yolo\_classes" bottom: "img\_info" top: "box\_out" top: "box\_out\_num" yolov2\_detection\_output\_param { boxes: 5 classes: 80 relative: true obj\_threshold: 0.5 score\_threshold: 0.5 iou\_threshold: 0.45 pre\_nms\_topn: 512 post\_nms\_topn: 1024 biases: 0.572730 biases: 0.677385 biases: 1.874460 biases: 2.062530 biases: 3.338430 biases: 5.474340 biases: 7.882820 biases: 3.527780 biases: 9.770520 biases: 9.168280 } }

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

## 3. Added the inputs.

The YoloV2DetectionOutput operator has the **img\_info** input. Add **img\_info** to model inputs. **[Figure 7-8](#page-164-0)** shows the comparison. The left part is the native operator prototxt, and the right part is the prototxt adapted to the Ascend AI Processor.

# <span id="page-164-0"></span>**Figure 7-8** Prototxt file before and after modification (1)

The following is a code example. **img\_info** has shape [batch, 4-tuple], where the 4-tuple is formatted [netH, netW, scaleH, scaleW]. **netH** and **netW** are H and W of the network model input, and **scaleH** and **scaleW** are H and W of the original image.

input: "img\_info" input\_shape { dim: 1 dim: 4 }

# **7.1.1.5.4 Modifying SSD Prototxt**

#### NO TE

All code samples in this section cannot be directly copied to the network model. You need to adjust the parameters to suit your use case. For example, the **bottom** and **top** parameters must match those in the corresponding network model, and the sequence of the bottom and top parameters is fixed.

On the SSD network, add a post-processing layer SSDDetectionOutput at the end of the original .prototxt file by referring to **[7.1.1.2 Extended Operator List](#page-77-0)**.

Add the declaration of the custom layer to the **LayerParameter** message by referring to the **caffe.proto** file.

message LayerParameter { ... optional SSDDetectionOutputParameter ssddetectionoutput\_param = 232; ... }

For details, see the **caffe.proto** file. The data types and attribute of this operator are defined as follows:

message SSDDetectionOutputParameter { optional int32 num\_classes= 1 [default = 2]; optional bool share\_location = 2 [default = true]; optional int32 background\_label\_id = 3 [default = 0]; optional float iou\_threshold = 4 [default = 0.45]; optional int32 top\_k = 5 [default = 400]; optional float eta = 6 [default = 1.0]; optional bool variance\_encoded\_in\_target = 7 [default = false]; optional int32 code\_type = 8 [default = 2]; optional int32 keep\_top\_k = 9 [default = 200]; optional float confidence\_threshold = 10 [default = 0.01]; }

As described in **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**, the SSDDetectionOutput operator has three inputs and two outputs. A code example is provided as follows:

layer { name: "detection\_out" type: "SSDDetectionOutput" <span id="page-165-0"></span> bottom: "bbox\_delta" bottom: "score" bottom: "anchors" top: "out\_boxnum" top: "y" ssddetectionoutput\_param { num\_classes: 2 share\_location: true background\_label\_id: 0 iou\_threshold: 0.45 top\_k: 400 eta: 1.0 variance\_encoded\_in\_target: false code\_type: 2 keep\_top\_k: 200 confidence\_threshold: 0.01 } }

- **bbox\_delta** corresponds to **mbox\_loc** on the original Caffe network, **score** corresponds to **mbox\_conf\_flatten** on the original Caffe network, and **anchors** corresponds to **mbox\_priorbox** on the original Caffe network. The value of **num\_classes** must be the same as that in the original Caffe network.
- In the scenario where the top output has a batch size greater than 1:
  - The output shape of **out\_boxnum** is (batchnum, 8). The first element of **batchnum** is the number of actual boxes.
  - The output shape of **y** is (batchnum,len,8), where **len** is the value of **keep\_top\_k** after 128-byte alignment. For example, if **batch = 2** and **keep\_top\_k = 200**, the output shape is (2,256,8), the first 256 x 8 data elements is the result of the first batch.

For details about parameter descriptions, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

# **7.1.1.5.5 Modifying BatchedMatMul Prototxt**

## NO TE

All code samples in this section cannot be directly copied to the network model. You need to adjust the parameters to suit your use case. For example, the **bottom** and **top** parameters must match those in the corresponding network model, and the sequence of the bottom and top parameters is fixed.

The BatchedMatMul operator multiply the two tensors: **y** = **x1** x **x2**. (The number of **x1** and **x2** dimensions must be greater than 2 and less than or equal to 8.) To use this operator in a network model, you need to modify its .prototxt file by referring to this section and then convert the model.

Add the declaration of the custom layer parameters to the **LayerParameter** message by referring to the **caffe.proto** file.

message LayerParameter { ... optional BatchMatMulParameter batch\_matmul\_param = 235; ... }

According to the **caffe.proto** file, the operator type and attributes are defined as follows:

<span id="page-166-0"></span> optional bool adj\_x2 = 2 [default = false]; }

According to **[7.1.1.4 Caffe Operator Specifications](#page-88-0)**, the BatchedMatMul operator has two inputs and one output. An example of the constructed operator code is as follows:

#### layer {

 name: "batchmatmul" type: "BatchedMatMul" bottom: "matmul\_data\_1" bottom: "matmul\_data\_2" top: "batchmatmul\_1" batch\_matmul\_param { adj\_x1:false adj\_x2:true }

For details about the parameter description, see **[7.1.1.4 Caffe Operator](#page-88-0) [Specifications](#page-88-0)**.

# **7.2 TensorFlow Network Model**

# **7.2.1 TensorFlow Operator Specifications**

|                  | Category | Description                         |
|------------------|----------|-------------------------------------|
| Abs              | math_ops | Computes the absolute value of a    |
| AccumulateNV2    | math_ops | Returns the element-wise sum of a   |
| Acos             | math_ops | Computes acos of x element-wise.    |
| Acosh            | math_ops | Computes inverse hyperbolic         |
| Add              | math_ops | Returns x + y element-wise.         |
| AddN             | math_ops | Add all input tensors element wise. |
| AddV2            | math_ops | Returns x + y element-wise.         |
| All              | math_ops | Computes the "logical and" of       |
| Any              | math_ops | Computes the "logical or" of        |
| ApproximateEqual | math_ops | Returns the truth value of abs(x-y) |

|                | Category    | Description                         |
|----------------|-------------|-------------------------------------|
| ArgMax         | math_ops    | Returns the index with the largest  |
| ArgMin         | math_ops    | Returns the index with the          |
| Asin           | math_ops    | Computes asin of x element-wise.    |
| Asinh          | math_ops    | Computes inverse hyperbolic sine    |
| Atan           | math_ops    | Computes atan of x element-wise.    |
| Atan2          | math_ops    | Computes arctangent of y/x          |
| Atanh          | math_ops    | Computes inverse hyperbolic         |
| AvgPool        | nn_ops      | Performs average pooling on the     |
| Batch          | batch_ops   |                                     |
| BatchMatMul    | math_ops    | Multiplies slices of two tensors in |
| BatchToSpace   | array_ops   | BatchToSpace for 4-D tensors of     |
| BatchToSpaceND | array_ops   | BatchToSpace for N-D tensors of     |
| BesselI0e      | math_ops    | Computes the Bessel i0e function    |
| BesselI1e      | math_ops    | Computes the Bessel i1e function    |
| Betainc        | math_ops    | Compute the regularized             |
| BiasAdd        | nn_ops      | Adds bias to value.                 |
| Bincount       | math_ops    | Counts the number of occurrences    |
| BitwiseAnd     | bitwise_ops |                                     |
| BitwiseOr      | bitwise_ops |                                     |

|                | Category         | Description                        |
|----------------|------------------|------------------------------------|
| BitwiseXor     | bitwise_ops      |                                    |
| BroadcastTo    | array_ops        | Broadcast an array for a           |
| Bucketize      | math_ops         | Bucketizes 'input' based on        |
| Cast           | math_ops         | Cast x of type SrcT to y of DstT.  |
| Ceil           | math_ops         | Returns element-wise smallest      |
| CheckNumerics  | array_ops        | Checks a tensor for NaN and Inf    |
| Cholesky       | linalg_ops       |                                    |
| CholeskyGrad   | linalg_ops       |                                    |
| ClipByValue    | math_ops         | Clips tensor values to a specified |
|                | math_ops         | Compare values of input to         |
| Concat         | array_ops        | Concatenates tensors along one     |
| ConcatV2       | array_ops        |                                    |
| Const          | array_ops        |                                    |
| ControlTrigger | control_flow_ops | Does nothing.                      |
| Conv2D         | nn_ops           | Computes a 2-D convolution given   |
|                | nn_ops           | Computes the gradients of          |
|                | nn_ops           | Computes the gradients of          |
| Cos            | math_ops         | Computes cos of x element-wise.    |
| Cosh           | math_ops         | Computes hyperbolic cosine of x    |
| Cumprod        | math_ops         | Compute the cumulative product     |

|              | Category         | Description                         |
|--------------|------------------|-------------------------------------|
| Cumsum       | math_ops         | Compute the cumulative sum of       |
|              | nn_ops           | Returns the dimension index in the  |
|              | nn_ops           | Returns the permuted vector/        |
| DepthToSpace | array_ops        | DepthToSpace for tensors of type    |
|              | nn_ops           | Computes a 2-D depthwise            |
|              | nn_ops           | Computes the gradients of           |
|              | nn_ops           | Computes the gradients of           |
| Dequantize   | array_ops        | Dequantize the 'input' tensor into  |
| Diag         | array_ops        | Returns a diagonal tensor with a    |
| DiagPart     | array_ops        | Returns the diagonal part of the    |
| Div          | math_ops         | Returns x / y element-wise.         |
| DivNoNan     | math_ops         | Returns 0 if the denominator is     |
| Elu          | nn_ops           | Computes exponential linear:        |
| Empty        | array_ops        | Creates a tensor with the given     |
| Enter        | control_flow_ops |                                     |
| Equal        | math_ops         | Returns the truth value of (x == y) |
| Erf          | math_ops         | Computes the Gauss error function   |

|                   | Category         | Description                           |
|-------------------|------------------|---------------------------------------|
| Erfc              | math_ops         | Computes the complementary            |
| Exit              | control_flow_ops |                                       |
| Exp               | math_ops         | Computes exponential of x             |
| ExpandDims        | array_ops        | Inserts a dimension of 1 into a       |
| Expm1             | math_ops         | Computes exponential of x - 1         |
|                   | array_ops        | Extract patches from images and       |
|                   | array_ops        | Fake-quantize the 'inputs' tensor,    |
|                   | array_ops        | Fake-quantize the 'inputs' tensor     |
|                   | array_ops        | Fake-quantize the 'inputs' tensor     |
| Fill              | array_ops        | Creates a tensor filled with a scalar |
| Floor             | math_ops         | Returns element-wise largest          |
| FloorDiv          | math_ops         | Returns x // y element-wise.          |
| FloorMod          | math_ops         | Returns element-wise remainder of     |
| FractionalAvgPool | nn_ops           | Performs fractional average           |
| FractionalMaxPool | nn_ops           | Performs fractional max pooling       |
| FusedBatchNorm    | nn_ops           | Batch normalization.                  |

|                  | Category  | Description                           |
|------------------|-----------|---------------------------------------|
| FusedBatchNormV2 | nn_ops    | Batch normalization.                  |
| Gather           | array_ops | Gather slices from params             |
| GatherNd         | array_ops | Gather slices from params into a      |
| GatherV2         | array_ops | Gather slices from params axis axis   |
| Greater          | math_ops  | Returns the truth value of (x > y)    |
| GreaterEqual     | math_ops  | Returns the truth value of (x >= y)   |
| GuaranteeConst   | array_ops | Gives a guarantee to the TF           |
|                  | math_ops  | Return histogram of values.           |
| Identity         | array_ops | Return a tensor with the same         |
| IdentityN        | array_ops | Returns a list of tensors with the    |
| Igamma           | math_ops  | Compute the lower regularized         |
| Igammac          | math_ops  | Compute the upper regularized         |
| IgammaGradA      | math_ops  |                                       |
| InplaceAdd       | array_ops | Adds v into specified rows of x.      |
| InplaceSub       | array_ops | Subtracts v into specified rows of x. |
| InplaceUpdate    | array_ops | Updates specified rows with values    |
| InTopK           | nn_ops    | Says whether the targets are in the   |
| InTopKV2         | nn_ops    | Says whether the targets are in the   |

|                       | Category         | Description                         |
|-----------------------|------------------|-------------------------------------|
| Inv                   | math_ops         | Computes the reciprocal of x        |
| Invert                | bitwise_ops      |                                     |
| InvertPermutation     | array_ops        | Computes the inverse permutation    |
| IsVariableInitialized | state_ops        | Checks whether a tensor has been    |
| L2Loss                | nn_ops           | L2 Loss.                            |
| Less                  | math_ops         | Returns the truth value of (x < y)  |
| LessEqual             | math_ops         | Returns the truth value of (x <= y) |
| LinSpace              | math_ops         | Generates values in an interval.    |
| ListDiff              | array_ops        |                                     |
| Log                   | math_ops         | Computes natural logarithm of x     |
| Log1p                 | math_ops         | Computes natural logarithm of (1    |
| LogicalAnd            | math_ops         | Returns the truth value of x AND y  |
| LogicalNot            | math_ops         | Returns the truth value of NOT x    |
| LogicalOr             | math_ops         | Returns the truth value of x OR y   |
| LogSoftmax            | nn_ops           | Computes log softmax activations.   |
| LoopCond              | control_flow_ops | Forwards the input to the output.   |
| LowerBound            | array_ops        |                                     |
| LRN                   | nn_ops           | Local Response Normalization.       |
| MatMul                | math_ops         | Multiply the matrix "a" by the      |
| MatrixBandPart        | array_ops        | Copy a tensor setting everything    |

|                   | Category         | Description                         |
|-------------------|------------------|-------------------------------------|
| MatrixDeterminant | linalg_ops       |                                     |
| MatrixDiag        | array_ops        | Returns a batched diagonal tensor   |
| MatrixDiagPart    | array_ops        | Returns the batched diagonal part   |
| MatrixInverse     | linalg_ops       |                                     |
| MatrixSetDiag     | array_ops        | Returns a batched matrix tensor     |
| MatrixSolve       | linalg_ops       |                                     |
| MatrixSolveLs     | linalg_ops       |                                     |
| Max               | math_ops         | Computes the maximum of             |
| Maximum           | math_ops         | Returns the max of x and y (i.e.    |
| MaxPool           | nn_ops           | Performs max pooling on the         |
| MaxPoolV2         | nn_ops           | Performs max pooling on the         |
|                   | nn_ops           | Performs max pooling on the input   |
| Mean              | math_ops         | Computes the mean of elements       |
| Merge             | control_flow_ops | Forwards the value of an available  |
| Min               | math_ops         | Computes the minimum of             |
| Minimum           | math_ops         | Returns the min of x and y (i.e.    |
| MirrorPad         | array_ops        | Pads a tensor with mirrored values. |
| MirrorPadGrad     | array_ops        |                                     |
| Mod               | math_ops         | Returns element-wise remainder of   |

|                 | Category         | Description                         |
|-----------------|------------------|-------------------------------------|
| Mul             | math_ops         |                                     |
| Multinomial     | random_ops       | Draws samples from a multinomial    |
| Neg             | math_ops         |                                     |
| NextIteration   | control_flow_ops | Makes its input available to the    |
| NoOp            | no_op            | Does nothing.                       |
| NotEqual        | math_ops         | Returns the truth value of (x != y) |
| NthElement      | nn_ops           | Finds values of the n-th order      |
| OneHot          | array_ops        | Returns a one-hot tensor.           |
| OnesLike        | array_ops        | Returns a tensor of ones with the   |
| Pack            | array_ops        |                                     |
| Pad             | array_ops        |                                     |
| ParallelConcat  | array_ops        |                                     |
|                 | random_ops       | Outputs random values from a        |
| Placeholder     | array_ops        |                                     |
| PopulationCount | bitwise_ops      |                                     |
| Pow             | math_ops         | Computes the power of one value     |
| PreventGradient | array_ops        |                                     |
| Prod            | math_ops         | Computes the product of elements    |
| Qr              | linalg_ops       |                                     |
| RandomGamma     | random_ops       | Outputs random values from the      |

|                  | Category            | Description                         |
|------------------|---------------------|-------------------------------------|
| RandomShuffle    | random_ops          | Randomly shuffles a tensor along    |
| RandomUniform    | random_ops          | Outputs random values from a        |
| Range            | math_ops            | Creates a sequence of numbers.      |
| RandomUniformInt | random_ops          | Outputs random integers from a      |
| Rank             | array_ops           | Returns the rank of a tensor.       |
| ReadVariableOp   | resource_variable_o |                                     |
| RealDiv          | math_ops            | Returns x / y element-wise for real |
| Reciprocal       | math_ops            | Computes the reciprocal of x        |
| RefEnter         | control_flow_ops    |                                     |
| RefExit          | control_flow_ops    |                                     |
| RefMerge         | control_flow_ops    |                                     |
| RefNextIteration | control_flow_ops    | Makes its input available to the    |
| RefSwitch        | control_flow_ops    | Forwards the ref tensor data to the |
| Relu             | nn_ops              | Computes rectified linear:          |
| Relu6            | nn_ops              | Computes rectified linear 6:        |
| Reshape          | array_ops           | Reshapes a tensor.                  |
| ReverseSequence  | array_ops           | Reverses variable length slices.    |
| ReverseV2        | array_ops           |                                     |
| RightShift       | bitwise_ops         |                                     |
| Rint             | math_ops            | Returns element-wise integer        |
| Round            | math_ops            | Rounds the values of a tensor to    |

|                | Category  | Description                         |
|----------------|-----------|-------------------------------------|
| Rsqrt          | math_ops  | Computes reciprocal of square root  |
| SegmentMax     | math_ops  | Computes the maximum along          |
| Select         | math_ops  |                                     |
| Selu           | nn_ops    | Computes scaled exponential         |
| Shape          | array_ops | Returns the shape of a tensor.      |
| ShapeN         | array_ops | Returns shape of tensors.           |
| Sigmoid        | math_ops  | Computes sigmoid of x element      |
| Sign           | math_ops  | Returns an element-wise indication  |
| Sin            | math_ops  | Computes sin of x element-wise.     |
| Sinh           | math_ops  | Computes hyperbolic sine of x       |
| Size           | array_ops | Returns the size of a tensor.       |
| Slice          | array_ops | Return a slice from 'input'.        |
| Snapshot       | array_ops | Returns a copy of the input tensor. |
| Softmax        | nn_ops    | Computes softmax activations.       |
| Softplus       | nn_ops    | Computes softplus:                  |
| Softsign       | nn_ops    | Computes softsign: features /       |
| SpaceToBatch   | array_ops | SpaceToBatch for 4-D tensors of     |
| SpaceToBatchND | array_ops | SpaceToBatch for N-D tensors of     |
| SpaceToDepth   | array_ops | SpaceToDepth for tensors of type    |
| Split          | array_ops | Splits a tensor into num_split      |
| SplitV         | array_ops | Splits a tensor into num_split      |

|                   | Category         | Description                          |
|-------------------|------------------|--------------------------------------|
| Sqrt              | math_ops         | Computes square root of x            |
| Square            | math_ops         | Computes square of x element        |
| SquaredDifference | math_ops         | Returns (x - y)(x - y) element-wise. |
| Squeeze           | array_ops        | Removes dimensions of size 1 from    |
| StopGradient      | array_ops        | Stops gradient computation.          |
| StridedSlice      | array_ops        | Return a strided slice from input.   |
| Sub               | math_ops         |                                      |
| Sum               | math_ops         | Computes the sum of elements         |
| Svd               | linalg_ops       |                                      |
| Switch            | control_flow_ops | Forwards data to the output port     |
| Tan               | math_ops         | Computes tan of x element-wise.      |
| Tanh              | math_ops         | Computes hyperbolic tangent of x     |
| Tile              | array_ops        | Constructs a tensor by tiling a      |
| TopK              | nn_ops           | Finds values and indices of the k    |
| TopKV2            | nn_ops           |                                      |
| Transpose         | array_ops        | Shuffle dimensions of x according    |
| TruncateDiv       | math_ops         | Returns x / y element-wise for       |
| TruncatedNormal   | random_ops       | Outputs random values from a         |
| TruncateMod       | math_ops         | Returns element-wise remainder of    |
| Unbatch           | batch_ops        |                                      |

|                  | Category          | Description                         |
|------------------|-------------------|-------------------------------------|
| UnbatchGrad      | batch_ops         |                                     |
| Unique           | array_ops         | Finds unique elements in a 1-D      |
| UniqueWithCounts | array_ops         | Finds unique elements in a 1-D      |
| Unpack           | array_ops         |                                     |
| UnravelIndex     | array_ops         | Converts a flat index or array of   |
|                  | math_ops          | Computes the minimum along          |
|                  | math_ops          | Computes the product along          |
|                  | math_ops          | Computes the sum along segments     |
| UpperBound       | array_ops         |                                     |
| Variable         | state_ops         | Holds state in the form of a tensor |
| Where            | array_ops         | Returns locations of nonzero / true |
| Xdivy            | math_ops          | Returns 0 if x == 0, and x / y      |
| Xlogy            | math_ops          | Returns 0 if x == 0, and x * log(y) |
| ZerosLike        | array_ops         | Returns a tensor of zeros with the  |
| Zeta             | math_ops          | Compute the Hurwitz zeta            |
| _Retval          | function_ops      |                                     |
| LeakyRelu        | nn_ops            |                                     |
| FusedBatchNormV3 | nn_ops/mkl_nn_ops |                                     |

<span id="page-179-0"></span>8.1 What Do I Do If Model Conversion Takes Too Long When the OS and Architecture Configuration of the Development Environment Is Arm (AArch64)? 8.2 How Do I Determine the Video Stream Format Standard When I Perform CSC on a Model Using AIPP?

[8.3 What Do I Do If a Network Model Containing an NPU-based Custom Operator](#page-180-0) [Failed to Be Frozen?](#page-180-0)

# **8.1 What Do I Do If Model Conversion Takes Too Long When the OS and Architecture Configuration of the Development Environment Is Arm (AArch64)?**

For an Arm (AArch64) development environment, to accelerate model conversion, you can use the numactl tool to specify CPU cores for model conversion as follows:

- 1. Log in to the development environment as the ATC installation user and run the **su root** command to switch to the **root** user.
- 2. Ensure that the development environment is connected to the network, and run the following command to install the numactl tool: yum -y install numactl
- 3. Switch to the ATC installation user and run the **numactl -C** command to specify CPU cores 16–31 to process model conversion: CPU cores 16–31 are recommended for better processing performance. You can also change the CPU cores as required. numactl -C 16-31 --localalloc <args>

Replace <args> with the actual ATC model conversion command.

# **8.2 How Do I Determine the Video Stream Format Standard When I Perform CSC on a Model Using AIPP?**

**Q:** How do I determine the video stream format standard when I perform CSC on a model using AIPP?

<span id="page-180-0"></span>**A:** The third-party tool **ffprobe** is used as an example. You can use other thirdparty tools.

- 1. Download the tool and related documents from **[https://www.ffmpeg.org/](https://www.ffmpeg.org/ffprobe-all.html#Description) [ffprobe-all.html#Description](https://www.ffmpeg.org/ffprobe-all.html#Description)**.
- 2. Obtain the video information by using the **ffprobe -show\_frames filename** parameter.

**Parameter function:** Show information about each frame and subtitle contained in the input multimedia stream. The information for each single frame is printed within a dedicated section with name "FRAME" or "SUBTITLE".

- 3. Determine the video standard based on the result:

**color\_range**: **tv** or **pc**

**color\_space**: **bt709** or **bt601**

**tv** indicates "limited", that is, narrow range. **pc** indicates "full", that is, wide range.

For example, **color\_range=tv and color\_space=bt709**. The video stream format standard is NARROW, BT-709.

## NO TE

If the command is different from the example, refer to the official description of the tool.

# **8.3 What Do I Do If a Network Model Containing an NPU-based Custom Operator Failed to Be Frozen?**

During model training on the NPU for a model containing an NPU-based custom operator, the **freeze** method in TensorFlow fails to be called to freeze the trained model. This problem can be solved by importing the NPU-based custom operator library to the **freeze** method of TensorFlow. For example:

**Figure 8-1** shows the model file exported by **estimator.export\_model** after the bert model is trained on the NPU. The model file contains an NPU-based custom operator.

**Figure 8-1** Trained model file

Run the following command to call the **freeze** method in TensorFlow to freeze the model:

The following error information is displayed.

The model file contains an NPU-based custom operator, which is stored in the **npu\_ops** package. Therefore, the **freeze** method cannot find the operator in the TensorFlow operator library.

Perform the following steps to solve the problem:

**Step 1** Find the Python file of the **freeze** method of TensorFlow, add **npu\_ops** to the modules to import in this file by adding the following content.

**Step 2** Run the following command to call the **freeze** method in TensorFlow to freeze the model:

python3 freeze\_npu\_savedModel.py --input\_saved\_model\_dir=savedModel --output\_node\_names=loss/ Softmax --output\_graph=bert\_910.pb

The model is successfully frozen by calling the **freeze** method in TensorFlow, and a normal **bert\_910.pb** file is generated.

**----End**

# NO TE

The **freeze** method in TensorFlow can be independently called. You are advised to copy the Python file of the **freeze** method to the user directory and add **npu\_ops** to the modules to import. In this way, the **freeze** method can be repeatedly used for models trained on the NPU. Ensure that the environment variables are correctly configured.