# Awesome-Deepfake-Detection-I-Read
Just I read, share rose to get fun.
# 目录
- [LAA-Net: Localized Artifact Attention Network for Quality-Agnostic and Generalizable Deepfake Detection, CVPR 2024](#LAA)
- [Unlocking the Hidden Potential of CLIP in Generalizable Deepfake Detection, arXiv 2025](#2025CLIP)
- [Rethinking Vision-Language Model in Face Forensics: Multi-Modal Interpretable Forged Face Detector, arXiv, 2025](#2025VLM)
<span id="LAA"></span>
## LAA-Net: Localized Artifact Attention Network for Quality-Agnostic and Generalizable Deepfake Detection, CVPR 2024: [Paper](https://arxiv.org/pdf/2401.13856) [Code](https://github.com/10Ring/LAA-Net)
---

### **一、深度伪造检测的挑战**
深度伪造技术（如换脸视频）越来越逼真，传统检测方法面临两大难题：
1. **高质量伪造的隐蔽性**：最新技术生成的伪造视频在**整体画面**（如肤色、光照）上几乎无破绽，但可能在**局部区域**（如发际线边缘、瞳孔反光）留下细微的“数字指纹”（称为**伪影**）。
2. **泛化能力不足**：现有模型在训练时见过的伪造类型上表现良好，但遇到**新型伪造技术**（如未知的生成算法）时，检测准确率大幅下降。

---

### **二、LAA-Net的核心创新**
论文提出LAA-Net（局部伪影注意力网络），通过两种关键技术解决上述问题：

#### **1. 显式注意力机制：让模型学会“聚焦”关键区域**
传统方法依赖模型自动学习关注区域（隐式注意力），但这种方式可能忽略关键细节。LAA-Net通过**多任务学习框架**，强制模型关注伪影易发区域：
- **任务1：真假分类**  
  基本任务，判断输入图像是真实还是伪造。
- **任务2：热图回归（Heatmap Regression）**  
  生成一张“热图”，高亮显示图像中可能包含伪影的区域（例如人脸边缘）。  
  **如何实现？**  
  - 通过模拟伪造过程生成训练数据：将两张真实图像混合，人为制造伪影区域（如图1）。  
  - 热图的生成基于混合区域的边界，利用高斯分布模拟伪影的扩散范围（如图2）。
- **任务3：自一致性回归（Self-Consistency Regression）**  
  检查图像不同区域的一致性。例如，若左脸光照与右脸不协调，可能暴露伪造痕迹。  
  **如何实现？**  
  - 随机选择一个伪影点，计算该点与周围区域的相似性，生成一致性矩阵作为监督信号。

**意义**：通过多任务学习，模型不仅学习判断真假，还“被迫”理解伪造的局部特征，提升对未知伪造技术的适应能力。

---

#### **2. 增强特征金字塔网络（E-FPN）：保留细节，减少冗余**
传统神经网络（如ResNet、EfficientNet）通过层层卷积提取特征，但低层细节（如纹理、边缘）会逐渐丢失。LAA-Net提出**E-FPN**，改进特征金字塔结构：
- **传统FPN的问题**：直接融合多层特征会导致冗余（重复信息），影响检测效率。
- **E-FPN的改进**：  
  - **特征过滤**：通过Sigmoid函数生成权重，抑制冗余特征，保留关键细节。  
  - **多尺度融合**：将不同层级的特征（高分辨率细节 + 高层语义信息）动态结合，确保伪影相关的低层信息传递到最终分类层。

**类比**：想象用不同倍数的显微镜观察同一张图片，E-FPN的作用是将所有倍数的关键信息综合，避免遗漏任何细节。

---

### **三、实验与结果**
#### **1. 数据集与指标**
- **训练数据**：FF++（包含4种常见伪造类型）。  
- **测试数据**：Celeb-DFv2（高质量伪造）、DFDC（复杂场景）等，涵盖不同质量的伪造内容。
- **评价指标**：  
  - **AUC**：衡量模型区分真假的能力（值越接近1越好）。  
  - **AP**：综合精度指标，考虑不同阈值下的表现。

#### **2. 关键结果**
- **跨数据集测试**（检测未知伪造类型）：  
  LAA-Net在Celeb-DFv2（高质量）上AUC达95.4%，远超传统方法（如Xception的61.18%）。  
- **抗干扰测试**：  
  对图像加入噪声、模糊等干扰后，LAA-Net在多数情况下保持稳定（如模糊干扰下AUC 98.22%），但对高斯噪声敏感（AUC降至53.9%）。

#### **3. 可视化验证**
- **热图与注意力区域**：  
  通过Grad-CAM可视化，LAA-Net能精准定位伪造区域（如嘴唇边缘），而传统方法可能误判背景或无关区域（如图5）。
- **E-FPN vs 传统FPN**：  
  E-FPN的特征融合更聚焦伪影区域，减少冗余激活（如图8）。

---

### **四、总结与展望**
#### **1. 为什么LAA-Net有效？**
- **显式注意力**：通过多任务强制模型学习局部伪影模式，而非依赖隐式特征。
- **特征保留**：E-FPN确保低层细节（如纹理异常）不被丢失，提升检测敏感度。

#### **2. 未来方向**
- **抗噪声优化**：例如结合去噪算法，提升对高斯噪声的鲁棒性。
- **时序信息利用**：当前仅处理单帧图像，未来可结合视频前后帧的动态特征。
---

**类比总结**：  
LAA-Net像一个经验丰富的鉴宝师，不仅观察文物的整体（全局特征），还会用放大镜仔细检查接缝、刻痕等细节（局部伪影），甚至比对不同部位的材质一致性（自一致性）。这种“细节+全局”的双重验证，让它能识破更高明的赝品。
在 Markdown 中，可以通过以下方式插入超链接和生成目录：
<span id="2025CLIP"></span>
## Unlocking the Hidden Potential of CLIP in Generalizable Deepfake Detection, arXiv 2025: [Paper](https://arxiv.org/pdf/2503.19683), [Code](https://github.com/yermandy/deepfake-detection)
### 1. 研究背景与目标  
本文针对**部分篡改的面部深度伪造检测**（如表情替换、身份替换等）展开研究。这类篡改仅修改局部特征（如嘴唇动作），保留大部分原始内容，导致检测难度高于完全合成图像。作者旨在利用**CLIP模型**（特别是其ViT-L/14视觉编码器）的通用表征能力，通过极简调整构建一个**高泛化性检测方法**，使其在未知数据集和伪造技术上均表现优异。

---
### 2.1 基线模型  
- **CLIP-ViT-L/14**：使用CLIP的视觉编码器，输入图像被划分为16×16的视觉块，提取1024维特征，仅保留分类令牌（CLS token）用于分类。  
- **线性分类器**：在冻结CLIP权重的前提下，仅训练一个线性分类层（参数为1024×2），通过交叉熵损失进行二分类（真实/伪造）。

### 2.2 数据集预处理  
采用**DeepfakeBench**的标准化流程：  
1. 从视频中均匀采样32帧。  
2. 使用DLIB检测最大人脸并对齐关键点。  
3. 扩展边界框1.3倍后裁剪为256×256图像，最终由CLIP预处理为224×224输入。  
此流程确保模型专注于面部区域，提升对局部篡改的敏感性。

### 2.3 参数高效微调（PEFT）  
为保留CLIP的预训练知识并减少过拟合，采用以下策略：  
- **LN-Tuning**：仅微调视觉编码器中的**层归一化（LayerNorm）参数**（共104K参数，占总参数0.03%）。  
- **Bias Tuning**：调整MLP层的偏置参数（本文未采用）。  
- **LoRA**：低秩矩阵适配（实验中发现易过拟合，未采用）。  

### 2.4 特征正则化到超球面  
- **L2归一化**：将分类前的特征向量归一化为单位长度，投影到超球面。  
  - 优势：提升特征在超球面上的紧密度，增强泛化能力（类似人脸识别任务）。  
- **度量学习**：在超球面上优化**均匀性（Uniformity）**和**对齐性（Alignment）**损失：  
  - **均匀性**：鼓励特征均匀分布，避免类内过聚集。  
  - **对齐性**：缩小同类特征距离，增大异类距离。  

### 2.5 潜在增强  
- **球面线性插值（Slerp）**：在同一类别的特征间进行插值，生成虚拟样本。  
  - 公式：  
    $$\text{slerp}(\mathbf{x}_i, \mathbf{x}_j; t) = \frac{\sin((1-t)\theta)}{\sin\theta}\mathbf{x}_i + \frac{\sin(t\theta)}{\sin\theta}\mathbf{x}_j$$
  - 作用：模拟未知生成器的指纹，增强对未见伪造技术的鲁棒性。

---

### 3.1 数据集  
- **训练集**：FaceForensics++（FF++），包含4种伪造方法（DeepFake、Face2Face、FaceSwap、NeuralTextures）。  
- **测试集**：Celeb-DF-v2（CDFv2）、DFDC、DFD、FFIW、DeepSpeak v1.0（DSv1）等，涵盖多种未知伪造技术。  

### 3.2 评估指标  
- **视频级AUROC**：对每段视频的32帧预测取平均，计算ROC曲线下面积，反映整体检测性能。  

### 3.3 实验结果  
- **跨数据集泛化**（表3）：  
  - 在CDFv2、DFD、DFDC、FFIW、DSv1上分别达到96.62、98.0、87.15、91.52、92.01的AUROC，优于或接近复杂SOTA方法（如LipForensics、RealForensics）。  
- **消融实验**（表4）：  
  1. **线性探测（仅分类层）**：性能最低（CDFv2 AUROC=78.13）。  
  2. **LN-Tuning**：显著提升性能（CDFv2 AUROC=94.88）。  
  3. **加入L2归一化**：进一步提升泛化能力（CDFv2 AUROC=96.21）。  
  4. **加入度量学习（UnAI）**：优化特征分布（DSv1 AUROC=91.16）。  
  5. **加入Slerp增强**：最终模型在DSv1上达92.01 AUROC，验证潜在增强的有效性。  

### 3.4 关键发现  
- **LN-Tuning**是性能提升的核心，仅调整0.03%参数即可避免过拟合。  
- **L2归一化**是提升泛化能力的最有效正则化手段。  
- **Slerp增强**通过插值生成虚拟样本，增强对未知生成器的适应性。  

---

### 贡献与展望  
- **贡献**：  
  1. 验证了CLIP视觉编码器在深度伪造检测中的潜力，提出极简调整的高效方法。  
  2. 构建了基于超球面正则化的通用检测框架，为未来研究提供强基线。  
- **未来方向**：  
  1. 结合时空信息（如视频时序特征）提升检测性能。  
  2. 探索多模态（如音频-视觉一致性）增强鲁棒性。  
  3. 优化DFDC等数据集的检测性能（当前AUROC=87.15）。  

本文通过高效利用CLIP的预训练能力，为深度伪造检测领域提供了一种简单而强大的解决方案，代码开源以促进后续研究。

实验结果表明，每个组件都对模型的正则化和泛化能力有积极影响。

### **3. 结论**
文章展示了 CLIP 模型在深度伪造检测中的潜力，并提出了一种简单而强大的基线方法。通过参数高效的微调和正则化技术，该方法在多个基准测试中表现出色。尽管如此，文章也指出了一些挑战，例如在 DFDC 数据集上的性能可以进一步提高，并建议未来的工作可以探索多模态方法和时空信息。
<span id="2025VLM"></span>
## Rethinking Vision-Language Model in Face Forensics: Multi-Modal Interpretable Forged Face Detector, arXiv, 2025: [Paper](https://arxiv.org/pdf/2503.20188), [Code](https://github.com/CHELSEA234/M2F2_Det)
### 文章详细解释

#### 1. 研究背景与目标  
本文针对**深度伪造检测**（Deepfake Detection）的**泛化性**和**可解释性**两大挑战展开研究。传统方法通常仅输出二分类结果（真实/伪造）或独立的文本解释，而本文提出的**M2F2-Det**（Multi-Modal Interpretable Forged Face Detector）首次实现了**检测结果与文本解释的同步生成**。通过结合CLIP的多模态学习能力和大型语言模型（LLM）的解释生成能力，M2F2-Det不仅提升了检测精度，还增强了用户对检测决策的信任。

---

### 提出的方法

#### 2.1 核心框架  
M2F2-Det由三个核心组件构成：  
1. **CLIP视觉编码器**（ $\mathcal{E}_I$ ）：提取输入图像的通用视觉特征。  
2. **深度伪造编码器**（ $\mathcal{E}_D$ ）：提供针对伪造特征的领域知识。  
3. **大型语言模型**（LLM）：基于视觉特征生成文本解释。  

通过**Bridge Adapter**（桥接适配器）和**Forgery Prompt Learning**（伪造提示学习）实现三者的高效协作。

#### 2.2 Forgery Prompt Learning（FPL）  
FPL通过优化两类可学习提示，将CLIP适配到深度伪造检测任务：  
1. **Universal Forgery Prompts（UF-prompts）**：包含通用伪造标记（捕捉跨数据集的共性伪造模式，如边缘模糊）和特定伪造标记（编码图像依赖的细粒度伪造特征，如眼部异常）。  
   - 公式：  
     $\mathbf{S} = [\mathbf{v}^G_1] \ldots [\mathbf{v}^G_m][\mathbf{v}^S_1] \ldots [\mathbf{v}^S_u][\text{forged}][\text{face}]$
     
     $\mathbf{v}^G为通用标记，\mathbf{v}^S为特定标记，通过MLP从图像特征动态生成。$
2. **Layer-wise Forgery Tokens（LF-tokens）**：在CLIP文本编码器的每一层引入可学习的伪造标记，增强对伪造语义的适配能力（图3b）。  

FPL生成**伪造注意力图**（ $\mathbf{M}_b$ ），通过计算图像块与文本提示的余弦相似度定位伪造区域，为检测提供空间先验知识。

#### 2.3 Bridge Adapter（桥接适配器）  
Bridge Adapter（ $\mathcal{E}_A$ ）是连接CLIP视觉编码器与深度伪造编码器的关键模块：  
1. **特征融合**：将CLIP的中间特征（ $\mathcal{E}_I$ ）与深度伪造编码器的输出（ $\mathcal{E}_D$ ）融合，生成鲁棒的伪造表示 $\mathbf{F}^0$ 。  
2. **频率令牌生成**：将 $\mathbf{F}^0$ 转换为频率令牌 $\mathbf{H}_F$ ，与CLIP的视觉令牌 $\mathbf{H}_V$ 共同输入LLM，指导解释生成。  

#### 2.4 解释生成模块  
LLM（如Vicuna-7b）基于视觉令牌（ $\mathbf{H}_V$ ）和频率令牌（ $\mathbf{H}_F$ ）生成检测结果及解释：  
- **频率令牌**：编码伪造特征的频域信息（如高频噪声），帮助LLM捕捉真实与伪造图像的差异。  
- 生成概率公式扩展为：

  $$
  p(\mathbf{X}_A | \mathbf{H}_V, \mathbf{H}_F,\mathbf{H}_T) = \prod_{z=1}^Z p_\theta(\mathbf{x}_z | \mathbf{H}_V, \mathbf{H}_F, \mathbf{H}_{T,<z}, \mathbf{x}_{A,<z})
  $$
---

### 实验设计与结果

#### 3.1 数据集与评估指标  
- **检测任务**：FaceForensics++（FF++）、Celeb-DF（CDF）、DFDC等6个数据集，指标为AUC和准确率。  
- **解释任务**：DD-VQA数据集，指标包括关键词准确率（F1）、BLEU-4、CIDEr、ROUGE-L等。  

#### 3.2 检测性能  
1. **同数据集测试**（表2）：  
   - FF++（c40）：M2F2-Det的AUC达**96.58%**，超越TALL（94.57%）和RECCE（95.02%）。  
   - Celeb-DF：AUC达**99.92%**，优于多数时序模型。  
2. **跨数据集测试**（表3）：  
   - DFDC和FFIW上的AUC分别为**87.80%**和**88.70%**，优于FreqBlender（87.56%和86.14%）。  

#### 3.3 解释生成性能  
1. **关键词准确率**（图5a）：M2F2-Det的准确率（86.05%）显著高于DDVQA-BLIP（68.31%）和LLaVA（81.54%）。  
2. **语义一致性**（图5b）：CIDEr得分达**1.92**，优于基线模型（如LLaVA的1.48），表明生成解释更贴近人工标注。  
3. **定性结果**（图6）：M2F2-Det能精准定位伪造区域（如模糊发际线、眼部不自然纹理），并生成详细解释，而DDVQA-BLIP和GPT-4o常出现误判或模糊描述。  

#### 3.4 消融实验与关键分析  
1. **伪造注意力图**（表4）：  
   - 仅使用UF-prompts或LF-tokens分别提升AUC至92.57%和92.66%，联合使用后达93.65%。  
   - 加入Bridge Adapter后，AUC进一步提升至**96.58%**（FF++ c40）。  
2. **频率令牌的作用**：移除\(\mathbf{H}_F\)导致关键词准确率下降10.12%，验证其重要性。  
3. **与CLIP基线的对比**（表5）：  
   - M2F2-Det的AUC（96.58%）显著高于Uni-Fake（72.40%）和DEFAKE（76.84%）。  

---

### 贡献与展望  
- **贡献**：  
  1. 首个多模态可解释深度伪造检测框架，同步输出检测结果与文本解释。  
  2. 提出**Forgery Prompt Learning**，高效适配CLIP至伪造检测任务。  
  3. 通过**Bridge Adapter**融合CLIP与领域知识，提升泛化性。  
- **未来方向**：  
  1. 探索更多模态（如音频）增强检测鲁棒性。  
  2. 优化低频伪造（如DFDC）的检测性能。  
  3. 扩展至视频时序分析与实时检测场景。  

本文通过多模态协同与可解释性设计，为深度伪造检测提供了新的研究范式，代码已开源以推动后续研究。
