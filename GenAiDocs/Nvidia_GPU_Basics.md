[TOC]

## The mapping between AWS instance types and the Nvidia GPUs they use

![Choosing the right GPU for deep learning on AWS | by Shashank ...](https://miro.medium.com/v2/resize:fit:1400/1*1qFKgyph2CT2Dxat3tSqmQ.png)



![Choosing the right GPU for deep learning on AWS | by Shashank ...](https://miro.medium.com/v2/resize:fit:1400/1*03A7iPvLFJiUbpCwug8teA.png)







## Blackwell vs. Hopper



### 问题1：INT8 和 FP4、FP8 的关系以及算力消耗

- **INT8** 是一种 8 位整数格式，主要用于深度学习中的量化推理，适合需要减少计算资源的任务。
- **FP4 和 FP8** 是浮点数格式，FP8 提供了比 INT8 更高的精度，而 FP4 则提供了最低的精度。通常来说，**FP8 消耗的算力大于 INT8**，而 FP4 的计算资源消耗通常低于 FP8。

### 问题2：Tensor Core 和 CUDA Core 的区别

- **CUDA Cores** 是英伟达 GPU 中的基本计算单元，主要用于处理常规的浮点运算和并行计算任务，支持广泛的通用计算工作负载。
- **Tensor Cores** 是专门为深度学习任务设计的单元，能够高效地处理矩阵运算，极大地提高了 AI 模型训练和推理的性能。Tensor Cores 尤其擅长处理像 FP16、BF16、TF32 等格式的计算。

### 问题3：Die 大小的影响

- **Die 大小** 和性能并没有简单的正相关关系。更大的 die 通常意味着更多的晶体管和更高的性能，但也会带来制造成本和散热管理的挑战。
- **多个 die 的组合**（如 MCM 技术）可以有效地提高计算能力和能效，特别是在大规模计算任务中，这种方法可以更好地管理热量和成本。因此，不一定 die 越大越好，多个 die 的组合在很多情况下更有优势。



## HBM

**HBM（High Bandwidth Memory）** 是一种高性能的显存技术，通常用于高端 GPU，如英伟达的 H100。HBM 和 DDR 系列内存（如 DDR4、DDR5）在功能上相似，都是用于存储和数据传输的标准，但有以下关键区别：

1. **带宽**：HBM 提供比 DDR 更高的带宽，能够支持更快的数据传输速率，非常适合需要大量数据处理的应用，如 AI 训练和图形渲染。
   
2. **堆叠技术**：HBM 通过将多层 DRAM 芯片垂直堆叠，并与 GPU 集成在一个封装内，减小了延迟并提高了效率。

3. **功耗**：HBM 通常比 DDR 系列功耗更低，尽管提供了更高的带宽和性能。

总的来说，HBM 不是单纯为了增加显存容量和速度，而是通过其独特的架构设计，提供了更高的性能和能效，适合高计算密集型任务。



## References

- Nvidia Blackwell Architecture Technical Brief: https://resources.nvidia.com/en-us-blackwell-architecture
- Nvidia H100 Tensor Core GPU Architecture: https://resources.nvidia.com/en-us-tensor-core
- Choosing the right GPU for deep learning on AWS: https://towardsdatascience.com/choosing-the-right-gpu-for-deep-learning-on-aws-d69c157d8c86
- How Nvidia GPUs have envolved from Tesla to Ampere to Hopper: https://techovedas.com/how-nvidia-gpus-have-evolved-from-tesla-to-ampere-to-hopper/