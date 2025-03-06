https://blog.csdn.net/qq_46009046/article/details/136642468
https://paperswithcode.com/task/visual-grounding/codeless

> Visual Grounding (VG) aims to locate the most relevant object or region in an image, based on a natural language query.

## 个人任务

#### 2024/09/21

- 电路图相关的视觉定位任务（识别故障，找元器件、导线）
	- 开关闭合之后的现象？
- 类似场景（科学教育）
	- 化学题目，流程图标注（化学分子式）
	- 流程图->找问题
- 表达物体之间关系的工作（强关联，逻辑关系无法直接用空间位置表达）
	- 传统编码题目
- 数据集调研

#### 2024/09/28

- VG方法调研
	- 路线
	- 优缺点
	- 损失
	- 模块
	- 方法
	
- 数据标注
	- 设计提问文本
	- 借助大模型标注
	
- *其他模态（文本之外）+ 图像 -> 定位*
	- *电路图作为输入判断实物的连接的问题*

#### 2024/10/13

- 数据集扩展方法
- 实验输入输出
	- 标记试题（适合VG任务）
	- 归纳试题
	- 输入文本（是否包含坐标信息）
	- 输出（导线输出、多个对象输出、关键点序列（统一））
- 调研VL开源模型（文本+视觉大模型）
	- 千问大模型
	- 多模态大模型
	- 类似SAM的VG模型（输出为Bounding Box）
	- 跑代码
#### 2024/10/15  
- 调研要朝向目的（以问题为中心（电路上的视觉定位问题），和主题呼应）
- 哪些技术可以参考，为我所用
- 图像推理能力（电路连接情况）
- 对象本身分析复杂构件（对象复杂构件的结构分析）
- 场景对象复杂组合关系的结构分析
#### 2024/11/05

- CLIPSelf 图像上的对比学习在区域上的理解是不佳的（整张图片的语义与图片中某个区域中的语义是有区别的）
- CLIPSelf的局部块直接训练，原本局部的块作为输入已成为全局的一部分了
- CLIPSelf块与块之间关系的一致性
- 图像空间中的对象核周围的Patch在转换到特征空间中的特征应该也是一个簇。

#### 2024/11/12

- 使用GPT生成更多题目看看，是否会出现文心一言生成的题目的问题（生成的题目GPT-4o回答准确率过高）
## 概述

- **定义**：自然语言描述+图像 --> 位置
- **关键词：visual instruction tuning、Visual grounding、LLaVA、Instruction-following large multimodal models**
- **常用评价指标**：
	- **mAP（mean Average Precision）**：平均精度的平均值，用于衡量模型在不同类别上的检测精度。
	- **RefCOCO评价指标**：RefCOCO数据集通常`使用Top-K Accuracy、Recall@K等指标`来评价模型在指代解析任务上的性能。
- **难点**: 
	1.  What is the main focus in a query?
	2.  How to understand an image?
	3.  How to locate an object?
- **任务类别**：
	1. Phrase Localization：又称为Phrase Grounding，如上图，对于给定的sentence，要定位其中提到的全部物体（phrase），在数据集中对于所有的phrase都有box标注。
		![[Pasted image 20240916174628.png]]
	 2. Referring Expression Comprehension（REC）：也称为Referring expression grounding。见上图，每个语言描述（这里是expression）只指示一个物体，每句话即使有上下文物体，也只对应一个指示物体的box标注。
	  ![[Pasted image 20240916174850.png]]

## 基础设施

### 公开数据集

- Flickr30k：Flickr30k是一个广泛用于视觉定位和图像标注任务的数据集。它由Flickr图像共享平台上的30,000张图像组成，每张图像都有5个人工标注的描述。这些描述涵盖了图像中的主要对象、场景和动作等信息。

- RefCOCO：RefCOCO数据集是COCO数据集的一个子集，用于指代解析任务。它提供了自然语言描述和指向图像中对象的标注。

- RefCOCO+：这是RefCOCO数据集的扩展版本，包含了更多复杂的自然语言描述和更精细的目标标注。

- RefCOCOg：RefCOCOg数据集是对于游戏情境中的指代解析任务而设计的，其中包含了复杂的自然语言描述和图像中对象的标注。
	

#### RefCOCO

![[Pasted image 20240918151119.png]]

RefCOCO 数据集是一个用于生成指代表达（REG）的数据集，主要用于理解指代图像中特定对象的自然语言表达。以下是关于 RefCOCO 的主要信息：

