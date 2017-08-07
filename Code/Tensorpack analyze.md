---
title: Tensorpack analyze
---
[toc]

## Code Architecture

tensorpack/examples/DeepQNetwork/DQN.py
https://github.com/ppwwyyxx/tensorpack/blob/master/examples/DeepQNetwork/DQN.py
QueueInputTrainer   tensorpack/tensorpack/train/feedfree.py

```python
config.data = QueueInput(config.dataflow, input_queue)
```
https://github.com/ppwwyyxx/tensorpack/blob/master/tensorpack/train/feedfree.py#L68


https://github.com/ppwwyyxx/tensorpack/blob/master/tensorpack/graph_builder/input_source.py#L201
FeedfreeInput

https://github.com/ppwwyyxx/tensorpack/blob/master/tensorpack/graph_builder/input_source.py#L227-L230
```python
self.queue = tf.FIFOQueue(
        50, [x.dtype for x in self._input_placehdrs],
        name='input_queue')
self.thread = EnqueueThread(self.queue, self.ds, self._input_placehdrs)
```
EnqueueThread

https://github.com/ppwwyyxx/tensorpack/blob/master/tensorpack/utils/concurrency.py#L101-L135
ShareSessionThread

Share Session
Share Graph

## Output Log
```
(tf3)seraphli@seraphli-MS-7885:~/Github/tensorpack$ ./examples/DeepQNetwork/DQN.py --rom breakout.bin
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcublas.so.8.0 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcudnn.so.5 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcufft.so.8.0 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcuda.so.1 locally
I tensorflow/stream_executor/dso_loader.cc:135] successfully opened CUDA library libcurand.so.8.0 locally
[0724 10:43:06 @fs.py:89] WRN Env var $TENSORPACK_DATASET not set, using /home/seraphli/tensorpack_data for datasets.
[0724 10:43:06 @DQN.py:156] ROM: breakout.bin, Num Actions: 4
[0724 10:43:06 @logger.py:92] WRN Log directory train_log/DQN-breakout exists! Please either backup/delete it, or use a new directory.
[0724 10:43:06 @logger.py:94] WRN If you're resuming from a previous run you can choose to keep it.
[0724 10:43:06 @logger.py:95] Select Action: k (keep) / b (backup) / d (delete) / n (new) / q (quit):
d
[0724 10:43:13 @logger.py:73] Argv: ./examples/DeepQNetwork/DQN.py --rom breakout.bin
[0724 10:43:13 @expreplay.py:145] Number of Legal actions: 4
[0724 10:43:13 @param.py:189] Use train_log/DQN-breakout/hyper.txt to control hyperparam learning_rate.
[0724 10:43:13 @gpu.py:32] Loading devices by TensorFlow ...
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE3 instructions, but these are available on your machine and could speed up CPU computations.
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use SSE4.2 instructions, but these are available on your machine and could speed up CPU computations.
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX instructions, but these are available on your machine and could speed up CPU computations.
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use AVX2 instructions, but these are available on your machine and could speed up CPU computations.
W tensorflow/core/platform/cpu_feature_guard.cc:45] The TensorFlow library wasn't compiled to use FMA instructions, but these are available on your machine and could speed up CPU computations.
I tensorflow/core/common_runtime/gpu/gpu_device.cc:885] Found device 0 with properties: 
name: GeForce GTX 1080
major: 6 minor: 1 memoryClockRate (GHz) 1.8225
pciBusID 0000:03:00.0
Total memory: 7.92GiB
Free memory: 7.34GiB
I tensorflow/core/common_runtime/gpu/gpu_device.cc:906] DMA: 0 
I tensorflow/core/common_runtime/gpu/gpu_device.cc:916] 0:   Y 
I tensorflow/core/common_runtime/gpu/gpu_device.cc:975] Creating TensorFlow device (/gpu:0) -> (device: 0, name: GeForce GTX 1080, pci bus id: 0000:03:00.0)
[0724 10:43:13 @input_source.py:222] Setting up the queue for CPU prefetching ...
[0724 10:43:13 @common.py:130] conv0 input: [None, 84, 84, 4]
[0724 10:43:13 @common.py:138] conv0 output: [None, 21, 21, 32]
[0724 10:43:13 @common.py:130] conv1 input: [None, 21, 21, 32]
[0724 10:43:13 @common.py:138] conv1 output: [None, 11, 11, 64]
[0724 10:43:13 @common.py:130] conv2 input: [None, 11, 11, 64]
[0724 10:43:13 @common.py:138] conv2 output: [None, 11, 11, 64]
[0724 10:43:13 @common.py:130] fc0 input: [None, 11, 11, 64]
[0724 10:43:13 @common.py:138] fc0 output: [None, 512]
[0724 10:43:13 @common.py:130] fct input: [None, 512]
[0724 10:43:13 @common.py:138] fct output: [None, 4]
[0724 10:43:13 @common.py:130] target/conv0 input: [None, 84, 84, 4]
[0724 10:43:13 @common.py:138] target/conv0 output: [None, 21, 21, 32]
[0724 10:43:13 @common.py:130] target/conv1 input: [None, 21, 21, 32]
[0724 10:43:13 @common.py:138] target/conv1 output: [None, 11, 11, 64]
[0724 10:43:13 @common.py:130] target/conv2 input: [None, 11, 11, 64]
[0724 10:43:13 @common.py:138] target/conv2 output: [None, 11, 11, 64]
[0724 10:43:13 @common.py:130] target/fc0 input: [None, 11, 11, 64]
[0724 10:43:13 @common.py:138] target/fc0 output: [None, 512]
[0724 10:43:13 @common.py:130] target/fct input: [None, 512]
[0724 10:43:13 @common.py:138] target/fct output: [None, 4]
[0724 10:43:14 @model_utils.py:47] Model Parameters: 
name           shape               dim
-------------  --------------  -------
conv0/W:0      [8, 8, 4, 32]      8192
conv0/b:0      [32]                 32
conv0/alpha:0  []                    1
conv1/W:0      [4, 4, 32, 64]    32768
conv1/b:0      [64]                 64
conv1/alpha:0  []                    1
conv2/W:0      [3, 3, 64, 64]    36864
conv2/b:0      [64]                 64
conv2/alpha:0  []                    1
fc0/W:0        [7744, 512]     3964928
fc0/b:0        [512]               512
fct/W:0        [512, 4]           2048
fct/b:0        [4]                   4
Total #vars=13, #param=4045479 (15.43 MB assuming all float32)
[0724 10:43:14 @base.py:136] Setup callbacks graph ...
[0724 10:43:14 @DQNModel.py:93] target/conv0/W <- conv0/W
[0724 10:43:14 @DQNModel.py:93] target/conv0/b <- conv0/b
[0724 10:43:14 @DQNModel.py:93] target/conv0/alpha <- conv0/alpha
[0724 10:43:14 @DQNModel.py:93] target/conv1/W <- conv1/W
[0724 10:43:14 @DQNModel.py:93] target/conv1/b <- conv1/b
[0724 10:43:14 @DQNModel.py:93] target/conv1/alpha <- conv1/alpha
[0724 10:43:14 @DQNModel.py:93] target/conv2/W <- conv2/W
[0724 10:43:14 @DQNModel.py:93] target/conv2/b <- conv2/b
[0724 10:43:14 @DQNModel.py:93] target/conv2/alpha <- conv2/alpha
[0724 10:43:14 @DQNModel.py:93] target/fc0/W <- fc0/W
[0724 10:43:14 @DQNModel.py:93] target/fc0/b <- fc0/b
[0724 10:43:14 @DQNModel.py:93] target/fct/W <- fct/W
[0724 10:43:14 @DQNModel.py:93] target/fct/b <- fct/b
[0724 10:43:14 @predictor_factory.py:59] Building predictor tower 'towerp0' on device /gpu:0 ...
[0724 10:43:14 @summary.py:30] Maintain moving average summary of 16 tensors.
[0724 10:43:14 @base.py:140] Creating the session ...
I tensorflow/core/common_runtime/gpu/gpu_device.cc:975] Creating TensorFlow device (/gpu:0) -> (device: 0, name: GeForce GTX 1080, pci bus id: 0000:03:00.0)
[0724 10:43:14 @sesscreate.py:34] Global variables initialized.
[0724 10:43:14 @base.py:144] Initializing the session ...
[0724 10:43:14 @base.py:151] Graph Finalized.
[0724 10:43:14 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
[0724 10:43:14 @expreplay.py:166] Populating replay memory with epsilon=1.0 ...
  0%|                                                                                                                                                                                 |0/50000[00:00<?,?it/s]Failed to load OpenCL runtime (expected version 1.1+)
100%|###################################################################################################################################################################|50000/50000[00:44<00:00,1124.59it/s]
[0724 10:43:59 @param.py:144] exploration at epoch 1 will change to 1.00000000
[0724 10:43:59 @concurrency.py:36] Starting EnqueueThread ...
[0724 10:43:59 @base.py:188] Start Epoch 1 ...
 10%|################3                                                                                                                                                    |2479/25000[00:23<03:24,110.31it/s][0724 10:44:22 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 20%|################################7                                                                                                                                    |4958/25000[00:46<03:05,107.87it/s][0724 10:44:45 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 30%|#################################################3                                                                                                                   |7479/25000[01:09<02:40,108.96it/s][0724 10:45:08 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 40%|#################################################################9                                                                                                   |9993/25000[01:32<02:21,105.85it/s][0724 10:45:32 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 50%|#################################################################################9                                                                                  |12493/25000[01:56<01:55,107.99it/s][0724 10:45:55 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 60%|##################################################################################################1                                                                 |14962/25000[02:19<01:33,107.01it/s][0724 10:46:18 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 70%|##################################################################################################################6                                                 |17482/25000[02:42<01:10,106.35it/s][0724 10:46:41 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 80%|##################################################################################################################################8                                 |19951/25000[03:05<00:46,108.92it/s][0724 10:47:05 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 90%|###################################################################################################################################################5                |22496/25000[03:29<00:23,107.61it/s][0724 10:47:28 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
100%|###################################################################################################################################################################9|24993/25000[03:52<00:00,106.63it/s][0724 10:47:52 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
100%|####################################################################################################################################################################|25000/25000[03:53<00:00,107.25it/s]
[0724 10:47:52 @base.py:198] Epoch 1 (global_step 25000) finished, time:233.11 sec.
[0724 10:47:52 @saver.py:85] Model saved to train_log/DQN-breakout/model-25000.
[0724 10:47:52 @param.py:144] exploration at epoch 2 will change to 0.91000000
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv0/W/rms: 0.00012757
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv0/alpha/rms: 0.0074362
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv0/b/rms: 0.0030228
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv1/W/rms: 6.1893e-05
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv1/alpha/rms: 0.0070182
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv1/b/rms: 0.0027063
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv2/W/rms: 5.5702e-05
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv2/alpha/rms: 0.0066997
[0724 10:47:52 @monitor.py:310] SummaryGradient/conv2/b/rms: 0.0024549
[0724 10:47:52 @monitor.py:310] SummaryGradient/fc0/W/rms: 6.8851e-06
[0724 10:47:52 @monitor.py:310] SummaryGradient/fc0/b/rms: 0.00026041
[0724 10:47:52 @monitor.py:310] SummaryGradient/fct/W/rms: 5.9511e-05
[0724 10:47:52 @monitor.py:310] SummaryGradient/fct/b/rms: 0.0033889
[0724 10:47:52 @monitor.py:310] cost: 0.001137
[0724 10:47:52 @monitor.py:310] expreplay/max_score: 9
[0724 10:47:52 @monitor.py:310] expreplay/mean_score: 0.84249
[0724 10:47:52 @monitor.py:310] input_queue_size: 49.998
[0724 10:47:52 @monitor.py:310] learning_rate: 0.001
[0724 10:47:52 @monitor.py:310] param-summary/conv0/W-rms: 0.095246
[0724 10:47:52 @monitor.py:310] param-summary/conv1/W-rms: 0.06483
[0724 10:47:52 @monitor.py:310] param-summary/conv2/W-rms: 0.060192
[0724 10:47:52 @monitor.py:310] param-summary/fc0/W-rms: 0.016261
[0724 10:47:52 @monitor.py:310] param-summary/fct/W-rms: 0.078549
[0724 10:47:52 @monitor.py:310] predict_reward: 0.31057
[0724 10:47:52 @base.py:188] Start Epoch 2 ...
 10%|################2                                                                                                                                                    |2461/25000[00:23<03:36,103.93it/s][0724 10:48:16 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 20%|################################9                                                                                                                                    |4999/25000[00:47<03:07,106.91it/s][0724 10:48:40 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 30%|#################################################4                                                                                                                   |7485/25000[01:10<02:43,107.33it/s][0724 10:49:03 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 40%|#################################################################9                                                                                                   |9987/25000[01:34<02:21,106.28it/s][0724 10:49:26 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 50%|#################################################################################9                                                                                  |12488/25000[01:57<01:59,104.70it/s][0724 10:49:50 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 60%|##################################################################################################                                                                  |14948/25000[02:20<01:34,106.45it/s][0724 10:50:13 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 70%|##################################################################################################################7                                                 |17497/25000[02:44<01:09,107.27it/s][0724 10:50:37 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 80%|###################################################################################################################################1                                |19992/25000[03:08<00:46,107.20it/s][0724 10:51:00 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 90%|###################################################################################################################################################5                |22486/25000[03:31<00:23,105.10it/s][0724 10:51:24 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
100%|###################################################################################################################################################################8|24981/25000[03:54<00:00,105.40it/s][0724 10:51:47 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
100%|####################################################################################################################################################################|25000/25000[03:55<00:00,106.30it/s]
[0724 10:51:47 @base.py:198] Epoch 2 (global_step 50000) finished, time:235.18 sec.
[0724 10:51:47 @saver.py:85] Model saved to train_log/DQN-breakout/model-50000.
[0724 10:51:47 @param.py:144] exploration at epoch 3 will change to 0.82000000
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv0/W/rms: 0.0001457
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv0/alpha/rms: 0.011308
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv0/b/rms: 0.001996
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv1/W/rms: 5.4917e-05
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv1/alpha/rms: 0.012538
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv1/b/rms: 0.0017477
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv2/W/rms: 4.2925e-05
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv2/alpha/rms: 0.0029696
[0724 10:51:47 @monitor.py:310] SummaryGradient/conv2/b/rms: 0.002482
[0724 10:51:47 @monitor.py:310] SummaryGradient/fc0/W/rms: 6.9543e-06
[0724 10:51:47 @monitor.py:310] SummaryGradient/fc0/b/rms: 0.00028959
[0724 10:51:47 @monitor.py:310] SummaryGradient/fct/W/rms: 6.7623e-05
[0724 10:51:47 @monitor.py:310] SummaryGradient/fct/b/rms: 0.0029173
[0724 10:51:47 @monitor.py:310] cost: 0.0010428
[0724 10:51:47 @monitor.py:310] expreplay/max_score: 7
[0724 10:51:47 @monitor.py:310] expreplay/mean_score: 0.82677
[0724 10:51:47 @monitor.py:310] input_queue_size: 49.994
[0724 10:51:47 @monitor.py:310] learning_rate: 0.001
[0724 10:51:47 @monitor.py:310] param-summary/conv0/W-rms: 0.10349
[0724 10:51:47 @monitor.py:310] param-summary/conv1/W-rms: 0.067335
[0724 10:51:47 @monitor.py:310] param-summary/conv2/W-rms: 0.061857
[0724 10:51:47 @monitor.py:310] param-summary/fc0/W-rms: 0.016336
[0724 10:51:47 @monitor.py:310] param-summary/fct/W-rms: 0.091077
[0724 10:51:47 @monitor.py:310] predict_reward: 0.38159
[0724 10:51:47 @base.py:188] Start Epoch 3 ...
 10%|################3                                                                                                                                                    |2470/25000[00:23<03:32,106.05it/s][0724 10:52:11 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 20%|#################################                                                                                                                                     |4983/25000[00:47<03:29,95.65it/s][0724 10:52:35 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 30%|#################################################3                                                                                                                   |7477/25000[01:11<02:45,106.04it/s][0724 10:52:59 @graph.py:63] Running Op PeriodicTrigger/RunOp/update_target_network ...
 31%|###################################################1                                                                                                                 |7745/25000[01:13<02:42,105.87it/s]^C[0724 10:53:02 @base.py:206] Detected Ctrl-C and exiting main loop.
 31%|###################################################3                                                                                                                 |7777/25000[01:14<02:44,104.89it/s]
W tensorflow/core/kernels/queue_base.cc:294] _0_input_queue: Skipping cancelled enqueue attempt with queue not closed
Segmentation fault (core dumped)
```