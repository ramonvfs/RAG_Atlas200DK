**CANN V100R020C20**

# **TensorFlow Network Model Porting and Training Guide**

**Issue** 01 **Date** 2021-02-09

#### **Trademarks and Permissions**

All other trademarks and trade names mentioned in this document are the property of their respective holders.

#### **Notice**

The purchased products, services and features are stipulated by the contract made between Huawei and the customer. All or part of the products, services and features described in this document may not be within the purchase scope or the usage scope. Unless otherwise specified in the contract, all statements, information, and recommendations in this document are provided "AS IS" without warranties, guarantees or representations of any kind, either express or implied.

The information in this document is subject to change without notice. Every effort has been made in the preparation of this document to ensure accuracy of the contents, but all statements, information, and recommendations in this document do not constitute a warranty of any kind, express or implied.

# **Contents**

| 2 Restrictions and Limitations.................................................................................................                                                             | 2  |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----|
| 3 Migration Workflow...............................................................................................................                                                         | 3  |
| 4 Auto Migration and Manual Migration.............................................................................                                                                          | 4  |
| 4.1 Model Preparation and Evaluation...................................................................................................................................                     | 4  |
| 4.3 Manual Migration...................................................................................................................................................................     | 6  |
| 4.3.2 tf.case, tf.cond, and tf.while_loop Options to Migrate............................................................................................                                    | 7  |
| 4.3.3 tf.ConfigProto Options to Migrate.................................................................................................................................                    | 7  |
| 4.3.5.3 Distributed Training Based on the PS-Worker Architecture............................................................................                                                | 17 |
| 5.1 Training Service Workflow.................................................................................................................................................              | 23 |
| 6 Performance Tuning.............................................................................................................                                                           | 31 |
| 6.2 Network-wide Performance Optimization...................................................................................................................                                | 32 |
| 6.2.1 Iteration Offloading..........................................................................................................................................................        | 32 |
| 6.2.2 Data Preprocessing Performance.................................................................................................................................                       | 37 |
| 6.2.3 Distributed Performance Improvement.....................................................................................................................                              | 38 |
| 6.2.4 Mixed Precision..................................................................................................................................................................     | 41 |
| 6.2.5 Loss Scaling......................................................................................................................................................................... | 43 |
| 6.3 Operator Performance Optimization.............................................................................................................................                          | 45 |
| 6.3.2 Operator Profiling.............................................................................................................................................................       | 45 |

| 7.2 Operator Overflow Detection...........................................................................................................................................                   | 49  |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| 9 More Functions......................................................................................................................                                                       | 59  |
| 9.1 Mixed Computing.................................................................................................................................................................         | 59  |
| 9.2 Log and Summary Operators...........................................................................................................................................                     | 60  |
| 9.3 Collective Communication APIs.......................................................................................................................................                     | 62  |
| 9.3.3 Specifying Devices to Perform Collective Communication.................................................................................                                                | 64  |
| 9.3.4 Obtaining the Group Information...............................................................................................................................                         | 64  |
| 9.3.6 Computing Tensor Nodes for Collective Communication...................................................................................                                                 | 65  |
| 10 Reference..............................................................................................................................                                                   | 67  |
| 10.1 Migration Examples...........................................................................................................................................................           | 67  |
| 10.1.1.3 Training Code Directory Structure.........................................................................................................................                          | 69  |
| 10.1.1.4 Data Preprocessing.....................................................................................................................................................             | 70  |
| 10.1.1.5 Model Building.............................................................................................................................................................         | 72  |
| 10.1.1.6 Run Configuration.......................................................................................................................................................            | 75  |
| 10.1.1.7 Training........................................................................................................................................................................... | 76  |
| 10.1.2 More Migration Cases...................................................................................................................................................               | 80  |
| 10.2 API Reference.......................................................................................................................................................................    | 80  |
| 10.2.1 Available TensorFlow APIs...........................................................................................................................................                  | 80  |
| 10.2.2.1 Overview......................................................................................................................................................................      | 109 |
| 10.2.2.2.3 DumpConfig Constructor....................................................................................................................................                        | 127 |
| 10.2.2.3 npu_bridge.estimator.npu.npu_estimator.........................................................................................................                                     | 129 |
| 10.2.2.3.1 NPUEstimator Constructor.................................................................................................................................                         | 129 |
| 10.2.2.3.2 NPUEstimatorSpec Constructor........................................................................................................................                              | 130 |
| 10.2.2.4 npu_bridge.estimator.npu.npu_hook..................................................................................................................                                 | 132 |

| 10.2.2.6 npu_bridge.estimator.npu.npu_loss_scale_optimizer....................................................................................                                           | 138 |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| 10.2.2.6.1 NPULossScaleOptimizer Constructor..............................................................................................................                               | 138 |
| 10.2.2.7 npu_bridge.estimator.npu.npu_loss_scale_manager.....................................................................................                                            | 140 |
| 10.2.2.7.2 ExponentialUpdateLossScaleManager Constructor...................................................................................                                              | 140 |
| 10.2.2.8 npu_bridge.estimator.npu_ops..............................................................................................................................                      | 141 |
| 10.2.2.8.2 LARSV2......................................................................................................................................................................  | 142 |
| 10.2.2.8.3 initialize_system.....................................................................................................................................................        | 143 |
| 10.2.2.8.4 shutdown_system..................................................................................................................................................             | 149 |
| 10.2.2.9 npu_bridge.estimator.npu.npu_rnn.....................................................................................................................                           | 149 |
| 10.2.2.11.2 create_iteration_per_loop_var.........................................................................................................................                       | 153 |
| 10.2.2.12 npu_bridge.estimator.npu.keras_to_npu.........................................................................................................                                 | 155 |
| 10.2.2.12.1 model_to_npu_estimator..................................................................................................................................                     | 155 |
| 10.2.2.13 Session Configuration in sess.run Mode.........................................................................................................                                | 156 |
| 10.2.3 Collective Communication APIs...............................................................................................................................                      | 173 |
| 10.2.3.1 Overview......................................................................................................................................................................  | 173 |
| 10.2.3.2 hccl.manage.api.........................................................................................................................................................        | 173 |
| 10.2.3.2.2 destroy_group.........................................................................................................................................................        | 176 |
| 10.2.3.2.3 get_rank_size...........................................................................................................................................................      | 177 |
| 10.2.3.2.6 get_local_rank_id...................................................................................................................................................          | 179 |
| 10.2.3.2.7 get_world_rank_from_group_rank...................................................................................................................                             | 180 |
| 10.2.3.2.8 get_group_rank_from_world_rank...................................................................................................................                             | 181 |
| 10.2.3.3 hccl.split.api................................................................................................................................................................. | 182 |
| 10.2.3.3.1 set_split_strategy_by_idx.....................................................................................................................................                | 182 |
| 10.2.3.4.3 broadcast..................................................................................................................................................................   | 187 |
| 10.2.3.4.4 reduce_scatter.........................................................................................................................................................       | 189 |

| 10.2.3.4.5 send............................................................................................................................................................................ | 190 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| 10.3 Environment Variables...................................................................................................................................................               | 192 |
| 10.3.1 JOB_ID.............................................................................................................................................................................. | 192 |
| 10.3.4 RANK_ID..........................................................................................................................................................................    | 193 |
| 10.3.5 RANK_SIZE......................................................................................................................................................................      | 193 |
| 10.3.6 GE_USE_STATIC_MEMORY........................................................................................................................................                         | 193 |
| 10.3.7 PROFILING_MODE.......................................................................................................................................................                | 194 |
| 10.3.9 SKT_ENABLE...................................................................................................................................................................        | 195 |
| 10.3.10 OP_NO_REUSE_MEM................................................................................................................................................                     | 195 |
| 10.3.12 DUMP_GE_GRAPH.....................................................................................................................................................                  | 196 |
| 10.3.14 TE_PARALLEL_COMPILER........................................................................................................................................                        | 196 |
| 10.3.16 ASCEND_REMAIN_CACHE_SIZE_RATIO..............................................................................................................                                        | 197 |
| 10.3.18 HCCL_INTRA_PCIE_ENABLE....................................................................................................................................                          | 197 |
| 10.3.21 ASCEND_GLOBAL_EVENT_ENABLE......................................................................................................................                                    | 199 |
| 10.3.22 ASCEND_LOG_DEVICE_FLUSH_TIMEOUT..........................................................................................................                                           | 199 |
| 11 FAQ.......................................................................................................................................                                               | 204 |
| 11.4 What Do I Do If uint8 and quint8 Addition/Subtraction Overflows?............................................................                                                           | 208 |
| 12 Appendixes.........................................................................................................................                                                      | 209 |
| 12.1 How Do I Install GCC 7.3.0?.........................................................................................................................................                   | 209 |
| 12.2 Change History.................................................................................................................................................................        | 210 |

# **1 About This Document**

#### <span id="page-6-0"></span>**Purpose**

This guide provides step-by-step instructions on migrating a training script developed with the TensorFlow Python API to Ascend AI Processor.

#### **Before You Start**

The code snippets in this document are only examples. Manual tweaking is needed.

# <span id="page-7-0"></span>**2 Restrictions and Limitations**