**收集方法**：该数据集通过 ReferitGame 这一双人游戏进行收集。在游戏中，第一名玩家查看带有目标对象分割的图像，并写下指代该对象的自然语言表达。第二名玩家只能看到图像和指代表达，必须点击对应的对象。如果两名玩家都正确完成任务，他们会得分并交换角色；否则，他们会收到新的对象和图像来进行描述。

**数据集变体**：

- **RefCOCO**：包含 142,209 条指代表达，涉及 19,994 张图像中的 50,000 个对象。
- **RefCOCO+**：包含 141,564 条指代表达，涉及 19,992 张图像中的 49,856 个对象。
- **RefCOCOg**：该变体包含 25,799 张图像，95,010 条指代表达，涉及 49,822 个对象实例。

**语言和限制**：RefCOCO 数据集允许指代表达中使用任何类型的语言。RefCOCO+ 禁止在表达中使用方位词，以便专注于基于外观的描述（例如“穿着黄底波点衬衫的男人”），而不是依赖于观察者的描述（例如“从左数第二个男人”）。

这些数据集是计算机视觉研究中用于指代表达分割、理解和视觉定位等任务的重要资源。

#### DIOR-RSVG

DIOR-RSVG 是一个大规模遥感数据（RSVG）基准数据集，旨在通过自然语言引导定位遥感（RS）图像中的指代对象。该新数据集包含用于训练和评估视觉定位模型的图像/表达/框三元组。
![[Pasted image 20240927214100.png]]


#### MRR-Benchmark
![[Pasted image 20240927214505.png]]

#### SK-VG

SK-VG 是一个用于场景知识引导视觉定位的数据集，在该数据集中，图像内容和指代表达不足以定位目标对象，迫使模型具备对长期场景知识进行推理的能力。为了执行此任务，SK-VG 是第四类数据集的首个实例，对于每张图像，我们提供人类撰写的知识来描述其内容。
![[Pasted image 20240927215145.png]]



### 算法

- **全监督（顾名思义，就是有object-phrase的box标注信息。）**
	- 两阶段算法
	- 单阶段算法
- **弱监督（输入只有image和对应的sentence，没有sentence中的object-phrase的box标注。）**
- **无监督（image-sentence的信息都没有。）**
![[Pasted image 20240916215614.png]]

#### 全监督：两阶段算法

> **两阶段方法在第一阶段生成区域提议和区域特征提取，然后在第二阶段利用语言表达来选择最匹配的区域。**

![[Pasted image 20240914154015.png]]
#### 全监督：单阶段算法

> **一阶段方法对语言上下文与视觉特征密集融合，并进一步利用融合的特征图以生成密集的候选框（滑动窗口等方式）执行边界框预测。**

![[Pasted image 20240914154542.png]]
![[Pasted image 20240914154727.png]]

### 文献

#### GroundingGPT: Language Enhanced Multi-modal Grounding Model（2024 ArXiv）

MLLM往往优先捕捉全局信息，而忽视了感知局部信息的重要性。这一局限性妨碍了它们有效理解细粒度细节的能力，并处理需要细致理解的定位任务。尽管一些近期的研究在这方面取得了进展，但它们主要集中在单一模态输入上。本文提出了 GroundingGPT，这是一个端到端的语言增强多模态定位模型。它旨在执行图像、视频和音频三种模态的细粒度定位任务。

训练经过三个阶段：
1. 多模态预训练
2. 细粒度对齐微调（第二阶段旨在使模型能够理解更详细的信息，包括坐标和时间戳）
3. 多粒度指令微调（本阶段旨在使模型能够生成更符合人类偏好的响应，并改善多模态交互。）

![[Pasted image 20241012175153.png]]

#### ENHANCING VISUAL GROUNDING AND GENERALIZATION: A MULTI-TASK CYCLE TRAINING APPROACH FOR VISION-LANGUAGE MODELS (2023 ArXiv)

当前的视觉语言模型专注于理解图像，忽略了与多任务指令的人机交互，从而限制了其多样性和响应的深度。本研究提出了ViLaM，一个支持多任务视觉定位的大型多模态模型，采用循环训练策略，并配备了丰富的交互指令。
引入了指代表达生成（REG）与指代表达理解（REC）之间的循环训练。
损失中y表示输出的tokens，定位时坐标以文本形式给出。

![[Pasted image 20241012171438.png]]
![[Pasted image 20241012173237.png]]
![[Pasted image 20241012173728.png]]
$$
	L_{cyc}(G,F) = L_{ce}(F(G(x)), x)+L_{ce}(G(F(y)), y)
