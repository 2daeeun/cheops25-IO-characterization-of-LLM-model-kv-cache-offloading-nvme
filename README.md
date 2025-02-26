# [ARTIFACT] CHEOPS'25 An I/O Characterizing Study of Offloading LLM Models and KV Caches to NVMe SSD

## Environment Setup

Install conda

```bash
wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
chmod +x Anaconda3-2024.02-1-Linux-x86_64.sh 
./Anaconda3-2024.02-1-Linux-x86_64.sh
conda create --name llm-storage python=3.11.10
conda install pip
conda install -c conda-forge gcc=12.1.0
```

Please refer to this link to install the CUDA driver: [https://docs.nvidia.com/cuda/archive/12.0.0/cuda-installation-guide-linux/index.html](https://docs.nvidia.com/cuda/archive/12.0.0/cuda-installation-guide-linux/index.html)

### Install DeepSpeed

### Install FlexGen

## Figure 2: Tensor Offloading

Download the following codes and put them in the figure2-tensor-offloading directory:
[https://github.com/deepspeedai/DeepSpeedExamples/tree/master/deepnvme](https://github.com/deepspeedai/DeepSpeedExamples/tree/master/deepnvme)

Then modify:
* line 7: the directory that the offloded tensors will be read from/written to.
* line 8: the python binary that supports the DeepSpeed framework.

```bash
cd figure2-tensor-offloading

# run the experiments
# NOTE the --fs option is only an identifier that indicates which filesystem is used, it will not reformat the file system.
python3 run_deepnvme.py --run --fs YOUR_FILE_SYSTEM

# Plot the results
python3 parse_deepnvme_fs.py --fs YOUR_FILE_SYSTEM
```

## Figure 3: Model Offloading: DeepSpeed

Download the following file from DeepSpeed examples [https://github.com/deepspeedai/DeepSpeedExamples/tree/master/inference/huggingface/zero_inference](https://github.com/deepspeedai/DeepSpeedExamples/tree/master/inference/huggingface/zero_inference):
* run_model.sh
* timer.py
* utils.py
And copy them to the 'figure3-model-offloading-deepspeed' directory.

Then modify:
* line 9: the offload dir
* line 10: change the python path to the binary of the conda environemnt where DeepSpeed is installed.

```bash
# Run the experiments
# Note that the --fs is simply an indicator on which file system is being used, it will not re-format the file system
python3 deepspeed-opt-13b-io-trace-block.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE --run

# Plot the results
python3 deepspeed-opt-13b-io-trace-parse.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE
```

## Figure 4: Model Offloading: FlexGen

Modify the following lines:
* line 9: the offload dir
* line 10: replace the python path with the binary that where the FlexGen is installed.

```bash
# Run the experiments
# Note Note that the --fs is simply an indicator on which file system is being used, it will not re-format the file system
python3 flexgen-opt30b-model-trace-block.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE --run

# Plot
python3 flexgen-opt-30b-io-trace-parse.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE
```

## Figure 5 & 6: KV cache offloding: FlexGen

Modify the following lines:
* line 9: the offload dir
* line 10: replace the python path with the binary that where the FlexGen is installed.

```bash
# Run the experiments
# Note Note that the --fs is simply an indicator on which file system is being used, it will not re-format the file system
python3 flexgen-opt-6.7b-kv-trace.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE --run

# Plot
python3 flexgen-opt-6.7b-kv-parse.py --fs YOUR_FILE_SYSTEM --bs BATCH_SIZE
```



