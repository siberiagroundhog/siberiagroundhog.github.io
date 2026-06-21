# 深度学习阶段性知识笔记：PyTorch、ANN、CNN 与 RNN

> 适用阶段：完成 Python 基础，并刚开始使用 PyTorch 编写神经网络。  
> 使用方法：第一次按顺序阅读；复习时优先查看每章末尾的“30 秒复习”和最后的速查附录。

---

## 目录

- [1. 当前阶段的知识地图](#1-当前阶段的知识地图)
- [2. 深度学习的最小知识框架](#2-深度学习的最小知识框架)
- [3. PyTorch 简单入门](#3-pytorch-简单入门)
- [4. Tensor：张量专章](#4-tensor张量专章)
- [5. ANN：人工神经网络](#5-ann人工神经网络)
- [6. CNN：结合 CIFAR-10 图像分类代码](#6-cnn结合-cifar-10-图像分类代码)
- [7. RNN：结合歌词生成代码](#7-rnn结合歌词生成代码)
- [8. ANN、CNN、RNN 横向对比](#8-anncnnrnn-横向对比)
- [9. PyTorch 通用训练模板](#9-pytorch-通用训练模板)
- [10. 常见错误与排查清单](#10-常见错误与排查清单)
- [11. 后续 NLP、GNN 与大模型学习路线](#11-后续-nlpgnn-与大模型学习路线)
- [12. 快速复习附录](#12-快速复习附录)

---

## 1. 当前阶段的知识地图

### 1.1 这些概念是什么关系？

人工神经网络（ANN）是一个大类。常见的多层全连接网络、CNN、RNN 都属于人工神经网络，只是它们针对的数据结构不同。

```text
深度学习
└─ 人工神经网络 ANN
   ├─ 全连接网络 / MLP：普通表格、固定长度向量
   ├─ CNN：图像、空间结构
   └─ RNN：文本、时间序列等顺序数据
```

- **ANN/MLP** 学习“特征之间的组合关系”。
- **CNN** 用卷积提取局部空间特征，例如边缘、纹理和物体部件。
- **RNN** 按顺序读取数据，用隐藏状态记录前文信息。
- **Transformer** 通过注意力机制建模序列，是当前 NLP 和大模型的核心结构。
- **GNN** 在图结构上聚合邻居信息，适合节点和边关系明显的数据。

### 1.2 目前的两个深度学习练习

| 项目 | 任务 | 输入 | 输出 | 核心模型 |
|---|---|---|---|---|
| `CNN/main.py` | CIFAR-10 图像分类 | `32×32` 彩色图片 | 10 个类别的分数 | 卷积层 + 全连接层 |
| `RNN/main.py` | 歌词文本生成 | 若干连续词的索引 | 下一个词的概率分数 | Embedding + RNN + Linear |

这两个项目已经包含了深度学习最核心的完整闭环：

```text
准备数据 → 构造批次 → 定义模型 → 前向传播 → 计算损失
        → 反向传播 → 更新参数 → 保存模型 → 加载模型 → 推理
```

### 30 秒复习

- ANN 是总称，CNN 和 RNN 都是 ANN 的具体类型。
- CNN 擅长空间结构，RNN 擅长顺序结构。
- PyTorch 负责张量计算、自动求导、模型搭建和训练。

---

## 2. 深度学习的最小知识框架

### 2.1 从一个分类任务开始理解

以 CIFAR-10 为例：给模型一张图片，希望模型判断它是飞机、汽车、鸟、猫等 10 类中的哪一类。

- **样本（sample）**：一张图片。
- **特征（feature）**：图片中的像素及模型从像素中提取出的信息。
- **标签（label）**：图片的正确类别，例如 `3` 表示猫。
- **参数（parameter）**：模型训练时自动学习的权重和偏置。
- **超参数（hyperparameter）**：由人设置的配置，如学习率、batch size、epoch 数。

模型训练可以理解为反复做四件事：

1. 根据当前参数做预测。
2. 用损失函数衡量预测与正确答案的差距。
3. 计算每个参数应该向哪个方向调整。
4. 由优化器更新参数，使下次预测更好。

### 2.2 前向传播、损失与反向传播

```text
输入 x ──前向传播──> 预测值 y_pred
                         │
正确答案 y ───────────> 损失 loss
                         │
                    反向传播
                         │
                    参数梯度
                         │
                    优化器更新
```

- **前向传播（forward）**：输入通过网络得到预测结果。
- **损失函数（loss function）**：用一个数表示预测有多差，通常越小越好。
- **反向传播（backpropagation）**：根据链式法则计算各参数的梯度。
- **梯度（gradient）**：损失对参数的变化率，提示参数的调整方向。
- **优化器（optimizer）**：根据梯度更新参数，例如 Adam、SGD。

### 2.3 batch、iteration、epoch

假设训练集有 50,000 张图片，`batch_size=1000`：

- **batch**：一次交给模型的 1,000 张图片。
- **iteration/step**：处理一个 batch 并更新一次参数。
- **epoch**：完整看完一次 50,000 张训练图片。
- 每个 epoch 大约有 `50000 / 1000 = 50` 次 iteration。

你的 CNN 使用 `batch_size=1024`，RNN 使用 `BATCH_SIZE=128`。

### 2.4 训练集、验证集与测试集

| 数据集 | 用途 | 是否更新模型参数 |
|---|---|---|
| 训练集 | 学习参数 | 是 |
| 验证集 | 选择超参数、观察过拟合 | 否 |
| 测试集 | 最终评估泛化能力 | 否 |

当前 CNN 项目使用训练集训练、测试集评估，还没有单独划分验证集。这对入门练习完全可以，但以后做正式实验时应增加验证集，避免一边调参一边“偷看”测试集。

### 2.5 欠拟合与过拟合

- **欠拟合**：训练集都学不好，可能模型太简单、训练时间不足或学习率不合适。
- **过拟合**：训练集表现很好，测试集表现明显较差，说明模型记住了训练数据但泛化不足。
- 常见缓解手段：更多数据、数据增强、正则化、Dropout、早停、减小模型复杂度。

### 30 秒复习

- 训练就是“预测 → 算损失 → 求梯度 → 更新参数”的循环。
- epoch 是完整遍历训练集一次；batch 是一次处理的一小批数据。
- 测试集只用于评估，不参与参数更新。

---

## 3. PyTorch 简单入门

### 3.1 PyTorch 的几个核心模块

| 模块 | 作用 | 当前代码中的例子 |
|---|---|---|
| `torch` | 张量、设备、自动求导、模型读写 | `torch.tensor`、`torch.save` |
| `torch.nn` | 神经网络层和损失函数 | `nn.Conv2d`、`nn.RNN`、`nn.CrossEntropyLoss` |
| `torch.optim` | 优化器 | `optim.Adam` |
| `torch.utils.data` | 数据集和批量加载 | `Dataset`、`DataLoader` |
| `torchvision` | 图像数据集和图像变换 | `CIFAR10`、`ToTensor` |

### 3.2 用 `nn.Module` 定义模型

PyTorch 模型通常继承 `nn.Module`：

```python
class MyModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.layer = nn.Linear(10, 3)

    def forward(self, x):
        return self.layer(x)
```

- `__init__()`：声明模型中有哪些可训练层。
- `forward()`：规定数据按什么顺序经过这些层。
- 调用 `model(x)` 时，PyTorch 会执行模型的 `forward(x)`。
- 只要层被赋值为模型成员，如 `self.layer`，其中的参数就会被 PyTorch 注册并交给优化器。

对应到项目：

- CNN 的 `ImageClassifier(nn.Module)` 声明卷积、池化和全连接层。
- RNN 的 `TextGenerator(nn.Module)` 声明 Embedding、RNN 和输出层。

### 3.3 CPU 与 GPU

CNN 代码会自动选择设备：

```python
device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
model = ImageClassifier().to(device)
x = x.to(device)
y = y.to(device)
```

重要规则：**参与同一次运算的模型和张量必须在同一设备上。**

例如模型在 GPU、输入在 CPU，就会出现设备不一致错误。RNN 代码目前全部运行在 CPU；以后迁移到 GPU 时，输入、标签和隐藏状态 `hidden` 都需要移动到相同设备。

### 3.4 Dataset 与 DataLoader

`Dataset` 负责回答两个问题：

1. 一共有多少条数据？——`__len__()`
2. 第 `idx` 条数据是什么？——`__getitem__(idx)`

`DataLoader` 进一步负责：

- 自动组成 batch；
- 打乱训练数据；
- 多进程读取数据；
- 把单条样本堆叠成批量张量。

```python
dataloader = DataLoader(
    dataset,
    batch_size=128,
    shuffle=True
)
```

训练时通常 `shuffle=True`，评估时通常 `shuffle=False`。

### 3.5 训练模式、评估模式和推理模式

```python
model.train()  # 训练模式
model.eval()   # 评估模式

with torch.inference_mode():
    y_pred = model(x)
```

- `model.train()` 与 `model.eval()` 会影响 Dropout、BatchNorm 等层的行为。
- `torch.inference_mode()` 表示只做推理，不构建梯度计算图，速度更快、内存占用更少。
- `model.eval()` 和 `torch.inference_mode()` 解决的是不同问题，正式推理时通常两者一起使用。

当前 CNN 的 `evaluate()` 已正确使用 `model.eval()` 和 `torch.inference_mode()`。

### 3.6 模型保存与加载

项目保存的是参数字典：

```python
torch.save(model.state_dict(), "./model/image_model.pth")
```

加载时必须先创建相同结构的模型：

```python
model = ImageClassifier()
model.load_state_dict(torch.load("./model/image_model.pth"))
```

`state_dict()` 主要包含每一层的权重和偏置，不包含 Python 类定义。因此加载模型时仍然需要 `ImageClassifier` 或 `TextGenerator` 的代码。

如果模型文件来自 GPU，而当前在 CPU 上运行，可以使用：

```python
state = torch.load("model.pth", map_location="cpu")
model.load_state_dict(state)
```

### 3.7 一个容易混淆的点：训练与继续训练

两个项目的 `train()` 都会先调用 `load_state_dict()`，因此实际含义是：

> 从已有 `.pth` 参数继续训练，而不是从随机参数开始训练。

如果 `.pth` 文件不存在，程序会报错。初次训练通常直接创建模型；继续训练时才加载检查点。

### 30 秒复习

- `nn.Module` 定义模型，`forward()` 定义数据流。
- `Dataset` 管单条样本，`DataLoader` 管批量、打乱和加载。
- 模型、输入、标签和隐藏状态必须在同一设备。
- 训练用 `train()`；评估用 `eval()` 加 `inference_mode()`。

---

## 4. Tensor：张量专章

### 4.1 什么是张量？

张量可以先理解成“可以放在 CPU/GPU 上进行批量计算的多维数组”。

| 维度 | 常见名称 | 示例 shape |
|---|---|---|
| 0 维 | 标量 | `()` |
| 1 维 | 向量 | `(5,)` |
| 2 维 | 矩阵 | `(3, 4)` |
| 3 维 | 三维张量 | `(batch, length, feature)` |
| 4 维 | 四维张量 | `(batch, channel, height, width)` |

`shape` 描述张量每个维度的大小：

```python
x = torch.randn(32, 3, 28, 28)
print(x.shape)       # torch.Size([32, 3, 28, 28])
print(x.size(0))     # 32，即 batch size
print(x.numel())     # 张量中元素总数
```

### 4.2 常见创建方法

```python
torch.tensor([1, 2, 3])       # 从 Python 数据创建
torch.zeros(2, 3)             # 全 0
torch.ones(2, 3)              # 全 1
torch.randn(2, 3)             # 标准正态分布随机数
torch.rand(2, 3)              # [0, 1) 均匀分布随机数
torch.arange(0, 10, 2)        # 0, 2, 4, 6, 8
torch.eye(3)                  # 3×3 单位矩阵
```

### 4.3 dtype：数据类型很重要

```python
x = torch.tensor([1.0, 2.0], dtype=torch.float32)
y = torch.tensor([0, 2, 1], dtype=torch.long)
```

常见规则：

- 神经网络输入和参数通常是 `torch.float32`。
- 分类标签通常是 `torch.long`，因为它保存的是类别索引。
- `nn.Embedding` 的输入也必须是整数索引，通常为 `torch.long`。
- 布尔掩码使用 `torch.bool`。

你的 RNN 中 `torch.tensor(x)` 由整数列表创建，因此默认得到整型张量，可以作为 Embedding 输入。

### 4.4 索引与切片

```python
x = torch.arange(12).reshape(3, 4)

x[0]          # 第 1 行
x[:, 1]       # 所有行的第 2 列
x[0:2, 1:3]   # 行和列切片
x[x > 5]      # 布尔条件筛选
```

索引通常会减少一个维度，切片通常会保留该维度：

```python
x[0].shape      # (4,)
x[0:1].shape    # (1, 4)
```

### 4.5 改变形状：reshape、view、flatten

```python
x = torch.randn(8, 3, 4)
y = x.reshape(8, 12)
z = x.flatten(start_dim=1)
```

- `reshape()`：把元素重新组织成指定形状，元素总数必须不变。
- `view()`：作用类似，但通常要求内存连续。
- `flatten(start_dim=1)`：从指定维度开始展平，CNN 中很常用。
- `-1` 表示让 PyTorch 自动推断该维度。

CNN 使用：

```python
x = x.reshape(x.size(0), -1)
```

这里保留第 0 维 batch，把每张图片剩余的通道和空间维度展平成一个向量。

### 4.6 交换维度：transpose 与 permute

```python
x = torch.randn(32, 5, 128)   # (batch, length, feature)

x.transpose(0, 1).shape       # (5, 32, 128)，只交换两个维度
x.permute(1, 0, 2).shape      # (5, 32, 128)，重新排列全部维度
```

RNN 代码中的 Embedding 输出是 `(batch, seq_len, embedding_dim)`，而当前 `nn.RNN` 默认要求 `(seq_len, batch, input_size)`，所以使用 `transpose(0, 1)`。

### 4.7 增加与删除长度为 1 的维度

```python
x = torch.tensor([1, 2, 3])

x.unsqueeze(0).shape   # (1, 3)
x.unsqueeze(1).shape   # (3, 1)
x.unsqueeze(0).squeeze(0).shape  # 回到 (3,)
```

RNN 生成时使用 `torch.tensor([[word_idx]])`，形状是 `(1, 1)`，表示 batch 为 1、序列长度为 1。

### 4.8 拼接：cat 与 stack

```python
a = torch.randn(2, 3)
b = torch.randn(2, 3)

torch.cat([a, b], dim=0).shape    # (4, 3)，沿已有维度连接
torch.stack([a, b], dim=0).shape  # (2, 2, 3)，创建一个新维度
```

记忆方法：`cat` 不增加维度数，`stack` 会增加一个维度。

### 4.9 广播机制

当两个张量形状不完全相同时，PyTorch 可能自动扩展长度为 1 的维度：

```python
x = torch.randn(32, 10)
b = torch.randn(10)
y = x + b  # b 自动扩展为每一行都加同一个偏置
```

广播从最后一个维度向前比较；两个维度相同，或其中一个为 1 时可以广播。广播很方便，但也可能让错误形状“悄悄算下去”，因此要养成检查 `shape` 的习惯。

### 4.10 聚合与矩阵乘法

```python
x.sum()                # 所有元素求和
x.mean(dim=0)          # 沿第 0 维求平均
x.max(dim=1)           # 同时返回最大值和索引
x.argmax(dim=-1)       # 最后一维最大值的位置

a @ b                  # 矩阵乘法
torch.matmul(a, b)     # 矩阵乘法
```

CNN 中：

```python
torch.argmax(y_pred, dim=-1)
```

`y_pred` 的形状为 `(batch, 10)`，最后一维是 10 个类别，`argmax` 取分数最高的类别索引，结果形状为 `(batch,)`。

### 4.11 自动求导与计算图

```python
w = torch.tensor(2.0, requires_grad=True)
x = torch.tensor(3.0)
y = w * x
loss = (y - 10) ** 2

loss.backward()
print(w.grad)
```

PyTorch 在前向计算时记录运算关系，形成计算图；`backward()` 从 loss 反向计算梯度。

训练循环中必须清除上一轮梯度：

```python
optimizer.zero_grad()
loss.backward()
optimizer.step()
```

PyTorch 默认会累加梯度，而不是覆盖梯度。这一设计支持梯度累积，但普通训练中每一步都要清零。

### 4.12 项目中的关键张量形状

| 场景 | 张量 | 形状 |
|---|---|---|
| CNN 输入 | 一批彩色图片 | `(B, 3, 32, 32)` |
| CNN 输出 | 每张图片的 10 类分数 | `(B, 10)` |
| CNN 标签 | 每张图片的类别索引 | `(B,)` |
| RNN 输入 | 一批词索引序列 | `(B, L)` |
| Embedding 输出 | 每个词的向量 | `(B, L, 128)` |
| RNN 输入格式 | 时间步在前 | `(L, B, 128)` |
| RNN 输出 | 每步隐藏状态 | `(L, B, 256)` |
| 文本输出层 | 每个位置的词表分数 | `(L×B, V)` |
| RNN 标签 | 每个位置的目标词索引 | `(L×B,)` |

其中 `B` 是 batch size，`L` 是序列长度，`V` 是词表大小。

### 30 秒复习

- 张量最先看 `shape`、`dtype`、`device`。
- `reshape` 改形状，`transpose` 交换维度，`argmax` 取最大值索引。
- 分类标签和 Embedding 输入通常是 `long`。
- `backward()` 求梯度；普通训练每一步先 `zero_grad()`。

---

## 5. ANN：人工神经网络

### 5.1 从一个神经元理解

一个最简单的人工神经元会做两步：

1. 对输入进行线性加权。
2. 通过激活函数增加非线性。

公式可以写成：

$$
z = Wx + b, \qquad y = \phi(z)
$$

- $x$：输入特征。
- $W$：权重。
- $b$：偏置。
- $\phi$：激活函数。

PyTorch 中的 `nn.Linear(in_features, out_features)` 就是在做 $Wx+b$。

```python
layer = nn.Linear(120, 84)
```

如果输入形状是 `(B, 120)`，输出形状就是 `(B, 84)`。

### 5.2 为什么需要激活函数？

如果多层网络之间完全没有激活函数，那么多个线性变换组合后仍然只是一个线性变换，深度就失去了意义。

常见激活函数：

| 激活函数 | 特点 | 常见用途 |
|---|---|---|
| ReLU | `max(0, x)`，简单高效 | 隐藏层常用 |
| Sigmoid | 输出在 `(0,1)` | 二分类输出、门控结构 |
| Tanh | 输出在 `(-1,1)` | 传统 RNN 隐藏状态 |
| Softmax | 把一组分数转成概率分布 | 多分类解释或推理 |

CNN 代码在全连接层之间使用：

```python
x = torch.relu(self.fc1(x))
x = torch.relu(self.fc2(x))
```

### 5.3 多层感知机 MLP

把多个“线性层 + 激活函数”串起来，就是多层感知机：

```text
输入 → Linear → ReLU → Linear → ReLU → Linear → 输出
```

CNN 最后的分类部分实际上就是一个 MLP：

```text
576 → 120 → 84 → 10
```

RNN 的 `self.out = nn.Linear(256, unique_word_count)` 也是全连接层，它把每个时间步的隐藏状态映射成词表中每个词的分数。

### 5.4 输出层、logits 与交叉熵

模型最后输出的原始分数称为 **logits**。例如十分类模型输出：

```text
[1.2, -0.3, 2.8, ..., 0.7]
```

`nn.CrossEntropyLoss()` 接收：

- 模型输出：形状 `(N, C)` 的 logits；
- 正确标签：形状 `(N,)` 的类别索引，取值范围为 `0 ~ C-1`。

```python
criterion = nn.CrossEntropyLoss()
loss = criterion(y_pred, y)
```

**使用 CrossEntropyLoss 前不要手动对模型输出做 Softmax。** 它内部已经以数值更稳定的方式组合了 LogSoftmax 和负对数似然损失。

### 5.5 优化器与学习率

```python
optimizer = optim.Adam(model.parameters(), lr=1e-3)
```

- `model.parameters()`：告诉优化器需要更新哪些参数。
- `lr`：学习率，决定每次更新的步幅。
- 学习率太大可能震荡或发散；太小则训练很慢。
- Adam 会自适应调整不同参数的更新幅度，适合作为入门默认选择。

### 5.6 ANN 的局限

如果把图片直接展平后送入全连接网络：

- 会忽略像素之间的二维空间关系；
- 参数数量很大；
- 同一个特征出现在图片不同位置时，需要重复学习。

CNN 用局部连接和参数共享缓解这些问题。类似地，普通 ANN 不会自然记住序列顺序，RNN 则专门处理顺序信息。

### 30 秒复习

- `Linear` 做 $Wx+b$，激活函数提供非线性。
- MLP 是多层 `Linear + 激活函数`。
- 分类模型输出 logits；`CrossEntropyLoss` 前不要手动 Softmax。
- CNN 和 RNN 的末尾通常仍会使用全连接层完成输出映射。

---

## 6. CNN：结合 CIFAR-10 图像分类代码

### 6.1 为什么图像适合 CNN？

图像具有两个重要特点：

1. **局部相关性**：相邻像素通常共同组成边缘、纹理和形状。
2. **位置可迁移性**：检测猫耳朵的模式无论出现在左上还是右下，含义相近。

卷积核像一个小窗口，在整张图片上滑动，用同一组参数寻找某种局部特征。这就是 **局部连接** 和 **参数共享**。

### 6.2 图像张量的 NCHW 格式

PyTorch 的二维卷积默认使用：

```text
(N, C, H, W)
```

- `N`：batch size。
- `C`：通道数；RGB 彩色图片为 3。
- `H`：高度。
- `W`：宽度。

CIFAR-10 图片输入形状为 `(B, 3, 32, 32)`。

### 6.3 卷积层参数

```python
nn.Conv2d(
    in_channels=3,
    out_channels=6,
    kernel_size=3,
    stride=1,
    padding=0
)
```

- `in_channels=3`：输入为 RGB 三通道。
- `out_channels=6`：使用 6 组卷积核，得到 6 张特征图。
- `kernel_size=3`：卷积窗口为 `3×3`。
- `stride=1`：每次滑动 1 格。
- `padding=0`：边缘不补零。

单个空间维度的卷积输出大小为：

$$
H_{out}=\left\lfloor\frac{H_{in}+2P-K}{S}\right\rfloor+1
$$

其中 $P$ 是 padding，$K$ 是 kernel size，$S$ 是 stride。

### 6.4 池化层

```python
nn.MaxPool2d(kernel_size=2, stride=2)
```

最大池化在每个 `2×2` 区域保留最大值，宽高大约减半。它可以：

- 减少后续计算量；
- 扩大后续神经元看到的有效区域；
- 保留较显著的局部响应。

池化没有可训练权重。

### 6.5 `ImageClassifier` 的完整形状追踪

模型结构：

```python
self.conv1 = nn.Conv2d(3, 6, 3, 1, 0)
self.pool1 = nn.MaxPool2d(2, 2, 0)
self.conv2 = nn.Conv2d(6, 16, 3, 1, 0)
self.pool2 = nn.MaxPool2d(2, 2, 0)
self.fc1 = nn.Linear(576, 120)
self.fc2 = nn.Linear(120, 84)
self.output = nn.Linear(84, 10)
```

逐层计算：

| 步骤 | 运算 | 输出形状 | 说明 |
|---|---|---|---|
| 输入 | CIFAR-10 图片 | `(B, 3, 32, 32)` | RGB 彩色图片 |
| 1 | `Conv2d(3,6,3)` | `(B, 6, 30, 30)` | `32-3+1=30` |
| 2 | `ReLU` | `(B, 6, 30, 30)` | 形状不变 |
| 3 | `MaxPool2d(2,2)` | `(B, 6, 15, 15)` | 宽高减半 |
| 4 | `Conv2d(6,16,3)` | `(B, 16, 13, 13)` | `15-3+1=13` |
| 5 | `ReLU` | `(B, 16, 13, 13)` | 形状不变 |
| 6 | `MaxPool2d(2,2)` | `(B, 16, 6, 6)` | `13` 池化后向下取整为 `6` |
| 7 | `reshape` | `(B, 576)` | `16×6×6=576` |
| 8 | `Linear(576,120)` | `(B, 120)` | 全连接层 |
| 9 | `Linear(120,84)` | `(B, 84)` | 全连接层 |
| 10 | `Linear(84,10)` | `(B, 10)` | 十个类别的 logits |

因此 `fc1` 的输入维度必须写成 `576`。只要前面的卷积参数或输入图片尺寸改变，这个数字就可能需要重新计算。

### 6.6 数据集与图像转换

```python
train_dataset = CIFAR10(
    root="./data",
    train=True,
    transform=ToTensor(),
    download=True
)
```

- `root`：数据保存目录。
- `train=True`：加载训练集；`False` 加载测试集。
- `ToTensor()`：把图片转换为 `(C,H,W)` 浮点张量，并把像素从 `0~255` 缩放到 `0~1`。
- `download=True`：本地没有数据时自动下载。

### 6.7 训练循环逐步解释

```python
for x, y in dataLoader:
    x = x.to(device)
    y = y.to(device)

    y_pred = model(x)
    loss = criterion(y_pred, y)

    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

每一步发生的事情：

1. DataLoader 给出图片 `x` 和类别 `y`。
2. 把数据移动到与模型相同的设备。
3. `model(x)` 得到 `(B,10)` 的类别分数。
4. 交叉熵比较预测分数和正确类别。
5. 清除旧梯度。
6. 反向传播计算新梯度。
7. Adam 更新模型参数。

准确率统计：

```python
pred = torch.argmax(y_pred, dim=-1)
correct = (pred == y).sum().item()
```

`loss.item()` 和 `.item()` 会把单元素张量转换为普通 Python 数值，适合日志统计，也避免无意中保留计算图。

### 6.8 为什么平均 loss 要乘样本数？

代码中：

```python
total_loss += loss.item() * y.size(0)
total_samples += y.size(0)
epoch_loss = total_loss / total_samples
```

`CrossEntropyLoss` 默认返回当前 batch 的平均损失。最后一个 batch 可能比其他 batch 小，因此先乘回当前 batch 样本数，累计后再除以总样本数，可以得到严格的全体样本平均损失。

### 6.9 评估流程

评估时不更新参数：

```python
model.eval()
with torch.inference_mode():
    for x, y in dataLoader:
        y_pred = torch.argmax(model(x), dim=-1)
```

评估数据不打乱，因为打乱不会影响最终准确率，而且保持顺序更利于复现和排查。

### 6.10 当前 CNN 代码的理解提示

以下是学习性提示，不代表必须立即修改：

1. **当前 `train()` 默认加载已有模型。** 这表示继续训练；模型文件不存在时会失败。
2. **相对路径依赖启动目录。** `./data` 和 `./model` 都是相对当前工作目录，而不一定是脚本所在目录。
3. **batch size 为 1024。** GPU 显存不足时可减小；batch 太大也会减少每个 epoch 的参数更新次数。
4. **目前只使用 `ToTensor()`。** 后续可学习标准化和随机裁剪、翻转等数据增强。
5. **模型较小且结构清晰。** 很适合学习卷积形状，但真实任务还会用 BatchNorm、Dropout、残差连接等结构。
6. **加载跨设备模型时可使用 `map_location=device`。** 这样在 GPU/CPU 环境切换时更稳妥。
7. **输出层没有 Softmax 是正确的。** 因为训练使用的是 `CrossEntropyLoss`。

### 30 秒复习

- 图像通常是 `(B,C,H,W)`。
- 卷积提取局部特征，池化压缩空间，全连接层完成分类。
- 本项目的核心形状是 `(B,3,32,32) → (B,16,6,6) → (B,576) → (B,10)`。
- 十分类输出是 logits，取 `argmax` 得到预测类别。

---

## 7. RNN：结合歌词生成代码

### 7.1 为什么文本需要序列模型？

文本中的顺序会影响含义：

```text
我喜欢你
你喜欢我
```

两个句子包含相同的词，但顺序不同、含义也不同。RNN 按时间步读取词，并把之前的信息保存在隐藏状态中。

传统 RNN 的核心递推可以简化为：

$$
h_t=\tanh(W_{xh}x_t+W_{hh}h_{t-1}+b)
$$

- $x_t$：当前词的向量。
- $h_{t-1}$：读完前一个词后的记忆。
- $h_t$：加入当前词后形成的新记忆。

### 7.2 从原始歌词到词索引

`build_vocab()` 主要完成四步：

1. 逐行读取歌词。
2. 使用 `jieba.lcut(line)` 进行中文分词。
3. 收集不重复的词，构造词表。
4. 把原始歌词转换为词索引序列。

例如：

```text
原句：我 喜欢 音乐
词表：{"我": 0, "喜欢": 1, "音乐": 2}
索引：[0, 1, 2]
```

神经网络不能直接计算字符串，所以先把词映射为整数 ID。这里的整数只是编号，本身没有大小或距离含义。

### 7.3 滑动窗口构造训练样本

`LyricsDataset` 会从长词序列中截取固定长度窗口。假设窗口长度为 4：

```text
x = [我, 想, 要, 有]
y = [想, 要, 有, 直升机]
```

模型在每个位置都学习预测下一个词：

```text
看到“我”          → 预测“想”
看到“我 想”       → 预测“要”
看到“我 想 要”    → 预测“有”
看到“我 想 要 有” → 预测“直升机”
```

这类任务称为 **自回归语言建模**：用前文预测下一个 token。

### 7.4 Embedding：把词 ID 变成词向量

```python
self.ebd = nn.Embedding(unique_word_count, 128)
```

Embedding 可以理解为一个可训练的查找表：

- 表中有 `unique_word_count` 行，每个词一行；
- 每行有 128 个数，即词向量维度；
- 输入词 ID，取出对应的 128 维向量；
- 这些向量会跟随模型一起训练。

形状变化：

```text
(B, L) → Embedding → (B, L, 128)
```

Embedding 让模型能够学习词之间的分布式表示，而不是把词 ID 当成普通数值。

### 7.5 `TextGenerator` 的完整形状追踪

模型结构：

```python
self.ebd = nn.Embedding(V, 128)
self.rnn = nn.RNN(128, 256, 1)
self.out = nn.Linear(256, V)
```

其中：

- `V`：词表大小。
- `L`：序列长度，训练代码中为 4。
- `B`：当前 batch 的样本数。

| 步骤 | 运算 | 形状 |
|---|---|---|
| 输入 | 词索引 | `(B, L)` |
| 1 | `Embedding(V,128)` | `(B, L, 128)` |
| 2 | `transpose(0,1)` | `(L, B, 128)` |
| 3 | `RNN(128,256,1)` 输出 | `(L, B, 256)` |
| 4 | 展平时间与 batch | `(L×B, 256)` |
| 5 | `Linear(256,V)` | `(L×B, V)` |
| 标签 | 转置并展平 | `(L×B,)` |

这样，`CrossEntropyLoss` 会把 `L×B` 个位置都视为一个分类样本，每个位置都要从词表的 `V` 个词中预测正确的下一个词。

### 7.6 隐藏状态的形状

```python
return torch.zeros(1, batch_size, 256)
```

RNN 隐藏状态的形状为：

```text
(num_layers, batch_size, hidden_size)
```

本项目中分别是：

- `num_layers=1`；
- `batch_size` 由当前 batch 决定；
- `hidden_size=256`。

训练时使用 `current_batch_size = x.size(0)` 很重要，因为最后一个 batch 可能不足 128 条数据。

### 7.7 RNN 的训练过程

```python
hidden = model.init_hidden(current_batch_size)
output, hidden = model(x, hidden)
y = torch.transpose(y, 0, 1).reshape(-1)
loss = criterion(output, y)
```

这里有两次关键对齐：

1. 模型输出按 `(时间, batch)` 展平为 `(L×B,V)`。
2. 标签也先交换时间和 batch，再展平为 `(L×B,)`。

两者的排列顺序必须完全一致，否则即使 shape 看起来正确，位置也会错配，模型学到错误关系。

### 7.8 歌词生成过程

`evaluate(start_word, sentence_length)` 的逻辑是：

1. 把起始词转换成词 ID。
2. 初始化隐藏状态。
3. 把当前词送入模型。
4. 选择输出分数最高的词。
5. 保留新的隐藏状态，把预测词作为下一次输入。
6. 重复直到达到指定长度。

```text
起始词 → RNN → 下一个词 → RNN → 再下一个词 → ...
             ↑保留 hidden↑
```

代码使用 `torch.argmax(output)`，这叫 **贪心解码**：每一步都选择当前最可能的词。它简单稳定，但容易生成重复、保守或不够自然的文本。

后续可以学习：

- temperature 温度采样；
- top-k 采样；
- top-p/nucleus 采样；
- 重复惩罚。

这些思想也会在大语言模型推理中再次出现。

### 7.9 如何看 `train_log.txt`

日志后期大致为：

```text
epoch:87, loss:1.1995
...
epoch:100, loss:1.1979
```

可以得到几点信息：

- 后期 loss 已在 `1.198` 左右小幅波动，说明当前设置下训练趋于平台期。
- 生成文本出现了局部通顺的片段，说明模型学到部分相邻词模式。
- 整体主题跳跃、句子衔接有限，符合短窗口、简单 RNN 和贪心解码的能力边界。
- 只有训练 loss 还不足以判断泛化能力；更完整的实验还需要验证集 loss。

不要只看 loss 的绝对值判断模型“好不好”。词表大小、数据处理方式和任务定义不同，loss 的可比性也不同。更重要的是观察下降趋势、验证集表现和实际生成质量。

### 7.10 当前 RNN 代码的理解提示

以下同样是后续学习方向，不要求现在立即修改：

1. **训练默认加载已有模型。** `.pth` 不存在时无法从头训练。
2. **推理可加入 `model.eval()` 和 `torch.inference_mode()`。** 当前推理会构建不需要的计算图。
3. **GPU 迁移需要统一设备。** Embedding 输入、标签和 `hidden` 都必须与模型在同一设备。
4. **当前采用贪心解码。** 输出确定，但文本多样性较低。
5. **词表用列表逐个判断是否重复。** 数据很大时可用集合或计数器提高构建速度。
6. **数据集末尾会重复一个有效窗口。** 当前 `__len__` 比“还要有一个目标词”的有效窗口数多 1，`__getitem__` 又把最后索引限制到上一窗口；理解滑动窗口边界时应特别留意。
7. **累计日志损失宜使用 `loss.item()`。** 直接累加 loss 张量可能在一个 epoch 内保留计算图引用，数据量大时会增加内存压力。
8. **普通 RNN 对长距离依赖能力有限。** LSTM、GRU 用门控结构缓解梯度消失；Transformer 则通过注意力直接建立远距离联系。

### 7.11 从 RNN 走向现代 NLP

```text
词表与 token
  → Embedding
  → RNN
  → LSTM / GRU
  → Attention
  → Transformer
  → 预训练语言模型与大模型
```

你当前代码中的“分词、token ID、Embedding、下一个词预测、逐词生成”，在大语言模型中仍然存在。将来变化最大的，是序列建模结构和训练规模，而不是任务主线。

### 30 秒复习

- RNN 用隐藏状态保存前文信息。
- Embedding 把整数词 ID 映射成可训练向量。
- 训练目标是让每个位置预测下一个词。
- 本项目的核心形状是 `(B,L) → (L,B,128) → (L,B,256) → (L×B,V)`。
- 生成时保留 hidden，并把预测词作为下一步输入。

---

## 8. ANN、CNN、RNN 横向对比

| 对比项 | ANN/MLP | CNN | RNN |
|---|---|---|---|
| 典型数据 | 固定长度向量、表格 | 图片、网格 | 文本、时间序列 |
| 核心结构 | 全连接层 | 卷积与池化 | 循环单元与隐藏状态 |
| 主要先验 | 特征可以整体组合 | 相邻位置相关 | 前后顺序相关 |
| 参数共享 | 通常较少 | 卷积核跨位置共享 | 同一递推参数跨时间共享 |
| 主要优势 | 简单通用 | 擅长局部空间特征 | 能处理变长顺序信息 |
| 主要局限 | 容易忽略结构 | 全局关系需更深网络 | 难并行、长依赖较弱 |
| 当前代码对应 | CNN/RNN 中的 Linear | CIFAR-10 分类 | 歌词生成 |

### 8.1 如何选择？

- 数据是固定长度特征向量：先考虑 MLP。
- 数据有明显二维局部结构：先考虑 CNN。
- 数据按时间或顺序到达：传统入门可考虑 RNN/LSTM/GRU。
- 现代文本任务：通常优先了解 Transformer。
- 数据由节点和边组成：考虑 GNN。

现实模型经常组合多种结构。例如 CNN 提取图像特征后接 MLP 分类；RNN 每个时间步的隐藏状态也要经过 Linear 变成词表分数。

### 30 秒复习

- 模型选择首先看数据结构，而不是只看模型名字。
- CNN 共享空间位置上的卷积参数；RNN 共享时间步上的递推参数。
- 全连接层几乎会出现在各种网络的输出部分。

---

## 9. PyTorch 通用训练模板

下面的模板用于理解标准流程，不替换项目现有代码：

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

model = MyModel().to(device)
criterion = nn.CrossEntropyLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=1e-3)

for epoch in range(num_epochs):
    model.train()
    total_loss = 0.0
    total_correct = 0
    total_samples = 0

    for x, y in train_loader:
        x = x.to(device)
        y = y.to(device)

        logits = model(x)
        loss = criterion(logits, y)

        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        total_loss += loss.item() * y.size(0)
        total_correct += (logits.argmax(dim=-1) == y).sum().item()
        total_samples += y.size(0)

    print(
        f"epoch={epoch + 1}, "
        f"loss={total_loss / total_samples:.4f}, "
        f"acc={total_correct / total_samples:.4f}"
    )
```

评估模板：

```python
model.eval()
total_correct = 0
total_samples = 0

with torch.inference_mode():
    for x, y in test_loader:
        x = x.to(device)
        y = y.to(device)

        logits = model(x)
        pred = logits.argmax(dim=-1)

        total_correct += (pred == y).sum().item()
        total_samples += y.size(0)

print(f"accuracy={total_correct / total_samples:.4f}")
```

### 9.1 训练循环的固定顺序

```text
model.train()
  ↓
读取 batch
  ↓
移动到 device
  ↓
forward 得到 logits
  ↓
计算 loss
  ↓
zero_grad
  ↓
backward
  ↓
optimizer.step
```

### 9.2 更完整的检查点

只保存 `state_dict` 足以做推理；如果希望中断后原样继续训练，通常还会保存优化器状态和 epoch：

```python
torch.save({
    "epoch": epoch,
    "model_state": model.state_dict(),
    "optimizer_state": optimizer.state_dict(),
}, "checkpoint.pth")
```

优化器也有内部状态。例如 Adam 会维护动量信息；只加载模型参数而不加载优化器状态，可以继续训练，但不等于完全恢复到中断前的状态。

### 30 秒复习

- 训练顺序：前向 → loss → 清梯度 → 反向 → 更新。
- 评估不需要 backward 和 optimizer。
- 完整恢复训练时，同时保存模型、优化器和 epoch。

---

## 10. 常见错误与排查清单

### 10.1 形状不匹配

典型报错关键词：

```text
mat1 and mat2 shapes cannot be multiplied
size mismatch
Expected input ...
```

排查方法：在关键层前后打印 shape。

```python
print("input:", x.shape)
x = self.conv1(x)
print("after conv1:", x.shape)
```

重点检查：

- CNN 是否是 `(B,C,H,W)`；
- Linear 的输入维度是否等于展平后的特征数；
- RNN 是否是 `(L,B,E)` 或是否正确设置了 `batch_first=True`；
- logits 与标签展平顺序是否一致。

### 10.2 dtype 不正确

典型情况：

- 卷积输入应为浮点数；
- Embedding 输入应为整数；
- CrossEntropyLoss 的标签应为 `long`。

```python
print(x.dtype, y.dtype)
x = x.float()
y = y.long()
```

### 10.3 设备不一致

```text
Expected all tensors to be on the same device
```

检查模型、输入、标签、隐藏状态：

```python
print(next(model.parameters()).device)
print(x.device, y.device, hidden.device)
```

### 10.4 CrossEntropyLoss 使用错误

正确形式：

```text
logits: (N, C)，float
target: (N,)，long，值在 [0, C-1]
```

常见错误：

- 先手动 Softmax；
- 标签做成 one-hot，但仍使用类别索引版本的 CrossEntropyLoss；
- 标签多一个维度，形状为 `(N,1)`；
- 模型输出类别数与词表大小或数据集类别数不一致。

### 10.5 忘记清除梯度

PyTorch 梯度默认累加。普通训练若忘记 `optimizer.zero_grad()`，会把多次 batch 的梯度叠加，导致更新异常。

### 10.6 忘记切换模式

- 训练前：`model.train()`。
- 验证和推理前：`model.eval()`。
- 推理计算：`torch.inference_mode()`。

即使当前模型没有 Dropout 和 BatchNorm，也建议保留正确习惯。

### 10.7 模型文件加载失败

检查：

- 路径是否相对于正确的工作目录；
- `.pth` 文件是否存在；
- 当前模型结构是否与保存时一致；
- CPU/GPU 是否需要 `map_location`。

### 10.8 CUDA 显存不足

典型处理顺序：

1. 减小 batch size。
2. 减小输入尺寸或模型规模。
3. 推理时使用 `inference_mode()`。
4. 后续学习混合精度训练。

不要把“清缓存”当作第一解决方案；真正占用显存的通常是模型、激活、梯度和优化器状态。

### 10.9 loss 不下降

依次检查：

- 标签是否正确；
- 输入是否经过合理预处理；
- 是否执行了 `backward()` 和 `optimizer.step()`；
- 学习率是否过大或过小；
- 输出与标签是否对齐；
- 模型是否有足够能力；
- 先尝试让模型在很小的一批数据上过拟合，以验证训练链路。

### 30 秒复习

遇到错误先打印四样东西：

```text
shape、dtype、device、数值范围
```

然后按“数据 → 模型输入 → 每层输出 → loss 输入 → 梯度 → 参数更新”的顺序排查。

---

## 11. 后续 NLP、GNN 与大模型学习路线

这一部分只建立地图。当前阶段不需要一次学完。

### 11.1 NLP 路线

建议顺序：

1. 文本清洗、分词、token 与词表。
2. one-hot、Embedding、词向量相似度。
3. RNN、梯度消失与梯度爆炸。
4. LSTM、GRU 的门控思想。
5. Seq2Seq、编码器与解码器。
6. Attention 注意力机制。
7. Transformer。
8. BERT 类编码器模型和 GPT 类自回归模型。

你已经接触的基础：分词、词表、token ID、Embedding、自回归预测和逐词生成。

### 11.2 GNN 路线

学习 GNN 前先理解图数据：

- 节点（node）：人、商品、分子中的原子等；
- 边（edge）：好友关系、购买关系、化学键等；
- 节点特征、边特征、图级特征；
- 邻接矩阵和边列表。

GNN 的核心思想是 **消息传递**：

```text
收集邻居信息 → 聚合 → 与自身信息结合 → 更新节点表示
```

建议继续学习：GCN → GraphSAGE → GAT → 图分类/节点分类/链接预测。

### 11.3 Transformer 与大模型路线

需要优先掌握：

1. Query、Key、Value 与自注意力。
2. Multi-Head Attention。
3. 位置编码。
4. 残差连接、LayerNorm、前馈网络。
5. 编码器、解码器和因果遮罩。
6. next-token prediction 预训练目标。
7. 预训练、指令微调、偏好对齐。
8. 推理采样、上下文窗口、KV Cache。
9. RAG、向量检索、工具调用。

当前 RNN 项目和大模型之间的共同点：

```text
文本 → token ID → Embedding → 序列模型 → 词表 logits
     → 选择下一个 token → 把结果继续作为输入
```

主要区别是：大模型用 Transformer 替代简单 RNN，并使用更大的数据、参数量和计算资源。

### 11.4 建议补充的数学基础

| 数学主题 | 深度学习中的用途 |
|---|---|
| 向量与矩阵 | 张量、Linear、Embedding |
| 矩阵乘法 | 神经网络几乎所有层的基础 |
| 导数与偏导 | 梯度和反向传播 |
| 链式法则 | 多层网络的梯度传递 |
| 概率分布 | Softmax、采样、生成模型 |
| 对数 | 交叉熵和数值稳定性 |
| 均值与方差 | 数据标准化和归一化层 |

建议采用“遇到问题再补数学”的方式，不需要先学完整本高等数学才开始实践。

### 11.5 推荐实践顺序

```text
完善当前 CNN 分类
  → 手写一个 MLP 对比 CNN
  → 将普通 RNN 换成 LSTM/GRU
  → 加入采样策略
  → 实现简化版 Attention
  → 学习 Transformer
  → 开始小型 NLP/GNN 项目
```

### 30 秒复习

- NLP 主线：token → Embedding → 序列建模 → 任务输出。
- GNN 主线：节点从邻居收集并聚合信息。
- 大模型主线仍是预测 token，只是核心结构升级为 Transformer。

---

## 12. 快速复习附录

### 12.1 一页流程速查

```text
数据阶段
Dataset → DataLoader → x, y → .to(device)

模型阶段
nn.Module → __init__ 定义层 → forward 定义数据流

训练阶段
model.train()
logits = model(x)
loss = criterion(logits, y)
optimizer.zero_grad()
loss.backward()
optimizer.step()

评估阶段
model.eval()
with torch.inference_mode():
    logits = model(x)
    pred = logits.argmax(dim=-1)

保存阶段
torch.save(model.state_dict(), path)
model.load_state_dict(torch.load(path))
```

### 12.2 高频 API 速查

| API | 作用 |
|---|---|
| `tensor.shape` / `tensor.size()` | 查看形状 |
| `tensor.dtype` | 查看数据类型 |
| `tensor.device` | 查看所在设备 |
| `tensor.to(device)` | 移动设备 |
| `reshape` / `flatten` | 改变形状或展平 |
| `transpose` / `permute` | 交换或排列维度 |
| `unsqueeze` / `squeeze` | 增加或删除长度为 1 的维度 |
| `cat` / `stack` | 沿已有维度连接或创建新维度堆叠 |
| `argmax(dim=-1)` | 取得分最高的索引 |
| `loss.backward()` | 反向传播计算梯度 |
| `optimizer.zero_grad()` | 清除旧梯度 |
| `optimizer.step()` | 更新参数 |
| `model.train()` | 切换到训练模式 |
| `model.eval()` | 切换到评估模式 |
| `torch.inference_mode()` | 关闭推理时的梯度记录 |
| `state_dict()` | 获取模型参数字典 |

### 12.3 关键公式速查

线性层：

$$
y=Wx+b
$$

卷积输出大小：

$$
H_{out}=\left\lfloor\frac{H_{in}+2P-K}{S}\right\rfloor+1
$$

简单 RNN：

$$
h_t=\tanh(W_{xh}x_t+W_{hh}h_{t-1}+b)
$$

梯度下降的直观形式：

$$
\theta \leftarrow \theta-\eta\nabla_{\theta}L
$$

- $\theta$：模型参数。
- $\eta$：学习率。
- $L$：损失。

### 12.4 两个项目的形状总表

#### CNN

```text
(B,3,32,32)
→ Conv1: (B,6,30,30)
→ Pool1: (B,6,15,15)
→ Conv2: (B,16,13,13)
→ Pool2: (B,16,6,6)
→ Flatten: (B,576)
→ FC1: (B,120)
→ FC2: (B,84)
→ Output: (B,10)
```

#### RNN

```text
token IDs: (B,L)
→ Embedding: (B,L,128)
→ Transpose: (L,B,128)
→ RNN: (L,B,256)
→ Reshape: (L×B,256)
→ Linear: (L×B,V)

target: (B,L)
→ Transpose + Reshape: (L×B,)
```

### 12.5 术语中英文对照

| 中文 | 英文 | 简要含义 |
|---|---|---|
| 张量 | Tensor | 多维数值数组 |
| 参数 | Parameter | 模型训练得到的权重和偏置 |
| 超参数 | Hyperparameter | 人为设置的训练配置 |
| 前向传播 | Forward propagation | 输入经过模型得到预测 |
| 反向传播 | Backpropagation | 根据损失计算梯度 |
| 梯度 | Gradient | 损失对参数的变化率 |
| 损失函数 | Loss function | 衡量预测错误程度 |
| 优化器 | Optimizer | 根据梯度更新参数 |
| 学习率 | Learning rate | 参数更新步幅 |
| 批次 | Batch | 一次处理的一组样本 |
| 轮次 | Epoch | 完整遍历一次训练集 |
| 激活函数 | Activation function | 为网络引入非线性 |
| 卷积核 | Kernel/Filter | 提取局部空间特征的小窗口 |
| 特征图 | Feature map | 卷积得到的通道输出 |
| 池化 | Pooling | 压缩空间尺寸 |
| 词表 | Vocabulary | token 与 ID 的映射集合 |
| 词嵌入 | Embedding | token 的可训练向量表示 |
| 隐藏状态 | Hidden state | RNN 保存的历史信息 |
| 逻辑值 | Logits | 输出层未经概率归一化的分数 |
| 推理 | Inference | 使用训练好的模型产生预测 |

### 12.6 自测题

1. 为什么 `CrossEntropyLoss` 前通常不手动调用 Softmax？
2. `optimizer.zero_grad()` 为什么不能省略？
3. CNN 输入 `(B,3,32,32)` 中四个维度分别表示什么？
4. 为什么 CNN 的 `fc1` 输入是 576？
5. `reshape(x.size(0), -1)` 为什么要保留第 0 维？
6. Embedding 的输入为什么是整数，输出为什么是浮点向量？
7. RNN 的 hidden 表示什么，生成文本时为什么不能每一步都重新初始化？
8. `model.eval()` 与 `torch.inference_mode()` 有什么区别？
9. ANN、CNN、RNN 分别适合什么数据结构？
10. 如果出现设备不一致报错，应该检查哪些对象？

<details>
<summary>点击查看参考答案</summary>

1. 因为 `CrossEntropyLoss` 内部已经以更稳定的方式完成 LogSoftmax 和损失计算。
2. PyTorch 默认累加梯度，不清除会把前几个 batch 的梯度一起用于当前更新。
3. batch、通道、高度、宽度。
4. 第二次池化后形状为 `(B,16,6,6)`，每张图展平后是 `16×6×6=576`。
5. 第 0 维是 batch；每个样本应单独展平，不能把不同样本混在一起。
6. 整数是词表中的查询 ID；Embedding 根据 ID 取出可训练的浮点向量。
7. hidden 是到当前时间步为止的历史信息；每步重置会让模型忘记前文。
8. `eval()` 改变部分层的行为；`inference_mode()` 关闭梯度记录。正式推理通常同时使用。
9. MLP 适合固定长度向量，CNN 适合空间网格，RNN 适合顺序数据。
10. 模型参数、输入、标签，以及 RNN 的 hidden 等辅助张量。

</details>

### 12.7 建议的小练习

1. 在不训练的情况下，给 CNN 每一层后加入临时 `print(x.shape)`，验证形状表。
2. 用 `torch.randn(2,3,32,32)` 手动测试 `ImageClassifier` 的输出 shape。
3. 把 RNN 序列长度从 4 改成 5，预测各阶段 shape 是否变化。
4. 比较 `argmax`、temperature 和 top-k 生成歌词的差异。
5. 用一个简单 MLP 直接分类 CIFAR-10，再与 CNN 准确率对比，体会模型结构先验的重要性。
6. 将 `nn.RNN` 替换为 `nn.GRU` 或 `nn.LSTM`，先只观察接口和 hidden 结构的变化。

---

## 结语

现阶段最重要的不是记住所有 API，而是建立三条稳定主线：

1. **数据主线**：原始数据如何变成模型需要的张量。
2. **形状主线**：张量进入每一层后，shape 如何变化。
3. **训练主线**：前向、损失、反向传播和参数更新如何连接。

当一个新模型看起来很复杂时，先问四个问题：

```text
输入是什么形状？
每一层改变了什么？
输出代表什么？
损失如何把输出和正确答案连接起来？
```

能回答这四个问题，就已经抓住了理解新模型最可靠的入口。