$$
#### Pink: Unveiling the Power of Referential Comprehension for Multi-modal LLMs （2024 ICLR）

MLLM在精细图像理解任务中的表现仍然有限。为了解决这一问题，本文提出了一个新框架，以增强多模态大语言模型（MLLMs）的精细图像理解能力。具体而言，我们提出了一种新方法，通过利用现有数据集中的标注，以低成本构建指令微调数据集。此外，还引入了一种自一致的自举方法，将现有的密集对象标注扩展为高质量的指代表达-边界框对。
![[Pasted image 20241012165852.png]]

![[Pasted image 20241012111204.png]]

#### MiniGPT-v2: Large Language Model As a Unified Interface for Vision-Language Multi-task Learning （2023arxiv）

![[Pasted image 20241012105928.png]]

#### Groma: Localized Visual Tokenization for Grounding Multimodal Large Language Models (2024ECCV)

目前的MLLMs通常在定位能力上存在不足，因此无法将理解与视觉上下文相结合，鉴于这一差距，一项研究方向试图增强大语言模型，使其能够直接输出量化的物体坐标以实现定位。尽管这种方法在设计上简单，但大语言模型的巨大计算需求使得处理高分辨率图像输入变得具有挑战性，而高分辨率图像对于准确定位至关重要。
这些担忧引发了另一项研究方向，该方向结合了一个外部定位模块，这种方法规避了上述问题，但在推理时引入了额外的延迟，因为它需要分别使用大语言模型和定位模块对图像输入进行两次处理。
我们将定位任务分解为两个子问题：发现物体（定位）和将物体与文本关联（识别）。

![[Pasted image 20241012102333.png]]
![[Pasted image 20241012102459.png]]
![[Pasted image 20241012103538.png]]

#### Shikra: Unleashing Multimodal LLM’s Referential Dialogue Magic（2023arxiv）

在人类对话中，个体可以在与他人交谈时指示场景中的相关区域。反过来，另一个人可以在必要时通过提及特定区域来回应。这种在对话中自然的指称能力在当前的多模态大型语言模型（MLLMs）中仍然缺失。为了解决这一问题，本文提出了一种名为 Shikra 的多模态大型语言模型（MLLM），它可以处理自然语言中的空间坐标输入和输出,不需要额外的词汇tokens和位置编码，来实现多种下游VL任务。采用自回归直出文本的方式。
本文选择了 CLIP 的预训练 ViT-L/14 作为视觉编码器，Vicuna-7/13B 作为大语言模型（LLM）。使用一个全连接层将 ViT 的 $16×16×1024$ 的输出嵌入 $V∈R^{16×16×1024}$ 映射到$V′∈R^{256×D}$，以实现模态对齐并纠正 LLM 的输入维度。
![[Pasted image 20241012101541.png]]
#### KOSMOS-2: Grounding Multimodal Large Language Models to the World （2024ICLR）


本文推出了MLLM KOSMOS-2，能够实现对物体描述（例如，边界框）的新能力，并将文本与视觉世界相结合。
![[Pasted image 20241012095319.png]]


#### Dynamic MDETR: A Dynamic Multimodal Transformer Decoder for Visual Grounding（2023 TPAMI）

由于现有的编码器的定位框架（例如 TransVG）由于自注意力操作的平方时间复杂度，面临着较大的计算负担。本文提出了一种新的多模态Transformer架构，称为动态多模态 DETR（Dynamic MDETR），将整个定位过程解耦为编码和解码阶段。图像中存在高度的空间冗余。因此，本文通过利用这种稀疏性设计了一种新的动态多模态Transformer解码器，以加速视觉定位过程。

一个关键设计是二维几何空间中的语言引导空间自适应采样。通过这个采样模块，动态多模态变换器解码器可以自适应地在二维空间中采样少量信息丰富的视觉标记，以便后续的多模态解码。另一个关键设计是文本引导解码，它在文本的指导下将采样的视觉特征解码为目标的定位。

本文的动态多模态Transformer解码器仅通过二维自适应采样选择少量采样特征点，并在语言查询的指导下解码这些采样的视觉特征。

![[Pasted image 20241012230331.png]]

2D Adaptive Sampling 通过预测相对于参考点的偏移量，在二维图像空间中采样少量视觉点。初始时，参考点设置为[0.5, 0.5]
![[Pasted image 20241012230734.png]]
![[Pasted image 20241012231437.png]]
![[Pasted image 20241012232354.png]]
![[Pasted image 20241012233746.png]]

