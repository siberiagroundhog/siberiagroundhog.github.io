# 机器学习笔记

## 目录

- [1. 学习背景与项目总览](#1-学习背景与项目总览)
- [2. 基础神经网络与函数拟合](#2-基础神经网络与函数拟合)
- [3. 支持向量机系列](#3-支持向量机系列)
- [4. 传统机器学习与可解释模型](#4-传统机器学习与可解释模型)
- [5. 无监督学习与竞争学习](#5-无监督学习与竞争学习)
- [6. 特征选择与变量重要性](#6-特征选择与变量重要性)
- [7. 时间序列与动态神经网络](#7-时间序列与动态神经网络)
- [8. 智能优化算法](#8-智能优化算法)
- [9. 自定义网络与训练工程](#9-自定义网络与训练工程)
- [10. 横向对比总结](#10-横向对比总结)
- [11. 这段时间的学习大总结](#11-这段时间的学习大总结)

## 1. 学习背景与项目总览

### 1.1 学习这本书的目的

本项目围绕《MATLAB 神经网络 43 个案例分析》展开。学习目标不是简单照抄书中的 MATLAB 代码，而是把书中典型案例迁移到 Python、PyTorch 和 sklearn 环境中，通过复现训练机器学习建模的基本能力。

这段时间的学习重点包括：

- 理解常见模型的基本原理。
- 熟悉分类、回归、聚类、时间序列预测、特征选择和参数优化等任务。
- 掌握数据预处理、训练测试划分、模型训练、指标评估和结果可视化。
- 通过 notebook 复现，把书中案例变成自己能解释、能修改、能复盘的实验。

### 1.2 当前已复现项目总览表

| 分类 | 已复现内容 | 对应目录或文件 |
| --- | --- | --- |
| 基础神经网络 | BP 非线性拟合 | `1_4_BP/bp.ipynb` |
| 基础神经网络 | RBF 回归、GRNN 回归 | `7_8_rbf_grnn/rbf_grnn.ipynb` |
| 基础神经网络 | ELM 回归与分类 | `29_ELM/elm.ipynb` |
| 支持向量机 | SVM 葡萄酒分类、SVM 参数优化 | `12_15_svm_wine/main.ipynb` |
| 支持向量机 | SVR 时间序列回归 | `16_17_svr_qqq/svr.ipynb` |
| 支持向量机 | SVC 信息粒化趋势分类 | `16_17_svr_qqq/svc_qqq.ipynb` |
| 树模型 | 决策树乳腺癌分类、剪枝、网格搜索 | `28_decisionTree/main.ipynb` |
| 竞争学习 | 自组织竞争网络分类 | `21_自组织竞争网络/compettitive_network.ipynb` |
| 特征选择 | MIV 变量重要性分析 | `25_MIV/miv.ipynb` |
| 特征选择 | 遗传算法特征选择 | `36_ga_select_feature/main.ipynb` |
| 时间序列 | Elman 神经网络预测 | `23_Elman/main.ipynb` |
| 时间序列 | 小波神经网络预测 | `32_小波神经网络/main.ipynb` |
| 时间序列 | 灰色预测、灰色神经网络、残差 BP | `37_grey_model/main.ipynb` |
| 时间序列 | NARX / 动态神经网络预测 | `40_narx/main.ipynb` |
| 自定义网络 | MLP、双分支网络、小波层、自定义网络结构 | `41_42_自定义_GPU加速/main.ipynb` |
| 训练工程 | 设备管理、训练模式、推理模式、数据加载、混合精度 | `41_42_自定义_GPU加速/main.ipynb` |
| 公共工具 | 通用 GA 参数优化函数 | `utils/ga.py` |
| 公共工具 | 通用 PSO 参数优化函数 | `utils/pso.py` |

### 1.3 本笔记的分类方式

本笔记不按书中案例编号逐个罗列，而是按能力和模型类型分类。这样整理的好处是更便于复习：同一类问题放在一起，可以比较不同模型在任务、数据、训练方式和结果解释上的差异。

每个模型或实验尽量按固定结构记录：

- 模型原理。
- 核心代码。
- 我的实验过程。
- 实验总结。
- 后续改进。

## 2. 基础神经网络与函数拟合

### 2.1 BP 神经网络非线性拟合

#### 1. 模型原理

BP 神经网络本质上是一个前馈神经网络，通过隐藏层和激活函数学习输入到输出之间的非线性映射。前向传播得到预测值，损失函数衡量预测值与真实值的差距，反向传播根据损失计算梯度，优化器根据梯度更新网络参数。

在本实验中，输入是一个一维变量 `x`，输出是连续值 `y`。目标函数为：

```text
y = sin(x) + 0.2x + noise
```

这个实验适合用来理解神经网络训练的最小闭环：构造数据、定义模型、定义损失函数、训练模型、可视化拟合结果。

#### 2. 核心代码

```python
class BPNet(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super(BPNet, self).__init__()
        self.layer1 = nn.Linear(input_dim, hidden_dim)
        self.relu = nn.ReLU()
        self.layer2 = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        x = self.layer1(x)
        x = self.relu(x)
        x = self.layer2(x)
        return x
```

```python
criterion = nn.MSELoss()
optimizer = optim.Adam(model.parameters(), lr=0.01)

for epoch in range(epochs):
    y_pred = model(X_tensor)
    loss = criterion(y_pred, y_tensor)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

#### 3. 我的实验过程

实验先用 `np.linspace(-5, 5, 500)` 构造一维输入，再生成带噪声的目标函数值。随后把数据转成 `torch.float32` 张量，定义两层全连接网络，用 MSE 作为回归损失，用 Adam 优化器训练。

训练完成后，通过散点图和预测曲线对比观察模型是否拟合了整体趋势。

#### 4. 实验总结

这个实验让我完整跑通了 PyTorch 训练流程。最关键的理解是：神经网络不是直接记住样本点，而是通过参数学习输入和输出之间的函数关系。ReLU 激活函数提供非线性表达能力，MSE 负责度量预测误差，`loss.backward()` 和 `optimizer.step()` 完成参数更新。

当前实验的不足是没有划分训练集和测试集，也没有记录完整 loss 曲线，模型保存和实验配置管理也还不完整。

#### 5. 后续改进

- 增加 train/test 划分。
- 记录并绘制 loss 曲线。
- 对比不同隐藏层维度、学习率和训练轮数。
- 加入模型保存和加载。
- 把训练和评估封装成函数。

### 2.2 RBF 径向基函数网络

#### 1. 模型原理

RBF 网络是一种基于径向基函数的前馈网络。它的核心思想是：先选择若干中心点，再计算样本到这些中心点的距离，距离越近响应越强，最后用线性输出层组合这些响应得到预测结果。

与普通 BP 网络相比，RBF 更强调局部响应。BP 通过全局参数逐层变换学习非线性函数，RBF 则通过“样本离中心点有多近”来构造隐藏层特征。

#### 2. 核心代码

```python
def gaussian_rbf(X, centers, spread):
    diff = X[:, None, :] - centers[None, :, :]
    dist_sq = np.sum(diff**2, axis=2)
    Phi = np.exp(-dist_sq / (2 * spread**2))
    return Phi
```

```python
class RBFRegressor:
    def __init__(self, n_centers=50, spread=1.0, random_state=None):
        self.n_centers = n_centers
        self.spread = spread
        self.random_state = random_state

    def fit(self, X, y):
        rng = np.random.default_rng(self.random_state)
        center_indices = rng.choice(len(X), size=self.n_centers, replace=False)
        self.centers_ = X[center_indices]
        Phi = self._gaussian_rbf(X, self.centers_)
        Phi_bias = np.column_stack([Phi, np.ones(len(Phi))])
        params = np.linalg.lstsq(Phi_bias, y, rcond=None)[0]
        self.weights_ = params[:-1]
        self.bias_ = params[-1]
        return self
```

#### 3. 我的实验过程

实验中先划分训练集和测试集，再手写高斯 RBF 函数。RBFRegressor 随机选择训练样本作为中心点，计算训练集到中心点的响应矩阵，然后用最小二乘求输出层权重。

#### 4. 实验总结

RBF 的训练过程比 BP 更“显式”：隐藏层不是通过反向传播慢慢学习出来，而是先由中心点和宽度参数决定，再求解输出权重。这个实验让我理解了“特征映射 + 线性回归”的思想。

RBF 的效果对中心点数量和 `spread` 很敏感。中心点太少可能欠拟合，太多可能过拟合；`spread` 太小会导致响应过窄，太大又会使局部差异被抹平。

#### 5. 后续改进

- 用 KMeans 选择中心点，而不是随机选择。
- 系统对比不同 `n_centers` 和 `spread`。
- 增加交叉验证选择参数。
- 与普通 MLP 做误差和曲线对比。

### 2.3 GRNN 广义回归神经网络

#### 1. 模型原理

GRNN 可以看成一种基于样本相似度加权的回归模型。预测新样本时，模型会计算它与所有训练样本的距离，用高斯函数把距离转成权重，再对训练样本的目标值加权平均。

它不需要像 BP 那样长时间迭代训练，核心参数是平滑因子 `spread`。`spread` 越小，模型越依赖近邻样本；`spread` 越大，预测越平滑。

#### 2. 核心代码

```python
class GRNNRegressor:
    def __init__(self, spread=1.0):
        self.spread = spread

    def fit(self, x, y):
        self.x_train_ = np.asarray(x)
        self.y_train_ = np.asarray(y).reshape(-1, 1)
        return self

    def predict(self, x):
        diff = x[:, None, :] - self.x_train_[None, :, :]
        dist_sq = np.sum(diff**2, axis=2)
        weights = np.exp(-dist_sq / (2 * self.spread**2))
        weight_sum = np.sum(weights, axis=1, keepdims=True)
        weight_sum = np.where(weight_sum == 0, 1e-12, weight_sum)
        return weights @ self.y_train_ / weight_sum
```

#### 3. 我的实验过程

实验中使用训练集样本作为 GRNN 的记忆样本。预测时不再训练参数，而是根据测试样本与训练样本的距离计算权重，并得到加权平均预测值。

#### 4. 实验总结

GRNN 的优点是实现简单、训练很快，适合小样本回归。这个实验让我理解了核回归的基本思想：预测结果来自相似样本的加权组合。

它的不足也很明显：预测时要和所有训练样本计算距离，数据量大时速度会下降；同时 `spread` 对效果影响很大，需要调参。

#### 5. 后续改进

- 对 `spread` 做网格搜索。
- 和 RBF、BP 在同一数据上比较误差。
- 观察样本量变大时预测速度变化。

### 2.4 ELM 极限学习机

#### 1. 模型原理

ELM 的核心思想是：隐藏层权重随机生成并固定，只求解输出层权重。它把神经网络训练转化为“随机特征映射 + 线性输出层求解”。

普通 BP 需要反向传播迭代更新所有参数，而 ELM 只需要一次前向计算隐藏层输出，再通过最小二乘或岭回归求输出层权重，因此训练速度通常很快。

#### 2. 核心代码

```python
class ELMRegressor:
    def __init__(self, n_hidden=50, activation="tanh", alpha=1e-3, random_state=None):
        self.n_hidden = n_hidden
        self.activation = activation
        self.alpha = alpha
        self.random_state = random_state

    def _activate(self, Z):
        if self.activation == "sigmoid":
            return 1 / (1 + np.exp(-Z))
        if self.activation == "tanh":
            return np.tanh(Z)
        if self.activation == "relu":
            return np.maximum(0, Z)
        raise ValueError("unsupported activation")
```

```python
class ELMClassifier:
    def fit(self, X, y):
        Y = self._one_hot(y)
        H = self._activate(X @ self.W_ + self.b_)
        I = np.eye(H.shape[1])
        self.beta_ = np.linalg.solve(H.T @ H + self.alpha * I, H.T @ Y)
        return self
```

#### 3. 我的实验过程

实验中分别实现了 ELMRegressor 和 ELMClassifier，并与 BPRegressor 做对比。ELM 用随机隐藏层构造特征，输出层通过解析方式求解；BP 则使用 PyTorch 网络，通过损失函数和优化器迭代训练。

#### 4. 实验总结

ELM 的最大特点是训练快，代码结构也比较清晰。它让我理解到：神经网络不一定都要通过完整反向传播训练，有时固定随机特征后，只训练输出层也能得到不错效果。

ELM 的不足是隐藏层随机性较强，效果依赖隐藏节点数量、激活函数和随机种子。它的表示能力不如可端到端训练的深层网络灵活。

#### 5. 后续改进

- 多次随机初始化，观察结果稳定性。
- 系统比较 `n_hidden`、`activation`、`alpha`。
- 对比 ELM 和 BP 的训练耗时、误差和泛化能力。

## 3. 支持向量机系列

### 3.1 SVM 葡萄酒分类

#### 1. 模型原理

SVM 用于分类时，核心目标是找到一个分类边界，使不同类别之间的间隔尽可能大。离分类边界最近、真正决定边界位置的样本称为支持向量。

当数据线性不可分时，可以使用核函数把数据映射到更高维空间。常用核函数包括线性核、多项式核和 RBF 核。SVM 对特征尺度敏感，因此训练前通常需要标准化或归一化。

#### 2. 核心代码

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.4, random_state=24
)

scaler = MinMaxScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)
```

```python
model = SVC(kernel="linear", C=1)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

acc = accuracy_score(y_test, y_pred)
print(confusion_matrix(y_test, y_pred))
print(model.support_vectors_.shape)
print(model.n_support_)
```

#### 3. 我的实验过程

实验使用 sklearn 的葡萄酒数据集。先做二分类实验，并选取两个特征便于观察分类边界；随后扩展到三分类任务，使用 RBF 核和多分类策略进行分类。

实验中特别注意了标准化不能对全量数据先 `fit`，而是必须只在训练集上 `fit_transform`，再对测试集 `transform`，避免数据泄漏。

#### 4. 实验总结

这个实验让我理解了 SVM 的几个关键点：支持向量决定分类边界，核函数决定模型如何处理非线性，`C` 控制间隔与误分类之间的权衡，标准化对基于距离和间隔的模型非常重要。

不足是最初只用少量特征做可视化时，模型表达的信息有限；如果使用全部特征，效果更完整但不容易画出二维分类边界。

#### 5. 后续改进

- 对不同核函数做系统对比。
- 增加分类报告，观察 precision、recall、F1。
- 对支持向量数量和分类效果之间的关系做分析。

### 3.2 SVM 参数优化：GridSearch、RandomizedSearch、GA、PSO

#### 1. 模型原理

SVM 的表现高度依赖超参数。`C` 决定模型对误分类的惩罚力度，`gamma` 决定 RBF 核的影响范围。参数过小或过大都可能导致欠拟合或过拟合。

本项目中尝试了四类调参方法：

- GridSearch：穷举给定网格。
- RandomizedSearch：随机抽取参数组合。
- GA：用遗传算法搜索参数。
- PSO：用粒子群算法搜索参数。

#### 2. 核心代码

```python
param_grid = {
    "C": [0.01, 0.1, 0.5, 1, 5, 10, 100],
    "gamma": ["scale", "auto"],
    "kernel": ["linear", "rbf", "poly", "sigmoid"],
    "decision_function_shape": ["ovo", "ovr"],
}

grid_search = GridSearchCV(
    estimator=model,
    param_grid=param_grid,
    cv=5,
    n_jobs=-1
)
grid_search.fit(X_train, y_train)
```

```python
def fitness_function(params):
    C = params[0]
    gamma = params[1]
    model = SVC(C=C, gamma=gamma, kernel="rbf")
    scores = cross_val_score(model, X_train, y_train, cv=5)
    return scores.mean()
```

#### 3. 我的实验过程

实验中先用 GridSearchCV 在固定参数表里搜索，再用 RandomizedSearchCV 随机搜索。随后将 `C`、`gamma` 等参数转化为连续搜索空间，用 GA 和 PSO 进行启发式搜索。

为了让调参效果更明显，实验中还使用了 `make_moons` 生成非线性分类数据。

#### 4. 实验总结

GridSearch 简单直接，但搜索空间大时成本高。RandomizedSearch 更灵活，适合先粗略探索。GA 和 PSO 不要求枚举所有组合，可以在连续空间中搜索，但实现和参数设置更复杂。

这个实验让我理解了：调参不是盲目换参数，而是要定义评价函数、搜索空间和验证方式。

#### 5. 后续改进

- 记录不同调参方法的耗时。
- 对比不同搜索方法得到的测试集表现。
- 把搜索过程画成收敛曲线。

### 3.3 SVR 时间序列回归：QQQ 收盘价预测

#### 1. 模型原理

SVR 是 SVM 的回归版本。分类 SVM 关注分类间隔，SVR 关注预测值与真实值之间的偏差，并允许一定范围内的误差不被惩罚。

时间序列回归的关键是把历史数据转成监督学习样本。例如用过去若干天的收盘价预测下一天收盘价。

#### 2. 核心代码

```python
model = SVR(kernel="rbf", C=1, gamma="scale")
model.fit(X_train, y_train)

y_pred = model.predict(X_test)
print(mean_squared_error(y_test, y_pred))
```

```python
param_grid = {
    "C": np.arange(0.1, 100, 5),
    "gamma": np.arange(0.01, 10, 0.5),
}

grid_search = GridSearchCV(
    estimator=model,
    param_grid=param_grid,
    cv=5,
    n_jobs=-1
)
grid_search.fit(X_train, y_train)
```

#### 3. 我的实验过程

实验使用 QQQ 历史行情数据，构造历史窗口作为输入，用未来收盘价作为标签。训练 SVR 后使用 MSE 评价误差，并画出真实价格和预测价格曲线。

实验还使用 GridSearchCV 搜索 `C` 和 `gamma`，再用最优参数重新训练模型。

#### 4. 实验总结

这个实验让我理解了时间序列可以通过滑动窗口转成普通监督学习问题。SVR 能处理非线性回归，但它对特征尺度和参数很敏感。

当前实验的不足是验证方式还可以更严格，时间序列任务不应随意打乱顺序；同时只预测价格本身容易受到趋势影响，评价时需要结合曲线和误差一起看。

#### 5. 后续改进

- 使用更严格的时间顺序验证。
- 增加简单基线模型，比如“明天等于今天”。
- 比较不同窗口长度对预测效果的影响。

### 3.4 SVC 信息粒化分类：QQQ 趋势预测

#### 1. 模型原理

信息粒化的思想是把连续数值问题转成更粗粒度的类别问题。本实验中不直接预测未来价格，而是把未来收益率划分为上涨、横盘、下跌三类。

这样做可以降低任务难度，也更适合观察模型是否能判断趋势方向。标签通过未来收益率的分位数生成，避免主观设定固定阈值。

#### 2. 核心代码

```python
df["return"] = df["close"].pct_change()
df["MA5"] = df["close"].rolling(window=5).mean()
df["MA5_ratio"] = df["close"] / df["MA5"]
df["MA20"] = df["close"].rolling(window=20).mean()
df["MA20_ratio"] = df["close"] / df["MA20"]
df["volume_change"] = df["volume"].pct_change()
```

```python
df["future_return"] = df["close"].shift(-5) / df["close"] - 1
upper = df["future_return"].quantile(0.66)
lower = df["future_return"].quantile(0.33)

def label_price(x):
    if x > upper:
        return 1
    if x < lower:
        return -1
    return 0
```

```python
model = Pipeline([
    ("scaler", StandardScaler()),
    ("svc", SVC(C=1, gamma="scale", kernel="rbf"))
])
model.fit(X_train, y_train)
```

#### 3. 我的实验过程

实验构造了收益率、均线比例、成交量变化率、RSI 等特征。标签使用未来 5 天收益率，并按 33% 和 66% 分位数划分为三类。

划分训练集和测试集时使用 `shuffle=False`，避免把未来数据混入训练集。调参时使用 `TimeSeriesSplit`，并用 PSO 搜索 `C`、`gamma` 和多分类策略。

#### 4. 实验总结

这个实验让我理解了真实数据建模中，特征工程和标签设计非常关键。相比直接预测价格，趋势分类更强调任务定义和数据处理。

不足是目前特征仍然比较基础，评价指标主要看 accuracy 和混淆矩阵，对三分类任务还可以补充每个类别的 precision、recall、F1。

#### 5. 后续改进

- 增加更多技术指标。
- 对不同预测周期进行比较。
- 增加分类报告。
- 用滚动验证方式评估模型稳定性。

## 4. 传统机器学习与可解释模型

### 4.1 决策树乳腺癌分类

#### 1. 模型原理

决策树通过一系列特征判断把样本划分到不同叶子节点。每一次分裂都会选择一个特征和阈值，使划分后的样本更“纯”。常见划分标准包括基尼系数和信息增益。

决策树的优点是可解释性强，能看出模型根据哪些特征做判断；缺点是容易过拟合，特别是树太深时会记住训练集细节。

#### 2. 核心代码

```python
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=24
)

model = DecisionTreeClassifier(random_state=24)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))
```

#### 3. 我的实验过程

实验使用 sklearn 的乳腺癌数据集，先训练基础决策树，再用混淆矩阵和分类报告评估分类效果。

#### 4. 实验总结

这个实验让我理解了决策树的直观性：模型的预测过程可以拆成一条条规则。相比 SVM 这类模型，决策树更容易解释，但也更容易过拟合。

#### 5. 后续改进

- 可视化树结构。
- 分析特征重要性。
- 和随机森林等集成模型做对比。

### 4.2 决策树剪枝与网格搜索

#### 1. 模型原理

剪枝是控制决策树复杂度的重要方法。预剪枝是在树生长过程中提前限制复杂度，例如限制最大深度、最小叶子样本数；后剪枝是在树先充分生长后，再通过代价复杂度参数 `ccp_alpha` 删除不必要分支。

网格搜索用于系统比较不同参数组合，选择交叉验证表现更好的模型。

#### 2. 核心代码

```python
param_grid = {
    "max_depth": [2, 3, 4, 5, None],
    "min_samples_split": [2, 5, 10],
    "min_samples_leaf": [1, 2, 4],
    "criterion": ["gini", "entropy"],
}

grid_search = GridSearchCV(
    DecisionTreeClassifier(random_state=24),
    param_grid=param_grid,
    cv=5,
    scoring="accuracy"
)
grid_search.fit(X_train, y_train)
```

#### 3. 我的实验过程

实验中先尝试手动设置树深度、叶子节点样本数等预剪枝参数，再使用 GridSearchCV 自动搜索。后剪枝部分通过不同 `ccp_alpha` 观察模型复杂度和效果变化。

#### 4. 实验总结

这个实验让我理解了过拟合和模型复杂度控制之间的关系。决策树如果不限制，会倾向于把训练集分得很细；剪枝能牺牲部分训练集拟合能力，换取更好的泛化能力。

#### 5. 后续改进

- 绘制 `ccp_alpha` 与测试准确率关系曲线。
- 对比剪枝前后树深度、叶子数和准确率。
- 分析误分类样本。

## 5. 无监督学习与竞争学习

### 5.1 自组织竞争网络

#### 1. 模型原理

自组织竞争网络通过“胜者为王”的机制学习样本原型。每个神经元有一个权重向量，输入样本到来时，计算样本与所有神经元权重的距离，距离最近的神经元获胜，并向该样本方向更新。

这个过程不直接使用标签训练，标签主要用于训练后评估聚类结果。

#### 2. 核心代码

```python
class CompetitiveNetwork:
    def __init__(self, n_neurons=2, lr=0.01, epochs=100, random_state=None):
        self.n_neurons = n_neurons
        self.lr = lr
        self.epochs = epochs
        self.random_state = random_state

    def fit(self, X):
        rng = np.random.default_rng(self.random_state)
        init_indices = rng.choice(len(X), size=self.n_neurons, replace=False)
        self.weights_ = X[init_indices].copy()

        for epoch in range(self.epochs):
            for idx in rng.permutation(len(X)):
                x = X[idx]
                distances = np.linalg.norm(self.weights_ - x, axis=1)
                winner = np.argmin(distances)
                self.weights_[winner] += self.lr * (x - self.weights_[winner])
        return self
```

#### 3. 我的实验过程

实验使用乳腺癌数据集，先做标准化，再用竞争网络训练聚类原型。训练完成后，将聚类编号映射到真实标签，用 accuracy、confusion matrix 和 classification report 评估聚类效果。

#### 4. 实验总结

这个实验让我理解了无监督学习和监督学习的区别：训练时模型不知道真实类别，只能根据样本分布学习原型。竞争网络的核心在于距离度量和获胜神经元更新。

不足是竞争网络对初始化、学习率和神经元数量敏感，聚类编号本身没有类别含义，需要后处理映射到真实标签。

#### 5. 后续改进

- 对比不同神经元数量。
- 调整学习率和训练轮数。
- 多次随机初始化观察稳定性。

### 5.2 竞争网络与 KMeans / PCA 可视化

#### 1. 模型原理

KMeans 和竞争网络都可以理解为学习若干聚类中心。区别是 KMeans 通常批量迭代更新中心，而竞争网络更像在线学习，每次样本只更新获胜神经元。

PCA 用于把高维数据降到二维，方便观察聚类结果和类别分布。

#### 2. 核心代码

```python
from sklearn.decomposition import PCA
from sklearn.cluster import KMeans

pca = PCA(n_components=2)
X_2d = pca.fit_transform(X_scaled)

kmeans = KMeans(n_clusters=2, random_state=24)
cluster_labels = kmeans.fit_predict(X_scaled)
```

#### 3. 我的实验过程

实验中使用 PCA 将乳腺癌高维特征压缩到二维，再可视化不同聚类标签的分布。KMeans 作为对照方法，用来帮助理解竞争网络学到的聚类结构。

#### 4. 实验总结

PCA 可视化让我更直观地看到高维样本在低维空间中的分布。KMeans 和竞争网络虽然实现不同，但都在寻找能代表样本群体的中心或原型。

#### 5. 后续改进

- 对比 PCA 前后聚类效果。
- 使用不同随机种子重复实验。
- 增加轮廓系数等无监督评价指标。

## 6. 特征选择与变量重要性

### 6.1 MIV 变量筛选

#### 1. 模型原理

MIV 的全称是 Mean Impact Value，核心思想是通过扰动某个输入变量，观察模型输出平均变化，从而判断该变量对预测结果的影响程度。

如果某个变量增加或减少后，模型输出变化很大，说明模型对这个变量比较敏感；如果输出变化很小，说明这个变量可能不重要。

#### 2. 核心代码

```python
class BPRegressor(nn.Module):
    def __init__(self, input_dim, hidden_dim=20):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.Tanh(),
            nn.Linear(hidden_dim, 1)
        )

    def forward(self, x):
        return self.net(x)
```

```python
def compute_miv(model, X_original, x_scaler, y_scaler, feature_names, change_rate=0.1):
    miv_results = []
    for j, name in enumerate(feature_names):
        X_plus = X_original.copy()
        X_minus = X_original.copy()
        X_plus[:, j] *= (1 + change_rate)
        X_minus[:, j] *= (1 - change_rate)

        plus_scaled = x_scaler.transform(X_plus)
        minus_scaled = x_scaler.transform(X_minus)
        with torch.no_grad():
            y_plus = model(torch.tensor(plus_scaled, dtype=torch.float32)).numpy()
            y_minus = model(torch.tensor(minus_scaled, dtype=torch.float32)).numpy()
        miv_results.append((name, np.mean(y_plus - y_minus)))
    return miv_results
```

#### 3. 我的实验过程

实验先训练一个 BP 回归模型，再分别对每个输入特征进行正向和负向扰动。通过扰动前后预测值的平均差异计算 MIV，并根据绝对值大小排序变量重要性。

#### 4. 实验总结

MIV 让我理解了“模型解释”的一种思路：不是只看模型预测结果，而是分析输入变量变化会如何影响输出。它类似特征消融和扰动分析。

不足是 MIV 依赖已经训练好的模型，如果模型本身训练不好，变量重要性也不可靠；同时特征之间如果强相关，单独扰动一个变量可能无法完全反映真实影响。

#### 5. 后续改进

- 多次训练模型，观察 MIV 排名稳定性。
- 与 permutation importance 做对比。
- 用筛选后的特征重新训练模型，验证性能变化。

### 6.2 遗传算法特征选择

#### 1. 模型原理

遗传算法特征选择把“是否选择某个特征”编码成 0/1 染色体。每个个体代表一个特征子集，适应度函数通常由模型交叉验证准确率和特征数量惩罚共同决定。

通过选择、交叉、变异不断产生新特征子集，逐步搜索效果更好、特征更少的组合。

#### 2. 核心代码

```python
def fitness_function(individual, X, y, penalty=0.01):
    selected_indices = np.where(np.asarray(individual) == 1)[0]
    if len(selected_indices) == 0:
        return 0.0

    X_selected = X[:, selected_indices]
    model = Pipeline([
        ("scaler", StandardScaler()),
        ("clf", LogisticRegression(max_iter=5000, random_state=24))
    ])
    scores = cross_val_score(model, X_selected, y, cv=5, scoring="accuracy")
    accuracy = scores.mean()
    feature_ratio = len(selected_indices) / X.shape[1]
    return accuracy - penalty * feature_ratio
```

```python
def initialize_population(pop_size, n_features):
    population = np.random.randint(0, 2, size=(pop_size, n_features))
    for i in range(pop_size):
        if population[i].sum() == 0:
            population[i, np.random.randint(0, n_features)] = 1
    return population
```

#### 3. 我的实验过程

实验使用乳腺癌数据集，以逻辑回归作为评价模型。先训练使用全部特征的 baseline，再用 GA 搜索特征子集。适应度函数不仅考虑交叉验证准确率，还加入特征数量惩罚，避免模型选择过多特征。

#### 4. 实验总结

这个实验让我理解了特征选择可以被转化为组合优化问题。GA 不直接优化模型参数，而是优化“选哪些特征”。特征数量惩罚很重要，否则算法可能倾向于保留大部分特征。

不足是 GA 搜索结果有随机性，且计算成本较高，因为每个个体都需要做交叉验证。

#### 5. 后续改进

- 多次运行 GA，观察选中特征稳定性。
- 对比无惩罚和有惩罚的结果。
- 与 MIV 筛选结果进行比较。

## 7. 时间序列与动态神经网络

### 7.1 Elman 神经网络

#### 1. 模型原理

Elman 网络是一种早期循环神经网络。它通过上下文层保存上一时刻隐藏状态，使模型能够利用历史信息。相比普通前馈网络，Elman 网络更适合处理带时间依赖的数据。

在本实验中，序列数据被构造成固定长度窗口，模型根据过去若干时刻预测未来输出。

#### 2. 核心代码

```python
def make_elman_dataset(data, seq_len=3):
    X, y = [], []
    for i in range(len(data) - seq_len):
        X.append(data[i:i + seq_len])
        y.append(data[i + seq_len])
    return np.array(X), np.array(y)
```

```python
class ElmanRegressor(nn.Module):
    def __init__(self, input_dim=3, hidden_dim=5, output_dim=3):
        super().__init__()
        self.rnn = nn.RNN(
            input_size=input_dim,
            hidden_size=hidden_dim,
            batch_first=True
        )
        self.fc = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        out, hidden = self.rnn(x)
        return self.fc(out[:, -1, :])
```

#### 3. 我的实验过程

实验先把时间序列数据处理成滑动窗口样本，再进行标准化。模型使用 PyTorch 的 RNN 层模拟 Elman 网络结构，最后接全连接层输出预测结果。

#### 4. 实验总结

Elman 实验让我理解了时间序列模型和普通回归模型的差别：普通模型只看当前构造好的特征，而 Elman 网络通过隐藏状态表达历史依赖。

不足是当前实验序列长度和隐藏层规模较小，对复杂时间依赖的表达能力有限。

#### 5. 后续改进

- 对比不同 `seq_len`。
- 增加训练/验证损失曲线。
- 与普通 MLP 滑动窗口模型比较。

### 7.2 小波神经网络

#### 1. 模型原理

小波神经网络把小波函数作为隐藏层激活函数。小波函数具有局部性，适合描述局部变化明显的序列。实验中使用 Morlet 小波函数，对滑动窗口输入进行非线性变换，再通过输出层预测下一时刻。

#### 2. 核心代码

```python
def make_window_dataset(values, window_size=6):
    X, y = [], []
    for i in range(len(values) - window_size):
        X.append(values[i:i + window_size])
        y.append(values[i + window_size])
    return np.array(X), np.array(y)
```

```python
def morlet(x):
    return torch.cos(1.75 * x) * torch.exp(-0.5 * x**2)

class WaveletNNRegressor(nn.Module):
    def __init__(self, input_dim, hidden_dim=10):
        super().__init__()
        self.W = nn.Parameter(torch.randn(input_dim, hidden_dim) * 0.5)
        self.b = nn.Parameter(torch.randn(hidden_dim) * 0.1)
        self.raw_a = nn.Parameter(torch.randn(hidden_dim) * 0.1)
        self.output_layer = nn.Linear(hidden_dim, 1)

    def forward(self, x):
        net = x @ self.W
        a = torch.nn.functional.softplus(self.raw_a) + 1e-6
        z = (net - self.b) / a
        h = morlet(z)
        return self.output_layer(h)
```

#### 3. 我的实验过程

实验以交通流量类时间序列为对象，先用滑动窗口构造输入和标签，再训练小波神经网络预测下一时刻值。随后又实现普通 MLPRegressor 作为对照模型。

#### 4. 实验总结

这个实验让我理解了模型结构可以根据任务特点进行设计。小波神经网络不是简单堆叠全连接层，而是把小波函数引入隐藏层，用局部响应建模序列变化。

不足是小波参数训练可能不稳定，且需要和普通 MLP 在相同划分和指标下系统比较。

#### 5. 后续改进

- 对比不同窗口长度。
- 对比小波网络和 MLP 的 MSE、MAE、R2。
- 观察小波伸缩参数和平移参数的训练结果。

### 7.3 灰色预测与灰色神经网络

#### 1. 模型原理

灰色预测适合小样本、不完全信息的序列预测。GM(1,1) 先对原始序列做一次累加生成，弱化随机波动，再通过微分方程形式拟合趋势。

灰色神经网络和残差 BP 的思想是：先用灰色模型捕捉整体趋势，再用神经网络补充非线性或残差部分。

#### 2. 核心代码

```python
class GM11:
    def fit(self, x0):
        x0 = np.asarray(x0, dtype=float)
        self.x0_ = x0
        x1 = np.cumsum(x0)
        z1 = 0.5 * (x1[1:] + x1[:-1])
        B = np.column_stack([-z1, np.ones(len(z1))])
        Y = x0[1:].reshape(-1, 1)
        params = np.linalg.lstsq(B, Y, rcond=None)[0].ravel()
        self.a_, self.b_ = params[0], params[1]
        return self
```

```python
def make_grey_nn_dataset(series, time_index, window_size=3):
    X_lag, X_time, y = [], [], []
    for i in range(window_size, len(series)):
        X_lag.append(series[i - window_size:i])
        X_time.append(time_index[i])
        y.append(series[i])
    return np.array(X_time).reshape(-1, 1), np.array(X_lag), np.array(y)
```

#### 3. 我的实验过程

实验实现了三种方案：GM(1,1)、书中灰色神经网络思路、GM(1,1) 加 BP 残差修正。先用灰色模型预测趋势，再计算训练残差，并用 BP 对残差序列建模。

#### 4. 实验总结

灰色模型让我理解了另一种时间序列建模方式：不是直接用大量样本训练复杂模型，而是利用累加生成和趋势方程处理小样本序列。残差 BP 的思想也很清晰，即先用简单模型拟合主趋势，再用神经网络修正误差。

不足是灰色模型假设较强，对非单调、强波动序列可能效果有限；残差模型也容易受到训练样本少的限制。

#### 5. 后续改进

- 对比 GM、灰色神经网络、残差 BP 的误差。
- 增加滚动预测实验。
- 分析残差序列是否仍有规律。

### 7.4 NARX / 动态神经网络

#### 1. 模型原理

NARX 模型强调两类历史信息：外部输入的历史值和目标输出的历史值。当前输出不仅由过去的外部变量决定，也由过去的输出状态决定。

本实验用动态延迟特征近似 NARX 思想，把过去若干时刻的 `x` 和 `y` 拼接为输入，再用前馈网络预测当前 `y`。

#### 2. 核心代码

```python
def make_dynamic_dataset(x_series, y_series, delay=3):
    X, y = [], []
    for i in range(delay, len(y_series)):
        x_history = x_series[i - delay:i]
        y_history = y_series[i - delay:i]
        features = np.concatenate([x_history, y_history])
        X.append(features)
        y.append(y_series[i])
    return np.array(X), np.array(y)
```

```python
class DynamicNNRegressor(nn.Module):
    def __init__(self, input_dim, hidden_dim=20):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(input_dim, hidden_dim),
            nn.Tanh(),
            nn.Linear(hidden_dim, hidden_dim),
            nn.Tanh(),
            nn.Linear(hidden_dim, 1)
        )

    def forward(self, x):
        return self.net(x)
```

#### 3. 我的实验过程

实验使用外部输入序列和目标输出序列构造动态样本。后续又设计了对比函数，分别测试只用历史 `x`、只用历史 `y`、同时使用历史 `x` 和 `y` 三种模式。

#### 4. 实验总结

这个实验让我理解了动态系统建模中“历史信息”的重要性。相比只看当前输入，加入历史输出能让模型获得系统自身状态变化的信息。

不足是当前实现是固定窗口特征加前馈网络，还不是完整反馈式网络；同时需要更严格比较不同 delay 和不同输入模式。

#### 5. 后续改进

- 对比 `mode="x"`、`mode="y"`、`mode="xy"` 的效果。
- 调整 delay 长度。
- 增加更完整的误差曲线和预测曲线。

## 8. 智能优化算法

### 8.1 遗传算法 GA

#### 1. 模型原理

遗传算法模拟生物进化过程，通过种群、适应度、选择、交叉和变异进行搜索。它适合处理参数搜索、特征选择等不容易直接求导的问题。

在当前项目中，GA 既用于 SVM 参数优化，也用于特征选择。

#### 2. 核心代码

```python
def ga_optimize(
    fitness_function,
    param_ranges,
    population_size=20,
    generations=20,
    mutation_rate=0.1,
    verbose=True
):
    population = []
    for _ in range(population_size):
        individual = [
            np.random.uniform(r[0], r[1])
            for r in param_ranges
        ]
        population.append(individual)
```

```python
combined = list(zip(population, fitness_scores))
combined = sorted(combined, key=lambda x: x[1], reverse=True)
survivors = [item[0] for item in combined[:population_size // 2]]

child = []
for j in range(len(param_ranges)):
    child.append(parent1[j] if np.random.rand() < 0.5 else parent2[j])
```

#### 3. 我的实验过程

在 SVM 参数优化中，个体表示一组超参数，适应度是交叉验证分数。在特征选择中，个体是 0/1 编码，表示是否选择某个特征。

#### 4. 实验总结

GA 的优点是通用，不要求目标函数可导，也不要求搜索空间很规则。缺点是计算成本较高，结果有随机性，需要设置种群规模、迭代次数和变异率。

#### 5. 后续改进

- 多次运行观察稳定性。
- 调整变异率和种群规模。
- 画出每一代最优适应度曲线。

### 8.2 粒子群优化 PSO

#### 1. 模型原理

PSO 模拟粒子群在搜索空间中移动。每个粒子有当前位置和速度，同时记住自己的历史最优位置和群体历史最优位置。速度更新由惯性项、个体学习项和群体学习项共同决定。

PSO 适合连续参数搜索。本项目中主要用于 SVM / SVC 参数优化。

#### 2. 核心代码

```python
def pso_optimize(
    fitness_function,
    param_ranges,
    particle_num=20,
    iterations=20,
    w=0.7,
    c1=2,
    c2=2,
    verbose=True
):
    positions = []
    velocities = []
    for _ in range(particle_num):
        position = [np.random.uniform(r[0], r[1]) for r in param_ranges]
        velocity = [
            np.random.uniform(-(r[1] - r[0]) * 0.1, (r[1] - r[0]) * 0.1)
            for r in param_ranges
        ]
        positions.append(position)
        velocities.append(velocity)
```

```python
velocities[i] = (
    w * velocities[i]
    + c1 * r1 * (pbest_positions[i] - positions[i])
    + c2 * r2 * (gbest_position - positions[i])
)
positions[i] = positions[i] + velocities[i]
```

#### 3. 我的实验过程

实验中把 `C`、`gamma` 等超参数作为粒子位置，适应度函数返回交叉验证准确率。每次迭代后更新粒子速度和位置，并记录全局最优分数。

#### 4. 实验总结

PSO 比网格搜索更灵活，可以在连续空间中移动搜索。相比 GA，PSO 的更新方向更依赖历史最优位置，收敛过程更像一群粒子逐渐靠近较优区域。

不足是 PSO 也需要调参数，如粒子数量、迭代次数、惯性权重和学习因子。

#### 5. 后续改进

- 对比不同 `w`、`c1`、`c2`。
- 与 GA、GridSearch 的耗时和效果比较。
- 绘制最优分数随迭代变化曲线。

### 8.3 GA / PSO 在当前实验中的使用对比

#### 1. 模型原理

GA 和 PSO 都属于启发式优化算法。它们不直接依赖梯度，而是通过反复试验候选解来寻找更优参数。

GA 更像“种群繁殖”，通过选择、交叉、变异产生新解；PSO 更像“群体移动”，通过个体经验和群体经验更新搜索方向。

#### 2. 核心代码

```python
best_params, best_score, history = ga_optimize(
    fitness_function=fitness_function,
    param_ranges=param_ranges,
    population_size=20,
    generations=20
)
```

```python
best_params, best_score, history = pso_optimize(
    fitness_function=fitness_function,
    param_ranges=param_ranges,
    particle_num=20,
    iterations=30,
    w=0.7,
    c1=2,
    c2=2
)
```

#### 3. 我的实验过程

在 SVM 类实验中，GA 和 PSO 都被用来搜索超参数；在特征选择实验中，GA 被用来搜索特征子集。

#### 4. 实验总结

GA 更适合离散组合问题，例如特征选择；PSO 更适合连续参数搜索，例如 `C` 和 `gamma`。两者都需要合理设计适应度函数，否则搜索结果没有意义。

#### 5. 后续改进

- 在同一任务上统一比较两者。
- 固定随机种子减少偶然性。
- 记录搜索过程和最终测试集表现。

## 9. 自定义网络与训练工程

### 9.1 自定义 MLP

#### 1. 模型原理

自定义 MLP 是理解 PyTorch 模型结构的基础。`__init__` 定义网络层和参数，`forward` 定义数据如何流过这些层。

#### 2. 核心代码

```python
class MLP(nn.Module):
    def __init__(self, input_dim, hidden_dim, output_dim):
        super().__init__()
        self.fc1 = nn.Linear(input_dim, hidden_dim)
        self.act = nn.ReLU()
        self.fc2 = nn.Linear(hidden_dim, output_dim)

    def forward(self, x):
        h = self.fc1(x)
        h = self.act(h)
        out = self.fc2(h)
        return out
```

#### 3. 我的实验过程

实验中先实现最基础的 MLP，明确 PyTorch 自定义模型的两个核心部分：层定义和前向传播。

#### 4. 实验总结

这个实验让我理解了 `nn.Module` 的基本写法。模型不是黑箱，网络结构需要在代码中明确表达。

#### 5. 后续改进

- 增加多层 MLP。
- 加入 Dropout 或 BatchNorm。
- 对比不同激活函数。

### 9.2 双分支网络 TwoBranchNet

#### 1. 模型原理

双分支网络适合处理两组不同来源或不同含义的输入。每个分支先独立提取特征，再把两个分支的表示拼接起来，送入统一输出头。

#### 2. 核心代码

```python
class TwoBranchNet(nn.Module):
    def __init__(self, dim_a, dim_b, hidden_dim, output_dim):
        super().__init__()
        self.branch_a = nn.Sequential(
            nn.Linear(dim_a, hidden_dim),
            nn.ReLU()
        )
        self.branch_b = nn.Sequential(
            nn.Linear(dim_b, hidden_dim),
            nn.ReLU()
        )
        self.head = nn.Sequential(
            nn.Linear(hidden_dim * 2, hidden_dim),
            nn.ReLU(),
            nn.Linear(hidden_dim, output_dim)
        )

    def forward(self, x_a, x_b):
        h_a = self.branch_a(x_a)
        h_b = self.branch_b(x_b)
        h = torch.cat([h_a, h_b], dim=1)
        return self.head(h)
```

#### 3. 我的实验过程

实验中通过 TwoBranchNet 学习如何处理多输入结构。两个输入分别进入不同分支，再融合为最终预测。

#### 4. 实验总结

这个实验让我理解了自定义网络不一定是单一路径。对于多源特征，分支结构能让模型先分别提取信息，再做融合。

#### 5. 后续改进

- 在真实多特征任务中使用双分支结构。
- 对比早期拼接输入和分支后融合的效果。

### 9.3 MorletLayer 与 WaveletNet

#### 1. 模型原理

MorletLayer 把小波函数封装为自定义神经网络层。WaveletNet 则利用这个层构建小波神经网络。它展示了如何把自己定义的数学变换写成 PyTorch 模块。

#### 2. 核心代码

```python
class MorletLayer(nn.Module):
    def __init__(self, input_dim, hidden_dim):
        super().__init__()
        self.W = nn.Parameter(torch.randn(input_dim, hidden_dim))
        self.b = nn.Parameter(torch.zeros(hidden_dim))
        self.raw_a = nn.Parameter(torch.zeros(hidden_dim))

    def forward(self, x):
        a = torch.nn.functional.softplus(self.raw_a) + 1e-6
        z = (x @ self.W - self.b) / a
        return torch.cos(1.75 * z) * torch.exp(-0.5 * z**2)
```

```python
class WaveletNet(nn.Module):
    def __init__(self, input_dim, hidden_dim):
        super().__init__()
        self.wavelet = MorletLayer(input_dim, hidden_dim)
        self.output = nn.Linear(hidden_dim, 1)

    def forward(self, x):
        h = self.wavelet(x)
        return self.output(h)
```

#### 3. 我的实验过程

实验把小波变换写成可训练层，并把它组合进完整网络中。这和前面的小波神经网络实验互相对应。

#### 4. 实验总结

这个实验让我理解了 PyTorch 的灵活性：只要能写出前向计算，并用 `nn.Parameter` 注册可训练参数，就可以构造自定义层。

#### 5. 后续改进

- 对自定义层做单元测试。
- 可视化不同隐藏节点的小波响应。
- 检查参数是否正常更新。

### 9.4 GPU 加速与高效训练流程

#### 1. 模型原理

GPU 加速的核心不是只把模型放到 GPU，而是保证模型、输入数据、标签和计算都在同一个设备上。训练时要使用 `model.train()`，推理时要使用 `model.eval()` 和 `torch.no_grad()` 或 `torch.inference_mode()`。

#### 2. 核心代码

```python
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = model.to(device)

for batch_X, batch_y in train_loader:
    batch_X = batch_X.to(device)
    batch_y = batch_y.to(device)

    y_pred = model(batch_X)
    loss = criterion(y_pred, batch_y)
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
```

```python
model.eval()
X_test_tensor = torch.tensor(X_test, dtype=torch.float32).to(device)
with torch.inference_mode():
    y_pred = model(X_test_tensor)
y_pred_np = y_pred.cpu().numpy()
```

```python
scaler = torch.amp.GradScaler("cuda", enabled=use_amp)
with torch.amp.autocast("cuda", enabled=use_amp):
    y_pred = model(batch_X)
    loss = criterion(y_pred, batch_y)
scaler.scale(loss).backward()
scaler.step(optimizer)
scaler.update()
```

#### 3. 我的实验过程

实验中整理了 GPU 训练模板、推理模板、DataLoader 参数和自动混合精度写法。重点是理解设备管理、训练模式、评估模式和数据加载效率。

#### 4. 实验总结

这个实验让我意识到训练工程不只是模型结构，还包括设备、数据加载、模式切换和推理过程。如果模型和数据不在同一设备上，会直接报错；如果评估时忘记关闭梯度，会浪费显存和计算。

#### 5. 后续改进

- 在真实训练脚本中统一封装 `device`。
- 增加训练日志和 checkpoint。
- 对比 CPU 和 GPU 训练耗时。

## 10. 横向对比总结

### 10.1 分类任务对比

| 模型 | 任务 | 优点 | 不足 |
| --- | --- | --- | --- |
| SVM / SVC | 葡萄酒分类、趋势分类 | 小样本表现好，核函数灵活 | 对特征尺度和参数敏感 |
| 决策树 | 乳腺癌分类 | 可解释性强，训练直观 | 容易过拟合 |
| 自组织竞争网络 | 乳腺癌聚类后映射分类 | 能体现无监督学习思想 | 聚类标签需要后处理 |
| ELMClassifier | 乳腺癌分类 | 训练快，结构简单 | 结果受随机隐藏层影响 |
| Logistic Regression + GA | 特征选择评价 | 简单稳定，适合做 baseline | 表达能力有限 |

### 10.2 回归任务对比

| 模型 | 任务 | 优点 | 不足 |
| --- | --- | --- | --- |
| BP | 非线性函数拟合 | 训练流程清晰，表达能力强 | 需要迭代训练和调参 |
| RBF | 非线性回归 | 局部响应直观 | 中心点和宽度敏感 |
| GRNN | 小样本回归 | 训练简单 | 预测时依赖全部训练样本 |
| SVR | 时间序列回归 | 适合非线性回归 | 参数敏感，解释性一般 |
| ELMRegressor | 回归拟合 | 训练速度快 | 随机性较强 |

### 10.3 时间序列任务对比

| 模型 | 使用历史信息方式 | 主要理解 |
| --- | --- | --- |
| SVR 滑动窗口 | 过去价格窗口 | 时间序列可转监督学习 |
| SVC 粒化分类 | 技术指标和未来收益标签 | 标签设计很关键 |
| Elman | RNN 隐藏状态 | 历史状态可参与预测 |
| 小波神经网络 | 窗口 + 小波激活 | 局部变化可用小波表达 |
| 灰色模型 | 累加生成和趋势方程 | 小样本趋势预测 |
| NARX | 历史外部输入 + 历史输出 | 动态系统要看延迟信息 |

### 10.4 特征选择方法对比

| 方法 | 思想 | 适用场景 |
| --- | --- | --- |
| MIV | 扰动单个变量，看输出变化 | 分析变量重要性 |
| GA 特征选择 | 用 0/1 染色体搜索特征子集 | 特征组合优化 |
| 决策树特征重要性 | 根据树分裂贡献评估特征 | 树模型解释 |

### 10.5 调参方法对比

| 方法 | 优点 | 不足 |
| --- | --- | --- |
| GridSearch | 简单、可复现 | 搜索空间大时成本高 |
| RandomizedSearch | 比网格搜索更灵活 | 结果有随机性 |
| GA | 适合组合优化和复杂空间 | 参数较多，计算成本高 |
| PSO | 适合连续参数搜索 | 需要设置粒子数量和学习因子 |

### 10.6 各模型适用场景

| 场景 | 可优先考虑的模型 |
| --- | --- |
| 小规模分类 | SVM、决策树、ELM |
| 可解释分类 | 决策树 |
| 非线性函数拟合 | BP、RBF、GRNN、ELM |
| 时间序列预测 | SVR、Elman、小波神经网络、NARX、灰色模型 |
| 特征选择 | MIV、GA |
| 参数优化 | GridSearch、RandomizedSearch、GA、PSO |
| 自定义结构实验 | PyTorch MLP、TwoBranchNet、WaveletNet |

## 11. 这段时间的学习大总结

### 11.1 我完成了哪些复现

这段时间完成的内容已经覆盖了机器学习中多个重要方向：

- 基础神经网络：BP、RBF、GRNN、ELM。
- 支持向量机：SVM 分类、SVR 回归、SVC 趋势分类。
- 树模型：决策树分类、剪枝、网格搜索。
- 无监督学习：自组织竞争网络、KMeans 对照、PCA 可视化。
- 特征选择：MIV、GA 特征选择。
- 时间序列预测：Elman、小波神经网络、灰色模型、NARX。
- 智能优化：GA、PSO。
- 训练工程：自定义网络结构、GPU 训练模板、推理模板、DataLoader 和混合精度。

### 11.2 我真正理解了哪些机器学习概念

通过这些实验，我对以下概念有了更具体的理解：

- 监督学习需要明确输入特征和输出标签。
- 分类、回归、聚类和时间序列预测的建模方式不同。
- 数据预处理会直接影响模型效果，尤其是标准化和时间顺序切分。
- 模型效果不能只看一次训练结果，需要结合验证方式和评价指标。
- 调参需要定义搜索空间和评价函数。
- 特征工程和标签设计常常比模型本身更影响实验结果。
- 简单模型也有价值，关键是知道它适合什么、不适合什么。
- 神经网络训练离不开损失函数、反向传播、优化器和训练循环。
- 时间序列任务要特别注意历史窗口、延迟信息和数据泄漏。

### 11.3 我目前还薄弱的地方

当前项目仍有一些需要继续补强的地方：

- 部分 notebook 还偏实验草稿，缺少统一训练函数和评估函数。
- 一些实验还没有系统记录 loss 曲线、对比表和最终指标。
- 对模型数学原理的理解还可以更深入，尤其是 SVM、RBF、灰色模型和动态网络。
- 时间序列实验还可以加入更严格的滚动验证。
- 代码结构还可以进一步模块化，把数据处理、模型、训练和评估拆开。
- 部分 notes 还比较简略，需要继续补充实验过程和结果解释。

### 11.4 后续继续复习和补强建议

后续复习可以按以下顺序进行：

1. 先复习 BP、SVM、决策树，确保分类、回归、训练评估这些基础概念扎实。
2. 再复习 RBF、GRNN、ELM，理解不同神经网络变体的建模思路。
3. 然后集中复习时间序列部分，包括 SVR、Elman、小波神经网络、灰色模型和 NARX。
4. 最后复习 GA、PSO、MIV 和 GA 特征选择，把优化和变量筛选串起来。
5. 每复习一个实验，都补充三个内容：最终指标、关键图像、自己对失败点的解释。

这份笔记可以作为后续回看整个学习过程的总索引。再次复习时，不必一开始就打开所有 notebook，可以先看本笔记中的原理、核心代码和实验总结，再根据需要回到对应目录查看完整实现。

