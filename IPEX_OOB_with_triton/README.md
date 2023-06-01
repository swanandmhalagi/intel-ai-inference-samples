# Serving DenseNet models with IPEX (w/o python backend) and Triton Server

## Description
This sample provide code to integrate Intel® Extension for PyTorch (IPEX) with Triton Inference Server framework. This readme provides a methodology to run IPEX model with out writting python backend (model.py) script for triton server.

## Preparation
Make sure that Docker is installed on host instance.
Sample images from ImageNet dataset. 

## Supported models
Currently AI Inference samples support following Bert models finetuned on Squad dataset:
- DenseNet121        - PyTorch+IPEX [DenseNet121](https://pytorch.org/hub/pytorch_vision_densenet/ "DenseNet121")

## Possible run scenarios
AI Inference samples allow user to run inference on localhost or on remote Triton Server Host. 
By default config.properties is filled with localhost run option. 

### Execution on localhost

#### 1 Download the LibTorch .zip file for the PyTorch
This example uses triton container 23.05 which uses PyTorch version 2.0.0. [Here](https://docs.nvidia.com/deeplearning/frameworks/support-matrix/index.html#framework-matrix-2023) is the list of triton containers and their corresponding built-in framework versions.

We will download the LibTorch 2.0.0 (C++\CPU cxx11 ABI) package as follows 

`$ wget https://download.pytorch.org/libtorch/cpu/libtorch-cxx11-abi-shared-with-deps-2.0.0%2Bcpu.zip`

`$ uzip libtorch-cxx11-abi-shared-with-deps-2.0.0%2Bcpu.zip` - unpack the source

#### 2 Create IPEX .so files for triton
[Visit](https://intel.github.io/intel-extension-for-pytorch/latest/tutorials/installation.html#install-via-source-compilation) and copy the link for your correspinding cxx11 ABI PyTorch version (2.0.0) -

`$ wget https://intel-extension-for-pytorch.s3.amazonaws.com/libipex/cpu/libintel-ext-pt-cxx11-abi-2.0.0%2Bcpu.run`

`$ bash libintel-ext-pt-cxx11-abi-2.0.0%2Bcpu.run install libtorch/`  - this will create libintel-ext-pt-cpu.so at libtorch/lib
  
#### 3 Create a docker container and copy files 
  
`$ docker run -it -p8000:8000 -p8001:8001 -p8002:8002 -v ${PWD}/model_repository:/models nvcr.io/nvidia/tritonserver:23.05-py3`

`$ docker cp <libtorch_path>/lib/libintel-ext-pt-cpu.so <Triton container>:/opt/tritonserver/backends/pytorch/`
 
`$ cd backends/pyorch & LD_PRELOAD="$(pwd)/libintel-ext-pt-cpu.so" tritonserver --model-repository=/models`

#### 4 Run inference 
  
`$ python3 client_imagenet.py --dataset /home/ubuntu/ImageNet/imagenet_images `  - sends requests to Triton Server Host for DenseNet model. This file uses ImagesNet images for inference. 

  
## Additional info
Downloading and loading models take some time, so please wait until you run client_imagenet.py.
Model loading progress can be tracked by following Triton Server Host docker container logs.

## Support
Please submit your questions, feature requests, and bug reports on the [GitHub issues page](https://github.com/intel/intel-ai-inference-samples/issues).

## License 
AI Inference samples project is licensed under Apache License Version 2.0. Refer to the [LICENSE](../LICENSE) file for the full license text and copyright notice.

This distribution includes third party software governed by separate license terms.

3-clause BSD license:
- [model.py](./model_repository/densenet/1/model.py) -  for PyTorch (IPEX)

This third party software, even if included with the distribution of the Intel software, may be governed by separate license terms, including without limitation, third party license terms, other Intel software license terms, and open source software license terms. These separate license terms govern your use of the third party programs as set forth in the [THIRD-PARTY-PROGRAMS](./THIRD-PARTY-PROGRAMS) file.

## Trademark Information
Intel, the Intel logo, OpenVINO, the OpenVINO logo and Intel Xeon are trademarks of Intel Corporation or its subsidiaries.
* Other names and brands may be claimed as the property of others.

&copy;Intel Corporation