#### VISUAL GROUNDING WITH TRANSFORMERS （2022 ICME）

本文提出了一种基于Transformer的视觉定位方法。

![[Pasted image 20241012225039.png]]
![[Pasted image 20241012225146.png]]
#### TRAR: Routing the Attention Spans in Transformer for Visual Question Answering（2021 ICCV）

在视觉问答（VQA）和指代表达理解（REC）等任务中，多模态预测通常需要从宏观到微观的视觉信息。因此，如何在 Transformer 中动态调度全局和局部依赖建模已成为一个新兴问题。本文提出了一种称为 Transformer Routing (TRAR) 的示例依赖路由方案来解决这一问题。具体来说，在 TRAR 中，每个视觉 Transformer 层都配备了一个具有不同注意力范围的路由模块。模型可以根据前一步推理的输出动态选择相应的注意力，从而为每个示例制定最佳的路由路径。经过精心设计，TRAR 可以将额外的计算和内存开销降至几乎可以忽略不计的程度。

![[Pasted image 20241012221313.png]]
![[Pasted image 20241012221516.png]]
$\alpha$和$D$ 控制路径
![[Pasted image 20241012222622.png]]
![[Pasted image 20241012223440.png]]
#### TransVG: End-to-End Visual Grounding with Transformers （2021CVPR）

传统方法融合模块设计中某些机制的介入，如查询分解、图像场景图等，使得模型容易过度拟合特定场景的数据集，限制了视觉-语言语境之间的充分互动。为了避免这个问题，本文利用Transformer建立多模态对应关系，并通过经验证明，复杂的融合模块（例如，模块化注意网络、动态图和多模态树）可以被具有更高性能的变换器编码器层的简单堆栈所取代。此外，本文将视觉定位重新表述为直接坐标回归问题，并避免从一组候选对象中进行预测。
![[Pasted image 20241010184948.png]]
![[Pasted image 20241010185215.png]]
#### Improving Visual Grounding with Visual-Linguistic Verification and Iterative Reasoning （2022 CVPR）

本文针对传统VG方法利用候选框对视觉特征进行建模可能无法充分利用文本查询中的视觉上下文和特征信息的问题，提出视觉语言验证模块细化视觉特征以关注与指称表达相关的区域，语言引导的上下文编码器收集信息丰富的视觉上下文以促进目标对象识别。同时。为了减少推理中的歧义，本文提出了一个多阶段跨模态解码器，该解码器迭代地思考视觉和语言信息，以区分目标对象与其他部分，并检索相关特征以进行对象定位。
![[Pasted image 20241010183258.png]]
![[Pasted image 20241010183124.png]]

![[Pasted image 20241010182600.png]]

#### SeqTR: A Simple Yet Universal Network for Visual Grounding（2022 ECCV）

视觉定位的典型任务通常需要在设计网络架构和损失函数方面拥有丰富的专业知识，因此很难在各个任务之间推广。本文将视觉定位视为以图像和文本输入为条件的点预测问题，其中边界框或二进制掩码表示为离散坐标标记序列，提出SeqTR，此外，SeqTR 还对所有任务共享相同的优化目标，使用简单的交叉熵损失，进一步降低了部署手工制作的损失函数的复杂性。
![[Pasted image 20241010191114.png]]

![[Pasted image 20241010190407.png]]
![[Pasted image 20241010190621.png]]

#### MMScan: A Multi-Modal 3D Scene Dataset with Hierarchical Grounded Language Annotations （视觉推理定位数据集（3D））

![[Pasted image 20240916213122.png]]
![[Pasted image 20240916213344.png]]
![[Pasted image 20240916213618.png]]
#### ScanReason: Empowering 3D Visual Grounding with Reasoning Capabilities （视觉推理定位方法）

> **Abstract.**  Although great progress has been made in 3D visual grounding, **current models still rely on explicit textual descriptions for grounding and lack the ability to reason human intentions from implicit instructions.** We propose a new task called 3D reasoning grounding and introduce a new benchmark ScanReason which provides over 10K questionanswer-location pairs from five reasoning types that require the synerization of reasoning and grounding. We further design our approach, ReGround3D, composed of the visual-centric reasoning module empowered by Multi-modal Large Language Model (MLLM) and the 3D grounding module to obtain accurate object locations by looking back to the enhanced geometry and fine-grained details from the 3D scenes. A chainof-grounding mechanism is proposed to further boost the performance with interleaved reasoning and grounding steps during inference. Extensive experiments on the proposed benchmark validate the effectiveness of our proposed approach.

