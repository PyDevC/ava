# Deep Learning Hardware

There are several hardware used to train and inference deep learning models, 
each has it's own way of executing the model and interacting with DL frameworks.

- General-purpose hardware: this includes GPUs, which can achieve high 
    parallelism due to it's high bandwidth and high number of cores. After Volta
    architecture the NVIDIA GPUs started adding tensor cores in their gpus, 
    allowing for efficient tensor and matrix operations at larger scale, rather
    than running tensor operations on Ray Tracing cores. For GPUs there are 
    several frameworks developed by vendors to increase the performance and 
    enegry efficiency of the GPU like cuDNN, ROCm, TensorRT, etc.

- Dedicated Hardware: this includes specialised hardware developed to improve
    performance and efficiency of these DL computations. With rise of Deep
    Learning in more and more applications, there is increase in many startups
    having their own custom hardwares dedicated to run DL models. Some of the
    Big hardware companies are also developing these specialised hardware such
    as Intel NNP, Qualcomm Cloud AI 100. There are also cloud providers how are 
    making these chips such as Google TPUs, Amazon Inferenatia, Alibaba Hanguang.
    The most well known hardware is TPUs, which consists of Matrix Multiplier 
    Unit (MXU), Unified Buffer (UB), and Activation Unit (AU), which is driven 
    with CISC instructions by the host processor.