- 1. The compatible TensorFlow version is 1.15. For details about the support for TensorFlow APIs, see **[10.2.1 Available TensorFlow APIs](#page-85-0)**.
- 2. In the **infershape** phase, the operators do not support unknown shape inference.
- 3. Currently, the system supports the following formats: NCHW, NHWC, NC, HWCN, and CN.
- 4. If manual mixed precision has been implemented in the original script (for example, explicitly calling the cast operator for precision conversion), the system preferentially retains the source image precision by default. That is, when the operator does not support the float32 data type, the precision is reduced to float16.
- 5. For condition branches and loop branches, only **TF.cond**, **TF.while\_loop**, and **tf.case** are supported.
- 6. In training with multiple devices, **NPURunconfig** does not support **save\_checkpoints\_secs**.
- 7. In training with multiple devices, it is not allowed to save the summary information of only a single device.
- 8. If the value of **iterations\_per\_loop** is greater than 1, the value of **save\_checkpoints\_steps** must be a positive integer multiple of **iterations\_per\_loop**; otherwise, checkpoint data is not saved as defined by **save\_checkpoints\_steps**. If the value of **iterations\_per\_loop** is greater than 1, data is not saved as defined by **save\_summary\_steps** and **log\_step\_count\_steps**. For details, see **[9.2 Log and Summary Operators](#page-65-0)**.
- 9. Only the Summary, Log, and Data operators support the string data type.
- 10. The operators do not support the inf or nan inputs.
- 11. The restrictions on collective communication are as follows:
  - Allocation at only 1, 2, 4, or 8 Ascend AI Processors per server is supported.
  - AllReduce and ReduceScatter support only the int8, int32, float16, and float32 data types.
- 12. For data preprocessing, the getnext operator can be offloaded to the device only by using **tf.data.make\_initializable\_iterator**.

# **3 Migration Workflow**

<span id="page-8-0"></span>Model migration is to migrate models that have been implemented in the opensource community to Ascend AI Processor. The following figure shows the model migration workflow.

# <span id="page-9-0"></span>**4 Auto Migration and Manual Migration**

4.1 Model Preparation and Evaluation 4.2 Auto Migration Using Tool [4.3 Manual Migration](#page-11-0)

# **4.1 Model Preparation and Evaluation**

#### **Model Preparation**

Before model migration to Ascend AI Processor, prepare a model developed on TensorFlow 1.15 and a matched dataset, run the model on GPU or CPU to test if the accuracy is converged as expected. In addition, record the accuracy and performance specifications for comparison on the Ascend AI Processor later on.

#### **Model Evaluation**

Currently, the Ascend platform works with TensorFlow 1.15 only. Check if all the APIs used in your model are listed in **[10.2.1 Available TensorFlow APIs](#page-85-0)** before you move on to **4.2 Auto Migration Using Tool**. Avoid using unsupported APIs in your model.

# **4.2 Auto Migration Using Tool**

#### **Overview**

The Ascend platform provides the **TF1.x** script migration tool, which is applicable to the migration of native TensorFlow training scripts. You can use this tool to automatically migrate and adapt TensorFlow Python APIs and Horovod Python APIs for training on Ascend AI Processors. The migration tool can only migrate most content of your training script. Perform manual migration for the residual part by referring to **[4.3 Manual Migration](#page-11-0)**.

#### **Tool Acquisition**

Find the **TF1.x** script migration tool from **/tfplugin/python/site-packages/ npu\_bridge/conver\_tf2npu/** in the TFPlugin installation directory.

#### **Restrictions**

Before using this tool, note the following restrictions:

Make sure the TensorFlow and Horovod modules have been imported to your training script in strict accordance with the following format:

import tensorflow as tf

import tensorflow.compat.v1 as tf

import horovod.tensorflow as hvd

#### **Tool Usage**

Run the following command in the directory where the migration tool is stored:

**python3 main.py -i /home/BERT -o /home/out -r /home/report**

The arguments are described as follows.

**main.py**: main function of the tool.

**/home/BERT**: path of the training script to be migrated. Must be a folder path.

**/home/out**: target path of script migration.

**/home/report**: path of the migration report.

#### NO TE

You can run the **python3 main.py -h** command to obtain the help information.

There are three types of migration reports:

- **success\_report.txt**: migration success report. For example, the following report indicates that **hvd.ran** in line 522 of **run\_ner.py** has been migrated to **get\_rank\_id** of **NPU**. /home/BERT/run\_ner.py:522 "change hvd.rank to get\_rank\_id"
- **failed\_report.txt**: migration failure report.
- **need\_migration\_doc.txt**: report about the part requiring manual migration. The information provided in this report might be incomplete currently. Refer to **[4.3 Manual Migration](#page-11-0)** for more information.

## **Options Migratable Using Tool**

- 1. TensorFlow or Horovod APIs can be automatically migrated by the tool. The mapping of APIs before and after migration is summarized as follows:
  - "batch(xxx)" --> "batch(xxx, drop\_remainder=True)"
  - "map\_and\_batch(xxx)" --> "map\_and\_batch(xxx, drop\_remainder=True)"
  - "dataset.shard(xxx, xxx)" --> "dataset.shard(get\_rank\_size(), get\_rank\_id())"

- <span id="page-11-0"></span>– "tf.device(xxx)" --> "tf.device(/cpu:0)"
- "tf.estimator.EstimatorSpec" --> "NPUEstimatorSpec"
- "tf.estimator.RunConfig" --> "NPURunConfig"
- "tf.estimator.Estimator" --> "NPUEstimator"
- "tf.nn.dropout" --> "npu\_ops.dropout"
- "tf.compat.v1.layers.max\_pooling2d" --> "tf.compat.v1.nn.max\_pool\_with\_argmax"
- "tf.layers.max\_pooling2d" --> "tf.nn.max\_pool\_with\_argmax"
- "hvd.init" --> "None"
- "hvd.DistributedOptimizer" --> "NPUDistributedOptimizer"
- "hvd.rank" --> "get\_rank\_id"
- "hvd.local\_rank" --> "get\_local\_rank\_id"
- "hvd.size" --> "get\_rank\_size"
- "BroadcastGlobalVariablesHook" --> "None"
- 2. The following function can be automatically migrated by the tool. The **gelu** functions in your script will be replaced with **npu\_unary\_ops.gelu**. See the following example. Script before migration: def gelu(x): cdf = 0.5 \* (1.0 + tf.tanh( (np.sqrt(2 / np.pi) \* (x + 0.044715 \* tf.pow(x, 3))))) return x\*cdf layers = gelu() Script after migration: layers = npu\_unary\_ops.gelu(x)
- 3. For a Python script, the "from npu\_bridge.npu\_init import \*" line will be added to the script for importing dependent NPU library files.

# **4.3 Manual Migration**

# **4.3.1 tf.config.optimizer.set\_experimental\_options Options to Migrate**

If the **tf.config.optimizer.set\_experimental\_options** API is used in your training script, note the following restrictions:

- The following configuration option is deactivated by default and should remain deactivated: rewrite\_options.disable\_model\_pruning
- The following configuration options are activated by default and should remain activated:
  - rewrite\_options.function\_optimization
  - rewrite\_options.constant\_folding
  - rewrite\_options.shape\_optimization
  - rewrite\_options.arithmetic\_optimization

- rewrite\_options.loop\_optimization
- rewrite\_options.dependency\_optimization
- rewrite\_options.layout\_optimizer
- rewrite\_options.memory\_optimization

# <span id="page-12-0"></span>**4.3.2 tf.case, tf.cond, and tf.while\_loop Options to Migrate**

If the input has a static shape, **tf.case**, **tf.cond**, and **tf.while\_loop** are supported by Ascend AI Processor naturally. Therefore, the script requires no more editing.

If the input has a dynamic shape, upgrade the control flow operators of the V1 version to those of the V2 version to support the dynamic shape function. Only TensorFlow V2 control flow operators (such as If, Case, While, For, and PartitionedCall) support dynamic shape. TensorFlow V1 control flow operators (such as Switch, Merge, Enter, LoopCond, NextIteration, Exit and ControlTrigger) corresponding to the **tf.case**, **tf.cond**, and **tf.while\_loop** APIs do not support dynamic shape. In addition, if there are many branch structures on the network, using the control flow operator of the V1 version may result in excessive streams. [ERROR] DRV(13489,rtstest\_host):2020-10-29-05:26:01.002.033 [hardware/dev\_plat/../dev\_core/tsdrv/ tsdrv\_id.c:172][devdrv] [drvStreamIdAlloc 172] ioctl failed, devId(0) tsid(0), strategy(0), ret(-1). [ERROR] RUNTIME(13489,rtstest\_host):2020-10-29-05:26:01.002.050 [runtime/feature/src/npu\_driver.cc: 102]13489 StreamIdAlloc:drvStreamIdAlloc failed: errorcode=17, device\_id=0, tsId=0, strategy=0 [ERROR] RUNTIME(13489,rtstest\_host):2020-10-29-05:26:01.002.061 [runtime/feature/src/stream.cc: 184]13489 Setup:**fail to alloc stream id**

In this case, you also need to upgrade the control flow operators of the V1 version to the V2 version to solve the problem.

Specifically:

Call the following API at the beginning of the training script:

tf.enable\_control\_flow\_v2()

Perform necessary adaptations. For example, the following error may be reported during script execution:

TypeError: In op 'NovoGrad/update\_Loss\_Optimization/FP32-master-copy/ForwardPass/w2l\_encoder/conv11/ kernel/ApplyMomentum', input types([tf.float32,tf.float32,tf.float32,tf.float32,tf.float32]) are not compatible with expected types ([tf.float32\_ref,tf.float32\_ref,tf.float32,tf.float32,tf.float32])

As the message tells, input0 and input1 of the ApplyMomentum operator are of type reference, instead of variable. In this case, convert the inputs of the ApplyMomentum operator from variables to resource variables:

tf.enable\_resource\_variables()

# **4.3.3 tf.ConfigProto Options to Migrate**

If your training script uses the **sess.run** method, add the following lines in bold in the training session after automatic migration is complete using the migration tool:

**config = tf.ConfigProto()** # If your script does not contain **tf.ConfigProto**, you need to manually add this line.

**custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()** # Manually add this line. **custom\_op.name = "NpuOptimizer"** # Manually add this line. **config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF** # Manually add this line.

sess.run(init) train\_batches\_per\_epoch=int(np.floor(train\_size/batch\_size))

# <span id="page-13-0"></span>**4.3.4 Keras Options to Migrate**

If you are using a Keras training script, the script migrated to the Ascend platform will lose support of certain features such as the dynamic learning rate. Therefore, you are not advised to migrate Keras scripts to the Ascend platform. To run a Keras script on the Ascend platform, you need to edit the script as follows:

Create a TensorFlow session, register Keras, and activate **use\_off\_line** for training on Ascend AI Processor. Remember to close the session when the training is complete.

import tensorflow as tf import tensorflow.python.keras as keras from tensorflow.python.keras import backend as K from npu\_bridge.npu\_init import \* **sess\_config = tf.ConfigProto() custom\_op = sess\_config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True sess\_config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF sess = tf.Session(config=sess\_config) K.set\_session(sess)** # Preprocess the data... # Construct a model... # Build the model... # Train the model...

#### **sess.close()**

With the migrated training script, the number of training iterations performed by the Ascend AI Processor is fixed to 1 in every **session.run** call. To reduce data transfers between the host and device and shorten the training duration, use the **model\_to\_npu\_estimator** API to convert the model constructed by Keras to an **NPUEstimator** object and set **iterations\_per\_loop** (number of training iterations performed by the Ascend AI Processor every **session.run** call) in **NPURunConfig** to a value that suits your needs. For details, see **[Setting iterations\\_per\\_loop with](#page-40-0) [Keras](#page-40-0)**.

# **4.3.5 Configuration Options for Distributed Training Migration**

#### **4.3.5.1 Typical Scenarios**

#### **Overview**

In a large-scale AI training cluster, training is generally completed in a data parallel manner. Data parallelism means that all devices use the same model and different training samples, and gradient data calculated by each device needs to be aggregated for parameter update.

**Figure 4-1** Schematic diagram of data-parallel training

If classification is performed in gradient aggregation manner, the mainstream implementation of data parallelism includes the Parameter Server-Workers (PS-Worker) architecture and AllReduce collective communication. The Ascend platform supports both implementation types. For details, see **[4.3.5.2 Distributed](#page-18-0) [Training Based on the AllReduce Architecture](#page-18-0)** and **[4.3.5.3 Distributed Training](#page-22-0) [Based on the PS-Worker Architecture](#page-22-0)**.

#### **Single-Server Scenario**

In the single-server scenario, one training server is used to complete the training. The server consists of eight Ascend AI Processors. Only one, two, four, or eight devices participate in collective communication, and devices 0 to 3 and devices 4 to 7 form a network respectively. When two or four devices are used for training, cross-network cluster creation is not supported.

**Figure 4-2** Single-server training

#### **Server Cluster Scenario**

In a server cluster scenario, a training server cluster consists of the master node for cluster management and a bunch of slave training servers. Currently, a server cluster can have up to 128 servers. Each server consists of eight Ascend AI Processors. In a server cluster, the number of Ascend AI Processors that participate in collective communication equals to one, two, four, or eight times the number of the training servers. The cluster performance is maximized when the number of training servers is a power of 2, which is therefore highly recommended.

#### NO TE

The number and positions of the Ascend AI Processors across the servers must be consistent. When the number of Ascend AI Processors used for training is 2 or 4 times the number of the training servers, devices 0 to 3 and devices 4 to 7 form a network respectively. In this case, cross-network cluster creation is not supported.

#### **Figure 4-3** Cluster training

#### NO TE

The master cluster management node manages the cluster and devices in the cluster, and supports distributed job management in the entire cluster.

In the cluster training scenario, a distributed training flow is as follows.

**Figure 4-4** Distributed training workflow

The training job is delivered to the training server through the master node. The job agent on each server starts a number of TensorFlow processes to perform training based on the number of devices specified by the application. One TensorFlow process corresponds to one Ascend AI Processor.

Typically, the eight network ports of each server are enabled to implement collective communication between servers. In some scenarios, training can be implemented with just some of the network ports. That is, you can enable just one, two, or four network ports on each server.

- One, two, or four network ports of each server are enabled to network a training cluster. Recommendation for network port selection is as follows:
  - To enable only one network port on each server, the performance of each network port is the same.
  - To enable two network ports on each server, select network ports [0, 5], [1, 4], [2, 7], or [3, 6] for optimal performance.
  - To enable four network ports on each server, select network ports [0, 2, 5, 7] or [1, 3, 4, 6] for optimal performance.
- Broadcast, AllReduce, ReduceScatter, and AllGather of the training cluster is implemented through intra-node and inter-node collective communication. Data communication between nodes can be implemented only through the available one, two, or four network ports.
- The number of Ascend AI Processors participated in collective communication must be 8 times the number of the training servers.

### **Atlas 300T training card (model: 9000)**

Currently, training using the training cards allows two scenarios: single-server single-device training and multi-server multi-device distributed training. A single training card is equipped with one Ascend AI Processor.

For distributed training with multiple servers, the 100G ETH ports provided by each training card are used for communication between servers, and the Ring and Halving-doubling algorithms are used to implement collective communication.

**Figure 4-5** Networking diagram

Precautions:

- 1. The number of training cards in different servers must be the same.
- 2. In the entire network, the IP addresses of the NICs of each training card are in the same network segment.
- 3. Currently, only AllReduce, AllGather, Broadcast, and ReduceScatter are supported.

- <span id="page-18-0"></span>4. Before training, use the **HCCL\_INTRA\_PCIE\_ENABLE** and **HCCL\_INTRA\_ROCE\_ENABLE** environment variables to set the communication mode between devices. The PCIe path is used by default. The RoCE path is recommended. For details, see **[10.3 Environment Variables](#page-197-0)**.
- 5. The number of Ascend AI Processors participating in training specified in the ranktable configuration file cannot be greater than the number of available Ascend AI Processors on the server. Template 1 must be used. For details, see **[10.4 Device Resource Configuration File Templates](#page-204-0)**.

#### **4.3.5.2 Distributed Training Based on the AllReduce Architecture**

#### **Overview**

In the AllReduce architecture, all devices used for training form a logical ring, as shown in **Figure 4-6**. There is no central node to aggregate the calculated gradients. Each device receives data from the upstream device and transmits data to the downstream device to fully utilize the bandwidth.

**Figure 4-6** AllReduce architecture

The Ascend platform provides the **NPUDistributedOptimizer** high-level distributed API to implement gradient aggregation in the AllReduce architecture. **NPUDistributedOptimizer** encapsulates the single-server training optimizer into an NPU-based distributed training optimizer, to support single-server multi-device and multi-server multi-device scenarios. Gradients are calculated in each device before aggregation. After the **NPUDistributedOptimizer** optimizer is called, AllReduce operators are inserted between the gradient computation and update operators in the generated training graph.

#### **Distributed Training with Estimator**

To perform distributed training in **Estimator** mode, modify the training script as follows:

- 1. TensorFlow uses **train\_distribute** in **Runconfig** to specify a distributed training policy. The Ascend platform does not support **train\_distribute**. You need to delete related code.
- 2. During model training, class **NPUDistributedOptimizer** encapsulates the single-server training optimizer into an NPU distributed training optimizer. from npu\_bridge.npu\_init import \*

def cnn\_model\_fn(features,labels,mode): # Construct the network. xxx # Calculate the loss. xxx

#Configure the TrainingOp(for TRAIN mode)

if mode == tf.estimator.ModeKeys.TRAIN:

 optimizer = tf.train.GradientDescentOptimizer(learning\_rate=0.001) # Use the SGD optimizer. distributedOptimizer=**NPUDistributedOptimizer**(optimizer) # Use the NPU-based distributed computing to update gradients.

train\_op=distributedOptimizer.minimize(loss=loss,global\_step=tf.train.get\_global\_step()) # Minimize

the loss. return tf.estimator.EstimatorSpec(mode=mode,loss=loss,train\_op=train\_op)

#### NO TE

- If the original script uses a TensorFlow API to calculate the gradients, for example, **grads = tf.gradients(loss, tvars)**, after **NPUDistributedOptimizer** is constructed, replace the API with the **compute\_gradients** and **apply\_gradients** methods of **NPUDistributedOptimizer**.
- In **Estimator** mode, when **NPUDistributedOptimizer** is used to implement the AllReduce function, **NPUBroadcastGlobalVariablesHook** is automatically added to **NPUEstimator**. Therefore, you do not need to manually implement the broadcast function.

#### **Distributed Training with sess.run**

To perform distributed training by using the **sess.run** method, modify the training script as follows:

- 1. When creating a session, you need to manually add the **GradFusionOptimizer** of the NPU to fuse broadcast operators and ensure that associated optimization options such as pruning of TensorFlow are enabled.

from npu\_bridge.npu\_init import \*

# Create a session. config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF **config.graph\_options.rewrite\_options.optimizers.extend(["pruning",**

 **"function", "constfold", "shape", "arithmetic", "loop", "dependency", "layout", "memory",**

 **"GradFusionOptimizer"])** sess = tf.Session(config=config)

- 2. After the variables are initialized and before the training, the variables are broadcast through the collective communication API **broadcast**. from npu\_bridge.npu\_init import \*

def broadcast\_global\_variables(root\_rank, index): """Broadcasts all global variables from root rank to all other processes. Arguments: root\_rank: rank of the process from which global variables will be broadcasted to all other processes. index: rank\_id """ op\_list = [] for var in tf.global\_variables(): # the input and out tensor of HCOMBroadcast interface are list if "float" in var.dtype.name: inputs = [var] outputs=hccl\_ops.**broadcast**(tensor=inputs,root\_rank=root\_rank) if outputs is not None: op\_list.append(outputs[0].op) op\_list.append(tf.**assign**(var, outputs[0]))

return tf.group(op\_list)

bcast\_op = broadcast\_global\_variables(root\_rank, index) sess = tf.Session()

... sess.run(bcast\_op)

- 3. During the training, after the gradient data of each device is calculated, the gradient data is aggregated through the collective communication API **allreduce**.

from npu\_bridge.npu\_init import \* grads = [ hccl\_ops.**allreduce**(grad, "sum") for grad in grads ]

Alternatively, use the **NPUDistributedOptimizer** distributed training optimizer to aggregate gradient data.

from npu\_bridge.npu\_init import \*

optimizer = tf.train.GradientDescentOptimizer(learning\_rate=0.001) # Use the SGD optimizer. distributedOptimizer=**NPUDistributedOptimizer**(optimizer) # Use the NPU-based distributed computing to update gradients.

#### **Distributed Training with Keras**

To perform distributed training by using the **Keras** method, modify the training script as follows:

- 1. Modify the optimizer during Keras model build. Use the TensorFlow singleserver training optimizer (do not use the Keras optimizer) and use class **NPUDistributedOptimizer** to encapsulate the single-server training optimizer. For example:

from npu\_bridge.npu\_init import \*

opt = tf.compat.v1.train.AdamOptimizer(learning\_rate=0.1)

**opt = NPUDistributedOptimizer(opt)**

keras\_model.compile(optimizer=opt,loss='sparse\_categorical\_crossentropy')

In the distributed scenario, the dynamic learning rate cannot be set in the callback function.

- 2. (Optional) If a session is created, you need to manually add the **GradFusionOptimizer** optimizer.

import tensorflow as tf

import tensorflow.python.keras as keras from tensorflow.python.keras import backend as K

from tensorflow.core.protobuf.rewriter\_config\_pb2 import RewriterConfig

from npu\_bridge.npu\_init import \*

sess\_config = tf.ConfigProto()

custom\_op = sess\_config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True

sess\_config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF

**sess\_config.graph\_options.rewrite\_options.optimizers.extend(["GradFusionOptimizer"]) # Add for distributed training** 

sess = tf.Session(config=sess\_config)

K.set\_session(sess)

# Preprocess the data... # Construct a model... # Build the model... # Train the model...

sess.close()

#### <span id="page-22-0"></span>**4.3.5.3 Distributed Training Based on the PS-Worker Architecture**

#### **Overview**

In the PS-Worker architecture, servers in a cluster are classified into two types: parameter servers and workers. Parameter servers store model parameters, and workers calculate the gradients. In each iteration, workers obtain parameters from parameter servers, and then return the calculated gradients to the parameter servers. Parameter servers aggregate the calculated gradients, update the parameters, and then broadcast the updated parameters to workers.

**Figure 4-7** PS-Worker architecture

This section describes how to migrate the training scripts developed using TensorFlow Python APIs, so as to perform distributed training on Ascend AI Processor based on the PS-Worker architecture.

#### NO TICE

- 1. Currently, distributed training on Ascend AI Processor based on the PS-Worker architecture supports only the **NPUEstimator** mode.
- 2. Process of a worker can only be executed on one device.
- 3. In the PS-Worker architecture scenario, you are advised to use high-speed NICs.

#### **Configuring the Cluster**

In the PS-Worker architecture, cluster is configured using the **TF\_CONFIG** environment variable, which contains the **cluster** and **task** components. **cluster** provides information about the entire cluster, namely the workers and parameter servers in the cluster. **task** provides information about the current task. For details, visit **[https://tensorflow.google.cn/tutorials/distribute/](https://tensorflow.google.cn/tutorials/distribute/multi_worker_with_estimator) [multi\\_worker\\_with\\_estimator](https://tensorflow.google.cn/tutorials/distribute/multi_worker_with_estimator)**.

The following uses the two-server (each server has one parameter server and 8 workers) scenario as an example:

#### 1. Set **TF\_CONFIG**.

- os.environ['TF\_CONFIG'] = json.dumps({ 'cluster': { #'chief':chief\_hosts, # Optional 'worker': worker\_hosts, 'ps': ps\_hosts, 'evaluator':evaluator\_hosts, # Not required if evaluation is not performed }, 'task': {'type': job\_name, 'index': task\_index} })
- 2. Configure **ps\_hosts** and **worker\_hosts** using **FLAGS** as follows. ps\_hosts = FLAGS.ps\_hosts.split(',') worker\_hosts = FLAGS.worker\_hosts.split(',') evaluator\_hosts = FLAGS.evaluator\_hosts.split(',') task\_index = FLAGS.task\_index job\_name = FLAGS.job\_name flags.DEFINE\_string("ps\_hosts", '192.168.1.100:2222,192.168.1.200:2222',) flags.DEFINE\_string("worker\_hosts", '192.168.1.100:2223, 192.168.1.100:2224, 192.168.1.100:2225, 192.168.1.100:2226,' '192.168.1.100:2227, 192.168.1.100:2228, 192.168.1.100:2229, 192.168.1.100:2230,' '192.168.1.200:2223, 192.168.1.200:2224, 192.168.1.200:2225, 192.168.1.200:2226,' '192.168.1.200:2227, 192.168.1.200:2228, 192.168.1.200:2229, 192.168.1.200:2230',) flags.DEFINE\_string("evaluator\_hosts", '192.168.1.100:2231',) flags.DEFINE\_string("job\_name", '', "One of 'ps', 'worker', 'evaluator', chief") flags.DEFINE\_integer("task\_index", 0, "Index of task within the job")

Configuration description:

- **worker\_hosts** and **ps\_hosts**: Separate the items by commas (,) without spaces.
- **chief\_hosts**: Only one argument can be set at most, which can also be left empty as in the preceding example. If **chief** is not specified, the first worker is used as the chief by default. The chief worker performs model training as other workers, and also manages other work, for example, checkpoint saving and restoration, as well as summary writing.
- **evaluator\_hosts**: Only one argument can be set, which is not required if evaluation is not performed. Next, you need to configure **TF\_CONFIG** for all workers.

#### **Defining the ParameterServerStrategy Instance**

To support distributed training in the PS-Worker architecture, the **tf.distribute.experimental.ParameterServerStrategy** instance needs to be defined first. For details about this strategy, see

**[tf.distribute.experimental.ParameterServerStrategy](https://tensorflow.google.cn/api_docs/python/tf/distribute/experimental/ParameterServerStrategy)**.

strategy = tf.distribute.experimental.ParameterServerStrategy()

## **Training and Evaluating the Model**

In **NPURunConfig**, you need to specify the distribution strategy for **NPUEstimator** by using the **distribute** parameter, and then call **[tf.estimator.train\\_and\\_evaluate](https://tensorflow.google.cn/api_docs/python/tf/estimator/train_and_evaluate)** to train and evaluate the model.

Ensure that **NPURunConfig.model\_dir** of all workers is set to the same directory. For example, for a shared file system that can be read and written by all workers, if a directory is set for worker 1, this shared directory must be mounted to worker 2, and the values of **NPURunConfig.model\_dir** must be the same.

from npu\_bridge.npu\_init import \*

run\_config = **NPURunConfig**( model\_dir=flags\_obj.model\_dir, session\_config=session\_config, keep\_checkpoint\_max=5, save\_summary\_steps=1, log\_step\_count\_steps=1, save\_checkpoints\_steps=100, enable\_data\_pre\_proc=True,  **mix\_compile\_mode=True, # PS-Worker supports only mixed precision. iterations\_per\_loop=1, # This value must be 1 in mixed precision mode.** precision\_mode='allow\_mix\_precision', **distribute=strategy**) classifier = tf.estimator.**NPUEstimator**( model\_fn=model\_fn, model\_dir='/tmp/multiworker', config=run\_config) tf.estimator.train\_and\_evaluate( classifier, train\_spec=tf.estimator.TrainSpec(input\_fn=input\_fn), eval\_spec=tf.estimator.EvalSpec(input\_fn=input\_fn))

#### NO TICE

The evaluation process can be executed on the device or the host CPU. You can decide as required.

The following uses the single-server, 8-device scenario as an example. One parameter server process and 8 worker processes are needed, and the 8 worker processes are executed on the device side.

- **To perform evaluation and training at the same time**, the number of processes started by the evaluator and workers at the same time cannot exceed the number of devices on the server (that is, 8 in the given example). Since the 8 devices are already used by the worker processes in the example, evaluation needs to be performed on the host CPU. In this case, although training and evaluation can be performed in parallel, the compute capability of Ascend AI Processor is not utilized for evaluation. If evaluation is performed in this mode, it is recommended that checkpoint storage duration be set longer than the evaluation duration. To perform evaluation on the host side, call the native TensorFlow **Estimator**. **Estimator** should not be converted into **NPUEstimator** to avoid using device resources. Otherwise, evaluation fails because the devices are already used for training.
- **To perform evaluation after training**, ensure that the evaluator is executed after the workers complete the training. In this case, both the training and evaluation processes are executed on the devices to achieve optimal performance.

#### <span id="page-25-0"></span>**Running the Script**

To run the script using the **ps\_hosts** and **worker\_hosts** information in the Python script (**chief** is not defined in the Python script):

python resnet50\_ps\_strategy.py --job\_name=ps --task\_index=0 python resnet50\_ps\_strategy.py --job\_name=ps --task\_index=1 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=0 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=1 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=2 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=3 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=4 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=5 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=6 python resnet50\_ps\_strategy.py --job\_name=worker --task\_index=7

To redefine **ps\_hosts** and **worker\_hosts** (**chief** is not defined in the Python script):

python resnet50\_ps\_strategy.py \ --ps\_hosts=192.168.1.79:2222,192.168.1.80:2222 \ - worker\_hosts=192.168.1.79:2223,192.168.1.79:2224,192.168.1.79:2225,192.168.1.79:2226,192.168.1.79:2227,1 92.168.1.79:2228,192.168.1.79:2229,192.168.1.79:2230,192.168.1.80:2223,192.168.1.80:2224,192.168.1.80:222 5,192.168.1.80:2226,192.168.1.80:2227,192.168.1.80:2228,192.168.1.80:2229,192.168.1.80:2230 \ --job\_name=ps \ --task\_index=0

To run **chief** and **evaluator**, modify **job\_name** to the defined type value as follows.

python resnet50\_ps\_strategy.py --job\_name=chief --task\_index=0 python resnet50\_ps\_strategy.py --job\_name=evaluator --task\_index=0

#### NO TE

For details about the dependent environment variables, see **[5.2 Training in a Bare Metal](#page-31-0) [Environment](#page-31-0)**.

### **4.3.5.4 Distributed Training Based on the Horovod Framework**

If your model is trained in distributed mode based on the Horovod framework, you need to manually edit your script after auto migration using the migration tool.

Original Horovod code:

import tensorflow as tf import horovod.tensorflow as hvd # Initialize Horovod hvd.init() # Pin GPU to be used to process local rank (one GPU per process) config = tf.ConfigProto() config.gpu\_options.visible\_device\_list = str(hvd.local\_rank()) # Build model... loss = ... opt = tf.train.AdagradOptimizer(0.01 \* hvd.size()) # Add Horovod Distributed Optimizer opt = hvd.DistributedOptimizer(opt) # Add hook to broadcast variables from rank 0 to all other processes during # initialization. hooks = [hvd.BroadcastGlobalVariablesHook(0)] # Make training operation

#### train\_op = opt.minimize(loss)

# Save checkpoints only on worker 0 to prevent other workers from corrupting them. checkpoint\_dir = '/tmp/train\_logs' if hvd.rank() == 0 else None

# The MonitoredTrainingSession takes care of session initialization,

# restoring from a checkpoint, saving to a checkpoint, and closing when done

# or an error occurs.

with tf.train.MonitoredTrainingSession(checkpoint\_dir=checkpoint\_dir,

config=config,

hooks=hooks) as mon\_sess:

 while not mon\_sess.should\_stop(): # Perform synchronous training.

#### mon\_sess.run(train\_op) Code after migration:

# The following libraries are already automatically imported by the migration tool.

import tensorflow as tf

from npu\_bridge.npu\_init import \*

# In this sample, the HCCL group management API is called. Therefore, HCCL needs to be initialized manually when a new session is started.

**npu\_int = npu\_ops.initialize\_system() npu\_shutdown = npu\_ops.shutdown\_system()**

**config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()**

**custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True**

**config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF init\_sess = tf.Session(config=config)**

**init\_sess.run(npu\_int)**

# Pin GPU to be used to process local rank (one GPU per process) config.gpu\_options.visible\_device\_list = str(**get\_local\_rank\_id()**) # Editing completed by the migration tool.

# Build model...

loss = ... opt = tf.train.AdagradOptimizer(0.01 \* **get\_rank\_size()**) # Editing completed by the migration tool.

# Add NPU Distributed Optimizer **opt = NPUDistributedOptimizer(opt)** # Editing completed by the migration tool.

# Add hook to broadcast variables from rank 0 to all other processes during

# initialization.

**# hooks = [hvd.BroadcastGlobalVariablesHook(0)]** # Editing completed by the migration tool.

# Manual editing: Call the collective communication API **broadcast** to broadcast variables in sess.run mode.

**input = tf.trainable\_variables()**

**bcast\_global\_variables\_op = hccl\_ops.broadcast(input, 0)**

# Make training operation train\_op = opt.minimize(loss)

# Save checkpoints only on worker 0 to prevent other workers from corrupting them. checkpoint\_dir = '/tmp/train\_logs' if **get\_rank\_id()** == 0 else None # Editing completed by the migration tool.

# The MonitoredTrainingSession takes care of session initialization,

# restoring from a checkpoint, saving to a checkpoint, and closing when done

# or an error occurs.

with tf.train.MonitoredTrainingSession(checkpoint\_dir=checkpoint\_dir,

config=config,

hooks=hooks) as mon\_sess:

# Manually broadcast the variables.

 **mon\_sess.run(bcast\_global\_variables\_op)**

 while not mon\_sess.should\_stop(): # Perform synchronous training. mon\_sess.run(train\_op)

# Manual editing: Call **shutdown\_system** and close the session after the training is complete. **init\_sess.run(npu\_shutdown) init\_sess.close()**

<span id="page-28-0"></span>5.1 Training Service Workflow [5.2 Training in a Bare Metal Environment](#page-31-0)

# **5.1 Training Service Workflow**

#### **Typical Training Service Workflow**

A typical training service workflow usually includes the model preparation, data preparation, training execution, and model storage phases.

- Prepare a model: Use the customized expressions of the NN framework to construct a model and associated loss functions, and specify an appropriate optimizer to define the gradient computation and tuning algorithms.
- Prepare data: Import and parse data files and preprocess the data. The NN framework encapsulates common data preprocessing and enhancement operations and allows you to read and preprocess data using third-party libraries or in a user-defined manner.
- Train the model: Initialize your model based on the network structure defined in the model preparation phase and restore the pre-trained model from the model file. Read the training data and start the training iterations. The model accuracy is evaluated along the training process. When the model accuracy

reaches a certain threshold, the training is stopped and the trained model is saved.

#### **Training Service Data Flow**

TF Adapter registers the NPU-TF Optimizer to partition the computational graph at the TensorFlow backend. The operators that can be offloaded to the device side are fused into one or more GEOP structures. Attr reflects all TensorFlow operators in the graph.

The data flow diagram of the training service contains a range of GEOP subgraphs:

- Data processing subgraph, including data reading and data augmentation. When both training and evaluation are performed, two data processing subgraphs are generated to process the training dataset and evaluation dataset, respectively.
- Model initialization subgraph, including variable initialization, variable verification, and variable broadcast (optional in distributed scenarios).
- Model training subgraph, whose input comes from the output of the training data processing subgraph. It performs forward propagation based on the network structure, calculates the loss, calculates the gradient, and updates the model parameters based on the optimization algorithm. It also saves the trained model and outputs logs in the training process. All the preceding steps are encapsulated in a loop body and are repeated iteratively according to the training configuration.
- Model evaluation subgraph, whose input comes from the output of the data processing subgraph. It performs forward propagation based on the network structure and calculates the model accuracy based on the defined algorithm.

# **5.2 Training in a Bare Metal Environment**

#### **Overview**

This section describes how to execute a TensorFlow training job in a bare-metal environment. You are welcome to visit **[Ascend ModelZoo](https://gitee.com/ascend/modelzoo)** for more samples that are ready to run on Ascend AI Processors. The following uses the single-device and two-device training scenarios as examples to describe the general training procedure on Ascend AI Processors.

#### NO TICE

One device corresponds to one training process. It is not supported to run multiple training processes on a single device.

#### **Prerequisite**

- You have prepared a bare metal server environment.
- You have prepared a dataset.
- Your TensorFlow training script have been migrated.

#### **Single-Device Training**

**Step 1** Prepare a single-device resource configuration file. If your training script does not involve calls to collective communication APIs, skip this step.

Assume that the configuration file is named **rank\_table\_1p.json**. The following provides a template of the file content.

{ "server\_count":"1", "server\_list": [ { "device":[ { "device\_id":"0", "device\_ip":"192.168.1.8", "rank\_id":"0" } ], "server\_id":"10.0.0.10"

<span id="page-31-0"></span> } ], "status":"completed", "version":"1.0" }

For details about the configuration file, see **[10.4 Device Resource Configuration](#page-204-0) [File Templates](#page-204-0)**.

#### **Step 2** Configure the environment variables required for starting the training process.

# Set the environment variables for the installation paths of the training component dependencies as

follows:

# Method 1: Install Ascend-CANN-Toolkit for training on an Ascend AI device in the development

environment.

export install\_path=/home/HwHiAiUser/Ascend/ascend-toolkit/latest# Ascend-CANN-Toolkit installation

path. Replace it as required.

# Method 2: Install Ascend-CANN-NNAE for training on an Ascend AI device.

export install\_path=/home/HwHiAiUser/Ascend/nnae/latest# Ascend-CANN-NNAE installation path.

Replace it as required. # Driver dependency

export LD\_LIBRARY\_PATH=/usr/local/Ascend/driver/lib64/common/:/usr/local/Ascend/driver/lib64/driver:

\$LD\_LIBRARY\_PATH # Required only in the containerized training scenario

# FwkACLlib dependency

export PATH=\${install\_path}/fwkacllib/ccec\_compiler/bin:\${install\_path}/fwkacllib/bin:\$PATH

export LD\_LIBRARY\_PATH=\${install\_path}/fwkacllib/lib64:\$LD\_LIBRARY\_PATH export PYTHONPATH=\${install\_path}/fwkacllib/python/site-packages:\$PYTHONPATH

# TFPlugin dependency

export PYTHONPATH=\${install\_path}/tfplugin/python/site-packages:\$PYTHONPATH

export PYTHONPATH=/home/HwHiAiUser/Ascend/tfplugin/latest/tfplugin/python/site-packages:

\$PYTHONPATH # Replace the TFPlugin installation path with the actual one.

# OPP dependency

export ASCEND\_OPP\_PATH=\${install\_path}/opp

# AI CPU dependency

export ASCEND\_AICPU\_PATH=\${install\_path}

#Script directory

export PYTHONPATH=/home/test:\$PYTHONPATH

export JOB\_ID=10086 export ASCEND\_DEVICE\_ID=0

export RANK\_ID=0

export RANK\_SIZE=1 export RANK\_TABLE\_FILE=/home/test/rank\_table\_1p.json // Unnecessary if your training script does not involve calls to collective communication APIs.

**Step 3** Run the training script to start the training process.

#### **python3.7 /home/test/xxx.py**

#### NO TE

The preceding lists only the environment variables necessary for starting a training process. For details about all available environment variables, see **[10.3 Environment Variables](#page-197-0)**.

If GCC in the training environment (such as CentOS, Debian, and BCLinux) needs to be upgraded, add **\${install\_path}/lib64** (replace **{install\_path}** with the GCC installation path) to this variable. For details, see **[Step 5](#page-215-0)**.

**----End**

## **Multi-Device Training**

For distributed training on multiple devices, you need to start the training processes in sequence. In the following distributed training example, two devices are used.

**Step 1** Prepare a 2-device resource configuration file. Assume that the file is named **rank\_table\_2p.json**. The following provides a template of the file content.

{ "server\_count":"1", "server\_list": [ { "device":[ { "device\_id":"0", "device\_ip":"192.168.1.8", "rank\_id":"0" }, { "device\_id":"4", "device\_ip":"192.168.1.9", // The two devices must be in the same network segment. Devices 0 and 4 are in the same network segment. "rank\_id":"1" } ], "server\_id":"10.0.0.10" } ], "status":"completed", "version":"1.0" }

For details about the configuration file, see **[10.4 Device Resource Configuration](#page-204-0) [File Templates](#page-204-0)**.

**Step 2** Start the training processes in different shell windows.

Start training process 0.

... Configure the dependency components. This can be done by following the same process as above.

... export PYTHONPATH=/home/test:\$PYTHONPATH export JOB\_ID=10086 export ASCEND\_DEVICE\_ID=0 export RANK\_ID=0 export RANK\_SIZE=2 export RANK\_TABLE\_FILE=/home/test/rank\_table\_2p.json python3.7 /home/test/xxx.py

Start training process 1.

... Configure the dependency components. This can be done by following the same process as above.

... export PYTHONPATH=/home/test:\$PYTHONPATH export JOB\_ID=10086 export ASCEND\_DEVICE\_ID=4 export RANK\_ID=1 export RANK\_SIZE=2 export RANK\_TABLE\_FILE=/home/test/rank\_table\_2p.json python3.7 /home/test/xxx.py

NO TE

Alternatively, you can create a startup script to start training processes by using a loop. Click **[here](https://gitee.com/ascend/modelzoo/blob/master/built-in/TensorFlow/Benchmark/nlp/Bert-base_for_TensorFlow/scripts/run_8p.sh)** for more information.

**----End**

## **Viewing Training Result**

**Step 1** Check whether error logs are generated during system runtime. Pay attention to the following important phases:

- 1. The training process has been started, as shown in **Figure 5-1**.

**Figure 5-1** Training process started

- 2. A training iteration has been started, as shown in **Figure 5-2**.

**Figure 5-2** A training iteration started

- 3. All training iterations are complete, as shown in **Figure 5-3**.

**Figure 5-3** All training iterations completed

- 4. The model is saved successfully, as shown in **Figure 5-4**.

**Figure 5-4** Model saved successfully

**Step 2** After the training is complete, find the following generated directories and check the accuracy convergence.

- **model**: stores checkpoint files and model files. Whether this directory is generated depends on the script implementation.
- **kernel\_meta**: stores the .o and .json files of the operator. The files can be used to locate GE and FE errors. By default, the files are deleted. You can set **op\_debug\_level** to **3** in the training script to retain the .o and .json files.

**----End**

#### **Fault Locating**

If the script execution fails, analyze and locate the fault based on the following logs:

Host log: **\$HOME/ascend/log/plog/plog\_\*.log**. Replace **\$HOME** with the root directory of the user on the host.

#### Log format:

[Level] ModuleName(PID,PName):DateTimeMS LogContent

#### NO TE

For more information, see **[Log Reference](https://support.huawei.com/enterprise/en/doc/EDOC1100180793?idPath=23710424%7C251366513%7C22892968%7C251168373)**.

You can identify the error module and determine the cause by using ERROR-level log messages.

#### **Figure 5-5** Error log example

#### **Table 5-1** Fault locating techniques

| Module Name        | Error Process           | Solution |
|--------------------|-------------------------|----------|
| System error       | Environment and version |          |
| Data Preprocessing | Data preprocessing      | N/A      |
| GE                 | GE graph build or       |          |
| FE                 | Operator selection or   |          |
| TEFUSION           | Operator build          | N/A      |
| RUNTIME            | Initialization or graph |          |

<span id="page-36-0"></span>6.1 Network Throughput Analysis [6.2 Network-wide Performance Optimization](#page-37-0) [6.3 Operator Performance Optimization](#page-50-0)

# **6.1 Network Throughput Analysis**

You can add the following performance data printing statement to your training script.

After the training script is executed, you can run the following command to check the logs to see whether the throughput meets the requirements:

#### <span id="page-37-0"></span>**gerp FPS log.txt**

Set the number of printing steps to **log\_every\_n\_steps=1** or **log\_every\_n\_steps=10** as needed.

If the throughput is not as expected, improve the network performance by referring to **6.2 Network-wide Performance Optimization**. If the throughput is still unsatisfactory, improve the performance of specific operators by referring to **[6.3 Operator Performance Optimization](#page-50-0)**.

# **6.2 Network-wide Performance Optimization**

# **6.2.1 Iteration Offloading**

#### **Overview**

**Iterations\_per\_loop** is the number of iterations per training loop performed on the device side per **sess.run()** call. Training is performed according to the specified number of iterations per loop (**iterations\_per\_loop**) on the device side and then the result is returned to the host. This parameter can save unnecessary interactions between the host and device and reduce the training time consumption. Note the following:

- The default value of **iterations\_per\_loop** is **1**, and the total number of training iterations must be an integer multiple of **iterations\_per\_loop**.
- If the value of **iterations\_per\_loop** is greater than 1, the value of **save\_checkpoints\_steps** must be a positive integer multiple of **iterations\_per\_loop**; otherwise, checkpoint data is not saved as defined by **save\_checkpoints\_steps**. If the value of **iterations\_per\_loop** is greater than 1, data is not saved as defined by **save\_summary\_steps** and **log\_step\_count\_steps**. For details, see **[9.2 Log and Summary Operators](#page-65-0)**.
- In mixed computing mode (**mix\_compile\_mode** is set to **True**), this parameter must be set to **1**.
- The getNext operator can be scheduled to the device side only when **enable\_data\_pre\_proc** is enabled and **iterator tf.data.make\_initializable\_iterator()** is used. In this case, the **iterations\_per\_loop** parameter takes effect only when it is set to a value greater than 1.

When **enable\_data\_pre\_proc** is disabled or other data preprocessing iterators, such as **tf.data.make\_one\_shot\_iterator()**, are used, the getNext operator will not be scheduled to the device side. Therefore, **iterations\_per\_loop** does not take effect when it is set to a value greater than 1.

In **Estimator** mode, if the return value of **input\_fn** is dataset,

**tf.data.make\_initializable\_iterator()** is implicitly called in internal processing of **Estimator**.

- During network commissioning, you are advised to set **iterations\_per\_loop** to **1** to facilitate log printing every iteration. After the network is set up correctly, you can set the **iterations\_per\_loop** parameter to shorten the training time.

#### **Iteration Offloading with Estimator**

In **Estimator** mode, configure **iterations\_per\_loop** in **NPURunConfig** as follows.

from npu\_bridge.npu\_init import \*

session\_config=tf.ConfigProto()

config = NPURunConfig(session\_config=session\_config, **iterations\_per\_loop=10**)

#### **Iteration Offloading with sess.run()**

In **sess.run** mode, configure the **iterations\_per\_loop** parameter by using **set\_iteration\_per\_loop** and change the number of **sess.run()** calls to the original number of calls divided by the value of **iterations\_per\_loop**. The following shows how to configure **iterations\_per\_loop**.

from \_\_future\_\_ import print\_function

import input\_data from npu\_bridge.npu\_init import \*

mnist = input\_data.read\_data\_sets("/test/", one\_hot=True)

import tensorflow as tf

# Set the model.

# Set the learning rate.

learning\_rate = 0.01 # Set the number of training iterations.

training\_epochs = 10 # Set the batch size.

batch\_size = 100 # Set the number of iterations after which the loss is displayed once.

display\_step = 1

x = tf.placeholder(tf.float32, [None, 784])

y = tf.placeholder(tf.float32, [None, 10])

# Set the model parameters. W = tf.Variable(tf.zeros([784, 10])) b = tf.Variable(tf.zeros([10]))

# Build the model.

pred = tf.nn.softmax(tf.matmul(x, W) + b)

# Define the loss function: cross entropy. cost = tf.reduce\_mean(-tf.reduce\_sum(y\*tf.log(pred), reduction\_indices=1))

# Optimize the gradient descent.

optimizer = tf.train.GradientDescentOptimizer(learning\_rate).minimize(cost)

init = tf.global\_variables\_initializer() config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True # Perform training on the Ascend AI Processor. custom\_op.parameter\_map["mix\_compile\_mode"].b = False # Disable the mixed computing function. Set this parameter as required. This function is disabled by default. **custom\_op.parameter\_map["iterations\_per\_loop"].i = 10** # Determine whether the training iteration is offloaded. Must be equal to **iterations\_per\_loop** of **set\_iteration\_per\_loop**. config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. # Train the model. with tf.Session(config=config) as sess: sess.run(init) # Set the number of iterations per loop to **10** in **sess.run** mode. **train\_op = util.set\_iteration\_per\_loop(sess, optimizer, 10)** for epoch in range(training\_epochs): avg\_cost = 0 total\_batch = int(mnist.train.num\_examples / batch\_size) for i in range(total\_batch): batch\_xs, batch\_ys = mnist.train.next\_batch(batch\_size) \_, c = sess.run([train\_op, cost], feed\_dict={x: batch\_xs, y: batch\_ys}) avg\_cost += c / total\_batch

The preceding API involves graph modification. If a graph cannot be modified (for example, the graph is frozen or a session is created using **tf.train.Supervisor**), you cannot use the **set\_iteration\_per\_loop** API to set the loops and iterations per loop. In this case, use the **create\_iteration\_per\_loop\_var** and **load\_iteration\_per\_loop\_var** APIs to set the number of iterations per loop. The following is an example.

from \_\_future\_\_ import print\_function

import input\_data

from npu\_bridge.npu\_init import \*

mnist = input\_data.read\_data\_sets("/test/", one\_hot=True)

import tensorflow as tf

# Set the model. # Set the learning rate. learning\_rate = 0.01

# Set the number of training iterations.

training\_epochs = 10 # Set the batch size. batch\_size = 100

# Set the number of iterations after which the loss is displayed once.

display\_step = 1

x = tf.placeholder(tf.float32, [None, 784]) y = tf.placeholder(tf.float32, [None, 10])

# Set the model parameters. W = tf.Variable(tf.zeros([784, 10])) b = tf.Variable(tf.zeros([10]))

# Build the model.

pred = tf.nn.softmax(tf.matmul(x, W) + b) # Define the loss function: cross entropy.

cost = tf.reduce\_mean(-tf.reduce\_sum(y\*tf.log(pred), reduction\_indices=1))

<span id="page-40-0"></span># Initialize all variables. init = tf.global\_variables\_initializer() config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True # Perform training on the Ascend AI Processor. custom\_op.parameter\_map["mix\_compile\_mode"].b = False # Disable the mixed computing function. Set this parameter as required. This function is disabled by default. **custom\_op.parameter\_map["iterations\_per\_loop"].i = 10** # Used for functional validation. Must be equal to **iterations\_per\_loop** of **set\_iteration\_per\_loop**. config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. # Train the model. with tf.Session(config=config) as sess: sess.run(init)  **# Set the number of iterations per loop to 10 in sess.run mode. iteration = util.IterationPerLoop() train\_op = iteration.create\_iteration\_per\_loop\_var(optimizer) # Modify the graph. tf.train.Supervisor(logdir="/home/xxxx",init\_op=init) # Freeze the graph. iteration.load\_iteration\_per\_loop\_var(sess, 10) # Set the number of iterations per loop.** for epoch in range(training\_epochs): avg\_cost = 0 total\_batch = int(mnist.train.num\_examples / batch\_size) for i in range(total\_batch): batch\_xs, batch\_ys = mnist.train.next\_batch(batch\_size) \_, c = sess.run([train\_op, cost], feed\_dict={x: batch\_xs, y: batch\_ys}) avg\_cost += c / total\_batch

#### **Setting iterations\_per\_loop with Keras**

On the Ascend platform, you can directly use the native Keras API for training. However, the number of iterations per training loop on Ascend AI Processor is fixed at 1 in each **sess.run()** call. To reduce the number of interactions between the host and devices and shorten the training duration, you need to use the **model\_to\_npu\_estimator** API to convert the model constructed by using Keras into an **NPUEstimator** object. Besides, you need to specify the number of iterations per training loop on Ascend AI Processor per **sess.run()** call by using the **iterations\_per\_loop** parameter in **NPURunConfig**.

Original TensorFlow code:

from keras.layers import Input, Dense from keras.models import Model

# This returns a tensor inputs = Input(shape=(224, 224, 3)) # This creates a model that includes # the Input layer and three Dense layers keras\_model = ResNet50(input\_tensor=inputs, weights=None, include\_top=True) keras\_model.compile(optimizer='rmsprop', loss='sparse\_categorical\_crossentropy') keras\_model.fit\_generator( train\_generator, steps\_per\_epoch=100, epochs=10)

Code after migration:

run\_config = NPURunConfig(save\_checkpoints\_steps=2, model\_dir=model\_path, iterations\_per\_loop=10) # Convert the model constructed by using **Keras** to an **NPUEstimator** object. est\_resnet = keras\_to\_npu.model\_to\_npu\_estimator(keras\_model=keras\_model, config=run\_config) # Perform training. est\_resnet.train(input\_fn=lambda: input\_fn(), max\_steps=1000)

In addition, you need to migrate the Keras data preprocessing part to **input\_fn** in **NPUEstimator**. The following is an example. In the following example, Keras reads image data from the folder, automatically labels the data, performs data augmentation operations such as data resize, normalization, and horizontal flip, and finally outputs the data. In **Estimator** mode, data is preprocessed in the same way as reading data from the file list. The difference is that the file name list needs to be read in advance and each image needs to be labeled to output the label list. The data is output after the same data enhancement operations such as normalization, resize, and horizontal flip.

#### Original TensorFlow code:

# Keras reads images from the folder. train\_datagen = ImageDataGenerator(rescale=1./255, horizontal\_flip=True) train\_generator = train\_datagen.flow\_from\_directory('data/', target\_size=(224, 224, 3), batch\_size=32, class\_mode='sparse')

#### Code after migration:

# The function is used to read the image files corresponding to the file names and resize the image files to a unified size.

 def \_parse\_function(filename, label): image = tf.read\_file(filename) image = tf.image.decode\_image(image) image = image / 255.0 image = tf.image.resize\_images(image, [224, 224, 3]) image = tf.image.random\_flip\_left\_right(image) return image, label

#### def input\_fn():

 # List of image files. The image list needs to be generated by yourself. filenames = tf.constant(["/data/image1.jpg", "/data/image2.jpg", ...]) # label[i] is the label of the filenames[i] image. The label list needs to be generated by yourself. labels = tf.constant([0, 5, ...]) # Now an element in the dataset is (filename, label). dataset = tf.data.Dataset.from\_tensor\_slices((filenames, labels)).repeat(10) # Now an element in the dataset is (image\_resized, label). dataset = dataset.map(\_parse\_function) # Now an element in the dataset is (image\_resized\_batch, label\_batch). dataset = dataset.shuffle().batch(32) return dataset

Note that the callback function of Keras cannot be used after being converted to an **NPUEstimator** object.

## **Checking Whether iterations\_per\_loop Takes Effect**

Search for "Insert op success" in the host log file to check whether **iterations\_per\_loop** takes effect, as shown in **[Figure 6-1](#page-42-0)**.

<span id="page-42-0"></span>**Figure 6-1** Log message (**iterations\_per\_loop > 1**)

**Figure 6-2** shows the related log information when the value of **iterations\_per\_loop** is set to **1** (the default value).

**Figure 6-2** Log message (**iterations\_per\_loop = 1**)

# **6.2.2 Data Preprocessing Performance**

#### **Balancing the Schedule of Data Preprocessing Operators**

When the data volume is large, the data preprocessing performance might not meet the compute performance requirements of the downstream layers in a graph. In this case, you can balance the data preprocessing operators executed on the host and device sides to improve the training performance.

Determine whether a data preprocessing operator is scheduled to the device side as follows: From the bottom up, schedule the preprocessing operators that can run on the device side to the device side, stop at the operator that cannot run on the device side, and schedule the downstream preprocessing operators to the host.

Currently, the following data preprocessing operators can run on the device side: map, batch, and map\_and\_batch. Other preprocessing operators run only on the host.

The following is a schedule example.

Original TensorFlow code:

TFRecordDataset and shuffle cannot run on the device side. Therefore, only the map and batch operators are scheduled on the device side.

train\_dataset=tf.contrib.data.TFRecordDataset("./train\_new.tfrecords") train\_dataset=train\_dataset.shuffle(1000) train\_dataset=train\_dataset.map(parse\_tf) train\_dataset=train\_dataset.batch(batch\_size)

Code after migration:

train\_dataset=tf.contrib.data.TFRecordDataset("./train\_new.tfrecords") train\_dataset=train\_dataset.shuffle(1000) train\_dataset=train\_dataset.map(parse\_tf) train\_dataset=train\_dataset.batch(batch\_size) train\_dataset = train\_dataset.prefetch(buffer\_size=buffer\_size)

Insert a prefetch operator between the map and batch operators. Since the prefetch operator cannot run on the device side, all its downstream operators are scheduled to the host.

#### <span id="page-43-0"></span>**Binding Training Process to CPU**

In the multi-device scenario, to evenly schedule the host CPU cores and further improve the training performance, you can bind each training process to specify host CPU cores. The following uses the 8-device scenario as an example.

- 1. Query the total number of host CPU cores, for example, **96**.

- 2. Calculate the number (n) of host CPU cores allocated to each training process.

n = Total CPU cores/8 = 12

3. Modify the training process startup script. Before starting the training script, use **taskset -c** to bind the processes to the specified host CPU cores, for

example: Device 0:

taskset -c 0-11 python3.7 /home/test/xxx.py /

Device 7:

taskset -c 84-95 python3.7 /home/test/xxx.py /

#### NO TE

For details about the startup script, see **[5.2 Training in a Bare Metal Environment](#page-31-0)**.

# **6.2.3 Distributed Performance Improvement**

#### **Background**

In the distributed training scenario, gradient aggregation is performed after gradients between devices are calculated. Gradient data is generated in order and does not change after being generated. To improve training performance, the gradient parameter data may be segmented. Gradient aggregation may be immediately started after gradient data of a segment is generated, so that some gradient parameter data is aggregated and forward and backward time is executed in parallel.

The default segmentation policy is two segments with the first taking up 96.54% of the data volume, and the second segment taking up 3.46% of the data volume (In some cases, the data is not segmented). This segmentation policy may not be applicable to other networks due to the data volume and calculation time differences of different network gradients. You can adjust the distributed gradient segmentation policy by referring to this section to improve the training performance in distributed scenarios.

### **Determining Gradient Segmentation Policy**

You need to use the Profiling tool to analyze the iteration traces of the training process to determine the gradient segmentation policy and improve the training performance in distributed scenarios.

#### NO TE

For details, see Profiling Tool Instructions.

Iteration tracing is to trace the software status of a training job and the Ascend AI Software Stack, which can be used to analyze the performance of a training job. If the default two-segment gradient segmentation policy is applied, the following iteration traces of a training job are printed to describe the job execution status in an iteration: **fp\_start**, **bp\_end**, **allreduce1\_start**, **allreduce1\_end**, **allreduce2\_start**, **allreduce2\_end**, and **Iteration\_end** in the training job.

An optimal gradient data segmentation policy meets the following rules:

- AR1 is hidden within the BPFP period.
- The time of the AR2 is as short as possible, so as to reduce the hangover caused by collective communication after the computation.

Based on the preceding segmentation rules, you can adjust the gradient segmentation policy to improve the training performance in distributed scenarios. The following uses two-segment gradient segmentation as an example to describe how to determine a gradient segmentation policy with three optimization scenarios.

[Optimization Scenario 1] Since AR1 starts earlier than AR2, the segmentation point can be moved backward to shorten the time of AR2.

For example, if the two gradient segments have the same data volume, the segmentation diagram is as follows.

If the data volume of the first gradient segment is increased to 80%, the segmentation diagram is as follows.

[Optimization Scenario 2] Since AR1 starts later than BPFP, the segmentation point can be moved forward to hide AR1 between BPFPs.

If the data volume of the first gradient segment is 90%, the segmentation diagram is as follows.

If the data volume of the first gradient segment is decreased to 80%, the segmentation diagram is as follows.

[Optimization Scenario 3] Since the BPFP data volume is large and the computation time is long, in the two-segment gradient segmentation scenario, if most gradient data is stored in AR1, the hangover time is long. For details, see optimization scenario 2. If most gradient data is stored in AR2, AR2 will take a long time. For details, see optimization scenario 1. However, there is still a relatively large amount of time in the BPFP to be utilized. In this case, more segmentation points may be added, to improve the parallelism.

#### **Adjusting Gradient Segmentation Policy**

You can call the gradient segmentation API in the training script to set the AllReduce segmentation and fusion policy in the backward propagation phase.

**set\_split\_strategy\_by\_idx**: sets the backward gradient segmentation policy in the collective communication group based on the gradient index ID.

from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_idx([20, 100, 159])

**set\_split\_strategy\_by\_size**: sets the backward gradient segmentation policy in the collective communication group by ratio.

from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_size([60, 20, 20])

Call either of the preceding APIs before the **allreduce** call (the collective communication API must be initialized). For example:

import tensorflow as tf from npu\_bridge.npu\_init import \*

npu\_init = npu\_ops.initialize\_system()

npu\_shutdown = npu\_ops.shutdown\_system() config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True **# Enable profiling iterative trace collection. custom\_op.parameter\_map["profiling\_mode"].b = True custom\_op.parameter\_map["profiling\_options"].s = tf.compat.as\_bytes("training\_trace")**  config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. with tf.Session(config=config) as sess: sess.run(npu\_init) **# Set the backward gradient segmentation policy. set\_split\_strategy\_by\_size([80, 20])** # Perform AllReduce. # Perform training... sess.run(npu\_shutdown)

# **6.2.4 Mixed Precision**

#### **Overview**

Mixed precision is the combined use of the float16 and float32 data types in training deep neural networks, which reduces memory usage and access

<span id="page-46-0"></span>

frequency. Mixed-precision training makes it easier to deploy larger networks without compromising the network accuracy with float32. Currently, Ascend AI Processor supports the following training precision modes. Choose one as needed in the training script.

- **allow\_fp32\_to\_fp16**: The original precision is preferentially retained. If an operator does not support the float32 data type, the float16 precision is used. Currently, the float32 type is not supported by convolution operators, such as Conv2D and DepthwiseConv2D. These operators are precision-insensitive and do not reduce the accuracy of the entire network. This is the default precision mode.
- **force\_fp16**: If an operator supports both float16 and float32 data types, float16 is forcibly selected.
- **must\_keep\_origin\_dtype**: The original precision is retained. In this mode, if a network contains a Conv2D operator, the training process will be interrupted when the input type of the original graph is float32 because the operator supports only the float16 type.
- **allow\_mix\_precision**: Mixed precision is allowed. For operators of the float32 data type on a network, the precision of some float32 operators can be automatically reduced to float16 based on the built-in optimization policy. In this way, the system performance is improved and the memory usage is reduced with little accuracy loss. Note that Ascend AI Processor supports only float32 to float16 casting. It is recommended that **[6.2.5 Loss Scaling](#page-48-0)** be enabled after mixed precision is enabled to compensate for the accuracy loss caused by precision reduction. If the original script has implemented the manual mixed precision, for example, the cast operator has been explicitly called to convert the compute precision, mixed precision of Ascend AI Processor does not need to be enabled.

#### **Setting the Precision Mode with Estimator**

In **Estimator** mode, the precision mode is specified by **precision\_mode** in **NPURunConfig**.

from npu\_bridge.npu\_init import \*

npu\_config=NPURunConfig( model\_dir=FLAGS.model\_dir, save\_checkpoints\_steps=FLAGS.save\_checkpoints\_steps, session\_config=tf.ConfigProto(allow\_soft\_placement=True,log\_device\_placement=False), **precision\_mode="allow\_mix\_precision"** )

If **allow\_mix\_precision** is enabled, you can make adjustment based on the built-in optimization policy to specify the operators whose precision can be reduced and the operators whose precision cannot.

- 1. Go to **/opp/op\_impl/built-in/ai\_core/tbe/config/ascend910** in the OPP installation path.
- 2. Grant the write permission on the **aic-ascend910-ops-info.json** file. chmod u+w aic-ascend910-ops-info.json
- 3. Modify or add the **precision\_reduce** field of the corresponding operator in the **aic-ascend910-ops-info.json** file of the operator information library. "precision\_reduce":{ "flag":"true" }

- <span id="page-48-0"></span>– **true**: allows precision reduction to float16 for operators supporting float32.
- **false**: forbids precision reduction to float16 for operators supporting float32.
- If not specified, the current operator uses the same mixed precision processing as the upstream operator.

#### **Setting Precision Mode with sess.run()**

In **sess.run()** mode, set the precision mode by using the session configuration option **precision\_mode**.

import tensorflow as tf

from npu\_bridge.npu\_init import \*

config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer"

custom\_op.parameter\_map["use\_off\_line"].b = True

**custom\_op.parameter\_map["precision\_mode"].s = tf.compat.as\_bytes("allow\_mix\_precision")** config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

with tf.Session(config=config) as sess:

print(sess.run(cost))

The parameter description and configuration method are the same as those in the **Estimator** method.

# **6.2.5 Loss Scaling**

#### **Overview**

Loss scaling is used to solve the underflow problem that occurs during the gradient calculation due to the small representation range of float16. The loss calculated in the forward pass is multiplied by the loss scale S to amplify the gradient during the backward gradient calculation. In the mixed precision training scenario on some networks, the loss scaling function needs to be enabled. Otherwise, the loss does not converge.

#### **Using Loss Scaling**

If the loss scaling function is used on the original network, you need to migrate **LossScaleOptimizer** to the **NPULossScaleOptimizer** or **NPUOptimizer** constructor. The following uses **NPULossScaleOptimizer** as an example.

- Static loss scaling: You can use a fixed loss scaling factor during mixed precision training. When using the static loss scaling, before creating **NPULossScaleOptimizer**, instantiate a **FixedLossScaleManager** class to specify the loss scaling factor.
- Dynamic loss scaling: You can adjust the loss scaling factor based on the abnormal status of floating-point computation during mixed precision training.

When using the dynamic loss scaling, before creating

**NPULossScaleOptimizer**, instantiate a **ExponentialUpdateLossScaleManager** class to dynamically specify the loss scaling factor.

In addition, when **NPULossScaleOptimizer** is used, set **is\_distributed** to **True** to support the loss scaling function in the distributed training scenario.

#### Original TensorFlow code:

if FLAGS.use\_fp16 and (FLAGS.bert\_loss\_scale not in [None, -1]): opt\_tmp = opt if FLAGS.bert\_loss\_scale == 0: loss\_scale\_manager = tf.contrib.mixed\_precision.ExponentialUpdateLossScaleManager(init\_loss\_scale=2\*\*32, incr\_every\_n\_steps=1000, decr\_every\_n\_nan\_or\_inf=2, decr\_ratio=0.5) elif FLAGS.bert\_loss\_scale >= 1: loss\_scale\_manager = tf.contrib.mixed\_precision.FixedLossScaleManager(loss\_scale=FLAGS.bert\_loss\_scale) else: raise ValueError("Invalid loss scale: %d" % FLAGS.bert\_loss\_scale) opt = tf.contrib.mixed\_precision.LossScaleOptimizer(opt\_tmp, loss\_scale\_manager)

#### Code after migration:

from npu\_bridge.npu\_init import \* if FLAGS.use\_fp16 and (FLAGS.bert\_loss\_scale not in [None, -1]): opt\_tmp = opt if FLAGS.bert\_loss\_scale == 0: loss\_scale\_manager = **ExponentialUpdateLossScaleManager**(init\_loss\_scale=2\*\*32, incr\_every\_n\_steps=1000, decr\_every\_n\_nan\_or\_inf=2, decr\_ratio=0.5) elif FLAGS.bert\_loss\_scale >= 1: loss\_scale\_manager = **FixedLossScaleManager**(loss\_scale=FLAGS.bert\_loss\_scale) else: raise ValueError("Invalid loss scale: %d" % FLAGS.bert\_loss\_scale) # Check whether the number of devices is greater than 1. If yes, perform distributed training. if ops\_adapter.size() > 1: **opt\_tmp = NPUDistributedOptimizer(opt\_tmp)** opt = **NPULossScaleOptimizer**(opt\_tmp, loss\_scale\_manager, is\_distributed=True) else: opt = **NPULossScaleOptimizer**(opt\_tmp, loss\_scale\_manager)

#### **Updating the Global Step**

After the loss scaling function is enabled, the step where the loss scaling overflow occurs needs to be discarded. For details, see the update step logic of the optimizer.

- In most cases, for example, **tf.train.MomentumOptimizer** used on the ResNet-50HC network updates the global step in **apply\_gradients**, the step does not need to be updated when overflow occurs. Therefore, the script does not need to be modified.
- However, for the BERT network, the global step update is implemented in **create\_optimizer**, including the judgment logic. In this case, the global step update needs to be performed in the optimizer. The following is a migration example:

In the original TensorFlow code, the global step is updated in **create\_optimizer**, including the judgment logic.

def create\_optimizer(loss, init\_lr, num\_train\_steps, num\_warmup\_steps, hvd=None, manual\_fp16=False, use\_fp16=False, num\_accumulation\_steps=1, optimizer\_type="adam", allreduce\_post\_accumulation=False): ... if tf.flags.FLAGS.npu\_bert\_clip\_by\_global\_norm: new\_global\_step = tf.cond(all\_are\_finite, lambda: global\_step + 1, lambda: global\_step) else: new\_global\_step = global\_step + 1

<span id="page-50-0"></span> new\_global\_step = tf.identity(new\_global\_step, name='step\_update') train\_op = tf.group(train\_op, [global\_step.assign(new\_global\_step)]) return train\_op

During the migration to the Ascend platform, you need to update the global step in the optimizer as follows:

- 1. Comment out the global step update logic implemented in **create\_optimizer** in the script. def create\_optimizer(loss, init\_lr, num\_train\_steps, num\_warmup\_steps, hvd=None, manual\_fp16=False, use\_fp16=False, num\_accumulation\_steps=1, optimizer\_type="adam", allreduce\_post\_accumulation=False): ...  **#if tf.flags.FLAGS.npu\_bert\_clip\_by\_global\_norm: # new\_global\_step = tf.cond(all\_are\_finite, lambda: global\_step + 1, lambda: global\_step) #else: # new\_global\_step = global\_step + 1 #new\_global\_step = tf.identity(new\_global\_step, name='step\_update') #train\_op = tf.group(train\_op, [global\_step.assign(new\_global\_step)])** return train\_op
- 2. Before the last return statement of the **apply\_gradients** function, add the logic for updating the global step in the **AdamWeightDecayOptimizer** and **LAMBOptimizer** classes, respectively. The **apply\_gradients** function is called only when overflow is not found in the status check during loss scaling. def apply\_gradients(self, grads\_and\_vars, global\_step=None, name=None, manual\_fp16=False): assignments = [] for (grad, param) in grads\_and\_vars: ...  **new\_global\_step = global\_step + 1 new\_global\_step = tf.identity(new\_global\_step, name='step\_update')** assignments.extend([global\_step.assign(new\_global\_step)]) return tf.group(\*assignments, name=name)

# **6.3 Operator Performance Optimization**

# **6.3.1 Automatic Operator Tuning**

If the network performance is still not satisfactory after the network-wide performance optimization actions are taken, you can look into tuning particular operators by identifying the optimal tiling policies with the Auto Tune tool. For details, see "**[Auto Tune Tool Instructions](https://support.huawei.com/enterprise/en/doc/EDOC1100180775/96627bed)**" in CANN Auxiliary Development Tool Guide.

# **6.3.2 Operator Profiling**

#### **Overview**

The Profiling tool provides an economical solution for achieving optimal performance by accurate location of bottlenecks in software and hardware, efficient analysis, and specific optimization in the training process. Currently, the following options can be traced in profiling.

- **training\_trace**: iteration tracing. Collects software profile data of a training job and the AI Software Stack to profile the training job. Focuses on data augmentation, forward and backward propagation, and gradient aggregation and update.

- **task\_trace**: task tracing. Collects the HWTS and AI Core hardware information of the Ascend AI Processor and the start and end of each task.
- **aicpu**: AI CPU data augmentation tracing. If you need to enable profile data collection during training (disabled by default),

modify the training script as follows.

#### **Collecting Profile Data with Estimator**

from npu\_bridge.npu\_init import \*

**profiling\_options = '{"output":"/tmp/profiling","training\_trace":"on","fp\_point":"resnet\_model/ conv2d/Conv2Dresnet\_model/batch\_normalization/ FusedBatchNormV3\_Reduce","bp\_point":"gradients/AddN\_70"}' profiling\_config = ProfilingConfig(enable\_profiling=True, profiling\_options = profiling\_options)** session\_config=tf.ConfigProto()

config = NPURunConfig(**profiling\_config=profiling\_config**, session\_config=session\_config)

#### **Collecting Profile Data with sess.run()**

In **sess.run()** mode, use **profiling\_mode** and **profiling\_options**, session configuration options, to enable tracing. custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True **custom\_op.parameter\_map["profiling\_mode"].b = True custom\_op.parameter\_map["profiling\_options"].s = tf.compat.as\_bytes('{"output":"/tmp/ profiling","training\_trace":"on","fp\_point":"resnet\_model/conv2d/Conv2Dresnet\_model/ batch\_normalization/FusedBatchNormV3\_Reduce","bp\_point":"gradients/AddN\_70"}')** config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. with tf.Session(config=config) as sess: sess.run()

#### **Collecting Profile Data with Environment Variables**

In addition to the preceding two methods, you can modify the environment variables in the startup script to enable profile data collection.

export PROFILING\_MODE=true export PROFILING\_OPTIONS='{"output":"/tmp/ profiling","training\_trace":"on","task\_trace":"on","aicpu":"on","fp\_point":"resnet\_model/conv2d/ Conv2Dresnet\_model/batch\_normalization/FusedBatchNormV3\_Reduce","bp\_point":"gradients/ AddN\_70","aic\_metrics":"PipeUtilization"}'

For details about environment variable configuration, see **[Environment Variable](#page-197-0) [Configuration](#page-197-0)**.

#### **Analyzing Profile Data**

After the training is complete, find the collected profile data in the **output** directory. You can use the Profiling tool to analyze the profile data by referring to "**[Profiling Tool Instructions](https://support.huawei.com/enterprise/en/doc/EDOC1100180775/786c1ac1)**" in CANN Auxiliary Development Tool Guide.

If the profiling reports show that it is the custom operators on the network that lower the network performance, you can tune the custom operators by referring to the "Performance Optimization" sections in **[TBE Custom Operator](https://support.huawei.com/enterprise/en/doc/EDOC1100180769?idPath=23710424%7C251366513%7C22892968%7C251168373) [Development Guide](https://support.huawei.com/enterprise/en/doc/EDOC1100180769?idPath=23710424%7C251366513%7C22892968%7C251168373)**.

# **7 Accuracy Tuning**

<span id="page-52-0"></span>7.1 Operator Accuracy Analysis [7.2 Operator Overflow Detection](#page-54-0)

# **7.1 Operator Accuracy Analysis**

#### **Overview**

During the training process, the compute result (dump data) of each operator can be collected and then compared with that of the counterpart standard operator (such as TensorFlow) to facilitate accuracy tuning. Currently, the following options can be dumped.

- **input**: dumps operator inputs only.
- **output**: dumps operator outputs only.
- **all**: dumps both the operator inputs and outputs.

If you need to enable dumping during training (disabled by default), modify the training script as follows.

#### **Precautions**

- Currently, all iterations can be dumped. You can specify the iterations to be dumped. If the training dataset is large, the dump data volume of each iteration can reach about dozens of GB or even more. You are advised to control the number of iterations.
- Data dump and overflow detection are mutually exclusive.
- Currently, only dump data of AI Core and AI CPU operators can be collected. Dump data of collective communication operators cannot be collected.

#### **Dumping with Estimator**

In **Estimator** mode, use **dump\_config** in **NPURunConfig** to collect dump data. Before **NPURunConfig is created**, instantiate a **DumpConfig** class for dump configuration, including the dump path, iteration data to be dumped, and whether to dump the inputs or outputs of the operator.

For details, see the **DumpConfig** constructor.

from npu\_bridge.npu\_init import \*

# **dump\_path**: dump path. Create the specified path in advance in the training environment (either in a container or on the host). The running user configured during installation must have the read and write permissions on this path.

# **enable\_dump**: dump enable.

# **dump\_step**: iterations to dump.

# **dump\_mode**: dump mode, which can be set to **input**, **output**, or **all**

**dump\_config = DumpConfig(enable\_dump=True, dump\_path = "/home/HwHiAiUser/output", dump\_step="0|5|10", dump\_mode="all")**

session\_config=tf.ConfigProto()

config = NPURunConfig(  **dump\_config=dump\_config**, session\_config=session\_config )

#### **Dumping with sess.run()**

In **sess.run** mode, set the dump parameters by setting the session configuration items **enable\_dump**, **dump\_path**, **dump\_step**, and **dump\_mode**.

config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True

# **enable\_dump**: dump enable

**custom\_op.parameter\_map["enable\_dump"].b = True**

# **dump\_path**: dump path. Create the specified path in advance in the training environment (either in a container or on the host). The running user configured during installation must have the read and write permissions on this path.

**custom\_op.parameter\_map["dump\_path"].s = tf.compat.as\_bytes("/home/HwHiAiUser/output")** 

# **dump\_step**: iterations to dump **custom\_op.parameter\_map["dump\_step"].s = tf.compat.as\_bytes("0|5|10")**

# **dump\_mode**: dump mode, which can be set to **input**, **output**, or **all custom\_op.parameter\_map["dump\_mode"].s = tf.compat.as\_bytes("all")**

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF

with tf.Session(config=config) as sess: print(sess.run(cost))

#### **Viewing Dump Data**

If data dump data is collected during training, a dump file is generated to the **{dump\_path}/{time}/{deviceid}/{model\_name}/{model\_id}/{data\_index}** directory, for example, **/home/HwHiAiUser/output/20200808163566/0/ ge\_default\_20200808163719\_121/11/0**. In addition, a GE graph file, for example, **ge\_proto\_xxxxx\_Build.txt**, is generated in the same directory of the training script.

The fields in the dump data path and file are described as follows.

- **dump\_path**: dump path, for example, **/home/HwHiAiUser/output**
- **time**: timestamp (for example, **20200317020343**)
- **deviceid**: device ID.
- **model\_name**: subnetwork name. If the **model\_name** directory contains more than one folder, dump data in the folder of the computational graph is used.

<span id="page-54-0"></span>After the training script is executed, one or multiple GE graphs will be generated in the directory of the training script. Take **ge\_proto\_\*\*\*\*\*\_Build.txt** as an example. To select the computational graph, view each GE graph, respectively. The GE graph with the IteratorV2, Iterator, or GetNext operator is the computational graph. Value of the **name** field in the computational graph is used as the name of the computational graph.

- **model\_id**: subnetwork ID.
- **data\_index**: iterations to dump. If **dump\_step** is specified, **data\_index** equals **dump\_step**. If not, **data\_index** is indexed starting at 0 and is incremented by 1 with each dump.
- **dump\_file**: formatted as **{op\_type}.{op\_name}.{taskid}.{timestamp}**
- Periods (.), forward slashes (/), backslashes (\), and spaces in **model\_name**, **op\_type** or **op\_name** are replaced by underscores (\_).

#### NO TE

- In the multi-device training scenario where more than one Ascend AI Processor is used, since the processes are not started at the same time as defined in the training script, multiple timestamp directories are generated when data is dumped.
- When the command is executed in a Docker, the generated data is stored in the Docker.

#### **Analyzing Dump Data**

You can use the Model Accuracy Analyzer to analyze the dump data by referring to "**[Model Accuracy Analyzer Instructions](https://support.huawei.com/enterprise/en/doc/EDOC1100180775/71da56ea)**" in CANN Auxiliary Development Tool Guide.

#### **More Cases**

- **[Accuracy Troubleshooting in Ascend 910 ALBERT-Base Migration](https://bbs.huaweicloud.com/forum/forum.php?mod=viewthread&tid=89908)**
- **[\[Ascend Model Training Challenge\] Ascend 910 BERT-Base Migration](https://bbs.huaweicloud.com/forum/thread-79873-1-1.html) [Workflow](https://bbs.huaweicloud.com/forum/thread-79873-1-1.html)**
- **[What Do I Do If Loss Converges but Accuracy Drops?](https://gitee.com/ascend/modelzoo/wikis/Loss%E6%98%AF%E6%94%B6%E6%95%9B%E4%BA%86%EF%BC%8C%E7%B2%BE%E5%BA%A6%E4%B8%8D%E5%A4%9F%E6%80%8E%E4%B9%88%E5%8A%9E%EF%BC%9F?sort_id=3148793)**
- **[PWCNet Accuracy Tuning](https://gitee.com/ascend/modelzoo/wikis/PWCNet%E7%B2%BE%E5%BA%A6%E8%B0%83%E4%BC%98?sort_id=3384954)**
- **[InceptionV4 Training Accuracy Tuning](https://gitee.com/ascend/modelzoo/wikis/InceptionV4%E8%AE%AD%E7%BB%83%E7%B2%BE%E5%BA%A6%E8%B0%83%E4%BC%98%E6%96%B9%E6%B3%95?sort_id=3154910)**

If the analysis reports show that it is the custom operators on the network that lower the network accuracy, you can tune the custom operators by referring to the section "Special Topics > Accuracy Optimization in DSL Mode" in **[TBE Custom](https://support.huawei.com/enterprise/en/doc/EDOC1100180769?idPath=23710424%7C251366513%7C22892968%7C251168373) [Operator Development Guide](https://support.huawei.com/enterprise/en/doc/EDOC1100180769?idPath=23710424%7C251366513%7C22892968%7C251168373)**.

# **7.2 Operator Overflow Detection**

#### **Overview**

For deep networks, a large amount of data will be dumped during operator accuracy analysis. In addition, due to the randomness of the network, it is difficult to locate the operators with accuracy drop compared with third-party equivalents. In this case, you can choose to enable the overflow detection function. Currently, the following three overflow detection modes are provided:

- **aicore\_overflow**: detects AI Core operator overflow. With normal inputs, abnormal extreme outputs (such as float16 65500, 38400, and 51200) are detected. Once such fault is detected, analyze the cause of the overflow and modify the operator implementation based on the network requirements and operator logic.
- **atomic\_overflow**: detects Atomic Add overflow. Atomic Add overflows are detected when data is moved from the UB to the external storage after AI Core computation.
- **all**: detects both AI Core operator overflow and Atomic Add overflow.

With the overflow detection result, you can locate the error operator, dump data of the particular operator, and analyze the dump data to solve the accuracy drop issue.

#### **Precautions**

- The data dump and overflow detection functions are mutually exclusive.
- The overflow detection function or data dump function might cause disk space insufficiency due to the generation of result files. You are advised to limit the number of iterations appropriately.

#### **Detecting Overflow with Estimator**

In **Estimator** mode, use **dump\_config** in **NPURunConfig** to set the overflow detection mode. Before creating **NPURunConfig**, create instance of class **DumpConfig**. For details, see the **DumpConfig** constructor.

from npu\_bridge.npu\_init import \*

# **dump\_path**: dump path. Create the specified path in advance in the training environment (either in a container or on the host). The running user configured during installation must have the read and write permissions on this path.

# **enable\_dump\_debug**: overflow detection enable.

# **dump\_debug\_mode**: overflow detection mode select, which can be **all**, **aicore\_overflow**, or **atomic\_overflow**

**dump\_config = DumpConfig(enable\_dump\_debug = True, dump\_path = "/home/HwHiAiUser/output", dump\_debug\_mode = "all" )** session\_config=tf.ConfigProto()

config = NPURunConfig(**dump\_config=dump\_config**, session\_config=session\_config)

#### **Detecting Overflow with sess.run()**

In **sess.run** mode, set the overflow detection mode by setting the session configuration options **dump\_path**, **enable\_dump\_debug**, and **dump\_debug\_mode**.

config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True

# **dump\_path**: dump path. Create the specified path in advance in the training environment (either in a container or on the host). The running user configured during installation must have the read and write permissions on this path.

**custom\_op.parameter\_map["dump\_path"].s = tf.compat.as\_bytes("/home/HwHiAiUser/output")**  # **enable\_dump\_debug**: overflow detection enable.

**custom\_op.parameter\_map["enable\_dump\_debug"].b = True**

# **dump\_debug\_mode**: overflow detection mode select, which can be **all**, **aicore\_overflow**, or **atomic\_overflow custom\_op.parameter\_map["dump\_debug\_mode"].s = tf.compat.as\_bytes("all")** config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

with tf.Session(config=config) as sess: print(sess.run(cost))

#### **Viewing Overflow Data**

If overflow data is collected during training, an overflow data file is generated to the **{dump\_path}/{time}/{deviceid}/{model\_id}/{data\_index}** directory, for example, **/home/HwHiAiUser/output/20200808163566/0/11/0**.

If no overflow data is collected during training, that is, no overflow occurs, the preceding directory is not generated.

The fields in the dump data path and file are described as follows.

- **dump\_path**: user-defined path for storing overflow data, for example, **/ home/HwHiAiUser/output**.
- **time**: timestamp (for example, **20200808163566**)
- **deviceid**: device ID.
- **model\_id**: subnetwork ID.
- **data\_index**: iterations to detect overflow.
- Dump files:
  - The dump file of an overflow operator is named as: **{op\_type}. {op\_name}.{taskid}.{timestamp}**. Any period (.), slash (/), backslash (\), or space in the **op\_type** or **op\_name** field is replaced by an underscore (\_). You can identify an overflow operator based on its dump file name. To view the inputs and outputs of an overflow operator, refer to **Analyzing the Dump File of an Overflow Operator**.
  - The operator overflow data file is named as: **OpDebug.Node\_Opdebug. {taskid}.{timestamp}**, where **taskid** is not the task ID of the overflow operator and can be ignored. To locate the overflow cause, follow the instructions in **[Analyzing an](#page-57-0) [Operator Overflow Data File](#page-57-0)**. NO TE
    - In the multi-device training scenario where more than one Ascend AI Processor is used, since the processes are not started at the same time as defined in the training script, multiple timestamp directories are generated when data is dumped.
    - When the command is executed in a Docker, the generated data is stored in the Docker.

### **Analyzing the Dump File of an Overflow Operator**

**Step 1** Upload the **{op\_type}.{op\_name}.{taskid}.{timestamp}** file to the environment installed with Toolkit.

<span id="page-57-0"></span>**Step 2** Go to the directory where the parse script is stored. The following assumes that the Toolkit installation path is **/home/HwHiAiUser/Ascend/ascend-toolkit/latest**.

**cd /home/HwHiAiUser/Ascend/ascend-toolkit/latest/toolkit/tools/ operator\_cmp/compare**

**Step 3** Run the **msaccucmp.pyc** script to convert the dump file into a NumPy file. The following is an example:

**python3.7.5 msaccucmp.pyc convert -d /home/HwHiAiUser/dump -out /home/ HwHiAiUser/dumptonumpy -v 2**

NO TE

The **-d** option enables the conversion of a single dump file or all dump files in a path.

**Step 4** Use Python to save the NumPy data into a text file. The following is an example:

#### **\$ python3.7.5**

**>>> import numpy as np**

**>>> a = np.load("/home/HwHiAiUser/dumptonumpy/ Pooling.pool1.1147.1589195081588018.output.0.npy")**

**>>> b = a.flatten()**

**>>> np.savetxt("/home/HwHiAiUser/dumptonumpy/ Pooling.pool1.1147.1589195081588018.output.0.txt", b)**

The dimension and **Dtype** information no longer exist in the .txt file. For details, visit the NumPy website.

**----End**

#### **Analyzing an Operator Overflow Data File**

Since the generated overflow data is in binary format, you need to interpret the binary file into a readable format, such as JSON.

**Step 1** Upload the overflow data file **OpDebug.Node\_Opdebug.{taskid}.{timestamp}** to the Toolkit installation environment.

NO TE

You are advised to go to the **data\_index** directory with the minimum value, and use the dump file with the minimum **timestamp** for analysis.

**Step 2** Go to the directory where the parse script is stored. The following assumes that the Toolkit installation path is **/home/HwHiAiUser/Ascend/ascend-toolkit/latest**.

**cd /home/HwHiAiUser/Ascend/ascend-toolkit/latest/toolkit/tools/ operator\_cmp/compare**

**Step 3** Run the parse command:

**python3.7.5 msaccucmp.pyc convert -d /home/HwHiAiUser/opdebug/ Opdebug.Node\_OpDebug.59.1597922031178434 -out /home/HwHiAiUser/ result**

- **-d**: directory of the overflow data, including the file name
- **-out**: directory of the parsing result. If not specified, the current directory is used.

#### **Step 4** Find the **result.txt** parse result as follows.

{ "DHA Atomic Add": { "model\_id": 0, "stream\_id": 0, "task\_id": 0, "task\_type": 0, "pc\_start": "0x0", "para\_base": "0x0", "status": 0 }, "L2 Atomic Add": { "model\_id": 0, "stream\_id": 0, "task\_id": 0, "task\_type": 0, "pc\_start": "0x0", "para\_base": "0x0", "status": 0 }, "AI Core": { "model\_id": 514, "stream\_id": 563, "task\_id": 57, "task\_type": 0, "pc\_start": "0x1008005b0000", "para\_base": "0x100800297000", "kernel\_code": "0x1008005ae000", "block\_idx": 1, "status": 32 } }

#### NO TE

If both AI Core operator overflow detection and Atomic Add overflow detection are enabled, only the earliest overflow record is displayed.

In the preceding example, the earliest overflow record is an AI Core operator overflow.

The fields are described as follows:

- **model\_id**: ID of the model where the overflow operator is located.
- **stream\_id**: ID of the stream where the overflow operator is located.
- **task\_id**: task ID of the overflow operator.
- **task\_type**: task type of the overflow operator.
- **pc\_start**: start of the code program of the overflow operator.
- **para\_base**: parameter start address of the overflow operator.
- **kernel\_code**: start of the code program of the overflow operator, which is equivalent to **pc\_start**.
- **block\_idx**: block ID of the overflow operator.
- **status**: status of the AI Core status register, including the overflow information. You can analyze the **status** value to identify the specific overflow error.

#### **Status Reference**

- The **status** field that reflects the AI Core operator overflow detection result is in decimal format. You need to convert it into the hexadecimal format before

- locating the fault. For example, assume that the value of **status** is **272**. The hexadecimal equivalent of the value is **0x00000110**. Therefore, the error cause is **0x00000010+0x00000100**.
- **0x00000008**: inversion overflow of the minimum negative sign bit of a signed integer
- **0x00000010**: integer addition, subtraction, multiplication, or multiplication overflow
- **0x00000020**: floating-point computation overflow
- **0x00000080**: negative input for floating-point to unsigned conversion
- **0x00000100**: FP32 to FP16 conversion or 32-bit signed integer to FP16 conversion overflow
- **0x00000400**: Cube accumulation overflow Note: The preceding floating-point errors correspond to the hexadecimal bits, which might lead to combinations of floating-point errors.
- The **status** field that reflects the DHA Atomic Add overflow detection result is in decimal format. You need to convert it into a binary number and convert bits 8 to 15 to a hexadecimal number before locating the fault. For example, assume that the value of status is **2546**. The binary equivalent of the value is **100111110010**. Convert bits 8 to 15 **1001** to obtain the hexadecimal number **0x9**, that is, the error cause.
  - **0x9**: atomic overflow
  - **0xA**: atomic underflow
  - **0xB**: atomic srcnnan (invalid source operand)
  - **0xC**: atomic dstnan (invalid destination operand)
  - **0xD**: atomic bothnan (invalid source operand and destination operand)
- The **status** field that reflects the L2 Atomic Add overflow detection result is in decimal format. You need to convert it into a binary number before locating the fault. For example, assume that the value of **status** is **2546**. The binary equivalent of the value is **100111110010**, and bits 16 to 18 make **000**. It can be determined that no error occurs.
  - **001**: atomic overflow
  - **010**: atomic underflow
  - **011**: atomic srcnnan (invalid source operand)
  - **100**: atomic dstnan (invalid destination operand)
  - **101**: atomic bothnan (invalid source operand and destination operand)

# <span id="page-60-0"></span>**8 Model Conversion and Storage**

#### **Model Conversion and Storage with sess.run()**

During TensorFlow training with **sess.run()**, **saver = tf.train.Saver()** and **saver.save()** are used to save the model. The following files are generated after each **saver.save()** call:

- **checkpoint**: a text file that records the latest checkpoint files and the list of other checkpoint files.
- **model.ckpt.data-00000-of-00001**: saves the current parameter settings.
- **model.ckpt.index**: saves the current parameter names.
- **model.ckpt.meta**: saves the current graph structure.

In this mode, the model weight data and model graph are saved separately. In the inference scenario, the TensorFlow **freeze\_graph** function is used to combine the weight data and model graph into a .pb file, as shown in the dotted box in the following figure.

The workflow of generating a .pb file by using the TensorFlow **freeze\_graph** function is as follows:

- 1. Specify the network model and checkpoint file path.
- 2. Define the input node. For example the input node for training is IteratorV2, but the input required for inference is a placeholder.
- 3. Define the output node. The output node required for training is the loss value, and the outputs required for inference are Argmax or BiasAdd.
- 4. Generally, an operator is processed in different ways in the training graph and inference graph (for example, BatchNorm and dropout operators). Therefore, you need to call the network model to generate an inference graph.
  - For the BatchNorm operator, the mean and variance of the BatchNorm operator are calculated based on the samples. However, during inference, the mean and variance of the BatchNorm operator are calculated based on the moving mean of the samples. Therefore, the mean calculation method of BatchNorm varies with the training or inference scenario.

- For the dropout operator: During inference, dropout needs to be masked by setting **rate** to **1**. if is\_training: x = npu\_ops.dropout(x, 0.65) else: x = npu\_ops.dropout(x, 1.0)

Based on the preceding differences, you need to find the entry function of the inference test logic in the training script and set **is\_training** to **False** during execution to generate an inference graph.

 # Call the network to generate an inference graph. **alexnet.inference** is the entry function of the inference test logic in the training script. logits = alexnet.**inference**(inputs, version="he\_uniform", num\_classes=1000, **is\_training=False**)

- 5. Call **tf.train.writegraph** to save the preceding inference graph to a .pb file as the input of the **freeze\_graph** function.
- 6. Call **freeze\_graph** to merge the .pb graph file generated by **tf.train.writegraph** and the checkpoint file to generate a .pb graph file for inference.

A code sample is provided as follows.

import tensorflow as tf from tensorflow.python.tools import freeze\_graph from npu\_bridge.npu\_init import \* # Import the network model file. import alexnet # Specify the checkpoint path. ckpt\_path = "/opt/npu/model\_ckpt/alexnet/model\_8p/model.ckpt-0" def main(): tf.reset\_default\_graph() # Define the input node of the network. inputs = tf.placeholder(tf.float32, shape=[None, 224, 224, 3], name="input") # Call the network to generate an inference graph. logits = alexnet.inference(inputs, version="he\_uniform", num\_classes=1000, is\_training=False) # Define the output node of the network. predict\_class = tf.argmax(logits, axis=1, output\_type=tf.int32, name="output") with tf.Session() as sess: # Save the graph. The **model.pb** file is generated in the **./pb\_model** folder. # The **model.pb** file will be provided as **input\_graph** to the following **freeze\_graph** call. tf.train.write\_graph(sess.graph\_def, './pb\_model', 'model.pb') # Generate a model file. freeze\_graph.freeze\_graph( input\_graph='./pb\_model/model.pb', # Pass the model file generated using **write\_graph**. input\_saver='', input\_binary=False, input\_checkpoint=ckpt\_path, # Pass the checkpoint file generated in training. output\_node\_names='output', # Consistent with the output node of the inference network. restore\_op\_name='save/restore\_all', filename\_tensor\_name='save/Const:0', output\_graph='./pb\_model/alexnet.pb', # Set to the name of the inference network to be generated. clear\_devices=False, initializer\_nodes='') print("done") if \_\_name\_\_ == '\_\_main\_\_': main()

The following describes the key parameters of **freeze\_graph**. Retain the default values for parameters not described below.

- **input\_graph**: model file generated by **write\_graph**.
- **input\_binary**: used in conjunction with **input\_graph**. If set to **true**, **input\_graph** is binary. If set to **false**, **input\_graph** is a file. Defaults to **false**.

- **input\_checkpoint**: path of the checkpoint file.
- **output\_node\_names**: name of the output node. Use commas (,) to separate multiple names.
- **output\_graph**: path of the converted .pb file.

After the script is executed, the **alexnet.pb** file is generated in the **./pb\_model/** folder. This file is the converted .pb image file used for inference.

#### NO TE

For details about the dependent environment variables, see **[5.2 Training in a Bare Metal](#page-31-0) [Environment](#page-31-0)**.

#### **Model Conversion and Storage with Estimator**

**Estimator** can save models in ckpt and saved\_model formats. The ckpt method is similar to the **sess.run** method. The saved\_model format is recommended because the saved model is lightweight and possible errors are avoided. saved\_model is generally saved using **estimator.export\_savedmodel**, as shown in the following figures.

If you need to convert the saved\_model model into a .pb model for inference, edit the training script as follows:

#### 1. Define the input node.

The input received by **Estimator** during training is in Iterator format, which facilitates iteration between epochs. Before saving a model for inference, use the placeholder to define a specific input.

def serving\_input\_fn(): input\_ids = tf.placeholder(tf.int32, [None, FLAGS.max\_seq\_length], name='input\_ids') input\_fn = tf.estimator.export.build\_raw\_serving\_input\_receiver\_fn({ 'input\_ids': input\_ids, })() return input\_fn

#### 2. Save the saved\_model model.

**Estimator** can directly call the **export\_savemodel** function to save the model and automatically switch the mode and freeze the graph.

if FLAGS.do\_export: estimator.evaluate() estimator.export\_savedmodel(FLAGS.output\_dir, serving\_input\_fn)

#### 3. Freeze the .pb model.

Use the **freeze\_graph** function of TensorFlow to freeze a graph into a .pb model. Note that if your model has an NPU custom operator, you need to import the NPU operator module to the source code of **freeze\_graph**.

import tensorflow as tf from tensorflow.python.tools import freeze\_graph **from npu\_bridge.npu\_init import \*** freeze\_graph.freeze\_graph( input\_saved\_model\_dir='savedModel', output\_node\_names='output', output\_graph='test.pb', initializer\_nodes='', input\_graph= None, input\_saver= False, input\_binary=False, input\_checkpoint=None, restore\_op\_name=None, filename\_tensor\_name=None, clear\_devices=False, input\_meta\_graph=False)

4. Run the **freeze\_npu\_savedModel.py** command to convert and save the

model. python3 freeze\_npu\_savedModel.py --input\_saved\_model\_dir=savedModel - output\_node\_names=loss/Softmax --output\_graph=test.pb

NO TE

For details about the dependent environment variables, see **[5.2 Training in a Bare](#page-31-0) [Metal Environment](#page-31-0)**.

#### **Offline Model Generation for Offline Inference**

Use the ATC tool to convert the obtained .pb model into an .om offline model, which can be executed for offline inference on the **/home/HwHiAiUser/Ascend/ ascend-toolkit/latest**. For details, see "**[ATC Tool Instructions](https://support.huawei.com/enterprise/en/doc/EDOC1100180776/a3cf4cee)**" in CANN Auxiliary Development Tool Guide.

# **9 More Functions**

<span id="page-64-0"></span>9.1 Mixed Computing [9.2 Log and Summary Operators](#page-65-0) [9.3 Collective Communication APIs](#page-67-0)

# **9.1 Mixed Computing**

#### **Overview**

By default, the fully offloaded mode is used on Ascend AI Processor. That is, the execution of compute operators is fully offloaded on the device side.

As a supplement to the fully offloaded mode, mixed computing allows some operators to be executed online in the frontend framework, improving the Ascend AI Processor flexibility for adapting to TensorFlow. Note the following:

- In mixed computing mode, **[iteration offloading](#page-37-0)** is not supported. That is, **iterations\_per\_loop** must retain the default value **1**.
- In addition to the operators that are not offloaded by default, you can also configure the operators that are not offloaded by using **without\_npu\_compile\_scope**.
- The FusedBatchNormV3 operator is released in 2019. Its fifth output is a CUDA-optimized output. It is not supported on Ascend AI Processor in mixed computing mode. If **tf.layers.batch\_normalization** is used in your training script, you can use "with compat.forward\_compatibility\_horizon(2019, 5, 1):" to skip this operator.

#### **Enabling Mixed Computing with Estimator**

In **Estimator** mode, use **mix\_compile\_mode** of **NPURunConfig** to enable the mixed computing function.

from npu\_bridge.npu\_init import \*

#### <span id="page-65-0"></span>**Enabling Mixed Computing with sess.run()**

In **sess.run()** mode, use the session configuration option **mix\_compile\_mode** to enable the mixed computing function and use **without\_npu\_compile\_scope** to configure operators not offloaded.

import tensorflow as tf from npu\_bridge.npu\_init import \* X = tf.random\_normal([2,]) Y = tf.random\_normal([2,]) **with npu\_scope.without\_npu\_compile\_scope(): pred = tf.add(tf.multiply(X, 1.), 0.)** cost = tf.reduce\_sum(tf.abs(pred-Y)) config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True **custom\_op.parameter\_map["mix\_compile\_mode"].b = True** config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. with tf.Session(config=config) as sess: print(sess.run(cost)) # The **reduce\_sum** node is executed on the host.

# **9.2 Log and Summary Operators**

#### **Background**

The execution of Log and Summary operators is offloaded to the device side. If you need to capture the Log and Summary information on the device side and view the information of the corresponding step on the host side, modify the training script by referring to this section.

#### **Log Printing**

In **Estimator** mode, the system starts the dequeue thread when the Log information is returned to the host. The Log information on the device side can be directly printed. Therefore, no modification is needed.

print\_op = tf.print(loss) with tf.control\_dependencies([print\_op]): train\_op = xxx # The Print operator depends on the nodes that can be executed on the graph. Otherwise, the Print operator does not take effect.

In **sess.run** mode, the dequeue thread is not started when the Log information is returned to the host. Therefore, you need to start the dequeue thread separately to obtain the buffered Log information.

from threading import Thread

import sys def dequeue(): tf.reset\_default\_graph() outfeed\_log\_tensors = npu\_ops.outfeed\_dequeue\_op( channel\_name="\_npu\_log", output\_types=[tf.string], output\_shapes=[()]) dequeue\_ops = tf.print(outfeed\_log\_tensors, sys.stderr) with tf.Session() as sess: i = 0 while i < max\_train\_steps: // **max\_train\_steps** indicates the maximum number of iterations. sess.run(dequeue\_ops)

i = i + 1

t1 = Thread(target=dequeue) t1.start()

For training, the Assert or Print operator is used to print Log information.

print\_op = tf.print(loss) with tf.control\_dependencies([print\_op]): train\_op = xxx # The Print operator depends on the nodes that can be executed on the graph. Otherwise, the Print operator does not take effect.

#### **Summary Printing**

In **sess.run** mode, it is not supported to send summary back to the host for viewing.

In **Estimator** mode, you need to define a **host\_call** function that contains the Summary information to be collected.

def \_host\_call\_fn(gs, loss): with summary.create\_file\_writer( "./model", max\_queue=1000).as\_default(): # Record summary every step. with summary.always\_record\_summaries(): # Record summary every 2000 steps. #with summary.record\_summaries\_every\_n\_global\_steps(2000,global\_step=gs): summary.scalar("host\_call\_loss", loss, step=gs) return summary.all\_summary\_ops()

Then, pass **host\_call** to the **NPUEstimatorSpec** constructor. The system starts the enqueue thread when the Summary operator is executed on the device side and starts the dequeue thread when the Summary information is sent back to the host, so that the information of each or N steps will be sent back to the host.

**host\_call** is a tuple consisting of a function and a list or dictionary of tensors. It is used to return a list of tensors. **host\_call** applies to **train()** and **evaluate()** calls.

from npu\_bridge.npu\_init import \*

host\_call = (\_host\_call\_fn, [global\_step, loss]) return NPUEstimatorSpec(mode=tf.estimator.ModeKeys.TRAIN, loss=loss, train\_op=train\_op, **host\_call=host\_call**)

The following is a complete code example.

from npu\_bridge.npu\_init import \*

# Define a **host\_call** function. from tensorflow.contrib import summary **def \_host\_call\_fn(gs, loss): with summary.create\_file\_writer( "./model", max\_queue=1000).as\_default(): with summary.always\_record\_summaries(): summary.scalar("host\_call\_loss", loss, step=gs) return summary.all\_summary\_ops()** def input\_fn(): "Build dataset" # Call **host\_call** in **model\_fn** to capture the information to be viewed. def model\_fn(): "Build a forward/backward model" model = \*\*\* loss = \*\*\* optimizer = tf.train.MomentumOptimizer(learning\_rate=c, momentum=0.9) <span id="page-67-0"></span> global\_step = tf.train.get\_or\_create\_global\_step() grad\_vars = optimizer.compute\_gradients(loss) minimize\_op = optimizer.apply\_gradients(grad\_vars, global\_step) update\_ops = tf.get\_collection(tf.GraphKeys.UPDATE\_OPS) train\_op = tf.group(minimize\_op, update\_ops) **host\_call = (\_host\_call\_fn, [global\_step, loss])** return **NPUEstimatorSpec**(mode=tf.estimator.ModeKeys.TRAIN, loss=loss, train\_op=train\_op, **host\_call=host\_call**) run\_config = NPURunConfig() classifier = NPUEstimator(model\_fn=model\_fn, config=run\_config, params={ }) classifier.train(input\_fn=lambda: input\_fn(), max\_steps=1000)

# **9.3 Collective Communication APIs**

# **9.3.1 API Overview**

The high-level API **NPUDistributedOptimizer** enables the user to automatically complete gradient aggregation without sensing AllReduce, implementing data parallel training. In addition, to meet users' requirements for flexibility, the API for atomic communication in collective communication is provided to implement native representation of data parallelism.

Currently, the **AllReduce**, **Broadcast**, **AllGather**, **ReduceScatter**, **Send**, as well as the **Receive** operations are supported, and a process group that participates in collective communication can be customized. For example, eight processors can be divided into groups of four for collective communication. Collective communication provides common APIs such as rank management, gradient segmentation, and collective communication prototype.

**Table 9-1** Terminology

<span id="page-68-0"></span>

| Term        | Description                                                  |
|-------------|--------------------------------------------------------------|
| Rank size ● | Rank size: indicates the number of ranks in a group. The     |
|             | maximum value is 4096                                        |
| ●           | Local rank size: indicates the number of ranks in a group on |
|             | be 1 , 2 , 4 or 8                                            |
| Rank ID ●   | Rank ID: indicates the ID of a process in a group. The value |
| ●           | World rank ID: indicates the rank ID of a process in a HCCl  |
| ●           | Local rank ID: indicates the rank ID of a process in a group |
|             | computational graph is fused and segmented. AllReduce is     |

# **9.3.2 Initializing Collective Communication**

If you call an HCCL API such as **get\_local\_rank\_id**, **get\_rank\_size**, or **get\_rank\_id** before calling **sess.run()** or **estimator.train()**, you need to start another session and execute **initialize\_system** to initialize collective communication. After the training is complete, execute **shutdown\_system** and close the session.

import tensorflow as tf from npu\_bridge.npu\_init import \*

npu\_int = npu\_ops.initialize\_system() npu\_shutdown = npu\_ops.shutdown\_system()

# If some specific functions need to be enabled during training, pass related arguments here. For details, see the description of the **initialize\_system** API. config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer"

custom\_op.parameter\_map["use\_off\_line"].b = True

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

init\_sess = tf.Session(config=config)

init\_sess.run(npu\_int)

# Call an HCCL API... # Perform training. If another session is started to perform training, set the same run parameters as the preceding ones.

#### <span id="page-69-0"></span>Or:

import tensorflow as tf from npu\_bridge.npu\_init import \*

npu\_init = npu\_ops.initialize\_system() npu\_shutdown = npu\_ops.shutdown\_system()

# If some specific functions need to be enabled during training, pass related arguments here. For details, see the description of the **initialize\_system** API.

config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

with tf.Session(config=config) as sess:

 sess.run(npu\_init) # Call an HCCL API... # Perform training... sess.run(npu\_shutdown)

# **9.3.3 Specifying Devices to Perform Collective Communication**

Call **create\_group** to specify the devices that participate in collective communication. If this API is not called to create a custom group, all devices are created as the global **hccl\_world\_group** by default based on the **ranktable** file.

For example, you can select the first two devices in **hccl\_world\_group** as a group for collective communication:

from npu\_bridge.npu\_init import \* create\_group("myGroup", 2, [0, 1])

# **9.3.4 Obtaining the Group Information**

You can call the group management API to obtain the group information.

**get\_rank\_size**: obtains the number of all devices in the current group.

from npu\_bridge.npu\_init import \* rankSize = get\_rank\_size("myGroup")

**get\_local\_rank\_size**: obtains the number of devices in a group on the server where the current device is located.

from npu\_bridge.npu\_init import \*

lcoalRankSize = get\_local\_rank\_size("myGroup")

**get\_rank\_id**: obtains the logical device ID (rank ID) of the current device in a group.

from npu\_bridge.npu\_init import \*

rankId = get\_rank\_id("myGroup")

**get\_local\_rank\_id**: obtains the logical device ID (local rank ID) of the current device on the server.

from npu\_bridge.npu\_init import \*

localRankId = get\_local\_rank\_id("myGroup")

# **9.3.5 Setting the Backward Gradient Segmentation Policy**

You can call the gradient segmentation APIs to set the AllReduce segmentation and fusion policy in the backward pass phase.

**set\_split\_strategy\_by\_idx**: sets the backward gradient segmentation policy in the collective communication group based on the gradient index ID.

<span id="page-70-0"></span>from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_idx([20, 100, 159])

**set\_split\_strategy\_by\_size**: sets the backward gradient segmentation policy in the collective communication group by ratio.

from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_size([60, 20, 20])

# **9.3.6 Computing Tensor Nodes for Collective Communication**

You can call the operator prototype APIs to compute the tensors involved in collective communication.

#### **AllReduce**

**allreduce**: performs reduction on tensors with the same name across ranks within a collective communication group. The Reduce operation is specified by the **reduction** parameter.

#---------------------AllReduce test (two devices)---------------------------------

from npu\_bridge.npu\_init import \* tensor = tf.random\_uniform((1, 3), minval=1, maxval=10, dtype=tf.float32) allreduce\_test = hccl\_ops.allreduce(tensor , "sum")

#### **AllGather**

**allgather**: Each device receives the aggregation of tensor data from all ranks in the order of the ranks.

#---------------------AllGather test (two devices)-------------------------------- from npu\_bridge.npu\_init import \* cCon = tf.constant([1.0,2.0,3.0]) allgather\_test = hccl\_ops.allgather(cCon, 2) #---------- rank 0/1 allgather \_test = [1.0, 2.0, 3.0, 1.0, 2.0, 3.0] ----------

#### **Broadcast**

#---------------------Broadcast test (two devices)-------------------------------- from npu\_bridge.npu\_init import \* cCon = tf.Variable([1.0,2.0,3.0]) input = [cCon] broadcast\_test = hccl\_ops.broadcast(input, 0) #---------------- rank 0/1 broadcast\_test = [1.0, 2.0, 3.0] --------------------

#### **ReduceScatter**

**reduce\_scatter**: performs the same operation as the Reduce operation, except that each rank receives a subpart of the result. The Reduce operation is specified by the **reduction** parameter.

#---------------------ReduceScatter test (two devices)-----------------------------

from npu\_bridge.npu\_init import \* cCon = tf.constant([1.0,2.0,3.0,4.0]) reducescatter\_test = hccl\_ops.reduce\_scatter(cCon, "sum", 2) #-----------------rank 0 reducescatter \_test = [2.0, 4.0] ---------------------- #-----------------rank 1 reducescatter \_test = [6.0, 8.0] ----------------------

#### **Send**

**send**: sends data to a rank within a collective communication group.

#---------------------------------Send test-------------------------------------

from npu\_bridge.npu\_init import \* sr\_tag = 0 dest\_rank = 1 hccl\_ops.send(tensor, sr\_tag, dest\_rank)

#### **Receive**

**receive**: receives data from a rank within a collective communication group.

#---------------------Receive test (two devices)---------------------------------- from npu\_bridge.npu\_init import \* sr\_tag = 0 src\_rank = 0

tensor = hccl\_ops.receive(tensor.shape, tensor.dtype, sr\_tag, src\_rank)

# **10 Reference**

<span id="page-72-0"></span>10.1 Migration Examples [10.2 API Reference](#page-85-0) [10.3 Environment Variables](#page-197-0) [10.4 Device Resource Configuration File Templates](#page-204-0)

# **10.1 Migration Examples**

# **10.1.1 ResNet-50 Model Training Using the ImageNet Dataset**

#### **10.1.1.1 Preparations**

#### **Obtaining the Dataset**

This sample uses the ImageNet dataset as an example. Download the dataset from **<http://www.image-net.org/>**.

#### **Model Overview**

ResNet-50 is a deep residual network that can be used to classify 1000 classes of the CIFAR-10 and ImageNet datasets.

#### **Obtaining the Original Model**

The original ResNet network script is available at **[https://github.com/tensorflow/](https://github.com/tensorflow/models/tree/r2.1_model_reference/official) [models/tree/r2.1\\_model\\_reference/official](https://github.com/tensorflow/models/tree/r2.1_model_reference/official)**.

#### **Directory Structure**

The directory is organized as follows. (Only some involved files are listed. For more files, see the original ResNet script.)

├── r1 // Original model directory.

<span id="page-73-0"></span>│ ├── \_\_init\_\_.py

│ ├── imagenet\_main.py // Script for training the network based on the ImageNet dataset.

│ ├── imagenet\_preprocessing.py // ImageNet preprocessing module.

│ ├── resnet\_model.py // ResNet model file.

│ ├── resnet\_run\_loop.py // Data input processing and run loop (training, validation, and test).

│ ├── README.md // Project description file.

│ ├── utils

│ │ ├── export.py // Data receive functions, which define the parameter format that the exported

model can respond to.

├── utils │ ├── flags

│ │ ├── core.py // Public APIs including the parameter definition.

│ ├── logs

│ │ ├── hooks\_helper.py // Tool used for custom model tests and training, such as the function for calculating the number of steps per second, and the function of capturing CPU/GPU analysis information.

│ │ ├── logger.py // Log tool.

│ ├── misc

│ │ ├── distribution\_utils.py // Auxiliary functions used for running models in distributed mode. │ │ ├── model\_helpers.py // Functions that can be called by models, such as functions that controls

whether models stop.

#### **Migration Description**

The following describes the migration workflow by manually editing the training script instead of using the migration tool, in order to help you understand all key migration points in detail.

#### **10.1.1.2 Training Workflow**

### **About Estimator**

**Estimator** is a high-level API of TensorFlow and is introduced in TensorFlow 1.10 released in 2018. It greatly streamlines the programming process of machine learning. **Estimator** has many advantages, for example, good support for distribution, simplified model creation, and code sharing between model developers.

To use the **Estimator** API to develop a training script, perform the following steps.

**Table 10-1** Training flow

### <span id="page-74-0"></span>**10.1.1.3 Training Code Directory Structure**

## **Directory Structure**

The directory is organized as follows. (Only some involved files are listed. For more files, see the original ResNet script.)

├── r1 │ ├── resnet // ResNet main directory. │ ├── imagenet\_main.py // Script for training the network based on the ImageNet dataset. │ ├── imagenet\_preprocessing.py // ImageNet preprocessing module. │ ├── resnet\_model.py // ResNet model file. │ ├── resnet\_run\_loop.py // Data input processing and run loop (training, validation, and test). ├── utils │ ├── flags │ │ ├── \_base.py // Defines the common parameters and set the default value.

### **Directory Files**

**Table 10-2** PY file description

<span id="page-75-0"></span>

| File Name          | Description                                      |
|--------------------|--------------------------------------------------|
| resnet_run_loop.py | Model runtime file, including input processing   |
|                    | includes constructing Estimator , and performing |

### **10.1.1.4 Data Preprocessing**

The data preprocessing process is the same as that of the original model. Some of the code is modified to adapt to the Ascend 910 AI Processor for higher compute capability. The displayed code shows the modifications.

#### **Defining the Input Function input\_fn**

Data preprocessing of the ImageNet dataset is used as an example. The modified .py files and functions for adapting to the Ascend 910 AI Processor are as follows.

**Table 10-3** Data preprocessing APIs

| Function      | Description                        | Location |
|---------------|------------------------------------|----------|
| input_fn()    | Input function that processes the  |          |
|               | dataset for Estimator training and |          |
| resnet_main() | Main API that contains data input, |          |

1. Import the following header files to the **official/r1/resnet/imagenet\_main.py**

file:

from hccl.manage.api import get\_rank\_size from hccl.manage.api import get\_rank\_id

2. Obtain the number of devices and device IDs to support parallel data

processing.

Tweak: **input\_fn()** in **official/r1/resnet/imagenet\_main.py** (The changes are in bold.)

def input\_fn(is\_training, data\_dir, batch\_size, num\_epochs=1, dtype=tf.float32, datasets\_num\_private\_threads=None, parse\_record\_fn=parse\_record, input\_context=None, drop\_remainder=False, tf\_data\_experimental\_slack=False): """Function that provides training and verification batches.

 Parameter description: **is\_training**: boolean value indicating whether the input is used for training. **data\_dir**: file path that contains the input dataset. **batch\_size**: size of each batch. **num\_epochs**: number of epochs. **dtype**: data type of an image or feature. **datasets\_num\_private\_threads**: number of threads dedicated to **tf.data**. **parse\_record\_fn**: entry function for parsing TFRecords. **input\_context**: **tf.distribute.InputContext** object passed by **tf.distribute.Strategy drop\_remainder**: specifies whether to retain or discard the last batch if the data volume of the last batch is smaller than the value of **batch\_size**. If set to **True**, the batch dimension is fixed. **tf\_data\_experimental\_slack**: specifies whether to enable the **experimental\_slack** option of **tf.data**. Returns: A dataset that can be used for iteration. """ # Obtain the file path. filenames = get\_filenames(is\_training, data\_dir) # Split the file based on the first dimension. dataset = tf.data.Dataset.from\_tensor\_slices(filenames) if input\_context: # Obtain the number of devices and device IDs to support parallel data processing. ############## npu modify begin ############# **dataset = dataset.shard(get\_rank\_size(),get\_rank\_id())** ############## npu modify end ############### # tf.compat.v1.logging.info( # 'Sharding the dataset: input\_pipeline\_id=%d num\_input\_pipelines=%d' % ( # input\_context.input\_pipeline\_id, input\_context.num\_input\_pipelines)) # dataset = dataset.shard(input\_context.num\_input\_pipelines, # input\_context.input\_pipeline\_id) if is\_training: # Disorder the files. dataset = dataset.shuffle(buffer\_size=\_NUM\_TRAIN\_FILES) # cycle\_length = 10 Read and deserialize 10 files in parallel. You can increase the value if the CPU resources are sufficient. dataset = dataset.interleave( tf.data.TFRecordDataset, cycle\_length=10, num\_parallel\_calls=tf.data.experimental.AUTOTUNE) return resnet\_run\_loop.process\_record\_dataset( dataset=dataset, is\_training=is\_training, batch\_size=batch\_size, shuffle\_buffer=\_SHUFFLE\_BUFFER, parse\_record\_fn=parse\_record\_fn, num\_epochs=num\_epochs, dtype=dtype, datasets\_num\_private\_threads=datasets\_num\_private\_threads, drop\_remainder=drop\_remainder, tf\_data\_experimental\_slack=tf\_data\_experimental\_slack, ) 3. In **input\_fn()** in the training or testing scenario, **drop\_remainder** must be set to **True**. Tweak: **resnet\_main()** in **official/r1/resnet/resnet\_run\_loop.py** (The **input\_fn\_train()** and **input\_fn\_eval()** child functions are tweaked.) def input\_fn\_train(num\_epochs, input\_context=None): ############## npu modify begin ############# # Use dtype=tf.float16 to improve data transfer performance. # In the current version, **drop\_remainder** can only be set to **True**. # **batch\_size** indicates the batch size of a single device instead of the global batch size. return input\_function( is\_training=True, data\_dir=flags\_obj.data\_dir,

<span id="page-77-0"></span> batch\_size=**flags\_obj.batch\_size,** num\_epochs=num\_epochs, dtype=**tf.float16,** input\_context=input\_context, **drop\_remainder=True**) def input\_fn\_eval(): # Use dtype=tf.float16 to improve data transfer performance. # In the current version, **drop\_remainder** can only be set to **True**. # **batch\_size** indicates the batch size of a single device instead of the global batch size. return input\_function( is\_training=False, data\_dir=flags\_obj.data\_dir, batch\_size=**flags\_obj.batch\_size,** num\_epochs=1, dtype=**tf.float16,** input\_context=True, **drop\_remainder**=**True**) ############## npu modify end ############### # **input\_fn()** for training and verification in the code are as follows. # def input\_fn\_train(num\_epochs, input\_context=None): # return input\_function( # is\_training=True, # data\_dir=flags\_obj.data\_dir, # batch\_size=distribution\_utils.per\_replica\_batch\_size( # flags\_obj.batch\_size, flags\_core.get\_num\_gpus(flags\_obj)), # num\_epochs=num\_epochs, # dtype=flags\_core.get\_tf\_dtype(flags\_obj), # datasets\_num\_private\_threads=flags\_obj.datasets\_num\_private\_threads, # input\_context=input\_context) # # def input\_fn\_eval(): # return input\_function( # is\_training=False, # data\_dir=flags\_obj.data\_dir, # batch\_size=distribution\_utils.per\_replica\_batch\_size( # flags\_obj.batch\_size, flags\_core.get\_num\_gpus(flags\_obj)), # num\_epochs=1, # dtype=flags\_core.get\_tf\_dtype(flags\_obj))

#### **10.1.1.5 Model Building**

Build a model the same as the original model. Some code is modified for adaption to improve compute performance. The sample code in this section shows the modifications.

#### **Defining Model Functions**

The following uses the model function constructed based on ImageNet as an example. The related APIs are as follows.

**Table 10-4** Model APIs

| Class or API               | Description Location    |
|----------------------------|-------------------------|
| learning_rate_with_decay() | Learning rate function. |
| resnet_model_fn()          | Constructs the          |
|                            | EstimatorSpec class,    |
| ImagenetModel()            | Inherited from Model    |
|                            | in the resnet_model     |
| __call__()                 | Adds more operations    |

#### **Performance Improvement**

1. Import the following header file to the **/official/r1/resnet/**

**resnet\_run\_loop.py** file: from npu\_bridge.hccl import **hccl\_ops**

- 2. Check the data type of the input features or images.

Tweak: **resnet\_model\_fn()** in **official/r1/resnet/resnet\_run\_loop.py** (The

changes are in bold.)

############# npu modify begin #############

# Check whether the data type of input features or images is consistent with the data type used

for compute.

**if features.dtype != dtype:**

# Change the data type of the features to **dtype**.

 **features = tf.cast(features, dtype)**

############## npu modify end ###############

 # The source code is as follows. # assert features.dtype == dtype

- 3. Use the float32 type for **labels** to improve accuracy.

Tweak: **resnet\_model\_fn()** in **official/r1/resnet/resnet\_run\_loop.py** (The

changes are in bold.)

 ############## npu modify begin ############# # Use the float32 type for **labels** to improve accuracy.

accuracy = tf.compat.v1.metrics.accuracy(**tf.cast(labels, tf.float32)**, predictions['classes'])

############## npu modify end ###############

# The accuracy computation code is as follows.

# accuracy = tf.compat.v1.metrics.accuracy(labels, predictions['classes'])

accuracy\_top\_5 = tf.compat.v1.metrics.mean(

tf.nn.in\_top\_k(predictions=logits, targets=labels, k=5, name='top\_5\_op'))

############## npu modify begin #############

# Calculate accuracy during distributed training. **rank\_size = int(os.getenv('RANK\_SIZE'))**

**newaccuracy = (hccl\_ops.allreduce(accuracy[0], "sum") / rank\_size, accuracy[1]) newaccuracy\_top\_5 = (hccl\_ops.allreduce(accuracy\_top\_5[0], "sum") / rank\_size,** 

**accuracy\_top\_5[1])**

**metrics = {'accuracy': newaccuracy,** 

 **'accuracy\_top\_5': newaccuracy\_top\_5}** ############## npu modify end #############

 # The metrics source code is as follows. # metrics = {'accuracy': accuracy,

# 'accuracy\_top\_5': accuracy\_top\_5}

4. Replace the max\_pooling2d operator with max\_pool\_with\_argmax for better

compute performance.

Tweak: **\_\_call\_\_()** in **official/r1/resnet/resnet\_model.py** (The changes are in

bold.)

# Determine whether to perform the first pooling.

if self.first\_pool\_size:

############## npu modify begin #############

# Replace max\_pooling2d with max\_pool\_with\_argmax for better performance.

 inputs,**argmax = tf.compat.v1.nn.max\_pool\_with\_argmax( input=inputs, ksize=(1,self.first\_pool\_size,self.first\_pool\_size,1), strides=(1,self.first\_pool\_stride,self.first\_pool\_stride,1), padding='SAME', data\_format='NCHW' if self.data\_format == 'channels\_first' else 'NHWC')**

############## npu modify end ###############

 # The code uses the **max\_pooling2d()** API for pooling. # inputs = tf.compat.v1.layers.max\_pooling2d( # inputs=inputs, pool\_size=self.first\_pool\_size, # strides=self.first\_pool\_stride, padding='SAME',

 # data\_format=self.data\_format) inputs = tf.identity(inputs, 'initial\_max\_pool')

#### <span id="page-80-0"></span>**Configuring Distributed Training**

- 1. Import the following header file to the **official/r1/resnet/resnet\_run\_loop.py** file: from npu\_bridge.estimator.npu.npu\_optimizer import **NPUDistributedOptimizer**
- 2. Add the distributed training optimizer **NPUDistributedOptimizer**. Tweak: **resnet\_model\_fn()** in **official/r1/resnet/resnet\_run\_loop.py** (The changes are in bold.) if flags.FLAGS.enable\_lars: optimizer = tf.contrib.opt.LARSOptimizer( learning\_rate, momentum=momentum, weight\_decay=weight\_decay, skip\_list=['batch\_normalization', 'bias']) else: optimizer = tf.compat.v1.train.MomentumOptimizer( learning\_rate=learning\_rate, momentum=momentum ) ############## npu modify begin ############# # Use the distributed training optimizer to encapsulate the single-server optimizer to support distributed training. # Add the following content to the source code. **optimizer = NPUDistributedOptimizer(optimizer)** ############## npu modify end ############### fp16\_implementation = getattr(flags.FLAGS, 'fp16\_implementation', None) if fp16\_implementation == 'graph\_rewrite': optimizer = ( tf.compat.v1.train.experimental.enable\_mixed\_precision\_graph\_rewrite( optimizer, loss\_scale=loss\_scale))

### **10.1.1.6 Run Configuration**

#### **Run Configuration**

Set run configuration using the **resnet\_main()** function.

**Table 10-5** Model configuration function

- 1. Import the following header files to the **/official/r1/resnet/ resnet\_run\_loop.py** file: from npu\_bridge.estimator.npu.npu\_config import **NPURunConfig** from npu\_bridge.estimator.npu.npu\_estimator import **NPUEstimator**
- 2. Replace **Runconfig** with **NPURunconfig** to configure run parameters. Tweak: **resnet\_main()** in **official/r1/resnet/resnet\_run\_loop.py**

<span id="page-81-0"></span> ############## npu modify begin ############# # Replace **Runconfig** with **NPURunconfi**g to adapt to the Ascend AI Processor. Save the checkpoint every 115200 steps and summary every 10000 times, # Preprocess data and enable the mixed precision mode to improve the training speed. run\_config = **NPURunConfig**( **model\_dir=flags\_obj.model\_dir,** session\_config=session\_config, **save\_checkpoints\_steps=115200, enable\_data\_pre\_proc=True, iterations\_per\_loop=100,** # enable\_auto\_mix\_precision=True, # Set the mixed precision mode. **precision\_mode='allow\_mix\_precision', hcom\_parallel=True** ) ############## npu modify end ############### # The run configuration in the code is as follows. # run\_config = tf.estimator.RunConfig( # train\_distribute=distribution\_strategy, # session\_config=session\_config, # save\_checkpoints\_secs=60 \* 60 \* 24, # save\_checkpoints\_steps=None)

#### NO TE

For details about how to set the mixed precision mode

(**precision\_mode='allow\_mix\_precision'** ), see **[6.2.4 Mixed Precision](#page-46-0)**.

#### 3. # Create **NPUEstimator** to replace **tf.estimator.Estimator**.

Tweak: **resnet\_main()** in **/official/r1/resnet/resnet\_run\_loop.py**. The modifications are as follows.

 # Replace **tf.estimator.Estimator** with **NPUEstimator**. classifier = **NPUEstimator**( model\_fn=model\_function, model\_dir=flags\_obj.model\_dir, config=run\_config, params={ 'resnet\_size': int(flags\_obj.resnet\_size), 'data\_format': flags\_obj.data\_format, 'batch\_size': flags\_obj.batch\_size, 'resnet\_version': int(flags\_obj.resnet\_version), 'loss\_scale': flags\_core.get\_loss\_scale(flags\_obj, default\_for\_fp16=128), 'dtype': flags\_core.get\_tf\_dtype(flags\_obj), 'fine\_tune': flags\_obj.fine\_tune, 'num\_workers': num\_workers, **'num\_gpus': flags\_core.get\_num\_gpus(flags\_obj),** }) # The creation of **Estimator** in the code is as follows. # classifier = tf.estimator.Estimator( # model\_fn=model\_function, model\_dir=flags\_obj.model\_dir, config=run\_config, # warm\_start\_from=warm\_start\_settings, params={ # 'resnet\_size': int(flags\_obj.resnet\_size), # 'data\_format': flags\_obj.data\_format, # 'batch\_size': flags\_obj.batch\_size, # 'resnet\_version': int(flags\_obj.resnet\_version), # 'loss\_scale': flags\_core.get\_loss\_scale(flags\_obj, # default\_for\_fp16=128), # 'dtype': flags\_core.get\_tf\_dtype(flags\_obj), # 'fine\_tune': flags\_obj.fine\_tune, # 'num\_workers': num\_workers, # })

#### **10.1.1.7 Training**

#### **Training Module**

**Table 10-6** Training module API

| Function       | Overview Location         |
|----------------|---------------------------|
| main()         | Main function, for        |
| run_imagenet() | Model training entry, for |
| resnet_main()  | Main function for run     |

#### **Distributed Training**

- 1. Import the following header files to the **official/r1/resnet/ resnet\_run\_loop.py** file: from npu\_bridge.estimator import npu\_ops from tensorflow.core.protobuf import rewriter\_config\_pb2
- 2. Initialize collective communication before training. Tweak: **main()** in **official/r1/resnet/imagenet\_main.py**. The modifications are as follows. def main(): ############## npu modify begin ############# # Call the HCCL API to initialize the NPU. # Add the following content to the code. **init\_sess, npu\_init = resnet\_run\_loop.init\_npu() init\_sess.run(npu\_init)** ############## npu modify end ############### with logger.benchmark\_context(flags.FLAGS): run\_imagenet(flags.FLAGS)
- 3. Define the collective communication initialization function. Tweak: **init\_npu()** in **official/r1/resnet/resnet\_run\_loop.py**. def resnet\_main(flags\_obj, model\_function, input\_function, dataset\_name, shape=None):... ############## npu modify begin ############# #Add the following code. def init\_npu(): """This API is used to manually initialize the NPU. Returns: `init\_sess` npu init session config. `npu\_init` npu init ops. """ npu\_init = npu\_ops.initialize\_system() config = tf.ConfigProto() config.graph\_options.rewrite\_options.remapping = rewriter\_config\_pb2.RewriterConfig.OFF custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" # custom\_op.parameter\_map["precision\_mode"].b = True custom\_op.parameter\_map["precision\_mode"].s = tf.compat.as\_bytes("allow\_mix\_precision") custom\_op.parameter\_map["use\_off\_line"].b = True init\_sess = tf.Session(config=config) return init\_sess, npu\_init ############## npu modify end ###############

- 4. Destroy the device allocations after a single training or verification process is complete.

Tweak: **resnet\_main()** in **official/r1/resnet/resnet\_run\_loop.py** (The changes are in bold.)

for cycle\_index, num\_train\_epochs in enumerate(schedule): tf.compat.v1.logging.info('Starting cycle: %d/%d', cycle\_index,

int(n\_loops))

if num\_train\_epochs:

 # Since we are calling classifier.train immediately in each loop, the # value of num\_train\_epochs in the lambda function will not be changed

# before it is used. So it is safe to ignore the pylint error here

# pylint: disable=cell-var-from-loop

classifier.train(

 input\_fn=lambda input\_context=None: input\_fn\_train( num\_train\_epochs, input\_context=input\_context),

hooks=train\_hooks,

max\_steps=flags\_obj.max\_train\_steps)

############## npu modify begin #############

 # When a single training process is complete, destroy the NPU allocations. You need to reinitialize the NPU before starting a new training process so that the HCCL API is available in the new training process:

 # Add the following content to the code. **init\_sess, npu\_init = init\_npu()**

**npu\_shutdown = npu\_ops.shutdown\_system()**

**init\_sess.run(npu\_shutdown)**

**init\_sess.run(npu\_init)** ############## npu modify end ###############

 tf.compat.v1.logging.info('Starting to evaluate.') eval\_results = classifier.evaluate(input\_fn=input\_fn\_eval, steps=flags\_obj.max\_train\_steps)

benchmark\_logger.log\_evaluation\_result(eval\_results)

if model\_helpers.past\_stop\_threshold(

flags\_obj.stop\_threshold, eval\_results['accuracy']):

break

 ############## npu modify begin ############# # When a single training process is complete, destroy the NPU allocations. You need to reinitialize the NPU before starting a new training process so that the HCCL API is available in the new training process: # Add the following content to the code.

**init\_sess, npu\_init = init\_npu(**)

**npu\_shutdown = npu\_ops.shutdown\_system()**

**init\_sess.run(npu\_shutdown) init\_sess.run(npu\_init)**

############## npu modify end ###############

- 5. Destroy device allocations after training or validation is complete.

After training or validation is complete, call **npu\_ops.shutdown\_system** to clean up the NPU allocations.

Tweak: **resnet\_main()** in **official/r1/resnet/resnet\_run\_loop.py**. The modifications are as follows.

if flags\_obj.export\_dir is not None:

 # Exports a saved model for the given classifier. export\_dtype = flags\_core.get\_tf\_dtype(flags\_obj) if flags\_obj.image\_bytes\_as\_serving\_input: input\_receiver\_fn = functools.partial(

image\_bytes\_serving\_input\_fn, shape, dtype=export\_dtype)

else:

 input\_receiver\_fn = export.build\_tensor\_serving\_input\_receiver\_fn( shape, batch\_size=flags\_obj.batch\_size, dtype=export\_dtype) classifier.export\_savedmodel(flags\_obj.export\_dir, input\_receiver\_fn,

<span id="page-84-0"></span> strip\_default\_attrs=True) ############## npu modify begin ############# # After training or validation is complete, destroy the NPU allocations through the **npu\_ops.shutdown\_system** API. Add the following content to the code. **npu\_shutdown = npu\_ops.shutdown\_system() init\_sess.run(npu\_shutdown)** ############## npu modify end ############### stats = {} stats['eval\_results'] = eval\_results stats['train\_hooks'] = train\_hooks return stats

#### **Loss Scaling Settings**

Set the default value of **loss\_scale**.

Tweak: **define\_imagenet\_flags()** in **official/r1/resnet/imagenet\_main.py**. The modifications are as follows. def define\_imagenet\_flags(): resnet\_run\_loop.define\_resnet\_flags( resnet\_size\_choices=['18', '34', '50', '101', '152', '200'], dynamic\_loss\_scale=True, fp16\_implementation=True) flags.adopt\_module\_key\_flags(resnet\_run\_loop) flags\_core.set\_defaults(train\_epochs=90) ############## npu modify begin ############# # The Ascend AI Processor supports mixed precision training by default. If the value of **loss\_scale** is too large, the gradient may explode. If the value is too small, the gradient may vanish. # Set **loss\_scale** as follows to avoid the preceding issues. **flags\_core.set\_defaults(loss\_scale='512')** ############## npu modify end ###############

#### **10.1.1.8 Script Execution**

#### **Preparing a Dataset**

Prepare a dataset and upload it to a directory in the operating environment, for example, **/home/data/resnet50/imagenet**.

#### **Preparing ranktable File**

For details about the **ranktable** file example and file description, see **[10.4 Device](#page-204-0) [Resource Configuration File Templates](#page-204-0)**.

### **Configuring Environment Variables**

For details about the environment variable configuration, see **[5.2 Training in a](#page-31-0) [Bare Metal Environment](#page-31-0)**.

## **Running Command**

**python3 /home/official/r1/resnet/imagenet\_main.py --batch\_size=32 - hooks=ExamplesPerSecondHook --data\_dir=/home/data/resnet50/imagenet**

# <span id="page-85-0"></span>**10.1.2 More Migration Cases**

For more migration cases, visit **<https://gitee.com/ascend/modelzoo>**.

- **[Accuracy Troubleshooting in Ascend 910 ALBERT-Base Migration](https://gitee.com/ascend/modelzoo/wikis/ALBERT-Base%E6%A8%A1%E5%9E%8B%E6%98%87%E8%85%BE910%E8%BF%81%E7%A7%BB%E8%BF%87%E7%A8%8B%E7%B2%BE%E5%BA%A6%E9%97%AE%E9%A2%98%E4%BB%A3%E7%A0%81%E5%AE%9A%E4%BD%8D?sort_id=3145861)**
- **[Dynamic Shape Tuning in Open-Source Network Migration \(Transformer\)](https://gitee.com/ascend/modelzoo/wikis/Transformer%E5%BC%80%E6%BA%90%E7%BD%91%E7%BB%9C%E8%BF%81%E7%A7%BB-%E5%8A%A8%E6%80%81shape%E8%B0%83%E8%AF%95%E6%96%B9%E6%B3%95?sort_id=3065701)**
- **[Dynamic Shape Tuning in Open-Source Network Migration \(YOLOv4\)](https://gitee.com/ascend/modelzoo/wikis/yolov4%E5%BC%80%E6%BA%90%E7%BD%91%E7%BB%9C%E8%BF%81%E7%A7%BB-%E5%8A%A8%E6%80%81shape%E8%B0%83%E8%AF%95%E6%96%B9%E6%B3%95?sort_id=3074644)**
- **[Dynamic Shape Tuning in Open-Source Network Migration \(WideDeep\)](https://gitee.com/ascend/modelzoo/wikis/WideDeep%E5%BC%80%E6%BA%90%E7%BD%91%E7%BB%9C%E8%BF%81%E7%A7%BB-%E5%8A%A8%E6%80%81shape%E8%B0%83%E8%AF%95%E6%96%B9%E6%B3%95?sort_id=3076852)**
- **[\[Ascend Model Training Challenge\] Ascend 910 BERT-Base Migration](https://bbs.huaweicloud.com/forum/thread-79873-1-1.html) [Workflow](https://bbs.huaweicloud.com/forum/thread-79873-1-1.html)**

# **10.2 API Reference**

# **10.2.1 Available TensorFlow APIs**

The training APIs of the are used with the TensorFlow 1.15 version. This section lists the support of TensorFlow Python APIs.

### **Supported Python APIs**

The following table lists part of the supported Python APIs.

| Module | Supported Python API       |
|--------|----------------------------|
| tf     | tf.Dtype                   |
| tf     | tf.FIFOQueue               |
| tf     | tf.FixedLenFeature         |
| tf     | tf.FixedLenSequenceFeature |
| tf     | tf.Graph                   |
| tf     | tf.IndexedSlices           |
| tf     | tf.NotDifferentiable       |
| tf     | tf.Operation               |
| tf     | tf.PaddingFIFOQueue        |
| tf     | tf.RandomShuffleQueue      |
| tf     | tf.ReaderBase              |
| tf     | tf.RegisterGradient        |
| tf     | tf.Session                 |
| tf     | tf.TFRecordReader          |
| tf     | tf.Tensor                  |
| tf     | tf.TensorArray             |

| Module | Supported Python API    |
|--------|-------------------------|
| tf     | tf.TensorShape          |
| tf     | tf.VarLenFeature        |
| tf     | tf.Variable             |
| tf     | tf.VariableAggregation  |
| tf     | tf.VariableScope        |
| tf     | tf.WholeFileReader      |
| tf     | tf.abs                  |
| tf     | tf.acos                 |
| tf     | tf.add                  |
| tf     | tf.add_n                |
| tf     | tf.argmax               |
| tf     | tf.as_dtype             |
| tf     | tf.assert_greater       |
| tf     | tf.assert_less_equal    |
| tf     | tf.cast                 |
| tf     | tf.ceil                 |
| tf     | tf.confusion_matrix     |
| tf     | tf.constant_initializer |
| tf     | tf.cos                  |
| tf     | tf.count_nonzero        |
| tf     | tf.cumsum               |
| tf     | tf.decode_csv           |
| tf     | tf.decode_raw           |
| tf     | tf.depth_to_space       |
| tf     | tf.diag_part            |
| tf     | tf.divide               |
| tf     | tf.dynamic_stitch       |
| tf     | tf.einsum               |
| tf     | tf.equal                |
| tf     | tf.exp                  |

| Module | Supported Python API                |
|--------|-------------------------------------|
| tf     | tf.fixed_size_partitioner           |
| tf     | tf.flags                            |
| tf     | tf.floor                            |
| tf     | tf.floordiv                         |
| tf     | tf.get_default_graph                |
| tf     | tf.get_default_session              |
| tf     | tf.get_seed                         |
| tf     | tf.global_norm                      |
| tf     | tf.glorot_uniform_initializer       |
| tf     | tf.gradients                        |
| tf     | tf.greater                          |
| tf     | tf.greater_equal                    |
| tf     | tf.less                             |
| tf     | tf.less_equal                       |
| tf     | tf.log                              |
| tf     | tf.logical_and                      |
| tf     | tf.logical_not                      |
| tf     | tf.logical_or                       |
| tf     | tf.matmul (int64 is not supported.) |
| tf     | tf.matrix_band_part                 |
| tf     | tf.maximum                          |
| tf     | tf.meshgrid                         |
| tf     | tf.minimum                          |
| tf     | tf.mod                              |
| tf     | tf.multinomial                      |
| tf     | tf.multiply                         |
| tf     | tf.name_scope                       |
| tf     | tf.not_equal                        |
| tf     | tf.one_hot                          |
| tf     | tf.ones_initializer                 |

| Module | Supported Python API             |
|--------|----------------------------------|
| tf     | tf.orthogonal_initializer        |
| tf     | tf.parse_example                 |
| tf     | tf.parse_single_example          |
| tf     | tf.parse_single_sequence_example |
| tf     | tf.pow                           |
| tf     | tf.read_file                     |
| tf     | tf.reduce_all                    |
| tf     | tf.reduce_any                    |
| tf     | tf.reduce_logsumexp              |
| tf     | tf.reduce_max                    |
| tf     | tf.reduce_mean                   |
| tf     | tf.reduce_min                    |
| tf     | tf.reduce_prod                   |
| tf     | tf.reduce_sum                    |
| tf     | tf.reverse_v2                    |
| tf     | tf.rint                          |
| tf     | tf.round                         |
| tf     | tf.rsqrt                         |
| tf     | tf.scalar_mul                    |
| tf     | tf.sequence_mask                 |
| tf     | tf.set_random_seed               |
| tf     | tf.sigmoid                       |
| tf     | tf.sign                          |
| tf     | tf.sin                           |
| tf     | tf.space_to_depth                |
| tf     | tf.sqrt                          |
| tf     | tf.square                        |
| tf     | tf.squared_difference            |
| tf     | tf.string_join                   |
| tf     | tf.string_to_hash_bucket_fast    |

| Module | Supported Python API            |
|--------|---------------------------------|
| tf     | tf.string_to_number             |
| tf     | tf.subtract                     |
| tf     | tf.tanh                         |
| tf     | tf.tensordot                    |
| tf     | tf.tile                         |
| tf     | tf.timestamp                    |
| tf     | tf.to_float                     |
| tf     | tf.to_int32                     |
| tf     | tf.to_int64                     |
| tf     | tf.transpose                    |
| tf     | tf.truediv                      |
| tf     | tf.truncated_normal             |
| tf     | tf.unique                       |
| tf     | tf.unsorted_segment_min         |
| tf     | tf.unsorted_segment_sum         |
| tf     | tf.unstack                      |
| tf     | tf.variance_scaling_initializer |
| tf     | tf.where                        |
| tf     | tf.while_loop                   |
| tf     | tf.zeros                        |
| tf     | tf.zeros_initializer            |
| tf     | tf.zeros_like                   |
| tf     | tf.add_to_collection            |
| tf     | tf.AggregationMethod            |
| tf     | tf.assign                       |
| tf     | tf.assign_add                   |
| tf     | tf.assign_sub                   |
| tf     | tf.batch_to_space_nd            |
| tf     | tf.boolean_mask                 |
| tf     | tf.case                         |

| Module | Supported Python API         |
|--------|------------------------------|
| tf     | tf.clip_by_global_norm       |
| tf     | tf.clip_by_norm              |
| tf     | tf.clip_by_value             |
| tf     | tf.concat                    |
| tf     | tf.cond                      |
| tf     | tf.constant                  |
| tf     | tf.container                 |
| tf     | tf.control_dependencies      |
| tf     | tf.convert_to_tensor         |
| tf     | tf.Dimension                 |
| tf     | tf.div                       |
| tf     | tf.enable_resource_variables |
| tf     | tf.expand_dims               |
| tf     | tf.eye                       |
| tf     | tf.fill                      |
| tf     | tf.gather                    |
| tf     | tf.gather_nd                 |
| tf     | tf.get_collection            |
| tf     | tf.get_collection_ref        |
| tf     | tf.get_local_variable        |
| tf     | tf.get_variable              |
| tf     | tf.get_variable_scope        |
| tf     | tf.global_variables          |
| tf     | tf.GraphDef                  |
| tf     | tf.GraphKeys                 |
| tf     | tf.group                     |
| tf     | tf.identity                  |
| tf     | tf.lin_space                 |
| tf     | tf.local_variables           |
| tf     | tf.model_variables           |

| Module | Supported Python API              |
|--------|-----------------------------------|
| tf     | tf.moving_average_variables       |
| tf     | tf.NodeDef                        |
| tf     | tf.norm                           |
| tf     | tf.no_op                          |
| tf     | tf.ones                           |
| tf     | tf.ones_like                      |
| tf     | tf.pad                            |
| tf     | tf.parallel_stack                 |
| tf     | tf.placeholder                    |
| tf     | tf.placeholder_with_default       |
| tf     | tf.print                          |
| tf     | tf.range                          |
| tf     | tf.rank                           |
| tf     | tf.report_uninitialized_variables |
| tf     | tf.reshape                        |
| tf     | tf.reverse                        |
| tf     | tf.scatter_nd                     |
| tf     | tf.shape                          |
| tf     | tf.size                           |
| tf     | tf.slice                          |
| tf     | tf.split                          |
| tf     | tf.squeeze                        |
| tf     | tf.stack                          |
| tf     | tf.stop_gradient                  |
| tf     | tf.string_split                   |
| tf     | tf.ConditionalAccumulator         |
| tf     | tf.QueueBase                      |
| tf     | tf.VariableSynchronization        |
| tf     | tf.assert_rank                    |
| tf     | tf.batch_gather                   |

| Module | Supported Python API            |
|--------|---------------------------------|
| tf     | tf.broadcast_to                 |
| tf     | tf.custom_gradient              |
| tf     | tf.ensure_shape                 |
| tf     | tf.import_graph_def             |
| tf     | tf.is_finite                    |
| tf     | tf.is_inf                       |
| tf     | tf.log1p                        |
| tf     | tf.print                        |
| tf     | tf.scatter_sub                  |
| tf     | tf.variable_creator_scope       |
| tf     | tf.make_template                |
| tf     | tf.make_tensor_proto            |
| tf     | tf.erf                          |
| tf     | tf.check_numerics               |
| tf     | tf.is_nan                       |
| tf     | tf.linspace                     |
| tf     | tf.tables_initializer           |
| tf     | tf.GradientTape                 |
| tf     | tf.floormod                     |
| tf     | tf.realdiv                      |
| tf     | tf.global_variables_initializer |
| tf     | tf.histogram_fixed_width        |
| tf     | tf.truncated_normal_initializer |
| tf     | tf.unique_with_counts           |
| tf     | tf.local_variables_initializer  |
| tf     | tf.get_logger                   |
| tf     | tf.TensorSpec                   |
| tf     | tf.argsort                      |
| tf     | tf.switch_case                  |
| tf     | tf.tensor_scatter_nd_update     |

| Module     | Supported Python API                       |
|------------|--------------------------------------------|
| tf         | tf.sort                                    |
| tf.app     | tf.app.flags                               |
| tf.app     | tf.app.run                                 |
| tf.bitwise | tf.bitwise.bitwise_and                     |
| tf.bitwise | tf.bitwise.bitwise_or                      |
| tf.bitwise | tf.bitwise.bitwise_xor                     |
| tf.bitwise | tf.bitwise.invert                          |
| tf.bitwise | tf.bitwise.left_shift                      |
| tf.bitwise | tf.bitwise.right_shift                     |
| tf.compat  | tf.compat.as_bytes                         |
| tf.compat  | tf.compat.as_text                          |
| tf.compat  | tf.compat.as_str_any                       |
| tf.config  | tf.ConfigProto                             |
| tf.config  | tf.config.optimizer                        |
| tf.data    | tf.data.Dataset                            |
| tf.data    | tf.data.FixedLengthRecordDataset           |
| tf.data    | tf.data.Iterator                           |
| tf.data    | tf.data.TFRecordDataset                    |
| tf.data    | tf.data.TextLineDataset                    |
| tf.data    | tf.data.Options                            |
| tf.data    | tf.data.experimental.RandomDataset         |
| tf.data    | tf.data.experimental.Reducer               |
| tf.data    | tf.data.experimental.SqlDataset            |
| tf.data    | tf.data.experimental.StatsAggregator       |
| tf.data    | tf.data.experimental.dense_to_sparse_batch |
| tf.data    | tf.data.experimental.get_single_element    |
| tf.data    | tf.data.experimental.group_by_reducer      |
| tf.data    | tf.data.experimental.ignore_errors         |
| tf.data    | tf.data.experimental.latency_stats         |
| tf.data    | tf.data.experimental.parse_example_dataset |

| Module           | Supported Python API                      |
|------------------|-------------------------------------------|
| tf.data          | tf.data.experimental.prefetch_to_device   |
| tf.data          | tf.data.experimental.set_stats_aggregator |
| tf.data          | tf.data.experimental.shuffle_and_repeat   |
| tf.data          | tf.data.experimental.unbatch              |
| tf.distributions | tf.distributions.Categorical              |
| tf.distributions | tf.distributions.Normal                   |
| tf.distributions | tf.distributions.Uniform                  |
| tf.dtypes        | tf.dtypes.as_string                       |
| tf.dtypes        | tf.dtypes                                 |
| tf.dtypes        | tf.dtypes.complex                         |
| tf.dtypes        | tf.dtypes.saturate_cast                   |
| tf.errors        | tf.errors.AbortedError                    |
| tf.errors        | tf.errors.CancelledError                  |
| tf.errors        | tf.errors.DeadlineExceededError           |
| tf.errors        | tf.errors.InternalError                   |
| tf.errors        | tf.errors.InvalidArgumentError            |
| tf.errors        | tf.errors.NotFoundError                   |
| tf.errors        | tf.errors.OutOfRangeError                 |
| tf.errors        | tf.errors.UnavailableError                |
| tf.errors        | tf.errors.AlreadyExistsError              |
| tf.errors        | tf.errors.DataLossError                   |
| tf.errors        | tf.errors.FailedPreconditionError         |
| tf.errors        | tf.errors.OpError                         |
| tf.errors        | tf.errors.PermissionDeniedError           |
| tf.errors        | tf.errors.ResourceExhaustedError          |
| tf.errors        | tf.errors.UnauthenticatedError            |
| tf.errors        | tf.errors.UnimplementedError              |
| tf.errors        | tf.errors.UnknownError                    |
| tf.errors        | tf.errors.error_code_from_exception_type  |
| tf.errors        | tf.errors.exception_type_from_error_code  |

| Module            | Supported Python API                                |
|-------------------|-----------------------------------------------------|
| tf.estimator      | tf.estimator.Estimator                              |
| tf.estimator      | tf.estimator.EstimatorSpec                          |
| tf.estimator      | tf.estimator.ModeKeys                               |
| tf.estimator      | tf.estimator.WarmStartSettings                      |
| tf.estimator      | tf.estimator.TrainSpec                              |
| tf.estimator      | tf.estimator.EvalSpec                               |
| tf.estimator      | tf.estimator.inputs.numpy_input_fn                  |
| tf.estimator      | tf.estimator.DNNClassifier                          |
| tf.estimator      | tf.estimator.DNNRegressor                           |
| tf.estimator      | tf.estimator.DNNLinearCombinedClassifier            |
| tf.estimator      | tf.estimator.LinearClassifier                       |
| tf.estimator      | tf.estimator.RunConfig                              |
| tf.estimator      | tf.estimator.export.build_parsing_serving_input_rec |
| tf.estimator      | tf.estimator.export.build_raw_serving_input_receive |
| tf.estimator      | tf.estimator.export.PredictOutput                   |
| tf.estimator      | tf.estimator.export.ServingInputReceiver            |
| tf.estimator      | tf.estimator.export.TensorServingInputReceiver      |
| tf.estimator      | tf.estimator.FinalExporter                          |
| tf.estimator      | tf.estimator.LatestExporter                         |
| tf.feature_column | tf.feature_column                                   |
| tf.feature_column | tf.feature_column.bucketized_column                 |
| tf.feature_column | tf.feature_column.categorical_column_with_hash_b    |
| tf.feature_column | tf.feature_column.categorical_column_with_vocab     |
| tf.feature_column | tf.feature_column.crossed_column                    |
| tf.feature_column | tf.feature_column.embedding_column                  |
| tf.feature_column | tf.feature_column.indicator_column                  |
| tf.feature_column | tf.feature_column.input_layer                       |
| tf.feature_column | tf.feature_column.make_parse_example_spec           |

| Module            | Supported Python API                              |
|-------------------|---------------------------------------------------|
| tf.feature_column | tf.feature_column.numeric_column                  |
| tf.feature_column | tf.feature_column.categorical_column_with_identit |
| tf.gfile          | tf.gfile.Copy                                     |
| tf.gfile          | tf.gfile.DeleteRecursively                        |
| tf.gfile          | tf.gfile.Exists                                   |
| tf.gfile          | tf.gfile.GFile                                    |
| tf.gfile          | tf.gfile.Glob                                     |
| tf.gfile          | tf.gfile.IsDirectory                              |
| tf.gfile          | tf.gfile.ListDirectory                            |
| tf.gfile          | tf.gfile.MakeDirs                                 |
| tf.gfile          | tf.gfile.MkDir                                    |
| tf.gfile          | tf.gfile.Open                                     |
| tf.gfile          | tf.gfile.Remove                                   |
| tf.gfile          | tf.gfile.Rename                                   |
| tf.gfile          | tf.gfile.Stat                                     |
| tf.gfile          | tf.gfile.Walk                                     |
| tf.gfile          | tf.gfile.FastGFile                                |
| tf.image          | tf.image.ResizeMethod                             |
| tf.image          | tf.image.adjust_brightness                        |
| tf.image          | tf.image.adjust_contrast                          |
| tf.image          | tf.image.adjust_hue                               |
| tf.image          | tf.image.adjust_saturation                        |
| tf.image          | tf.image.central_crop                             |
| tf.image          | tf.image.convert_image_dtype                      |
| tf.image          | tf.image.crop_and_resize                          |
| tf.image          | tf.image.crop_to_bounding_box                     |
| tf.image          | tf.image.decode_and_crop_jpeg                     |
| tf.image          | tf.image.decode_image                             |
| tf.image          | tf.image.decode_jpeg                              |

| Module          | Supported Python API                   |
|-----------------|----------------------------------------|
| tf.image        | tf.image.decode_png                    |
| tf.image        | tf.image.draw_bounding_boxes           |
| tf.image        | tf.image.encode_jpeg                   |
| tf.image        | tf.image.encode_png                    |
| tf.image        | tf.image.extract_jpeg_shape            |
| tf.image        | tf.image.flip_left_right               |
| tf.image        | tf.image.flip_up_down                  |
| tf.image        | tf.image.grayscale_to_rgb              |
| tf.image        | tf.image.non_max_suppression           |
| tf.image        | tf.image.non_max_suppression_padded    |
| tf.image        | tf.image.pad_to_bounding_box           |
| tf.image        | tf.image.per_image_standardization     |
| tf.image        | tf.image.random_brightness             |
| tf.image        | tf.image.random_contrast               |
| tf.image        | tf.image.random_flip_left_right        |
| tf.image        | tf.image.random_flip_up_down           |
| tf.image        | tf.image.random_hue                    |
| tf.image        | tf.image.random_saturation             |
| tf.image        | tf.image.resize_area                   |
| tf.image        | tf.image.resize_bicubic                |
| tf.image        | tf.image.resize_bilinear               |
| tf.image        | tf.image.resize_image_with_crop_or_pad |
| tf.image        | tf.image.resize_images                 |
| tf.image        | tf.image.resize_nearest_neighbor       |
| tf.image        | tf.image.rgb_to_grayscale              |
| tf.image        | tf.image.rot90                         |
| tf.image        | tf.image.sample_distorted_bounding_box |
| tf.image        | tf.image.decode_bmp                    |
| tf.image        | tf.image.decode_gif                    |
| tf.initializers | tf.initializers.he_uniform             |

| Module          | Supported Python API                  |
|-----------------|---------------------------------------|
| tf.initializers | tf.initializers.variables             |
| tf.keras        | tf.keras.layers.Lambda                |
| tf.keras        | tf.keras.utils.get_file               |
| tf.keras        | tf.keras.estimator                    |
| tf.keras        | tf.keras.estimator.model_to_estimator |
| tf.layers       | tf.layers.Dense                       |
| tf.layers       | tf.layers.Layer                       |
| tf.layers       | tf.layers.average_pooling2d           |
| tf.layers       | tf.layers.batch_normalization         |
| tf.layers       | tf.layers.conv2d                      |
| tf.layers       | tf.layers.dense                       |
| tf.layers       | tf.layers.dropout                     |
| tf.layers       | tf.layers.flatten                     |
| tf.layers       | tf.layers.max_pooling2d               |
| tf.layers       | tf.layers.separable_conv2d            |
| tf.layers       | tf.layers.InputSpec                   |
| tf.logging      | tf.logging.debug                      |
| tf.logging      | tf.logging.error                      |
| tf.logging      | tf.logging.fatal                      |
| tf.logging      | tf.logging.get_verbosity              |
| tf.logging      | tf.logging.info                       |
| tf.logging      | tf.logging.log                        |
| tf.logging      | tf.logging.log_every_n                |
| tf.logging      | tf.logging.set_verbosity              |
| tf.logging      | tf.logging.warn                       |
| tf.logging      | tf.logging.warning                    |
| tf.losses       | tf.losses.Reduction                   |
| tf.losses       | tf.losses.add_loss                    |
| tf.losses       | tf.losses.compute_weighted_loss       |
| tf.losses       | tf.losses.get_losses                  |

| Module     | Supported Python API                   |
|------------|----------------------------------------|
| tf.losses  | tf.losses.get_total_loss               |
| tf.losses  | tf.losses.huber_loss                   |
| tf.losses  | tf.losses.log_loss                     |
| tf.losses  | tf.losses.mean_squared_error           |
| tf.losses  | tf.losses.sigmoid_cross_entropy        |
| tf.losses  | tf.losses.softmax_cross_entropy        |
| tf.losses  | tf.losses.sparse_softmax_cross_entropy |
| tf.losses  | tf.losses.get_regularization_loss      |
| tf.manip   | tf.manip.roll                          |
| tf.manip   | tf.manip.space_to_batch_nd             |
| tf.math    | tf.math.exp                            |
| tf.math    | tf.math.acosh                          |
| tf.math    | tf.math.asinh                          |
| tf.math    | tf.math.atan                           |
| tf.math    | tf.math.atan2                          |
| tf.math    | tf.math.atanh                          |
| tf.math    | tf.math.cosh                           |
| tf.math    | tf.math.expm1                          |
| tf.math    | tf.math.log1p                          |
| tf.math    | tf.math.reciprocal                     |
| tf.math    | tf.math.segment_max                    |
| tf.math    | tf.math.sinh                           |
| tf.math    | tf.math.tan                            |
| tf.math    | tf.math.unsorted_segment_mean          |
| tf.math    | tf.math.unsorted_segment_prod          |
| tf.math    | tf.math.unsorted_segment_sqrt_n        |
| tf.math    | tf.math.xdivy                          |
| tf.math    | tf.math.xlogy                          |
| tf.metrics | tf.metrics.accuracy                    |
| tf.metrics | tf.metrics.auc                         |

| Module     | Supported Python API                     |
|------------|------------------------------------------|
| tf.metrics | tf.metrics.mean                          |
| tf.metrics | tf.metrics.mean_iou                      |
| tf.metrics | tf.metrics.recall_at_k                   |
| tf.metrics | tf.metrics.root_mean_squared_error       |
| tf.metrics | tf.metrics                               |
| tf.metrics | tf.metrics.false_negatives               |
| tf.metrics | tf.metrics.false_negatives_at_thresholds |
| tf.metrics | tf.metrics.false_positives               |
| tf.metrics | tf.metrics.false_positives_at_thresholds |
| tf.metrics | tf.metrics.mean_absolute_error           |
| tf.metrics | tf.metrics.mean_cosine_distance          |
| tf.metrics | tf.metrics.mean_per_class_accuracy       |
| tf.metrics | tf.metrics.mean_relative_error           |
| tf.metrics | tf.metrics.mean_tensor                   |
| tf.metrics | tf.metrics.percentage_below              |
| tf.metrics | tf.metrics.precision                     |
| tf.metrics | tf.metrics.precision_at_thresholds       |
| tf.metrics | tf.metrics.precision_at_top_k            |
| tf.metrics | tf.metrics.recall                        |
| tf.metrics | tf.metrics.recall_at_thresholds          |
| tf.metrics | tf.metrics.sensitivity_at_specificity    |
| tf.metrics | tf.metrics.sparse_average_precision_at_k |
| tf.metrics | tf.metrics.sparse_precision_at_k         |
| tf.metrics | tf.metrics.true_negatives                |
| tf.metrics | tf.metrics.true_negatives_at_thresholds  |
| tf.metrics | tf.metrics.true_positives                |
| tf.metrics | tf.metrics.true_positives_at_thresholds  |
| tf.nest    | tf.nest.flatten                          |
| tf.nest    | tf.nest.map_structure                    |
| tf.nest    | tf.nest.pack_sequence_as                 |

| Module | Supported Python API                    |
|--------|-----------------------------------------|
| tf.nn  | tf.nn.avg_pool                          |
| tf.nn  | tf.nn.batch_normalization               |
| tf.nn  | tf.nn.bias_add                          |
| tf.nn  | tf.nn.bidirectional_dynamic_rnn         |
| tf.nn  | tf.nn.conv2d                            |
| tf.nn  | tf.nn.convolution                       |
| tf.nn  | tf.nn.depthwise_conv2d                  |
| tf.nn  | tf.nn.dropout                           |
| tf.nn  | tf.nn.dynamic_rnn                       |
| tf.nn  | tf.nn.embedding_lookup                  |
| tf.nn  | tf.nn.fused_batch_norm                  |
| tf.nn  | tf.nn.in_top_k                          |
| tf.nn  | tf.nn.l2_loss                           |
| tf.nn  | tf.nn.l2_normalize                      |
| tf.nn  | tf.nn.leaky_relu                        |
| tf.nn  | tf.nn.log_softmax                       |
| tf.nn  | tf.nn.lrn                               |
| tf.nn  | tf.nn.max_pool                          |
| tf.nn  | tf.nn.moments                           |
| tf.nn  | tf.nn.relu                              |
| tf.nn  | tf.nn.relu6                             |
| tf.nn  | tf.nn.rnn_cell.BasicLSTMCell            |
| tf.nn  | tf.nn.rnn_cell.GRUCell                  |
| tf.nn  | tf.nn.rnn_cell.LSTMCell                 |
| tf.nn  | tf.nn.rnn_cell.LSTMStateTuple           |
| tf.nn  | tf.nn.rnn_cell.RNNCell                  |
| tf.nn  | tf.nn.sampled_softmax_loss              |
| tf.nn  | tf.nn.separable_conv2d                  |
| tf.nn  | tf.nn.sigmoid                           |
| tf.nn  | tf.nn.sigmoid_cross_entropy_with_logits |

| Module       | Supported Python API                           |
|--------------|------------------------------------------------|
| tf.nn        | tf.nn.softmax                                  |
| tf.nn        | tf.nn.softmax_cross_entropy_with_logits        |
| tf.nn        | tf.nn.softmax_cross_entropy_with_logits_v2     |
| tf.nn        | tf.nn.softplus                                 |
| tf.nn        | tf.nn.softsign                                 |
| tf.nn        | tf.nn.sparse_softmax_cross_entropy_with_logits |
| tf.nn        | tf.nn.static_rnn                               |
| tf.nn        | tf.nn.tanh                                     |
| tf.nn        | tf.nn.top_k                                    |
| tf.nn        | tf.nn.xw_plus_b                                |
| tf.nn        | tf.nn.zero_fraction                            |
| tf.nn        | tf.nn.crelu                                    |
| tf.nn        | tf.nn.elu                                      |
| tf.nn        | tf.nn.max_pool_with_argmax                     |
| tf.nn        | tf.nn.normalize_moments                        |
| tf.nn        | tf.nn.rnn_cell                                 |
| tf.nn        | tf.nn.rnn_cell.BasicRNNCell                    |
| tf.nn        | tf.nn.rnn_cell.MultiRNNCell                    |
| tf.nn        | tf.nn.selu                                     |
| tf.nn        | tf.nn.static_bidirectional_rnn                 |
| tf.nn        | tf.nn.sufficient_statistics                    |
| tf.python_io | tf.python_io.TFRecordCompressionType           |
| tf.python_io | tf.python_io.TFRecordWriter                    |
| tf.python_io | tf.python_io.tf_record_iterator                |
| tf.python_io | tf.python_io.TFRecordOptions                   |
| tf.random    | tf.random_crop                                 |
| tf.random    | tf.random_normal                               |
| tf.random    | tf.random_normal_initializer                   |
| tf.random    | tf.random_shuffle                              |
| tf.random    | tf.random_uniform                              |

| Module             | Supported Python API                               |
|--------------------|----------------------------------------------------|
| tf.random          | tf.random_uniform_initializer                      |
| tf.resource_loader | tf.resource_loader.get_data_files_path             |
| tf.saved_model     | tf.saved_model                                     |
| tf.saved_model     | tf.saved_model.builder.SavedModelBuilder           |
| tf.saved_model     | tf.saved_model.signature_constants                 |
| tf.saved_model     | tf.saved_model.tag_constants                       |
| tf.saved_model     | tf.saved_model.utils                               |
| tf.saved_model     | tf.saved_model.save                                |
| tf.saved_model     | tf.saved_model.Builder                             |
| tf.saved_model     | tf.saved_model.loader.load                         |
| tf.saved_model     | tf.saved_model.signature_def_utils.build_signature |
| tf.saved_model     | tf.saved_model.utils.build_tensor_info             |
| tf.sparse          | tf.sparse.to_dense                                 |
| tf.sparse          | tf.sparse_tensor_to_dense                          |
| tf.sparse          | tf.SparseConditionalAccumulator                    |
| tf.spectral        | tf.spectral.dct                                    |
| tf.spectral        | tf.spectral.idct                                   |
| tf.strings         | tf.strings.substr                                  |
| tf.strings         | tf.strings.to_number                               |
| tf.summary         | tf.Summary                                         |
| tf.summary         | tf.Summary.Image                                   |
| tf.summary         | tf.Summary.Value                                   |
| tf.summary         | tf.summary.FileWriter                              |
| tf.summary         | tf.summary.FileWriterCache                         |
| tf.summary         | tf.summary.histogram                               |
| tf.summary         | tf.summary.image                                   |
| tf.summary         | tf.summary.merge                                   |
| tf.summary         | tf.summary.merge_all                               |
| tf.summary         | tf.summary.scalar                                  |

| Module       | Supported Python API               |
|--------------|------------------------------------|
| tf.summary   | tf.summary.text                    |
| tf.sysconfig | tf.sysconfig.get_compile_flags     |
| tf.sysconfig | tf.sysconfig.get_link_flags        |
| tf.test      | tf.test.TestCase                   |
| tf.test      | tf.test.get_temp_dir               |
| tf.test      | tf.test.main                       |
| tf.train     | tf.train.Scaffold                  |
| tf.train     | tf.trainable_variables             |
| tf.train     | tf.train.cosine_decay              |
| tf.train     | tf.train.exponential_decay         |
| tf.train     | tf.train.piecewise_constant        |
| tf.train     | tf.train.polynomial_decay          |
| tf.train     | tf.train.create_global_step        |
| tf.train     | tf.train.get_global_step           |
| tf.train     | tf.train.get_or_create_global_step |
| tf.train     | tf.train.global_step               |
| tf.train     | tf.train.Saver                     |
| tf.train     | tf.train.Checkpoint                |
| tf.train     | tf.train.CheckpointSaverHook       |
| tf.train     | tf.train.checkpoint_exists         |
| tf.train     | tf.train.NewCheckpointReader       |
| tf.train     | tf.train.get_checkpoint_state      |
| tf.train     | tf.train.init_from_checkpoint      |
| tf.train     | tf.train.latest_checkpoint         |
| tf.train     | tf.train.load_checkpoint           |
| tf.train     | tf.train.import_meta_graph         |
| tf.train     | tf.train.list_variables            |
| tf.train     | tf.train.BytesList                 |
| tf.train     | tf.train.FloatList                 |
| tf.train     | tf.train.Int64List                 |

| Module   | Supported Python API                     |
|----------|------------------------------------------|
| tf.train | tf.train.Feature                         |
| tf.train | tf.train.Features                        |
| tf.train | tf.train.FeatureList                     |
| tf.train | tf.train.FeatureLists                    |
| tf.train | tf.train.Example                         |
| tf.train | tf.train.SequenceExample                 |
| tf.train | tf.train.Optimizer                       |
| tf.train | tf.train.AdadeltaOptimizer               |
| tf.train | tf.train.AdamOptimizer                   |
| tf.train | tf.train.GradientDescentOptimizer        |
| tf.train | tf.train.MomentumOptimizer               |
| tf.train | tf.train.RMSPropOptimizer                |
| tf.train | tf.train.MonitoredSession                |
| tf.train | tf.train.Coordinator                     |
| tf.train | tf.train.SessionRunHook                  |
| tf.train | tf.train.SessionRunArgs                  |
| tf.train | tf.train.LoggingTensorHook               |
| tf.train | tf.train.ProfilerHook                    |
| tf.train | tf.train.SummarySaverHook                |
| tf.train | tf.train.write_graph                     |
| tf.train | tf.train.SecondOrStepTimer               |
| tf.train | tf.train.AdagradOptimizer                |
| tf.train | tf.train.ChiefSessionCreator             |
| tf.train | tf.train.ExponentialMovingAverage        |
| tf.train | tf.train.FtrlOptimizer                   |
| tf.train | tf.train.MonitoredTrainingSession        |
| tf.train | tf.train.SyncReplicasOptimizer           |
| tf.train | tf.train.cosine_decay_restarts           |
| tf.train | tf.train.generate_checkpoint_state_proto |
| tf.train | tf.train.linear_cosine_decay             |

| Module   | Supported Python API              |
|----------|-----------------------------------|
| tf.train | tf.train.load_variable            |
| tf.train | tf.train.start_queue_runners      |
| tf.train | tf.train.summary_iterator         |
| tf.train | tf.train.assert_global_step       |
| tf.train | tf.train.queue_runner             |
| tf.train | tf.train.SessionRunHook           |
| tf.train | tf.train.CheckpointManager        |
| tf.train | tf.train.CheckpointSaverHook      |
| tf.train | tf.train.checkpoints_iterator     |
| tf.train | tf.train.piecewise_constant_decay |

### **Unsupported Python APIs**

The following table lists part of the unsupported Python APIs.

| Module     | Unsupported Python API                    |
|------------|-------------------------------------------|
| tf         | tf.py_func                                |
| tf.metrics | tf.metrics.specificity_at_sensitivity     |
| tf.nn      | tf.nn.ctc_loss                            |
| tf         | tf.device                                 |
| tf         | tf.SparseTensor                           |
| tf         | tf.map_fn                                 |
| tf         | tf.reset_default_graph                    |
| tf         | tf.GPUOptions                             |
| tf         | tf.GPUOptions.Experimental                |
| tf         | tf.GPUOptions.Experimental.VirtualDevices |
| tf         | tf.RunOptions.Experimental                |
| tf         | tf.executing_eagerly                      |
| tf         | tf.enable_eager_execution                 |
| tf         | tf.autograph                              |
| tf         | tf.distribute                             |

| Module       | Unsupported Python API                       |
|--------------|----------------------------------------------|
| tf           | tf.disable_v2_tensorshape                    |
| tf           | tf.enable_control_flow_v2                    |
| tf           | tf.enable_tensor_equality                    |
| tf           | tf.enable_v2_behavior                        |
| tf           | tf.enable_v2_tensorshape                     |
| tf           | tf.CriticalSection                           |
| tf           | tf.IndexedSlicesSpec                         |
| tf           | tf.Module                                    |
| tf           | tf.OptionalSpec                              |
| tf           | tf.RaggedTensor                              |
| tf           | tf.function                                  |
| tf           | tf.disable_control_flow_v2                   |
| tf           | tf.disable_eager_execution                   |
| tf           | tf.disable_tensor_equality                   |
| tf           | tf.disable_v2_behavior                       |
| tf.config    | tf.ConfigProto.Experimental                  |
| tf.config    | tf.ConfigProto.Experimental                  |
| tf.config    | tf.config.get_soft_device_placement          |
| tf.config    | tf.config.optimizer.get_experimental_options |
| tf.config    | tf.config.optimizer.get_jit                  |
| tf.config    | tf.config.optimizer.set_experimental_options |
| tf.config    | tf.config.optimizer.set_jit                  |
| tf.config    | tf.config.set_soft_device_placement          |
| tf.estimator | tf.estimator.train_and_evaluate              |
| tf.estimator | tf.estimator.tpu.TPUConfig                   |
| tf.estimator | tf.estimator.tpu.RunConfig                   |
| tf.estimator | tf.estimator.tpu.TPUEstimatorSpec            |
| tf.estimator | tf.estimator.tpu.InputPipelineConfig         |
| tf.estimator | tf.estimator.tpu.TPUEstimator                |
| tf.keras     | tf.keras.models.Sequential                   |

| Module   | Unsupported Python API                              |
|----------|-----------------------------------------------------|
| tf.keras | tf.keras.utils.multi_gpu_model                      |
| tf.keras | tf.keras.preprocessing.image.DirectoryIterator      |
| tf.keras | tf.keras.preprocessing.text.hashing_trick           |
| tf.keras | tf.keras.preprocessing.text.one_hot                 |
| tf.keras | tf.keras.preprocessing.text.text_to_word_sequence   |
| tf.keras | tf.keras.utils.model_to_dot                         |
| tf.keras | tf.keras.preprocessing.sequence.pad_sequences       |
| tf.keras | tf.keras.preprocessing.sequence.skipgrams           |
| tf.keras | tf.keras.preprocessing.text.Tokenizer               |
| tf.keras | tf.keras.preprocessing.image.ImageDataGenerator     |
| tf.keras | tf.keras.preprocessing.image.Iterator               |
| tf.keras | tf.keras.preprocessing.image.NumpyArrayIterator     |
| tf.keras | tf.keras.preprocessing.image.apply_affine_transfor  |
| tf.keras | tf.keras.preprocessing.image.apply_brightness_shift |
| tf.keras | tf.keras.preprocessing.image.apply_channel_shift    |
| tf.keras | tf.keras.preprocessing.image.array_to_img           |
| tf.keras | tf.keras.preprocessing.image.img_to_array           |
| tf.keras | tf.keras.preprocessing.image.load_img               |
| tf.keras | tf.keras.preprocessing.image.random_brightness      |
| tf.keras | tf.keras.preprocessing.image.random_zoom            |
| tf.keras | tf.keras.preprocessing.image.save_img               |
| tf.keras | tf.keras.preprocessing.sequence.TimeseriesGenera   |
| tf.keras | tf.keras.preprocessing.sequence.make_sampling_ta    |
| tf.keras | tf.keras.preprocessing.image.random_rotation        |
| tf.keras | tf.keras.preprocessing.image.random_shift           |
| tf.keras | tf.keras.preprocessing.image.random_shear           |
| tf.keras | tf.keras.preprocessing.image.random_channel_shift   |
| tf.math  | tf.math.segment_mean                                |

| Module      | Unsupported Python API                      |
|-------------|---------------------------------------------|
| tf.math     | tf.math.segment_min                         |
| tf.math     | tf.math.segment_prod                        |
| tf.math     | tf.math.segment_sum                         |
| tf.profiler | tf.profiler.ProfileOptionBuilder            |
| tf.profiler | tf.profiler.profile                         |
| tf.profiler | tf.profiler.Profiler                        |
| tf.profiler | tf.profiler.AdviceProto                     |
| tf.profiler | tf.profiler.AdviceProto.Checker             |
| tf.profiler | tf.profiler.AdviceProto.CheckersEntry       |
| tf.profiler | tf.profiler.GraphNodeProto                  |
| tf.profiler | tf.profiler.GraphNodeProto.InputShapesEntry |
| tf.profiler | tf.profiler.MultiGraphNodeProto             |
| tf.profiler | tf.profiler.OpLogProto                      |
| tf.profiler | tf.profiler.OpLogProto.IdToStringEntry      |
| tf.profiler | tf.profiler.advise                          |
| tf.profiler | tf.profiler.write_op_log                    |
| tf.summary  | tf.summary.all_v2_summary_ops               |
| tf.train    | tf.train.ClusterSpec                        |
| tf.train    | tf.train.replica_device_setter              |
| tf.train    | tf.train.Server                             |
| tf.train    | tf.train.ClusterDef                         |
| tf.train    | tf.train.JobDef                             |
| tf.train    | tf.train.JobDef.TasksEntry                  |

## **Deprecated Python APIs**

The following table lists the deprecated TensorFlow Python APIs. You are not advised to use them.

| Module | Deprecated Python API |
|--------|-----------------------|
| tf     | tf.op_scope           |
| tf     | tf.all_variables      |

| Module     | Deprecated Python API                          |
|------------|------------------------------------------------|
| tf         | tf.colocate_with                               |
| tf         | tf.initialize_all_variables                    |
| tf         | tf.initialize_variables                        |
| tf         | tf.Print                                       |
| tf         | tf.sparse_to_dense                             |
| tf         | tf.initialize_all_tables                       |
| tf         | tf.initialize_local_variables                  |
| tf         | tf.disable_resource_variables                  |
| tf.contrib | tf.contrib                                     |
| tf.contrib | tf.contrib.cluster_resolver.TPUClusterResolver |
| tf.contrib | tf.contrib.cudnn_rnn.CudnnCompatibleLSTMCell   |
| tf.contrib | tf.contrib.data.batch_and_drop_remainder       |
| tf.contrib | tf.contrib.data.bucket_by_sequence_length      |
| tf.contrib | tf.contrib.data.group_by_window                |
| tf.contrib | tf.contrib.data.map_and_batch                  |
| tf.contrib | tf.contrib.data.parallel_interleave            |
| tf.contrib | tf.contrib.distribute.MirroredStrategy         |
| tf.contrib | tf.contrib.distribute.OneDeviceStrategy        |
| tf.contrib | tf.contrib.framework.add_arg_scope             |
| tf.contrib | tf.contrib.framework.arg_scope                 |
| tf.contrib | tf.contrib.framework.deprecated                |
| tf.contrib | tf.contrib.framework.filter_variables          |
| tf.contrib | tf.contrib.framework.get_variables             |
| tf.contrib | tf.contrib.framework.is_tensor                 |
| tf.contrib | tf.contrib.framework.model_variable            |
| tf.contrib | tf.contrib.image                               |
| tf.contrib | tf.contrib.layers                              |
| tf.contrib | tf.contrib.layers.flatten                      |
| tf.contrib | tf.contrib.layers.fully_connected              |
| tf.contrib | tf.contrib.layers.l2_regularizer               |

| Module     | Deprecated Python API                           |
|------------|-------------------------------------------------|
| tf.contrib | tf.contrib.layers.optimize_loss                 |
| tf.contrib | tf.contrib.layers.summarize_collection          |
| tf.contrib | tf.contrib.layers.variance_scaling_initializer  |
| tf.contrib | tf.contrib.layers.xavier_initializer            |
| tf.contrib | tf.contrib.learn.Experiment                     |
| tf.contrib | tf.contrib.learn.ModeKeys                       |
| tf.contrib | tf.contrib.learn.utils                          |
| tf.contrib | tf.contrib.lookup.HashTable                     |
| tf.contrib | tf.contrib.lookup.KeyValueTensorInitializer     |
| tf.contrib | tf.contrib.opt.LazyAdamOptimizer                |
| tf.contrib | tf.contrib.opt.MovingAverageOptimizer           |
| tf.contrib | tf.contrib.quantize                             |
| tf.contrib | tf.contrib.quantize.create_eval_graph           |
| tf.contrib | tf.contrib.quantize.create_training_graph       |
| tf.contrib | tf.contrib.rnn.DropoutWrapper                   |
| tf.contrib | tf.contrib.rnn.LSTMCell                         |
| tf.contrib | tf.contrib.slim                                 |
| tf.contrib | tf.contrib.tfprof                               |
| tf.contrib | tf.contrib.tpu                                  |
| tf.contrib | tf.contrib.tpu.CrossShardOptimizer              |
| tf.contrib | tf.contrib.tpu.RunConfig                        |
| tf.contrib | tf.contrib.tpu.TPUConfig                        |
| tf.contrib | tf.contrib.tpu.TPUEstimator                     |
| tf.contrib | tf.contrib.tpu.TPUEstimatorSpec                 |
| tf.contrib | tf.contrib.tpu.bfloat16_scope                   |
| tf.contrib | tf.contrib.tpu.initialize_system                |
| tf.contrib | tf.contrib.tpu.rewrite                          |
| tf.contrib | tf.contrib.tpu.shutdown_system                  |
| tf.contrib | tf.contrib.training.GreedyLoadBalancingStrategy |
| tf.contrib | tf.contrib.training.HParams                     |

| Module     | Deprecated Python API                             |
|------------|---------------------------------------------------|
| tf.contrib | tf.contrib.training.StopAfterNEvalsHook           |
| tf.contrib | tf.contrib.training.SummaryAtEndHook              |
| tf.contrib | tf.contrib.training.checkpoints_iterator          |
| tf.contrib | tf.contrib.training.evaluate_repeatedly           |
| tf.contrib | tf.contrib.all_reduce                             |
| tf.contrib | tf.contrib.data.padded_batch_and_drop_remainder   |
| tf.contrib | tf.contrib.data.unbatch                           |
| tf.contrib | tf.contrib.estimator.clip_gradients_by_norm       |
| tf.contrib | tf.contrib.framework.argsort                      |
| tf.contrib | tf.contrib.framework.assign_from_checkpoint_fn    |
| tf.contrib | tf.contrib.framework.get_variables_to_restore     |
| tf.contrib | tf.contrib.framework.local_variable               |
| tf.contrib | tf.contrib.framework.smart_cond                   |
| tf.contrib | tf.contrib.layers.apply_regularization            |
| tf.contrib | tf.contrib.layers.batch_norm                      |
| tf.contrib | tf.contrib.layers.bow_encoder                     |
| tf.contrib | tf.contrib.layers.conv2d                          |
| tf.contrib | tf.contrib.layers.layer_norm                      |
| tf.contrib | tf.contrib.learn                                  |
| tf.contrib | tf.contrib.learn.read_batch_features              |
| tf.contrib | tf.contrib.lookup.index_table_from_file           |
| tf.contrib | tf.contrib.lookup.index_table_from_tensor         |
| tf.contrib | tf.contrib.lookup.index_to_string_table_from_file |
| tf.contrib | tf.contrib.metrics.aggregate_metric_map           |
| tf.contrib | tf.contrib.metrics.streaming_pearson_correlation  |
| tf.contrib | tf.contrib.opt.LARSOptimizer                      |
| tf.contrib | tf.contrib.rnn.DeviceWrapper                      |
| tf.contrib | tf.contrib.rnn.LayerNormBasicLSTMCell             |
| tf.contrib | tf.contrib.rnn.MultiRNNCell                       |
| tf.contrib | tf.contrib.rnn.NASCell                            |

| Module         | Deprecated Python API                            |
|----------------|--------------------------------------------------|
| tf.contrib     | tf.contrib.rnn.ResidualWrapper                   |
| tf.contrib     | tf.contrib.saved_model                           |
| tf.contrib     | tf.contrib.seq2seq.AttentionWrapper              |
| tf.contrib     | tf.contrib.seq2seq.AttentionWrapperState         |
| tf.contrib     | tf.contrib.seq2seq.BahdanauAttention             |
| tf.contrib     | tf.contrib.seq2seq.BasicDecoder                  |
| tf.contrib     | tf.contrib.seq2seq.BeamSearchDecoder             |
| tf.contrib     | tf.contrib.seq2seq.GreedyEmbeddingHelper         |
| tf.contrib     | tf.contrib.seq2seq.Helper                        |
| tf.contrib     | tf.contrib.seq2seq.LuongAttention                |
| tf.contrib     | tf.contrib.seq2seq.SampleEmbeddingHelper         |
| tf.contrib     | tf.contrib.seq2seq.TrainingHelper                |
| tf.contrib     | tf.contrib.seq2seq.dynamic_decode                |
| tf.contrib     | tf.contrib.seq2seq.tile_batch                    |
| tf.contrib     | tf.contrib.signal.inverse_stft                   |
| tf.contrib     | tf.contrib.signal.stft                           |
| tf.contrib     | tf.contrib.staging.StagingArea                   |
| tf.contrib     | tf.contrib.summary                               |
| tf.contrib     | tf.contrib.tensorboard                           |
| tf.contrib     | tf.contrib.training.byte_size_load_fn            |
| tf.graph_util  | tf.graph_util.convert_variables_to_constants     |
| tf.graph_util  | tf.graph_util.extract_sub_graph                  |
| tf.graph_util  | tf.graph_util.must_run_on_cpu                    |
| tf.graph_util  | tf.graph_util.remove_training_nodes              |
| tf.graph_util  | tf.graph_util.tensor_shape_from_node_def_name    |
| tf.saved_model | tf.saved_model.get_tensor_from_tensor_info       |
| tf.saved_model | tf.saved_model.main_op.main_op_with_restore      |
| tf.saved_model | tf.saved_model.main_op_with_restore              |
| tf.saved_model | tf.saved_model.simple_save                       |
| tf.saved_model | tf.saved_model.utils.get_tensor_from_tensor_info |

<span id="page-114-0"></span>

| Module   | Deprecated Python API                     |
|----------|-------------------------------------------|
| tf.train | tf.train.QueueRunner                      |
| tf.train | tf.train.add_queue_runner                 |
| tf.train | tf.train.batch                            |
| tf.train | tf.train.batch_join                       |
| tf.train | tf.train.range_input_producer             |
| tf.train | tf.train.slice_input_producer             |
| tf.train | tf.train.Supervisor                       |
| tf.train | tf.train.shuffle_batch                    |
| tf.train | tf.train.string_input_producer            |
| tf.train | tf.train.input_producer                   |
| tf.train | tf.train.do_quantize_training_on_graphdef |
| tf.train | tf.train.limit_epochs                     |
| tf.train | tf.train.maybe_batch                      |
| tf.train | tf.train.maybe_batch_join                 |
| tf.train | tf.train.maybe_shuffle_batch              |
| tf.train | tf.train.maybe_shuffle_batch_join         |
| tf.train | tf.train.shuffle_batch_join               |
| tf.train | tf.train.queue_runner.QueueRunner         |
| tf.train | tf.train.queue_runner.add_queue_runner    |

# **10.2.2 TF Adapter APIs**

#### **10.2.2.1 Overview**

Since TF Adapter provides APIs adapted to the TensorFlow framework, you can develop TensorFlow-based training scripts.

#### <span id="page-115-0"></span>**Figure 10-1** TF Adapter

To use TF Adapter, the TFPlugin software package must be installed.

- If the **--pylocal** mode is used for the installation of TFPlugin, the corresponding .whl is installed in **/tfplugin/python/site-packages/** in the TFPlugin installation path.
- If the **--pylocal** mode is not used to for the installation of TFPlugin, the corresponding .whl is installed in the local Python path.

#### **10.2.2.2 npu\_bridge.estimator.npu.npu\_config**

#### **10.2.2.2.1 NPURunConfig Constructor**

#### **Prototype**

def \_\_init\_\_(self,

iterations\_per\_loop=1,

profiling\_config=None,

model\_dir=None,

tf\_random\_seed=None,

save\_summary\_steps=0,

save\_checkpoints\_steps=None,

save\_checkpoints\_secs=None,

session\_config=None,

keep\_checkpoint\_max=5,

keep\_checkpoint\_every\_n\_hours=10000,

log\_step\_count\_steps=100,

distribute=None,

enable\_data\_pre\_proc=True,

precision\_mode=None,

variable\_format\_optimize=True, mix\_compile\_mode=False, hcom\_parallel=False, graph\_memory\_max\_size=None, variable\_memory\_max\_size=None, auto\_tune\_mode=None, dump\_config=None, stream\_max\_parallel\_num=None, is\_tailing\_optimization=False, horovod\_mode = False, graph\_run\_mode = 1, op\_debug\_level = 0, enable\_scope\_fusion\_passes = None, enable\_exception\_dump = 0, op\_select\_implmode=None, optypelist\_for\_implmode=None, dynamic\_input\_config=None, mstune\_mode=None, work\_path=None, buffer\_optimize="l2\_optimize", enable\_small\_channel=0, fusion\_switch\_file=None, enable\_compress\_weight=False, compress\_weight\_conf=None, op\_compiler\_cache\_mode=None, op\_compiler\_cache\_dir=None, debug\_dir=None )

### **Description**

Constructor of the **NPURunConfig** class, which inherits the **RunConfig** class and can call the native APIs of the base class.

#### **Restrictions**

In multi-device training scenarios, the **save\_checkpoints\_secs** parameter cannot be used to save files by time.

#### **Parameters**

| Parameter Description                                                        |                              |
|------------------------------------------------------------------------------|------------------------------|
| save_checkpoints_steps Interval (in steps) for saving the checkpoint         |                              |
| file. Defaults to                                                            | None                         |
| ● This parameter and                                                         |                              |
| save_checkpoints_secs                                                        | are mutually                 |
| ● If save_checkpoints_steps                                                  | and                          |
| save_checkpoints_secs                                                        | are set to None ,            |
| ● If the value of                                                            | iterations_per_loop is       |
| save_checkpoints_steps                                                       | must be a                    |
| iterations_per_loop                                                          | ; otherwise, checkpoint      |
| save_checkpoints_steps=50 if hvd.rank() == 0 else                            | None ,                       |
| save_checkpoints_steps=50 if rank_id == 0 else                               | 0 ,                          |
| save_checkpoints_secs Interval (in seconds) for saving the checkpoint        |                              |
| file. Defaults to                                                            | None                         |
| This parameter and                                                           | save_checkpoints_steps       |
| session_config ConfigProto format object for session                         |                              |
| configuration. Defaults to                                                   | None                         |
| keep_checkpoint_max Maximum number of checkpoint files that can              |                              |
| be stored. Defaults to                                                       | 5                            |
| keep_checkpoint_every_n_hours Number of hours to retain the checkpoint file. |                              |
| Defaults to                                                                  | 10000 . This function can be |
| keep_checkpoint_max                                                          | to a large value.            |

| Parameter               | Description                                    |
|-------------------------|------------------------------------------------|
| log_step_count_steps    | Interval (in steps) for recording the global   |
|                         | step and loss values. Defaults to 100          |
|                         | iterations_per_loop = 1 . If                   |
|                         | iterations_per_loop > 1 , the configured value |
| NPURunConfig            | does not support the following parameters:     |
| train_distribute        | Indicates whether to perform distributed       |
|                         | specified by experimental_distribute           |
|                         | NPUDistributedOptimizer class, to construct    |
| device_fn               | Obtains the function of the Device field of    |
| protocol                | Protocol used to start the server. This        |
| eval_distribute         | Indicates whether to perform distributed       |
|                         | specified by experimental_distribute           |
| experimental_distribute | Distributed configuration                      |
| The following           | NPURunConfig parameters are added:             |

| Parameter Description |                                        |                                           |
|-----------------------|----------------------------------------|-------------------------------------------|
| iterations_per_loop   | Number of iterations per training loop |                                           |
|                       | performed on the per                   | sess.run() call. Defaults                 |
| to 1                  |                                        | . The total number of training iterations |
| value of              | iterations_per_loop                    | . Training is                             |
|                       | of iterations per loop (               | iterations_per_loop )                     |
| (                     | mix_compile_mode                       | is set to True ), this                    |
|                       | parameter must be set to               | 1                                         |
| Note: When            | iterations_per_loop                    | is set to a                               |
| profiling_config      | Profiling switch. Before creating      |                                           |
| NPURunConfig          |                                        | , you can instantiate a                   |
| ProfilingConfig       |                                        | class to configure profiling.             |
| ProfilingConfig       | class, see                             | 10.2.2.2.2                                |
| dump_config           | Dump switch. Before creating           | NPURunConfig ,                            |
|                       | you can instantiate a                  | DumpConfig class for                      |
|                       | constructor of the                     | DumpConfig class, see                     |
| enable_data_pre_proc  | Indicates whether to offload the data  |                                           |
| ● True                | (default): enabled                     |                                           |
| ● False               | : disabled                             |                                           |

| Parameter               | Description                                    |
|-------------------------|------------------------------------------------|
| op_select_implmode      | Operator implementation mode select. Some      |
|                         | ● high_precision : high precision              |
|                         | ● high_performance (default): high             |
| optypelist_for_implmode | List of operator types separated by commas     |
|                         | specified by the op_select_implmode            |
|                         | op_select_implmode parameter, for example:     |
| dynamic_input_config    | Dynamic dimension configuration. Before        |
|                         | creating NPURunConfig , you can instantiate    |
|                         | a DynamicInputConfig class to set related      |
|                         | the DynamicInputConfig class, see              |
| mstune_mode             | This parameter is not supported in the current |
| work_path               | This parameter is not supported in the current |
| buffer_optimize         | This parameter is not supported in the current |
| enable_small_channel    | This parameter is not supported in the current |

| Parameter              | Description                                    |
|------------------------|------------------------------------------------|
| fusion_switch_file     | Directory of the fusion switch configuration   |
|                        | fusion_switch.cfg fusion pattern               |
|                        | configuration file. on indicates that a fusion |
|                        | pattern is enabled, and off indicates that a   |
| enable_compress_weight | This parameter is not supported in the current |
| compress_weight_conf   | This parameter is not supported in the current |

<span id="page-130-0"></span>

#### **Returns**

An object of the **NPURunConfig** class is returned as the initialization parameter of **NPUEstimator**.

#### **Example**

The following describes how to construct a config instance in fully offloaded mode and setting the number of iterations per loop to **1000** as an example:

from npu\_bridge.estimator.npu.npu\_config import NPURunConfig

session\_config=tf.ConfigProto()

config = NPURunConfig(session\_config=session\_config, mix\_compile\_mode=False, iterations\_per\_loop=1000)

#### **10.2.2.2.2 ProfilingConfig Constructor**

#### **Prototype**

def \_\_init\_\_(self, enable\_profiling=False, profiling\_options=None )

#### **Description**

Constructs an object of class **ProfilingConfig** as the profiling configuration.

#### **Parameters**

| Parameter Input/                         |               |                                    |
|------------------------------------------|---------------|------------------------------------|
| enable_profiling Input Profiling enable. |               |                                    |
| ●                                        | True          | : enabled. The profiling option is |
|                                          | determined by | enable_options                     |
| ●                                        | False         | (default): disabled.               |

<span id="page-132-0"></span>

| Parameter Input/      |                                  |
|-----------------------|----------------------------------|
| tf.io.write_graph     | in the training script to        |
| ● aic_metrics         | : AI Core metric to profile.     |
| ArithmeticUtilization | : percentages of                 |
| PipeUtilization       | (default): percentages of        |
| Memory                | : percentages of external memory |
| MemoryL0              | : percentages of internal memory |
| ResourceConflictRatio | : percentages of                 |

#### **Returns**

An object of the **ProfilingConfig** class, as an argument passed to the **NPURunConfig** call.

#### **10.2.2.2.3 DumpConfig Constructor**

#### **Prototype**

def \_\_init\_\_(self, enable\_dump=False, dump\_path=None, dump\_step=None, dump\_mode="output", enable\_dump\_debug=False, dump\_debug\_mode="all")

#### **Description**

#### **Restrictions**

**enable\_dump** and **enable\_dump\_debug** cannot be both enabled.

#### **Parameters**

| Parameter Input/                                      |                                           |                                  |
|-------------------------------------------------------|-------------------------------------------|----------------------------------|
| enable_dump Input Data dump enable                    |                                           |                                  |
| ●                                                     | True                                      | : enabled. The dump file path is |
|                                                       | read from                                 | dump_path                        |
| ●                                                     | False                                     | (default): disabled              |
| dump_path Input Dump path. This parameter is required |                                           |                                  |
| when                                                  |                                           | enable_dump or                   |
| enable_dump_debug                                     |                                           | is set to True                   |
| ●                                                     | An absolute path starts with a slash (/), |                                  |
|                                                       | for example,                              | /home/HwHiAiUser/                |
| ●                                                     | A relative path starts with a directory   |                                  |
|                                                       | name, for example,                        | output                           |
| dump_step Input Iterations to dump. Defaults to       |                                           | None ,                           |
| dump_mode Input Dump mode.                            |                                           |                                  |
| ●                                                     | input                                     | : dumps only operator inputs.    |
| ●                                                     | output                                    | (default): dumps only operator   |
| ●                                                     | all                                       | : dumps both operator inputs and |
| enable_dump_debug Input Overflow detection enable.    |                                           |                                  |
| ●                                                     | True                                      | : enabled. The dump file path is |
|                                                       | read from                                 | dump_path . If dump_path is      |
|                                                       | set to                                    | None , an exception occurs.      |
| ●                                                     | False                                     | (default): disabled.             |

<span id="page-134-0"></span>

| Parameter Input/                               |                                     |                      |
|------------------------------------------------|-------------------------------------|----------------------|
| dump_debug_mode Input Overflow detection mode. |                                     |                      |
| ●                                              | aicore_overflow                     | : detects AI Core    |
| ●                                              | atomic_overflow                     | : detects Atomic Add |
| ●                                              | all (default): detects both AI Core |                      |

#### **Returns**

An object of the **DumpConfig** class, as an argument passed to the **NPURunConfig** call.

#### **10.2.2.3 npu\_bridge.estimator.npu.npu\_estimator**

#### **10.2.2.3.1 NPUEstimator Constructor**

#### **Prototype**

def \_\_init\_\_(self, model\_fn=None, model\_dir=None, config=None, params=None, job\_start\_file='' )

## **Description**

Constructor of the **NPUEstimator** class, which inherits the **Estimator** class and can call the native APIs of the base class.

<span id="page-135-0"></span>

| Parameter Input/                                                      |                                      |                              |
|-----------------------------------------------------------------------|--------------------------------------|------------------------------|
| model_fn Input Model function definition. This function returns the   |                                      |                              |
| NPUEstimatorSpec                                                      |                                      | class object.                |
| NPUEstimatorSpec                                                      |                                      | class, see 10.2.2.3.2        |
| model_dir Input Model storage path, which is used to save or restore  |                                      |                              |
| model files. Defaults to                                              |                                      | None                         |
| If model_dir                                                          | set in                               | NPURunConfig and             |
| NPUEstimator                                                          | are different, an error is reported. |                              |
| If either                                                             | NPURunConfig                         | or NPUEstimator is           |
| configured with                                                       | model_dir                            | , the configured path        |
| If neither                                                            | NPURunConfig                         | nor NPUEstimator is          |
| configured with                                                       | model_dir                            | , a                          |
| model_dir_                                                            | xxxxxxxxxx                           | directory is created in the  |
| config Input NPURunConfig                                             | class object                         |                              |
| NPURunConfig                                                          | class, see                           | 10.2.2.2.1                   |
| params Input Argument of                                              | model_fn                             | , which is of the dictionary |
| job_start_file Input Path of the CSA job startup file. This parameter |                                      |                              |

#### **Returns**

An object of the **NPUEstimator** class.

#### **10.2.2.3.2 NPUEstimatorSpec Constructor**

#### **Prototype**

def \_\_new\_\_(cls,

mode,

predictions=None,

loss=None,

eval\_metric\_ops=None, export\_outputs=None, training\_chief\_hooks=None, training\_hooks=None, scaffold=None, evaluation\_hooks=None, prediction\_hooks=None, host\_call=None)

#### **Description**

Constructor of the **NPUEstimatorSpec** class, which inherits the **EstimatorSpec** class and can call the native APIs of the base class.

**EstimatorSpec** is the return data structure of **model\_fn**, including the **mode**, **predictions**, **loss**, **train\_op**, and **export\_outputs** fields. If **EstimatorSpec** cannot meet the training requirements, define **NPUEstimatorSpec** to replace **EstimatorSpec**.

#### **Parameters**

| Parameter        | Input/                 |                                         |
|------------------|------------------------|-----------------------------------------|
| NPUEstimatorSpec | inherits the following | EstimatorSpec parameters:               |
| mode             | Input                  | Mode, indicating whether training,      |
|                  | ●                      | ModeKeys.TRAIN : training               |
|                  | ●                      | ModeKeys.EVAL : validation              |
|                  | ●                      | ModeKeys.PREDICT : inference            |
| predictions      | Input                  | Inference output tensor, required when  |
|                  |                        | mode is set to ModeKeys.PREDICT         |
| loss             | Input                  | Training loss                           |
| train_op         | Input                  | Training operator                       |
| eval_metric_ops  | Input                  | Dictionary of the measurement result    |
|                  | ●                      | Metric instance                         |
|                  | ●                      | Result of calling the metric function,  |
|                  |                        | that is, the (metric_tensor, update_op) |

<span id="page-137-0"></span>

| Parameter            | Input/ |                                     |                                       |
|----------------------|--------|-------------------------------------|---------------------------------------|
| export_outputs       | Input  |                                     | Used to save a model, describing the  |
| training_chief_hooks | Input  | SessionRunHooks                     | set of the master node                |
| training_hooks       | Input  | SessionRunHooks                     | set during training                   |
| scaffold             | Input  | Defines                             | scaffold (providing the capability of |
|                      |        | customizing                         | saver , init_op , summary_op ,        |
|                      |        | and                                 | global_step ).                        |
| evaluation_hooks     | Input  | SessionRunHooks                     | set during evaluation                 |
| prediction_hook      | Input  | SessionRunHooks                     | set during inference                  |
| NPUEstimatorSpec     |        | has the following parameters added: |                                       |
| host_call            | Input  |                                     | Captures the summary information and  |
|                      |        | host_call                           | is a tuple consisting of a function   |
|                      |        | host_call                           | applies to train() and                |

#### **Returns**

An object of the **NPUEstimatorSpec** class

### **10.2.2.4 npu\_bridge.estimator.npu.npu\_hook**

## **10.2.2.4.1 NPUCheckpointSaverHook Constructor**

#### **Prototype**

def \_\_init\_\_(self, checkpoint\_dir, save\_secs=None, save\_steps=None, saver=None, checkpoint\_basename="model.ckpt", scaffold=None,

listeners=None)

#### <span id="page-138-0"></span>**Description**

Constructor of the **NPUCheckpointSaverHook** class, which is used to save the checkpoint file. The **NPUCheckpointSaverHook** class inherits the **CheckpointSaverHook** class and can call native APIs of the base class.

#### **Restrictions**

When **NPUEstimator** is used and **iteration\_per\_loop** is set to a value greater than 1, the hook may not take effect.

#### **Parameters**

| Parameter           | Input/ |                                        |
|---------------------|--------|----------------------------------------|
| checkpoint_dir      | Input  | Checkpoint file directory              |
| save_secs           | Input  | Interval (in seconds) for saving the   |
| save_steps          | Input  | Interval (in steps) for saving the     |
| saver               | Input  | Saver object                           |
| checkpoint_basename | Input  | Basename of the checkpoint file        |
| scaffold            | Input  | Scaffold of the saver object           |
| listeners           | Input  | Example of the CheckpointSaverListener |

#### **Returns**

An object of the **NPUCheckpointSaverHook** class

#### **Example**

from npu\_bridge.estimator.npu.npu\_hook import NPUCheckpointSaverHook checkpoint\_hook = NPUCheckpointSaverHook(checkpoint\_dir='./ckpt', save\_steps=2000)

... mnist\_classifier.train( input\_fn=train\_input\_fn, steps=2000, hooks=[checkpoint\_hook])

#### **10.2.2.4.2 NPUOutputTensorHook Constructor**

#### **Prototype**

def \_\_init\_\_(self, tensors,

output\_fn=None, output\_every\_n\_steps=0 )

#### **Description**

Constructor of the **NPUOutputTensorHook** class. **NPUOutputTensorHook** is the kook in the train, evaluate, and predict processes of **NPUEstimator**, and it is used to call the user-defined **output\_fn** every N steps or at the end to print the output tensors. The **NPUOutputTensorHook** class inherits the **LoggingTensorHook** class and can call native APIs of the base class.

#### **Restrictions**

When **Iterations\_per\_loop > 1**, **output\_fn** cannot be called as specified by **output\_every\_n\_steps**.

#### **Parameters**

| Parameter            | Input/ |                                              |
|----------------------|--------|----------------------------------------------|
| tensors              | Input  | Name set of the input tensors, in dictionary |
| dependencies         | Input  | Dependencies corresponding to tensors        |
| output_fn            | Input  | Print function of tensor output              |
| output_every_n_steps | Input  | The user-defined output_fn , which is called |
|                      |        | when the session is executed for N times     |

#### **Returns**

An object of the **NPUOutputTensorHook** class

#### **Example**

from npu\_bridge.estimator.npu.npu\_hook import NPUOutputTensorHook # Define **output\_fn**. def output\_fn(inputs): device\_id = os.environ["ASCEND\_DEVICE\_ID"] ouput\_file = os.path.join("/code", device\_id, "test\_npu\_output\_tensor.txt") for item in inputs: content = "step:{},loss:{}".format(str(item['global\_step']), str(item['loss'])) with open(ouput\_file, 'a') as f: f.write(content) f.write("\n") # Define **output\_hook** for calling the user-defined **output\_fn**. tensors = {'global\_step': global\_step, 'loss': loss} output\_hook = NPUOutputTensorHook(

<span id="page-140-0"></span> tensors, dependencies=train\_op\_list, output\_fn=output\_fn, output\_every\_n\_steps=10) train\_hook.append(output\_hook)

# Pass the hook to **EstimatorSpec**. return tf.estimator.EstimatorSpec( mode=mode, predictions=predictions, loss=loss, train\_op=train\_op, training\_chief\_hooks=train\_hook, eval\_metric\_ops=metrics)

#### **10.2.2.5 npu\_bridge.estimator.npu.npu\_optimizer**

#### **10.2.2.5.1 NPUDistributedOptimizer Constructor**

#### **Prototype**

def \_\_init\_\_(self, optimizer, name=None)

#### **Description**

Constructor of the **NPUDistributedOptimizer** class, which is used to package the single-server training optimizer provided by the user and construct the NPU distributed training optimizer.

In single-server single-device, single-server multi-device, and multi-server multidevice networking modes, gradient aggregation can be performed among devices after gradient calculation.

#### **Parameters**

| Parameter | Input/ |                                            |
|-----------|--------|--------------------------------------------|
| optimizer | Input  | Standalone training optimizer for gradient |
| name      | Input  | Name of the optimizer                      |

#### **Returns**

An object of the **NPUDistributedOptimizer** class

#### **Example**

After defining a standalone optimizer, you can use it for packaging. The following is an example.

import tensorflow as tf from npu\_bridge.estimator.npu.npu\_optimizer import NPUDistributedOptimizer optimizer = tf.train.GradientDescentOptimizer(learning\_rate=learning\_rate) optimizer = NPUDistributedOptimizer(optimizer)

#### <span id="page-141-0"></span>**10.2.2.5.2 NPUOptimizer Constructor**

#### **Prototype**

def \_\_init\_\_(self,

opt,

loss\_scale\_manager=None,

is\_distributed=False,

is\_loss\_scale=False,

is\_tailing\_optimization=False,

name=None)

#### **Description**

Constructor of the **NPUOptimizer** class, which combines the **[NPUDistributedOptimizer](#page-140-0)** and **[NPULossScaleOptimizer](#page-143-0)** optimizers.

It provides the following functions:

- Loss scaling: Loss scaling can be enabled during mixed precision training to solve the underflow problem caused by a small float16 representation range.
- Distributed training: The single-server training optimizer of the user is packaged and an NPU distributed training optimizer is constructed. In singleserver single-device, single-server multi-device, and multi-server multi-device networking modes, gradient aggregation is performed after gradient calculation on devices.
- Communication hangover optimization: By changing a computation dependency relationship, a computation operation that does not depend on the last AR (gradient aggregation fragment) is scheduled to be performed in parallel with the last AR, to optimize communication hangover.

#### **Parameters**

| Parameter Input                                                     |                    |                                           |
|---------------------------------------------------------------------|--------------------|-------------------------------------------|
| loss_scale_manager Input This parameter needs to be configured only |                    |                                           |
| when                                                                |                    | is_loss_scale is set to True and the loss |
| ●                                                                   | Before creating    | NPUOptimizer , you can                    |
|                                                                     | instantiate a      | FixedLossScaleManager                     |
|                                                                     | constructor of the | FixedLossScaleManag                      |
|                                                                     | er class, see      | 10.2.2.7.1                                |
| ●                                                                   | Before creating    | NPUOptimizer , you can                    |
|                                                                     | instantiate an     | ExponentialUpdateLossS                   |
|                                                                     | caleManager        | class to dynamically                      |
|                                                                     | constructor of the | ExponentialUpdate                        |
|                                                                     | LossScaleManager   | class, see 10.2.2.7.2                     |
| is_distributed Input Distributed training enable                    |                    |                                           |
| ●                                                                   | True               | : executes AllReduce.                     |
| ●                                                                   | False              | (default)                                 |
| is_loss_scale Input Loss scaling enable                             |                    |                                           |
| ●                                                                   | True               | : enabled (recommended if mixed           |
|                                                                     | the value of       | loss_scale_manager cannot                 |
|                                                                     | be                 | None                                      |
| ●                                                                   | False              | (default): disabled                       |
| is_tailing_optimization Input Communication hangover optimization   |                    |                                           |
| is_distributed                                                      |                    | is set to True                            |
| ●                                                                   | True               | : enabled                                 |
| ●                                                                   | False              | (default): disabled                       |
| same as that set in                                                 |                    | 10.2.2.2.1 NPURunConfig                   |
| name Input Name of the optimizer                                    |                    |                                           |

#### <span id="page-143-0"></span>**Returns**

An object of the **NPUOptimizer** class

#### **Example**

import tensorflow as tf from npu\_bridge.estimator.npu.npu\_optimizer import NPUOptimizer from npu\_bridge.estimator.npu.npu\_loss\_scale\_manager import FixedLossScaleManager from npu\_bridge.estimator.npu.npu\_loss\_scale\_manager import ExponentialUpdateLossScaleManager # Define a single-server optimizer. optimizer = LAMBOptimizer( learning\_rate=learning\_rate, weight\_decay\_rate=0.01, beta\_1=0.9, beta\_2=0.999, epsilon=1e-6, exclude\_from\_weight\_decay=["LayerNorm", "layer\_norm", "bias"]) # Enable loss scaling. if tf.flags.FLAGS.npu\_bert\_loss\_scale not in [None, -1]: if tf.flags.FLAGS.npu\_bert\_loss\_scale == 0: loss\_scale\_manager = ExponentialUpdateLossScaleManager(init\_loss\_scale=tf.flags.FLAGS.init\_loss\_scale\_value, incr\_every\_n\_steps=1000, decr\_every\_n\_nan\_or\_inf=2, decr\_ratio=0.5) elif tf.flags.FLAGS.npu\_bert\_loss\_scale >= 1: loss\_scale\_manager = FixedLossScaleManager(loss\_scale=tf.flags.FLAGS.npu\_bert\_loss\_scale) else: raise ValueError("Invalid loss scale: %d" % tf.flags.FLAGS.npu\_bert\_loss\_scale) optimizer = NPUOptimizer(optimizer, loss\_scale\_manager, is\_distributed=tf.flags.FLAGS.distributed, is\_loss\_scale=True, is\_tailing\_optimization=True) # Disable loss scaling. else: optimizer = NPUOptimizer(optimizer, is\_distributed=tf.flags.FLAGS.distributed)

### **10.2.2.6 npu\_bridge.estimator.npu.npu\_loss\_scale\_optimizer**

#### **10.2.2.6.1 NPULossScaleOptimizer Constructor**

#### **Prototype**

def \_\_init\_\_(self, opt, loss\_scale\_manager, is\_distributed=False)

#### **Description**

Constructor of the **NPULossScaleOptimizer** class, which is used to enable loss scaling during mixed precision training. Loss scaling solves the underflow problem caused by the small float16 representation range. The **NPULossScaleOptimizer** class inherits the **LossScaleOptimizer** class and can call native APIs of the base class.

#### **Returns**

#### An object of the **NPULossScaleOptimizer** class

#### **Example**

from npu\_bridge.estimator.npu.npu\_loss\_scale\_optimizer import NPULossScaleOptimizer from npu\_bridge.estimator.npu.npu\_loss\_scale\_manager import FixedLossScaleManager from npu\_bridge.estimator.npu.npu\_loss\_scale\_manager import ExponentialUpdateLossScaleManager

if FLAGS.use\_fp16 and (FLAGS.npu\_bert\_loss\_scale not in [None, -1]):

opt\_tmp = opt

if FLAGS.npu\_bert\_loss\_scale == 0:

loss\_scale\_manager = ExponentialUpdateLossScaleManager(init\_loss\_scale=2\*\*32,

incr\_every\_n\_steps=1000, decr\_every\_n\_nan\_or\_inf=2, decr\_ratio=0.5)

elif FLAGS.npu\_bert\_loss\_scale >= 1:

loss\_scale\_manager = FixedLossScaleManager(loss\_scale=FLAGS.npu\_bert\_loss\_scale)

else:

raise ValueError("Invalid loss scale: %d" % FLAGS.npu\_bert\_loss\_scale)

 opt = NPULossScaleOptimizer(opt\_tmp, loss\_scale\_manager, is\_distributed=True) else: opt = NPULossScaleOptimizer(opt\_tmp, loss\_scale\_manager)

#### <span id="page-145-0"></span>**10.2.2.7 npu\_bridge.estimator.npu.npu\_loss\_scale\_manager**

#### **10.2.2.7.1 FixedLossScaleManager Constructor**

#### **Prototype**

def \_\_init\_\_(self, loss\_scale)

#### **Description**

Constructor of the **FixedLossScaleManager** class, which is used to define static loss scale parameters.

#### **Parameters**

#### **Returns**

An object of the **FixedLossScaleManager** class

#### **10.2.2.7.2 ExponentialUpdateLossScaleManager Constructor**

#### **Prototype**

def \_\_init\_\_(self,

init\_loss\_scale,

incr\_every\_n\_steps,

decr\_every\_n\_nan\_or\_inf=2,

incr\_ratio=2,

#### <span id="page-146-0"></span>**Description**

Constructor of the **ExponentialUpdateLossScaleManager** class, which is used to define dynamic loss scale parameters.

#### **Parameters**

| Parameter               | Input/ |                                             |
|-------------------------|--------|---------------------------------------------|
| init_loss_scale         | Input  | Initial loss scale value. A float.          |
| incr_every_n_steps      | Input  | If no overflow occurs for N iterations,     |
| decr_every_n_nan_or_inf | Input  | If an overflow occurs for N iterations,     |
|                         |        | to 2                                        |
| incr_ratio              | Input  | Percentage increase of loss scale. Defaults |
|                         |        | to 2                                        |
| decr_ratio              | Input  | Percentage decrease of loss scale. Defaults |
|                         |        | to 0.8                                      |

#### **Returns**

An object of the **ExponentialUpdateLossScaleManager** class

#### **10.2.2.8 npu\_bridge.estimator.npu\_ops**

#### **10.2.2.8.1 dropout**

#### **Prototype**

def dropout(x, keep\_prob, noise\_shape=None, seed=None, name=None)

#### **Description**

The function works the same as **tf.nn.dropout**. Scales the input tensor by 1/ keep\_prob, and the reservation probability of the input tensor is **keep\_prob**. Otherwise, **0** is output, and the shape of the output tensor is the same as that of the input tensor.

<span id="page-147-0"></span>

| Parameter   | Input |                                                    |
|-------------|-------|----------------------------------------------------|
| x           | Input | Input tensor of type float.                        |
| keep_prob   | Input | Scalar tensor of type float, which indicates the   |
| noise_shape | Input | 1D tensor of type int32, which indicates the shape |
| seed        | Input | Random seed                                        |
| name        | Input | Name of the network layer.                         |

#### **Returns**

Tensor: output tensor after the dropout operation is performed on input **x**.

#### **Example**

from npu\_bridge.estimator import npu\_ops layers = npu\_ops.dropout()

#### **10.2.2.8.2 LARSV2**

#### **Prototype**

def LARSV2(input\_weight,

input\_grad,

weight\_decay,

learning\_rate,

hyperpara=0.001,

epsilon=0.00001,

use\_clip=False,

name=None)

#### **Description**

This operator scales gradients based on the norm of weight and the norm of gradient at different levels using different learning rates. It is used to improve the training precision in large batch size scenarios and is used for large-scale cluster training to reduce the training time.

<span id="page-148-0"></span>

| Parameter     | Input/ |                                                |
|---------------|--------|------------------------------------------------|
| input_weight  | Input  | Weight tensor of type float.                   |
| input_grad    | Input  | Weight gradient tensor of type float.          |
| weight_decay  | Input  | Scalar tensor of type float.                   |
| learning_rate | Input  | Scalar tensor of type float, indicating the    |
| hyperpara     | Input  | Scalar of type float, for the                  |
|               |        | set to 0.001                                   |
| epsilon       | Input  | A scalar, added to avoid dividing by zero.     |
|               |        | Generally set to 1e-5                          |
| use_clip      | Input  | A bool. Defaults to False                      |
|               |        | If this parameter is set to True , the scaling |
| name          | Input  | Name of the network layer.                     |

#### **Returns**

Tensor: output gradient tensor after the input gradient is updated.

#### **Example**

from npu\_bridge.estimator import npu\_ops layers = npu\_ops.LARSV2(input\_weight , input\_grad, weight\_decay, learning\_rate)

#### **10.2.2.8.3 initialize\_system**

#### **Prototype**

def initialize\_system(name = None)

#### **Description**

Generally, this API is not required for training. If you want to exclude the GE initialization time in the training time statistics, you can call this API. Before using the collective communication API, you need to call this API to initialize the collective communication.

#### **Restrictions**

If the **initialize\_system** API needs to be called and the following functions need to be enabled during training, the configuration must be performed when a session is started in **initialize\_system**.

**Table 10-7** Session configuration options in initialize\_system

| Item              | Description       |                                                        |
|-------------------|-------------------|--------------------------------------------------------|
| profiling_mode    | Profiling enable. |                                                        |
| ●                 | True              | : enabled. The profiling option is determined          |
|                   | by                | enable_options                                         |
| ●                 | False             | (default): disabled.                                   |
| profiling_options |                   | Option (or options separated by colons) to be          |
| ●                 |                   | training_trace : iteration tracing. Collects           |
| ●                 |                   | task_trace : task tracing. Collects the HWTS and       |
| ●                 | op_trace          | : single-operator tracing. To do so, you               |
|                   |                   | variable is exclusive with training_trace and          |
|                   | ● If              | training_trace is selected, fp_point and bp_point      |
|                   | ●                 | If set to task_trace , training_trace is automatically |
| fp_point          | Required if       | training_trace is selected.                            |
|                   |                   | a .pbtxt file by using tf.io.write_graph in the        |

| Item            | Description     |                                                   |
|-----------------|-----------------|---------------------------------------------------|
| dump_debug_mode |                 | Overflow detection mode.                          |
| ●               | aicore_overflow | : detects AI Core operator                        |
| ●               | atomic_overflow | : detects Atomic Add overflow,                    |
| ●               | all             | : detects both AI Core operator overflow and      |
| precision_mode  |                 | A string for the operator precision mode.         |
| ●               |                 | allow_fp32_to_fp16 or None : If an operator       |
| ●               | force_fp16      | : If an operator supports both float16            |
| ●               |                 | must_keep_origin_dtype : The original precision   |
| ●               |                 | allow_mix_precision : Mixed precision is enabled. |
|                 |                 | details about the related APIs, see 10.2.2.6.1    |
| auto_tune_mode  |                 | You can determine whether to use the Auto Tune    |
|                 | Example:        | auto_tune_mode = "RL,GA"; If this                 |
|                 | see "           | Auto Tune Tool Instructions " in CANN             |

| Item Description                                                      |                                    |                                                     |
|-----------------------------------------------------------------------|------------------------------------|-----------------------------------------------------|
| graph_run_mode Graph run mode.                                        |                                    |                                                     |
| ●                                                                     | 0                                  | : online inference                                  |
| ●                                                                     | 1                                  | (default): training                                 |
| op_debug_level Operator debug enable.                                 |                                    |                                                     |
| ●                                                                     | 0                                  | (default): disables operator debug.                 |
| ●                                                                     | 1                                  | : enables operator debug and generates a TBE        |
|                                                                       | generated in the                   | kernel_meta folder in the                           |
| ●                                                                     | 2                                  | : enables operator debug and generates a TBE        |
|                                                                       | generated in the                   | kernel_meta folder in the                           |
|                                                                       | compiler                           | -O0-g . You can locate the AI Core error            |
| ●                                                                     | 3                                  | : disables operator debug and retains the           |
|                                                                       | operator .o and .json files in the | kernel_meta                                         |
|                                                                       |                                    | You are advised to set this parameter to 0 or 3 for |
|                                                                       |                                    | parameter to 1 or 2 , which might compromise the    |
| enable_exception_dump Whether to dump the inputs and outputs of error |                                    |                                                     |
| ●                                                                     | 0                                  | (default): disabled                                 |
| ●                                                                     | 1                                  | : enabled. Keeping this switch enabled does not     |

#### **Returns**

An operator for the user to initialize GE by using **sess.run(op)**

#### **Example**

If you call an HCCL API such as **get\_local\_rank\_id**, **get\_rank\_size**, or **get\_rank\_id** before calling **sess.run()** or **estimator.train()**, you need to start another session and execute **initialize\_system** to initialize collective communication. After the training is complete, execute **shutdown\_system** and close the session.

import tensorflow as tf from npu\_bridge.estimator import npu\_ops from tensorflow.core.protobuf.rewriter\_config\_pb2 import RewriterConfig

npu\_int = npu\_ops.initialize\_system() npu\_shutdown = npu\_ops.shutdown\_system()

config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() <span id="page-154-0"></span>custom\_op.name = "NpuOptimizer"

custom\_op.parameter\_map["use\_off\_line"].b = True

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

init\_sess = tf.Session(config=config)

init\_sess.run(npu\_int) # Call an HCCL API... # Perform training...

init\_sess.run(npu\_shutdown)

init\_sess.close()

Or:

import tensorflow as tf

from npu\_bridge.estimator import npu\_ops

from tensorflow.core.protobuf.rewriter\_config\_pb2 import RewriterConfig

npu\_init = npu\_ops.initialize\_system() npu\_shutdown = npu\_ops.shutdown\_system()

config = tf.ConfigProto()

custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add()

custom\_op.name = "NpuOptimizer"

custom\_op.parameter\_map["use\_off\_line"].b = True

config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping.

with tf.Session(config=config) as sess:

 sess.run(npu\_init) # Call an HCCL API... # Perform training... sess.run(npu\_shutdown)

#### **10.2.2.8.4 shutdown\_system**

#### **Prototype**

def shutdown\_system(name = None)

#### **Description**

Disables all devices. This API is used in conjunction with **[10.2.2.8.3](#page-148-0) [initialize\\_system](#page-148-0)**.

#### **Parameters**

#### **Returns**

An operator is returned for the user to close the device by using **sess.run(op)**.

#### **10.2.2.9 npu\_bridge.estimator.npu.npu\_rnn**

#### <span id="page-155-0"></span>**10.2.2.9.1 npu\_dynamic\_rnn**

#### **Prototype**

def npu\_dynamic\_rnn(cell, inputs, initial\_state=None, dtype=None, sequence\_length=None, scope=None)

#### **Description**

Creates a high-performance neural network specified by RNNCell.

#### **Restrictions**

This API applies to the neural machine translation (NMT) network training script in the **while\_loop** expansion scenario.

#### **Parameters**

<span id="page-156-0"></span>

| Parameter | Input |                                                 |
|-----------|-------|-------------------------------------------------|
| scope     | Input | Creates VariableScope of the subgraph. Defaults |
|           |       | to rnn                                          |

#### **Returns**

Tensor: output tensor of the RNN

State: final state

#### **Example**

from npu\_bridge.estimator.npu.npu\_rnn import npu\_dynamic\_rnn code: inputs = npu\_unstack(self.encoder\_emb\_inp, axis=0) encoder\_outputs , encoder\_state = static\_rnn( cell, inputs, dtype= dtype, sequence\_length = sequence\_length ) encoder\_outputs = npu\_stack( encoder\_outputs, axis=0 ) Modified code: encoder\_outputs , encoder\_state = npu\_dynamic\_rnn( cell, inputs=self.encoder\_emb\_inp, dtype= dtype, sequence\_length= sequence\_length)

#### **10.2.2.10 npu\_bridge.estimator.npu.npu\_scope**

#### **10.2.2.10.1 without\_npu\_compile\_scope**

#### **Prototype**

def without\_npu\_compile\_scope()

#### **Description**

Configures the operator built on the host.

#### **Restrictions**

This API is valid only in mixed computing mode.

#### **Parameters**

#### <span id="page-157-0"></span>**Returns**

None

#### **10.2.2.10.2 keep\_dtype\_scope**

#### **Prototype**

def keep\_dtype\_scope()

#### **Description**

Specifies the operators that retain the original precision.

#### **Restrictions**

None

#### **Parameters**

None

#### **Returns**

None

#### **Example**

with npu\_scope.keep\_dtype\_scope(): X = tf.conv2d(a)

#### **10.2.2.11 npu\_bridge.estimator.npu.util**

#### **10.2.2.11.1 set\_iteration\_per\_loop**

#### **Prototype**

def set\_iteration\_per\_loop(sess, train\_op, iterations\_per\_loop=1)

#### **Description**

Sets the number of iterations per training loop in **sess.run** mode, that is, the number of training iterations executed on the device side in each **sess.run()** call. This API can save unnecessary interactions between the host and device and reduce the training time consumption.

#### **Restrictions**

The preceding API involves graph modification. If a graph cannot be modified (for example, the graph is frozen or a session is created using **tf.train.Supervisor**), you cannot use the **set\_iteration\_per\_loop** API to set the loops and iterations per loop. In this case, use **[10.2.2.11.2 create\\_iteration\\_per\\_loop\\_var](#page-158-0)** and **[10.2.2.11.3](#page-159-0) [load\\_iteration\\_per\\_loop\\_var](#page-159-0)**.

<span id="page-158-0"></span>

| Parameter           | Input/ |                                                   |
|---------------------|--------|---------------------------------------------------|
| sess                | Input  | Created TensorFlow session                        |
| train_op            | Input  | Operation that updates a variable or gradient     |
| iterations_per_loop | Input  | Number of iterations per training loop per        |
|                     |        | sess.run() call on the device side. Defaults to 1 |
|                     |        | In mixed computing mode ( mix_compile_mode        |
|                     |        | is set to True ), this parameter must be set to 1 |

#### **Returns**

An operator for the user to call by using **sess.run(op)**

#### **10.2.2.11.2 create\_iteration\_per\_loop\_var**

#### **Prototype**

def create\_iteration\_per\_loop\_var(self, train\_op)

#### **Description**

This API is used in conjunction with **[10.2.2.11.3 load\\_iteration\\_per\\_loop\\_var](#page-159-0)** to set the number of iterations per training loop every **sess.run()** call on the device side. This API is used to modify a graph and set the number of iterations per loop using **[10.2.2.11.3 load\\_iteration\\_per\\_loop\\_var](#page-159-0)**.

#### **Parameters**

#### **Returns**

An operator for the user to call by using **sess.run(op)**

#### <span id="page-159-0"></span>**Example**

# Train a model. with tf.Session(config=config) as sess: sess.run(init) # Set the number of iterations per loop to **10** in **sess.run** mode. **iteration = util.IterationPerLoop() train\_op = iteration.create\_iteration\_per\_loop\_var(optimizer) # Modify the graph.** tf.train.Supervisor(logdir="/home/xxxx",init\_op=init) # Freeze the graph. iteration.load\_iteration\_per\_loop\_var(sess, 10) # Set the number of iterations per loop. for epoch in range(training\_epochs): avg\_cost = 0 total\_batch = int(mnist.train.num\_examples / batch\_size) for i in range(total\_batch): batch\_xs, batch\_ys = mnist.train.next\_batch(batch\_size) \_, c = sess.run([train\_op, cost], feed\_dict={x: batch\_xs, y: batch\_ys}) avg\_cost += c / total\_batch

#### **10.2.2.11.3 load\_iteration\_per\_loop\_var**

#### **Prototype**

def load\_iteration\_per\_loop\_var(self, sess, iterations\_per\_loop=1)

#### **Description**

This API is used in conjunction with **[10.2.2.11.2 create\\_iteration\\_per\\_loop\\_var](#page-158-0)** to set the number of iterations per training loop every **sess.run()** call on the device side.

#### **Restrictions**

In mixed computing mode (**mix\_compile\_mode** is set to **True**), this parameter must be set to **1**.

#### **Parameters**

| Parameter           | Input/ |                                                 |
|---------------------|--------|-------------------------------------------------|
| sess                | Input  | Created TensorFlow session                      |
| iterations_per_loop | Input  | Number of iterations per training loop per      |
|                     |        | sess.run() call on the device side. Defaults to |
|                     |        | 1 . The total number of iterations per training |

#### **Returns**

#### <span id="page-160-0"></span>**Example**

# Train a model. with tf.Session(config=config) as sess: sess.run(init) # Set the number of iterations per loop to **10** in **sess.run** mode. **iteration = util.IterationPerLoop()**  train\_op = iteration.create\_iteration\_per\_loop\_var(optimizer) # Modify the graph. tf.train.Supervisor(logdir="/home/xxxx",init\_op=init) # Freeze the graph. **iteration.load\_iteration\_per\_loop\_var(sess, 10) # Set the number of iterations per loop.** for epoch in range(training\_epochs): avg\_cost = 0 total\_batch = int(mnist.train.num\_examples / batch\_size) for i in range(total\_batch): batch\_xs, batch\_ys = mnist.train.next\_batch(batch\_size) \_, c = sess.run([train\_op, cost], feed\_dict={x: batch\_xs, y: batch\_ys}) avg\_cost += c / total\_batch

#### **10.2.2.12 npu\_bridge.estimator.npu.keras\_to\_npu**

#### **10.2.2.12.1 model\_to\_npu\_estimator**

#### **Prototype**

def model\_to\_npu\_estimator(keras\_model=None,

keras\_model\_path=None,

custom\_objects=None,

model\_dir=None,

checkpoint\_format='saver',

config=None,

job\_start\_file='')

#### **Description**

Converts the model constructed by using **Keras** to an **NPUEstimator** object.

#### **Restrictions**

Currently, only the function model and sequence model (Keras graph construction mode) can be converted into an NPUEstimator object using the **model\_to\_npu\_estimator** API.

#### **Parameters**

| Parameter   | Description                                      |
|-------------|--------------------------------------------------|
| keras_model | Built Keras model object.                        |
|             | This parameter and keras_model_path are mutually |

<span id="page-161-0"></span>

#### **Returns**

An **NPUEstimator** object is returned based on the input Keras model.

## **10.2.2.13 Session Configuration in sess.run Mode**

When training or online inference is performed on the in **sess.run** mode, the following configuration options are supported.

#### NO TICE

If this parameter is not listed in the table, it is reserved or applicable to other chip versions. You can ignore this parameter.

**Table 10-8** Session configuration items

| Item Description                                               |               | Use                                 |
|----------------------------------------------------------------|---------------|-------------------------------------|
| use_off_line Whether the training is performed on the .        |               |                                     |
| ●                                                              | True          | (default): yes                      |
| ●                                                              | False         | : not (training is performed on the |
| enable_data_pre_proc Data preprocessing enable                 |               |                                     |
| ●                                                              | True          | (default): enabled                  |
| ●                                                              | False         | : disabled                          |
| iterations_per_loop Number of iterations per loop set by using |               |                                     |
| set_iteration_per_loop                                         |               | in sess.run mode,                   |
| training loop every                                            |               | sess.run() call on the              |
| the value of                                                   |               | iterations_per_loop set by          |
| set_iteration_per_loop                                         |               | for function                        |
| profiling_mode Profiling enable.                               |               |                                     |
| ●                                                              | True          | : enabled. The profiling option is  |
|                                                                | determined by | enable_options                      |
| ●                                                              | False         | (default): disabled.                |

| Item Description                        | Use                          |
|-----------------------------------------|------------------------------|
| BP_POINT                                | and FP_POINT are used to     |
| tf.io.write_graph                       | in the training script       |
| ● aic_metrics                           | : AI Core metric to profile. |
| ArithmeticUtilization                   | : percentages of             |
| PipeUtilization                         | (default): percentages       |
| Memory                                  | : percentages of external    |
| MemoryL0                                | : percentages of internal    |
| ResourceConflictRatio                   | : percentages of             |
| Online inference supports               | task_trace and aicpu         |
| but does not support                    | training_trace               |
| enable_dump Data dump enable            |                              |
| ● True : enabled. The dump file path is |                              |
| read from                               | dump_path . If dump_path is  |
| set to None                             | , an exception occurs.       |
| ● False (default): disabled.            |                              |

| Item      | Description | Use                                       |
|-----------|-------------|-------------------------------------------|
| dump_path |             | Dump path. This parameter is required     |
|           | when        | enable_dump or                            |
|           |             | enable_dump_debug is set to True          |
| ●         |             | An absolute path starts with a slash (/), |
|           |             | for example, /home/HwHiAiUser/            |
| ●         |             | A relative path starts with a directory   |
|           |             | name, for example, output                 |
| dump_step |             | Iterations to dump. Defaults to None ,    |
| dump_mode | Dump mode.  |                                           |
| ●         | input       | : dumps only operator inputs.             |
| ●         | output      | (default): dumps only operator            |
| ●         | all         | : dumps both operator inputs and          |

| Item              | Description | Use                                  |
|-------------------|-------------|--------------------------------------|
| enable_dump_debug |             | Overflow detection enable.           |
| ●                 | True        | : enabled. The dump file path is     |
|                   | read from   | dump_path . If dump_path is          |
|                   | set to      | None , an exception occurs.          |
| ●                 | False       | (default): disabled.                 |
| dump_debug_mode   |             | Overflow detection mode.             |
| ●                 |             | aicore_overflow : detects AI Core    |
| ●                 |             | atomic_overflow : detects Atomic Add |
| ●                 | all         | : detects both AI Core operator      |

| Item                     | Description | Use                                       |
|--------------------------|-------------|-------------------------------------------|
| precision_mode           |             | A string for the operator precision mode. |
| ●                        |             | allow_fp32_to_fp16 or None : If an        |
| ●                        | force_fp16  | : If an operator supports both            |
| ●                        |             | must_keep_origin_dtype : The original     |
| ●                        |             | allow_mix_precision : Mixed precision is  |
| variable_format_optimize |             | Variable format optimization enable       |
| ●                        | True        | (default): enabled                        |
| ●                        | False       | : disabled                                |

| Item Description                                                 |       | Use                       |
|------------------------------------------------------------------|-------|---------------------------|
| mix_compile_mode Mixed computing enable                          |       |                           |
| ●                                                                | True  | : enabled                 |
| ●                                                                | False | (default): disabled       |
| hcom_parallel Whether to enable the AllReduce gradient           |       |                           |
| ●                                                                | True  | : enabled                 |
| ●                                                                | False | (default): disabled       |
| graph_memory_max_size Network static memory and maximum          |       |                           |
| the sum of                                                       |       | graph_memory_max_size and |
| variable_memory_max_size                                         |       | cannot exceed             |
| variable_memory_max_size Variable memory, which can be specified |       |                           |
| the sum of                                                       |       | graph_memory_max_size and |
| variable_memory_max_size                                         |       | cannot exceed             |

| Item Description                                                  | Use                        |
|-------------------------------------------------------------------|----------------------------|
| auto_tune_mode You can determine whether to use the Auto          |                            |
| Tune tool, see "                                                  | Auto Tune Tool             |
| Instructions                                                      | " in CANN Auxiliary        |
| stream_max_parallel_num AI CPU and AI Core engine parallelism for |                            |
| DNN_VM_TF                                                         | is the name of the AI CPU  |
| DNN_V100                                                          | is the name of the AI Core |
| concurrent tasks of the AI Core engine is                         | 1                          |
| engine and AI Core engine is                                      | 1 by default.              |

| Item                    |      | Description Use                            |
|-------------------------|------|--------------------------------------------|
| is_tailing_optimization |      | Communication hangover optimization        |
| ●                       | True | : enabled                                  |
| ●                       |      | False (default): disabled                  |
|                         |      | NPUOptimizer and the value must be the     |
|                         |      | same as that of is_tailing_optimization in |
| graph_run_mode          |      | Graph run mode                             |
| ●                       | 0    | : online inference                         |
| ●                       | 1    | (default): training                        |

| Item Description                      |                            | Use                                           |
|---------------------------------------|----------------------------|-----------------------------------------------|
| op_debug_level Operator debug enable. |                            |                                               |
| ●                                     | 0                          | (default): disables operator debug.           |
| ●                                     | 1                          | : enables operator debug and                  |
|                                       | files are generated in the | kernel_meta                                   |
| ●                                     | 2                          | : enables operator debug and                  |
|                                       | files are generated in the | kernel_meta                                   |
|                                       | O0-g                       | . You can locate the AI Core error            |
| ●                                     | 3                          | : disables operator debug and retains         |
|                                       | kernel_meta                | folder in the training script                 |
|                                       |                            | You are advised to set this parameter to 0 or |
|                                       |                            | 3 for training. If you need to locate AI Core |
|                                       |                            | errors, set this parameter to 1 or 2 , which  |

| Item                       | Description | Use                                    |
|----------------------------|-------------|----------------------------------------|
| enable_scope_fusion_passes |             | Scope fusion pattern (or scope fusion  |
| ●                          | General     | : common scope fusion patterns         |
| ●                          |             | Non-General : scope fusion patterns    |
|                            | use         | enable_scope_fusion_passes to          |
| enable_exception_dump      |             | Whether to dump the inputs and outputs |
| ●                          | 0           | (default): disabled                    |
| ●                          | 1           | : enabled. Keeping this switch enabled |

| Item                    | Description         | Use                                          |
|-------------------------|---------------------|----------------------------------------------|
| op_select_implmode      |                     | Operator implementation mode select.         |
| ●                       | high_precision      | : high precision                             |
| ●                       | high_performance    | (default): high                              |
| optypelist_for_implmode |                     | List of operator types. The operators in the |
|                         | OP_SELECT_IMPL_MODE | parameter.                                   |
|                         | OP_SELECT_IMPL_MODE | parameter, for                               |
|                         | Set                 | op_select_implmode to                        |
|                         | Set                 | optypelist_for_implmode to Pooling           |

| Item Description                                        | Use                                    |
|---------------------------------------------------------|----------------------------------------|
| input_shape Input shape.                                |                                        |
| three inputs:                                           | data (1, 1, 40, –1), label (1, –       |
| 1), and                                                 | mask (–1, –1). Separate each input     |
| and its shape with colons (:). A                        | –1 indicates                           |
| are configured by using                                 | dynamic_dims                           |
| ●                                                       | If a network has both dataset inputs   |
| ●                                                       | For scalar inputs, set the shape to 0. |
| dynamic_dims Input dimension size choices. Separate the |                                        |
| dim                                                     | values one-to-one correspond to the –  |
| 1 s in the                                              | input_shape argument, and the          |
| number of                                               | –1 s equals the number of              |
| input_shape                                             | and dynamic_dims                       |
| Based on the                                            | input_shape information in             |
| ●                                                       | Choice 0: data(1, 1, 40, 20)+label(1,  |
| ●                                                       | Choice 1: data(1, 1, 40, 40)+label(1,  |
| ●                                                       | Choice 2: data(1, 1, 40, 80)+label(1,  |

| Item Description                                                |              | Use                 |
|-----------------------------------------------------------------|--------------|---------------------|
| dynamic_node_type Type of the dynamic input node.               |              |                     |
| ●                                                               | 0            | : dataset input     |
| ●                                                               | 1            | : placeholder input |
| mstune_mode This parameter is not supported in the              |              |                     |
| work_path This parameter is not supported in the                |              |                     |
| buffer_optimize Buffer optimization enable.                     |              |                     |
| ●                                                               | l2_optimize  | (default): enabled  |
| ●                                                               | off_optimize | : disabled          |
| enable_small_channel Small channel optimization enable. If this |              |                     |
| ●                                                               | 0            | (default): disabled |
| ●                                                               | 1            | : enabled           |

| Item               | Description                                  | Use |
|--------------------|----------------------------------------------|-----|
| fusion_switch_file | Directory of the fusion switch configuration |     |
|                    | fusion_switch.cfg configuration file. on     |     |

| Item                   | Description | Use                                      |
|------------------------|-------------|------------------------------------------|
| op_compiler_cache_mode |             | Disk cache enable for operator build.    |
| ●                      | enable      | : enabled. If enabled, operators         |
| ●                      | disable     | (default): disabled.                     |
| ●                      | force       | : forcibly flushes the cache. That is,   |
| ●                      |             | It needs to be used in pair with         |
|                        |             | op_compiler_cache_dir . The cache        |
|                        |             | op_debug_level is set to 0 or 3          |
| ●                      | If set to   | force , the existing cache will be       |
| ●                      | disable     | and force are recommended for            |
| op_compiler_cache_dir  |             | Disk cache directory for operator build. |
| a                      |             | kernel_cache subdirectory is             |
|                        | and the     | kernel_cache subdirectory.               |
|                        | Defaults to | \$HOME/atc_data                          |

<span id="page-178-0"></span>

#### **Example**

import tensorflow as tf from npu\_bridge.estimator import npu\_ops from tensorflow.core.protobuf.rewriter\_config\_pb2 import RewriterConfig config = tf.ConfigProto() custom\_op = config.graph\_options.rewrite\_options.custom\_optimizers.add() custom\_op.name = "NpuOptimizer" custom\_op.parameter\_map["use\_off\_line"].b = True config.graph\_options.rewrite\_options.remapping = RewriterConfig.OFF # Disable remapping. with tf.Session(config=config) as sess: sess.run(cost)

# **10.2.3 Collective Communication APIs**

#### **10.2.3.1 Overview**

The high-level API **NPUDistributedOptimizer** enables the user to automatically complete gradient aggregation without sensing AllReduce, implementing data parallel training. In addition, to meet the users' requirements for flexibility, collective communication provides common APIs such as rank management, gradient segmentation, and collective communication prototype.

To use the collective communication APIs, the FwkACLlib and TFPlugin software packages must be installed.

- If the **--pylocal** mode is used for the installation of FwkACLlib and TFPlugin, the corresponding .whl files are installed in the software installation paths, which are **/fwkacllib/python/site-packages** in the FwkACLlib installation path and **/tfplugin/python/site-packages/** in the TFPlugin installation path.
- If the **--pylocal** mode is not used to for the installation of FwkACLlib and TFPlugin, the corresponding .whl files are installed in the local Python path.

## **10.2.3.2 hccl.manage.api**

#### <span id="page-179-0"></span>**10.2.3.2.1 create\_group**

#### **Prototype**

def create\_group(group, rank\_num, rank\_ids)

#### **Description**

Creates a user-defined group for collective communication.

#### **Parameters**

| Parameter | Input/ |                                                  |
|-----------|--------|--------------------------------------------------|
| group     | Input  | A string containing a maximum of 128 bytes,      |
|           |        | hccl_world_group is the default group created    |
|           |        | using this API. If hccl_world_group is passed to |
| rank_num  | Input  | An int.                                          |
|           |        | The maximum value is 4096                        |

#### **Returns**

None

### **Example**

#### <span id="page-181-0"></span>**Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by **group** in the current API. Otherwise, the API fails to be called.
- If a group with the same name is created repeatedly, group creation fails.
- This API is available only when the number of ranks in the ranktable file is 1, 2, 4, or 8.

#### **10.2.3.2.2 destroy\_group**

#### **Prototype**

def destroy\_group(group)

#### **Description**

Destroy a user-defined group.

#### **Parameters**

#### **Returns**

None

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) destroy\_group(" myGroup ")

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- Groups with the same name must be used in conjunction with **10.2.3.2.2 destroy\_group** and **[10.2.3.2.1 create\\_group](#page-179-0)** and called after **create\_group** is complete.

- If the group transferred by the user is **hccl\_world\_group** (default group), the group fails to be destroyed.

#### <span id="page-182-0"></span>**10.2.3.2.3 get\_rank\_size**

#### **Prototype**

def get\_rank\_size(group="hccl\_world\_group")

#### **Description**

Obtains the number of ranks (that is, the number of devices) in a group.

#### **Parameters**

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | hccl_world_group is used.                      |

#### **Returns**

An int for the number of ranks in a group.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) rankSize = get\_rank\_size("myGroup") #rankSize = 4

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is complete, this API is called to obtain the number of ranks in the current group.
- If **hccl\_world\_group** is passed, the number of ranks in **world\_group** is returned.

#### **10.2.3.2.4 get\_local\_rank\_size**

#### **Prototype**

#### <span id="page-183-0"></span>**Description**

Obtains the number of local ranks on the server where the devices in the group are located.

#### **Parameters**

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | hccl_world_group is used.                      |

#### **Returns**

An int for the number of local ranks on the server where the device is located.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) lcoalRankSize = get\_local\_rank\_size("myGroup") #localRankSize = 1

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is complete, this API is called to obtain the number of local ranks in the current group.
- If **hccl\_world\_group** is passed, the number of local ranks in **world\_group** is returned.

#### **10.2.3.2.5 get\_rank\_id**

#### **Prototype**

def get\_rank\_id(group="hccl\_world\_group")

#### **Description**

Obtains the rank ID of a device in a group.

<span id="page-184-0"></span>

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | hccl_world_group is used.                      |

#### **Returns**

An int for the rank ID of the group to which the device belongs is returned.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) rankId = get\_rank\_id(" myGroup ") #rankId = 0/1/2/3

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is complete, this API is called to obtain the rank ID of a process in a group.
- If **hccl\_world\_group** is passed, the rank ID of the process in **world\_group** is returned.

#### **10.2.3.2.6 get\_local\_rank\_id**

#### **Prototype**

def get\_local\_rank\_id(group="hccl\_world\_group")

#### **Description**

Obtains the local rank ID of a device in a group.

<span id="page-185-0"></span>

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | hccl_world_group is used.                      |

#### **Returns**

An int for the ID of the local rank on the server where the device is located.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) localRankId = get\_local\_rank\_id("myGroup") #rankId = 0

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is complete, this API is called to obtain the local rank ID of a process in a group.
- If **hccl\_world\_group** is passed, the local rank ID of the process in **world\_group** is returned.

#### **10.2.3.2.7 get\_world\_rank\_from\_group\_rank**

#### **Prototype**

def get\_world\_rank\_from\_group\_rank(group, group\_rank\_id)

#### **Description**

Obtains the world rank ID of the process using the group rank ID.

<span id="page-186-0"></span>

| Parameter     | Input/ |                                                |
|---------------|--------|------------------------------------------------|
| group         | Input  | A string of up to 128 bytes, including the end |
|               |        | or hccl_world_group                            |
| group_rank_id | Input  | An int.                                        |

#### **Returns**

An int for the rank ID of the process in **hccl\_world\_group**.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) worldRankId = get\_world\_rank\_from\_group\_rank (" myGroup", 1) #worldRankId = 8

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is compete, this API is called to convert the group rank ID to the world rank ID.

#### **10.2.3.2.8 get\_group\_rank\_from\_world\_rank**

#### **Prototype**

def get\_group\_rank\_from\_world\_rank(world\_rank\_id, group)

#### **Description**

Obtains the group rank ID of the process in the group using the world rank ID.

#### **Parameters**

| Parameter     | Input/ |                                            |
|---------------|--------|--------------------------------------------|
| world_rank_id | Input  | An int.                                    |
|               |        | Rank ID of the process in hccl_world_group |

<span id="page-187-0"></span>

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | or hccl_world_group                            |

#### **Returns**

An int for the rank ID of a process in a group.

#### **Example**

from npu\_bridge.npu\_init import \* create\_group(" myGroup", 4, [0, 1, 2, 3]) groupRankId = get\_group\_rank\_from\_world\_rank (8, " myGroup ") #groupRankId = 1

#### **Restrictions**

- This API must be called after the initialization of collective communication is complete.
- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- After **[10.2.3.2.1 create\\_group](#page-179-0)** is compete, this API is called to convert the world rank ID to the group rank ID.

#### **10.2.3.3 hccl.split.api**

#### **10.2.3.3.1 set\_split\_strategy\_by\_idx**

#### **Prototype**

def set\_split\_strategy\_by\_idx(idxList, group="hccl\_world\_group")

#### **Description**

Sets the backward gradient segmentation policy in the collective communication group based on the gradient index ID to implement the convergence of AllReduce.

#### <span id="page-189-0"></span>**Returns**

None

#### **Example**

from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_idx ([20, 100, 159], "group")

#### **Restrictions**

- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- If you do not call the gradient segmentation API to set the segmentation policy, the default backward gradient segmentation policy is used. Default segmentation policy: two segments with the first taking up 96.54% of the gradient data volume, and the second segment taking up 3.46% (In some cases, there is only one segment).

#### **10.2.3.3.2 set\_split\_strategy\_by\_size**

#### **Prototype**

def set\_split\_strategy\_by\_size(dataSizeList, group="hccl\_world\_group")

#### **Function**

Sets the backward gradient segmentation policy in the collective communication group based on the data volume percentage to implement the convergence of AllReduce.

#### **Parameters**

| Parameter Input/   |                                                |
|--------------------|------------------------------------------------|
| dataSizeList Input | A list.                                        |
| ●                  | The index ID list of the gradient must be non |
| ●                  | A maximum of eight gradient segments are       |
| ●                  | For example, if the model has 150 MB           |
|                    | set dataSizeList to [60, 20, 20]               |

<span id="page-190-0"></span>

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | or hccl_world_group . Defaults to              |

#### **Returns**

None

#### **Example**

from npu\_bridge.npu\_init import \* set\_split\_strategy\_by\_size ([60, 20, 20], "group")

#### **Restrictions**

- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- When the backward gradient segmentation policy is set based on both the gradient data volume percentage and the gradient index ID, the setting result based on the gradient data volume percentage is preferred.
- If you do not call the gradient segmentation API to set the segmentation policy, the default backward gradient segmentation policy is used. Default segmentation policy: The optimal segmentation location of ResNet-50 is as follows: ResNet-50 is divided into two segments based on the gradient data volume. The data volume of the first segment is 96.54%, and that of the second segment is 3.46%.

### **10.2.3.4 npu\_bridge.hccl.hccl\_ops**

#### **10.2.3.4.1 allreduce**

#### **Prototype**

def allreduce(tensor, reduction, fusion=1, fusion\_id=-1, group = "hccl\_world\_group")

#### **Description**

Provides the AllReduce function for collective communication in a group to reduce tensors with the same name on all nodes.

#### **Returns**

The result tensor

#### **Example**

from npu\_bridge.hccl import hccl\_ops result = hccl\_ops.allreduce(tensor, "sum")

#### **Restrictions**

- The caller rank must be within the range defined by **group** in the current API. Otherwise, the API fails to be called.
- The upstream node of AllReduce must not be variable.
- The input tensor size must be less than or equal to 8 GB.
- The AllReduce operator can be fused only when the **reduction** is set to **sum**.

#### <span id="page-192-0"></span>**10.2.3.4.2 allgather**

#### **Prototype**

def allgather(tensor, rank\_size, group = "hccl\_world\_group")

#### **Description**

Each device receives the aggregation of tensor data from all ranks in the order of the ranks.

#### **Parameters**

| Parameter | Input/ |                                             |
|-----------|--------|---------------------------------------------|
| tensor    | Input  | TensorFlow tensor type.                     |
|           |        | int32, float16, float32, int64, uint64      |
| rank_size | Input  | An int.                                     |
|           |        | The maximum value is 4096                   |
| group     | Input  | A string containing a maximum of 128 bytes, |
|           |        | or hccl_world_group                         |

#### **Returns**

The result tensor

#### **Example**

from npu\_bridge.hccl import hccl\_ops rank\_size = 2 result = hccl\_ops.allgather (tensor, rank\_size)

#### **Restrictions**

- The caller rank must be within the range defined by **group** in the current API. Otherwise, the API fails to be called.

#### **10.2.3.4.3 broadcast**

#### **Prototype**

def broadcast(tensor, root\_rank, fusion=0,fusion\_id=-1,group = "hccl\_world\_group")

#### **Description**

Copies an N-element buffer on the root rank to all ranks.

#### **Parameters**

| Parameter | Input/ |                                                  |
|-----------|--------|--------------------------------------------------|
| tensor    | Input  | TensorFlow tensor type. A list.                  |
|           |        | int32, float16, float32, int64, uint64           |
| root_rank | Input  | An int.                                          |
| group     | Input  | A string containing a maximum of 128 bytes,      |
|           |        | or hccl_world_group                              |
| fusion    | Input  | An int.                                          |
|           |        | ● 0 (default): disabled. The AllReduce operator  |
|           |        | ● 2 : enabled. AllReduce operators with the same |
|           |        | fusion_id are fused.                             |
|           |        | ● Other values: invalid                          |
| fusion_id | Input  | Fusion ID of the Broadcast operator.             |
|           |        | Broadcast operators with the same fusion_id are  |

#### **Returns**

The result tensor

#### **Example**

from npu\_bridge.hccl import hccl\_ops root = 0 inputs = [tensor] result = hccl\_ops.broadcast (inputs, root)

#### **Restrictions**

- The caller rank must be within the range defined by **group** in the current API. Otherwise, the API fails to be called.
- In **sess.run** mode, if you call this API to fuse broadcast operators, the **GradFusionOptimizer** cannot be used at the same time.

- In **Estimator** mode, this API cannot be called to fuse broadcast operators.

#### <span id="page-194-0"></span>**10.2.3.4.4 reduce\_scatter**

#### **Prototype**

def reduce\_scatter(tensor, reduction, rank\_size, group = "hccl\_world\_group")

#### **Description**

Performs the same operation as the Reduce operation, except that each rank receives a subpart of the result. The Reduce operation is specified by the **reduction** parameter.

#### **Parameters**

| Parameter | Input/ |                                                |
|-----------|--------|------------------------------------------------|
| tensor    | Input  | TensorFlow tensor type                         |
|           |        | int32, float16, float32.                       |
| reduction | Input  | A string.                                      |
|           |        | max , min , prod , or sum                      |
| rank_size | Input  | An int.                                        |
|           |        | The maximum value is 4096                      |
| group     | Input  | A string of up to 128 bytes, including the end |
|           |        | or hccl_world_group                            |

#### **Returns**

The result tensor. It is recommended that the result tensor size be 32-byte aligned. Otherwise, the performance deteriorates.

#### **Example**

from npu\_bridge.npu\_init import \*

rank\_size = 2 result = hccl\_ops. reduce\_scatter (tensor, "sum", rank\_size)

#### <span id="page-195-0"></span>**Restrictions**

- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- The input tensor size must be less than or equal to 8 GB.

#### **10.2.3.4.5 send**

#### **Prototype**

def send(tensor, sr\_tag, dest\_rank, group = "hccl\_world\_group")

#### **Description**

Sends data to a rank within a collective communication group.

#### **Parameters**

| Parameter | Input/ |                                                 |
|-----------|--------|-------------------------------------------------|
| tensor    | Input  | TensorFlow tensor type                          |
|           |        | int32, float16, float32.                        |
| sr_tag    | Input  | An int.                                         |
|           |        | sr_tag can receive and send data.               |
| dest_rank | Input  | An int.                                         |
|           |        | Target node of data. rank indicates the rank ID |
| group     | Input  | A string of up to 128 bytes, including the end  |
|           |        | or hccl_world_group                             |

#### **Returns**

None

#### **Example**

from npu\_bridge.npu\_init import \* sr\_tag = 0 dest\_rank = 1 hccl\_ops. send (tensor, sr\_tag, dest\_rank)

#### **Restrictions**

- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.

- Only point-to-point data transmit and receive between ranks with the same ID from different servers are supported.
- The Send and Receive functions must be used in pairs and depend on other operators in the graph.

#### <span id="page-196-0"></span>**10.2.3.4.6 receive**

#### **Prototype**

def receive(shape, data\_type, sr\_tag, src\_rank, group = "hccl\_world\_group")

#### **Description**

Receives data from a rank within a collective communication group.

#### **Parameters**

| Parameter | Input/ |                                                  |
|-----------|--------|--------------------------------------------------|
| shape     | Input  | Shape of the received tensor.                    |
| data_type | Input  | Data type of the received data.                  |
|           |        | int32, float16, float32.                         |
| sr_tag    | Input  | An int.                                          |
|           |        | sr_tag can receive and send data.                |
| src_rank  | Input  | An int.                                          |
|           |        | Source node of the received data. rank indicates |
| group     | Input  | A string of up to 128 bytes, including the end   |
|           |        | or hccl_world_group                              |

#### **Returns**

The result tensor

#### **Example**

from npu\_bridge.npu\_init import \*

sr\_tag = 0

src\_rank = 0

tensor = hccl\_ops. receive (tensor.shape, tensor.dtype, sr\_tag, src\_rank)

#### <span id="page-197-0"></span>**Restrictions**

- The caller rank must be within the range defined by the **group** argument passed to this API call. Otherwise, the API call fails.
- Only point-to-point data transmit and receive between ranks with the same ID from different servers are supported.
- The Send and Receive functions must be used in pairs and depend on other operators in the graph.

# **10.3 Environment Variables**

# **10.3.1 JOB\_ID**

#### **Description**

Sets the training job ID, which is user-defined. Allows letters, digits, hyphens (-), and underscores (\_) only. You are not advised to set **JOB\_ID** to pure digits starting with 0.

#### **Example**

export JOB\_ID=10087

# **10.3.2 ASCEND\_DEVICE\_ID**

#### **Description**

Sets the logical ID of a processor.

The value range is [0, N – 1] and the default value is **0**. N indicates the device count in the physical machine, VM, or container.

#### **Example**

export ASCEND\_DEVICE\_ID=0

# **10.3.3 RANK\_TABLE\_FILE**

#### **Description**

Sets the processor resource information of distributed training, that is, the directory of the ranktable file, including the file name. For details, see **[10.4 Device](#page-204-0) [Resource Configuration File Templates](#page-204-0)**.

#### **Example**

export RANK\_TABLE\_FILE=/home/test/ranktable.json

# <span id="page-198-0"></span>**10.3.4 RANK\_ID**

#### **Description**

Sets the rank ID corresponding to the training process in the collective communication process group.

When ranktable template 1 is used, the value of this parameter is the same as that of **rank\_id**.

When ranktable template 2 is used, the value of this parameter is the same as that of **pod\_name**.

#### **Example**

export RANK\_ID=0

# **10.3.5 RANK\_SIZE**

#### **Description**

Sets the cluster rank size corresponding to the current training process, that is, the number of devices in the cluster.

#### **Example**

export RANK\_SIZE=2

# **10.3.6 GE\_USE\_STATIC\_MEMORY**

#### **Description**

Enables static memory allocation for network execution.

Set this parameter to **1** to enable static memory allocation. This is especially useful when executing a deep inference network, for example, the BERT24 network whose intermediate data volume in feature map computation could reach 25 GB. In this case, enabling static memory allocation can improve the collaboration efficiency between the communication DIMMs in multi-device scenarios. You are advised to retain the default value to use dynamic memory allocation for networks other than BERT24.

In static memory allocation mode, the default allocation is 31 GB, which is determined by the sum of **graph\_memory\_max\_size** and **variable\_memory\_max\_size**. In dynamic memory allocation mode, the allocation is within the sum of **graph\_memory\_max\_size** and **variable\_memory\_max\_size**.

#### **Example**

export GE\_USE\_STATIC\_MEMORY=1

# <span id="page-199-0"></span>**10.3.7 PROFILING\_MODE**

#### **Description**

Enables Profiling.

- **true**: enabled. The option to be traced is determined by **PROFILING\_OPTIONS**.
- **false** or not configured: disabled.

#### **Example**

export PROFILING\_MODE=true

# **10.3.8 PROFILING\_OPTIONS**

#### **Description**

Sets Profiling options.

- **output**: path for storing profiling result files. Create the specified path in advance in the environment (either in a container or on the host) where training is performed. The running user configured during installation must have the read and write permissions on this path. The path can be an absolute path or a relative path relative to the path where the training script is executed.
  - An absolute path starts with a slash (/), for example, **/home/ HwHiAiUser/output**.
  - A relative path starts with a directory name, for example, **output**.
- **training\_trace**: iteration tracing switch. Collects software profile data of a training job and the AI Software Stack to profile the training job. Focuses on data augmentation, forward and backward propagation, and gradient aggregation and update. Either **on** or **off**. A value other than **on** or **off** is equivalent to **off**.
- **task\_trace**: task tracing switch. Collects the HWTS hardware information of the and the start and end of each task. Either **on** or **off**. A value other than **on** or **off** is equivalent to **off**.
- **aicpu**: AI CPU data augmentation profiling switch. Either **on** or **off**. A value other than **on** or **off** is equivalent to **off**.
- **fp\_point**: required when **training\_trace** is **on**. Specifies the start point of the forward propagated operator in iteration traces, to record the start timestamp of forward propagation. Set the value to the name of the top operator in forward propagation. You can save the graph as a .pbtxt file by using **tf.io.write\_graph** in the training script to obtain this name.
- **bp\_point**: required when **training\_trace** is **on**. Specifies the end point of the backward propagated operator in iteration traces, to record the end timestamp of backward propagation. **BP\_POINT** and **FP\_POINT** are used to compute the time used by forward and backward propagation. Set the value to the name of the bottom operator in backward propagation. You can save the graph as a .pbtxt file by using **tf.io.write\_graph** in the training script to obtain this name.

- <span id="page-200-0"></span>● **aic\_metrics**: AI Core metrics to profile. **ArithmeticUtilization**: percentages of arithmetic utilization. **PipeUtilization** (default): percentages of time taken by the compute units and MTEs. **Memory**: percentages of external memory read/write instructions. **MemoryL0**: percentages of internal memory read/write instructions. **ResourceConflictRatio**: percentages of pipeline queue Instructions. NO TE

Online inference supports **task\_trace** and **aicpu** but does not support **training\_trace**.

#### **Example**

export PROFILING\_OPTIONS='{"output":"/tmp/ profiling","training\_trace":"on","task\_trace":"on","aicpu":"on","fp\_point":"resnet\_model/conv2d/ Conv2Dresnet\_model/batch\_normalization/FusedBatchNormV3\_Reduce","bp\_point":"gradients/ AddN\_70","aic\_metrics":"PipeUtilization"}'

# **10.3.9 SKT\_ENABLE**

#### **Description**

Enables superkernel. If enabled, operator tasks are fused into one for delivery to accelerate task scheduling and network execution.

- **1**: enabled.
- **0**: disabled. The default value is **false**.

#### **Example**

export SKT\_ENABLE=1

# **10.3.10 OP\_NO\_REUSE\_MEM**

#### **Description**

Selects the operator to skip in memory reuse. (Memory reuse is enabled by default.)

The specified operator (or operators separated by commas) will use exclusively allocated memory.

#### **Examples**

- Configuration by node name: export OP\_NO\_REUSE\_MEM=gradients/logits/semantic/kernel/Regularizer/l2\_regularizer\_grad/ Mul\_1,resnet\_v1\_50/conv1\_1/BatchNorm/AssignMovingAvg2
- Configuration by operator type: export OP\_NO\_REUSE\_MEM=FusedMulAddN,BatchNorm
- Configuration by operator name and operator type: export OP\_NO\_REUSE\_MEM=FusedMulAddN, resnet\_v1\_50/conv1\_1/BatchNorm/AssignMovingAvg

# <span id="page-201-0"></span>**10.3.11 ENABLE\_NETWORK\_ANALYSIS\_DEBUG**

#### **Description**

Ignores graph build failures. If this environment variable is set (to any value), GE always returns a success even if graph build fails. In this way, the adapter can still deliver the graph to GE.

#### **Example**

export ENABLE\_NETWORK\_ANALYSIS\_DEBUG=1

# **10.3.12 DUMP\_GE\_GRAPH**

#### **Description**

Sets the graph dump mode.

- **1**: dumps all.
- **2**: dumps without data such as weights.
- **3**: dumps only node relationships.

#### **Example**

export DUMP\_GE\_GRAPH=1

# **10.3.13 DUMP\_GRAPH\_LEVEL**

#### **Description**

Sets the graph to dump.

- **1**: dumps all graphs.
- **2** (default): dumps all graphs except sub-graphs.
- **3**: dumps the generated built graph.

This environment variable takes effect only when **DUMP\_GE\_GRAPH** is enabled.

#### **Example**

export DUMP\_GRAPH\_LEVEL=1

# **10.3.14 TE\_PARALLEL\_COMPILER**

#### **Description**

Sets the maximum number of parallel operator build processes. Defaults to **8**. When the value is greater than **0**, parallel build is enabled.

Parallel build is especially useful when a large network is used. The maximum value is calculated as follows: Maximum value = 80% the number of CPU cores/ Number of Ascend AI Processors.

#### <span id="page-202-0"></span>**Example**

export TE\_PARALLEL\_COMPILER=8

# **10.3.15 ASCEND\_MAX\_OP\_CACHE\_SIZE**

#### **Description**

Sets the maximum size (MB) of the cache folder of a specified processor in the scenario where the operator build cache function is enabled. Defaults to **500** (MB).

#### **Example**

export ASCEND\_MAX\_OP\_CACHE\_SIZE=500

# **10.3.16 ASCEND\_REMAIN\_CACHE\_SIZE\_RATIO**

#### **Description**

Specifies how much (in percentage) of the build cache space is retained when the build cache space of a specified processor reaches **MAX\_OP\_CACHE\_SIZE** in the scenario where the operator build cache function is enabled. Defaults to **50** (%).

#### **Example**

export ASCEND\_REMAIN\_CACHE\_SIZE\_RATIO=50

# **10.3.17 HCCL\_INTRA\_ROCE\_ENABLE**

#### **Description**

Applies to Atlas 300T training card (model: 9000) only. Specifies whether to use the RoCE path for multi-device communication within the server.

#### **Example**

export HCCL\_INTRA\_ROCE\_ENABLE=1

# **10.3.18 HCCL\_INTRA\_PCIE\_ENABLE**

#### **Description**

Applies to Atlas 300T training card (model: 9000) only. Specifies whether to use the PCIe path for multi-processor communication within the server. Use this environment variable in conjunction with **HCCL\_INTRA\_ROCE\_ENABLE**.

**HCCL\_INTRA\_PCIE\_ENABLE** and **HCCL\_INTRA\_ROCE\_ENABLE** control only the communication mode between the Ascend AI Processors in a server in the Atlas 300T training card (model: 9000) scenario. The mode of communication between servers is fixed to RoCE path communication. The following describes the configuration combination of **HCCL\_INTRA\_PCIE\_ENABLE** and **HCCL\_INTRA\_ROCE\_ENABLE**.

- <span id="page-203-0"></span>● If neither **HCCL\_INTRA\_PCIE\_ENABLE** or **HCCL\_INTRA\_ROCE\_ENABLE** is configured or they are both set to **0**, the PCIe path is used for communication between the Ascend AI Processors in a server.
- If **HCCL\_INTRA\_PCIE\_ENABLE** is set to **1** and **HCCL\_INTRA\_ROCE\_ENABLE** is set to **0**, the PCIe path is used for communication between the Ascend AI Processors in a server.
- If **HCCL\_INTRA\_PCIE\_ENABLE** is set to **0** and **HCCL\_INTRA\_ROCE\_ENABLE** is set to **1**, the RoCE path is used for communication between the Ascend AI Processors in a server **(recommended)**.

NO TE

**HCCL\_INTRA\_PCIE\_ENABLE** and **HCCL\_INTRA\_ROCE\_ENABLE** cannot be both set to **1**.

#### **Example**

export HCCL\_INTRA\_PCIE\_ENABLE=1

# **10.3.19 ASCEND\_SLOG\_PRINT\_TO\_STDOUT**

#### **Description**

Enables log printing.

- **0** or not configured: disabled.
- **1**: enabled.

#### **Example**

export ASCEND\_SLOG\_PRINT\_TO\_STDOUT=1

# **10.3.20 ASCEND\_GLOBAL\_LOG\_LEVEL**

#### **Description**

Sets the global log level of application logs.

- **0**: DEBUG
- **1**: INFO
- **2**: WARNING
- **3**: ERROR
- **4**: NULL (no log output)
- Other values: invalid

#### **Example**

export ASCEND\_GLOBAL\_LOG\_LEVEL=1

# <span id="page-204-0"></span>**10.3.21 ASCEND\_GLOBAL\_EVENT\_ENABLE**

#### **Description**

Enables event log level of application logs.

- **0**: disabled
- **1**: enabled
- Other values: invalid

If the environment variable is not set or set to an invalid value, the event log level is enabled by default.

#### **Example**

export ASCEND\_GLOBAL\_EVENT\_ENABLE=0

# **10.3.22 ASCEND\_LOG\_DEVICE\_FLUSH\_TIMEOUT**

#### **Description**

Sets the time for transferring application logs from the device to the host. Must be set to an integer within the range of 0–180000, in milliseconds.

If the environment variable is not set or set to an invalid value, the default timeout interval 3000 ms is used.

#### **Example**

export ASCEND\_LOG\_DEVICE\_FLUSH\_TIMEOUT=5000

# **10.4 Device Resource Configuration File Templates**

#### **Overview**

Before training, you need to prepare a device resource configuration file (that is, a ranktable file) and upload it to the operating environment. A ranktable file describes the Ascend AI Processor resources used for training and its path is specified by the **RANK\_TABLE\_FILE** environment variable.

A ranktable file is in JSON format. For example, for training with two Ascend AI Processors (that is, two devices), the file can be named **rank\_table\_2p.json**.

#### **Precautions**

- 1. Configure the number of devices used in training in the ranktable file. Currently, two configuration templates are supported. Template 1 is recommended for new scenarios, and template 2 is compatible with some existing scenarios.
- 2. In single-server or cluster scenarios, the number of configured devices must be one, two, four, or eight times the number of servers participating in training. When the number of devices used for training is two or four times

the number of the training servers, devices 0–3 and devices 4–7 form a network respectively, and cross-network cluster creation is not supported.

- 3. For training with Atlas 300T training card (model: 9000), the number of configured devices must not be greater than the number of devices in the server, and **template 1** must be used for the configuration.

#### **Template 1 (Recommended)**

{ "server\_count":"1", // Number of servers. In this example, there is only one AI Server. "server\_list": [ { "device":[// List of devices in the server { "device\_id":"0", // Device HDC channel ID "device\_ip":"192.168.1.8", // Actual NIC IP address of the device "rank\_id":"0" // Rank ID, indexed starting at 0 }, { "device\_id":"4", "device\_ip":"192.168.1.9", "rank\_id":"1" } ], "server\_id":"10.0.0.10" // Server ID, which is an IP address in dotted decimal notation } ], "status":"completed", // Ranktable availability flag. The value **completed** indicates that the ranktable is available. "version":"1.0" // Version of the ranktable template. Must be **1.0**. }

**Table 10-9** Description of the ranktable file (template 1)

| Item         | Description                                      | Require                           |
|--------------|--------------------------------------------------|-----------------------------------|
| server_count | Number of server instances involved in training  | Required                          |
| status       | Availability flag of the rank table              |                                   |
|              | ● completed                                      | : The rank table is available and |
|              | ● initializing                                   | : The rank table is unavailable   |
| version      | Version of a ranktable template. Currently, only |                                   |
|              | 1.0 is supported.                                |                                   |
| server_list  | List of server instances involved in training.   | Required                          |
| server_id    | Physical IP address of a server, which is a      |                                   |
|              | example, 10.0.0.10                               |                                   |

#### **Template 2 (Compatible with Some Existing Scenarios)**

{

"status":"completed", // Ranktable availability flag. The value **completed** indicates that the ranktable is

available.

"group\_count":"1", // Number of groups. The recommended value is **1**.

"group\_list": // List of groups

 [ {

 "group\_name":"hccl\_world\_group",// Group name. The recommended value is **hccl\_world\_group**. "instance\_count":"2", // Number of instances, which can be considered as the number of containers

in the container scenario.

"device\_count":"2", // Number of all devices in the group

"instance\_list":[

{

 "pod\_name":"tf-bae41", // Instance name, which is generally the container name. "server\_id":"10.0.0.10", // Server ID, which is an IP address in dotted decimal notation

"devices":[ // List of devices of the instance

 "device\_id":"0", // Device HDC channel ID "device\_ip":"192.168.1.8", // Actual NIC IP address of the device } ] }, { "pod\_name":"tf-tbdf1", "server\_id":"10.0.0.10", "devices":[ { "device\_id":"1", "device\_ip":"192.168.1.9" } ] } ] } ] }

**Table 10-10** Description of the ranktable file (template 2)

| Item Description      | Require                                       |
|-----------------------|-----------------------------------------------|
| status                | Availability flag of the rank table           |
| ●                     | completed : The rank table is available and   |
| ●                     | initializing : The rank table is unavailable  |
| group_count           | Number of groups that a user applies for. The |
|                       | recommended value is 1                        |
| group_list Group list | Required                                      |
| group_name            | Group name. When group_count is set to 1 ,    |
|                       | hccl_world_group or leave it empty. In the    |
|                       | hccl_world_group is created regardless of the |
| configuration         | file, the system automatically                |
| instance_count        | The value of this parameter must be the same  |
| device_count          | Number of devices in a group. Required        |

<span id="page-209-0"></span>11.1 What Do I Do If Network Size Reaches Threshold? [11.2 What Do I Do If Training Times Out Due to Too Many Dataset Shuffle](#page-210-0) [Operations?](#page-210-0) [11.3 How Do I Determine fp\\_point and bp\\_point?](#page-211-0) [11.4 What Do I Do If uint8 and quint8 Addition/Subtraction Overflows?](#page-213-0)

# **11.1 What Do I Do If Network Size Reaches Threshold?**

#### **Symptom**

When the batch size or network size is too large, a message is displayed indicating that the memory usage exceeds the threshold.

[ERROR] GE(179560,python3.7):2020-10-31-11:06:40.656.258 [graphengine/ge/graph/manager/ graph\_var\_manager.cc:285]182539 AssignVarMem: ErrorNo: 1343225857(Parameter's invalid!) **Out of memory** : current var size[5382237696] exceeds total var size[5368709120] [ERROR] GE(179560,python3.7):2020-10-31-11:06:40.656.374 [graphengine/ge/graph/manager/ graph\_var\_manager.cc:504]182539 AssignVarMem: ErrorNo: 1343225860(Internal errors) AssignVarMem by offset failed. [ERROR] GE(179560,python3.7):2020-10-31-11:06:40.656.420 [graphengine/ge/graph/build/memory/ var\_mem\_assign\_util.cc:65]182539 AssignStaticMemory2Node: ErrorNo: -1(failed) [ERROR] GE(179560,python3.7):2020-10-31-11:06:40.669.315 [graphengine/ge/graph/build/memory/ memory\_assigner.cc:27]182539 AssignMemory: ErrorNo: -1(failed) Memory assigner failed [ERROR] GE(179560,python3.7):2020-10-31-11:06:40.669.401 [graphengine/ge/graph/build/ model\_builder.cc:722]182539 BuildModelForGetTask: ErrorNo: -1(failed) Assign Memory Failed!

#### **Cause Analysis**

By default, the framework isolates the weight memory and feature map memory for management convenience. By default, 5 GB is allocated for weight use, and 26 GB is allocated for feature map use.

#### **Solution**

You can tune **graph\_memory\_max\_size** and **variable\_memory\_max\_size** to adjust the memory limits. The prerequisite is that the total memory of the weight and feature map is within 31 GB.

<span id="page-210-0"></span>graph\_mem = 1024 \* 1024 \* 1024 \* 16 var\_mem = 1024 \* 1024 \* 1024 \* 15 run\_config = NPURunConfig(hcom\_parallel=False, enable\_data\_pre\_proc=True, session\_config=session\_config, model\_dir = params['log\_dir'], iterations\_per\_loop=1, graph\_memory\_max\_size = graph\_mem, variable\_memory\_max\_size = var\_mem)

# **11.2 What Do I Do If Training Times Out Due to Too Many Dataset Shuffle Operations?**

#### **Symptom**

An error is reported during the training process.

2020-11-27 11:26:00.510219: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 2169 of 10000 2020-11-27 11:26:10.454132: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 3252 of 10000 2020-11-27 11:26:20.375176: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 3915 of 10000 2020-11-27 11:26:30.543144: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 4672 of 10000 2020-11-27 11:26:40.479843: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 5439 of 10000 2020-11-27 11:26:50.496244: I tensorflow/core/kernels/data/shuffle\_dataset\_op.cc:145] Filling up shuffle buffer (this may take a while): 6232 of 10000 2020-11-27 11:26:50.638388: W tensorflow/core/framework/op\_kernel.cc:1639] Unavailable: Internal errors 2020-11-27 11:26:53.638664: F tf\_adapter/kernels/geop\_npu.cc:669] GeOp33\_0GEOP::::DoRunAsync Failed [ERROR] RUNTIME(62299)model execute error, retCode=0x91, [the model stream execute failed]. [ERROR] RUNTIME(62299)model execute task failed, device\_id=0, model stream\_id=575, model task\_id=1, model\_id=522, first\_task\_id=65535 Fatal Python error: Aborted

#### **Cause Analysis**

When data preprocessing is offloaded to the NPU, preprocessing is performed in parallel with forward and backward propagation. If too many data shuffle operations are involved during preprocessing, preprocessing output will be unavailable in a long time after the forward propagation task is delivered. As a result, the forward propagation task times out.

Assume that the shuffle buffer size is set to **10000**. According to the preceding log, when the forward propagation task times out, the buffer receives only 6232 out of 10000 pieces of data. As a result, the task timeout error is printed.

#### **Solution**

You can resolve this problem in either of thr following ways:

Reduce the number of shuffle operations based on the actual capacity. In this example, only 6232 shuffle operations are complete. Therefore, we can set **buffer\_size** to **5000**.

<span id="page-211-0"></span>Disable the data preprocessing offloading switch (**enable\_data\_pre\_proc = False**) so that preprocessing and forward and backward propagation can be executed in serial. However, the performance may be compromised.

run\_config = NPURunConfig(enable\_data\_pre\_proc=False)

# **11.3 How Do I Determine fp\_point and bp\_point?**

To trace training iterations to profile a training job, you need to input the dotting operators for forward and backward propagation to obtain the forward and backward compute durations.

Obtain **fp\_point** and **bp\_point** by taking the following steps:

You can save the graph as a .pbtxt file by using **tf.io.write\_graph** in the training script to obtain this name.

Start the search from the top. The first node (excluding the data and storage nodes) is the **fp\_point** operator. Data and storage nodes can be identified by the **name** or **op** field. Generally, operators with **op** set to Const, VariableV2, IteratorV2, Identity, Reshape, or Cast, or **name** containing step, Dataset, seed, or kernel should be excluded. The following figure shows the **fp\_point** operator of ResNet-50.

As for the **bp\_point** operator, start the search from the bottom. The first node with gradient is the **bp\_point** operator. The following figure shows the **bp\_point** operator of ResNet-50.

The operator may have been fused or renamed. To solve this problem, look up the name of the operator in the **ge\_proto\_xxxxx\_Build.txt** graph generated by GE. If an exact match is found, the operator name can be used directly. If a fuzzy match is found (generally with a **\_1** suffix), the operator name in GE should be used.

# **11.4 What Do I Do If uint8 and quint8 Addition/ Subtraction Overflows?**

#### **Symptom**

The cumulative addition and subtraction result of operators of type uint8 and quint8 types overflow, which are different from the theoretical results.

For example, the theoretical compute result with the uint8 data type is **257**. However, the actual compute result on the Ascend AI Processor is **255**.

#### **Cause Analysis**

Ascend AI Processor addresses value overflows by saturating at the upper limits. Because **257** exceeds the maximum value (**255**) of an uint8, **255** is output.

### **Solution**

You can choose to scale the value range to avoid overflows.

<span id="page-213-0"></span>

# **12 Appendixes**

<span id="page-214-0"></span>12.1 How Do I Install GCC 7.3.0?

[12.2 Change History](#page-215-0)

# **12.1 How Do I Install GCC 7.3.0?**

Perform the following steps as the **root** user.

**Step 1** Download **gcc-7.3.0.tar.gz** from **[https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/](https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/gcc-7.3.0/gcc-7.3.0.tar.gz) [gcc-7.3.0/gcc-7.3.0.tar.gz](https://mirrors.tuna.tsinghua.edu.cn/gnu/gcc/gcc-7.3.0/gcc-7.3.0.tar.gz)**.

**Step 2** To install GCC, you need to reserve adequate temporary space. You can run the following command to clear the **/tmp** directory in advance:

sudo rm -rf /tmp/\*

**Step 3** Install the dependency.

For CentOS/BCLinux, run the following command:

yum install bzip2

For Ubuntu/Debian, run the following command:

apt-get install bzip2

#### **Step 4** Build and install GCC.

- 1. Go to the directory where the source code package **gcc-7.3.0.tar.gz** is located and run the following command to extract it: tar -zxvf gcc-7.3.0.tar.gz
- 2. Go to the extracted directory and run the following command to download the GCC dependency packages:

cd gcc-7.3.0

./contrib/download\_prerequisites

If an error is reported during the command execution, run the following commands in the **gcc-7.3.0/** directory to download the dependency packages:

wget http://gcc.gnu.org/pub/gcc/infrastructure/gmp-6.1.0.tar.bz2 wget http://gcc.gnu.org/pub/gcc/infrastructure/mpfr-3.1.4.tar.bz2 wget http://gcc.gnu.org/pub/gcc/infrastructure/mpc-1.0.3.tar.gz wget http://gcc.gnu.org/pub/gcc/infrastructure/isl-0.16.1.tar.bz2

<span id="page-215-0"></span>After downloading the preceding dependency packages, run the following command:

./contrib/download\_prerequisites

If the verification fails, check whether the dependency package is repeatedly downloaded. The package should be downloaded at a time.

- 3. Run the following commands for configuration, build, and installation. ./configure --enable-languages=c,c++ --disable-multilib --with-system-zlib --prefix=/usr/local/ linux\_gcc7.3.0 make -j15 # The value **15** indicates the number of CPUs, which is configurable and can be queried by running **grep -w processor /proc/cpuinfo|wc -l**. make install

#### CA UTION

The **--prefix** parameter is used to specify the linux\_gcc7.3.0 installation path, which is configurable. Do not set it to **/usr/local** or **/usr**, which is the default installation path for the GCC installed by using the software source. Otherwise, a conflict occurs and the original GCC compilation environment of the system is damaged. In this example, the installation path is set to **/usr/ local/linux\_gcc7.3.0**.

#### **Step 5** Set the environment variable.

The build environment after GCC upgrade is required for training. Therefore, you need to configure the following environment variable in the training script:

export LD\_LIBRARY\_PATH=\${install\_path}/lib64:\${LD\_LIBRARY\_PATH}

**\${install\_path}** indicates the GCC 7.3.0 installation path configured in **3**. In this example, the GCC 7.3.0 installation path is **/usr/local/gcc7.3.0/**.

#### NO TE

The environment variable needs to be configured only when you need to use the build environment after the GCC upgrade.

**----End**

# **12.2 Change History**