![[Pasted image 20240917165622.png]]![[Pasted image 20240917170915.png]]
![[Pasted image 20240917171327.png]]

#### Phrase Localization Without Paired Training Examples
![[Pasted image 20240918154115.png]]


# 具体任务（专业上下文下推理的视觉定位）


- 电学实验
- 化学合成推理
- 地图阅读与地理分析
- 生物学实验与解剖
- 编程与计算机科学
- 体育赛事分析（篮球，台球，等）
- 数学公式分析



## 电学实验中的定位

1. ~~电路连接情况（电路的拓扑结构）：~~
	1. ~~器件位置和类别【电压表在哪里？】~~
	2. ~~器件间的连接情况【和电流表直接串联且相连的器件在哪里？】~~
2. 电路连接的正确性问题：
	1. 违背实验目的的错误。【如图为限流法测电阻的实验电路，其中不按要求连接的器件和导线有哪些？】
	2. 安全性（惯例）错误。（电源短路，电流表正负极接反）【图中哪些器件或导线连接可能会造成安全隐患？】（在不提供实验目的的情况下也是错误的）
	3. 在某种错误的电路连接情况下可能造成哪些器件故障。
3. 电路现象解释：
	1. 正常情况下可读器件的变化（电流表、电压表、小灯泡）【电流表读数变大了可能是哪些器件的变化造成的？】
	2. 器件损坏下可读器件的变化（电阻断路、电流表烧坏、电源没电了）【灯泡熄灭，电流表读数为0电压表读数不为0，哪里造成了这种现象？】【电源为3V，通过测试发现，A，B两点间电压为3V，BC间电压为0V，电路中哪个器件发生了故障】
4. 电路预测类型：
	1. 可调器件的调整【向左移动滑变，流经哪些器件的电流变大、哪些变小】（滑变、开关）
	2. 改变某些器件的某个属性（电阻阻值，电源电压），电路种发生的变化。【已知电阻A的阻值大于电阻B的阻值，把电阻A和电阻B互换位置哪个电流表的读数更大（假设有多个电流表）】
5. 电路修改：
	1. 移除哪些导线或器件使电路达到某种情况。
	2. 添加某些器件达到某种情况。【添加一个电压表测量电阻的两端的电压，定位该电压表的连接点。】


![[Pasted image 20240927193223.png]]
![[Pasted image 20240927200118.png]]![[Pasted image 20240927200149.png]]![[Pasted image 20240927200206.png]]![[Pasted image 20240927200219.png]]![[Pasted image 20240927200251.png]]![[Pasted image 20240927200304.png]]![[Pasted image 20240927200332.png]]![[Pasted image 20240927200422.png]]![[Pasted image 20240927200459.png]]![[Pasted image 20240927200540.png]]
![[Pasted image 20240927193231.png]]
![[Pasted image 20240927193248.png]]
![[Pasted image 20240927193557.png]]
## 化学

应遵循以最少的修改达到目的准则。

1. 反应错误的地方
	1. 反应条件错误（高温写成低温）【下图是制取某物的流程，其中反应条件不符合现实的有哪些？在图中什么位置？】
	2. 生成物错误（生成物分子式写错，或生成物中有不合化学逻辑的部分等）
	3. 反应物错误（反应物分子式写错，或反应物中有不合化学逻辑的部分等）
2. 结果不符合要求
	1. 生成物不满足要求（题干要求生成某物，生成的却是另外一种）
	2. 反应过程中的反应条件不符合要求（上下文中要求不能使用某些条件却用了某些条件）【不提供加入设备，流程图中错误的部分有哪些？】
3. 纠错类
	1. 修改流程图中某个物质或反应条件使反应正常？
	2. 修改流程图中某个物质或反应条件以加快或减缓反应速度？
	
![[Pasted image 20240927195037.png]]
![[Pasted image 20240927212820.png]]
![[Pasted image 20240927212916.png]]
![[Pasted image 20240927212940.png]]
![[Pasted image 20240927212955.png]]
![[Pasted image 20240927213044.png]]
![[Pasted image 20240927213051.png]]



## 地理

![[Pasted image 20240927210348.png]]
![[Pasted image 20240927210451.png]]
![[Pasted image 20240927210558.png]]
![[Pasted image 20240927210954.png]]
![[Pasted image 20240927211113.png]]
![[Pasted image 20240927211150.png]]
![[Pasted image 20240927211217.png]]

## 生物

![[Pasted image 20240927222955.png]]
![[Pasted image 20240927223023.png]]


