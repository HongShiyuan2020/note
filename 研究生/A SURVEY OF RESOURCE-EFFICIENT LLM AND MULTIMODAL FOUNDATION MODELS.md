
总结：
1. 本文对2024年以前的LLM（大语言模型）、Vision Foundation Models和Multimodal Foundation Models（多模态基础模型）从架构、训练、微调等多个角度分析这类模型在内存、算力等多方面资源消耗情况，为设计更加高效利用资源的基础模型提供指导。本文篇幅较长，目前阅读了前两章节（概括、FOUNDATION MODEL OVERVIEW（基础模型简介））。![[Pasted image 20240807210150.png]]LLM基本使用基于Transformer的网络作为模型基础单元，从架构上可以分为：Encoder-only——如BERT、Encoder-decoder——如T5和Decoder-only——如GPT系列三种模型。Vision Foundation Models以ViT为开山之作，基本沿着类比BERT的思路将注意力引入计算机视觉领域。在架构上大都为Encoder-only——如ViT、BEiT。多模态模型是指能够处理和融合来自不同模态（如文本、图像、音频、视频等）信息的机器学习模型。这些模型旨在利用多种类型的数据来提高任务的表现。目前该领域由两条重要的研究路线：将不同模态的数据编码到相同的隐空间中（编码对齐）、实现跨模态的数据转换。在多模态模型的架构中一般拥有多个编码器，每个编码器对应一种模态，用于将该模态数据编码为对齐的向量，在拥有了对齐后的编码后一般围绕LLM或Diffusion模型为核心生成对应的文本、图像、视频等等不同模态的数据。在架构上可以分为：Encoder-Only（CLIP）、Encoder-Decoder（SAM、Stable-Diffusion）。其中SAM和我们的任务高度相关，后续会查阅相关资料。

总结：
1. 本文后续章节着重讨论资源高效的模型架构：注意力机制、动态神经网络、Diffusion优化方法、ViT的优化方法，资源高效算法：预训练算法、推理算法、模型压缩算法以及资源高效系统：分布式训练、联邦学习、云服务和边界服务等等，讨论改进方法降低内存占用和算力消耗。