# Python 项目日志管理教程

> 适用对象：正在写 Python 脚本、算法题项目、机器学习训练项目、数据分析项目、Web 后端项目的初学者。  
> 目标：用 `logging` 替代杂乱的 `print()`，让项目运行过程可追踪、可定位、可排查。

---

## 1. 日志管理是什么？

日志管理不是简单地把信息打印出来，而是**有层次、有格式、有位置地记录程序运行过程**。

在正式项目中，日志主要回答这些问题：

- 程序什么时候启动？
- 程序运行到了哪一步？
- 使用了哪些关键配置？
- 读取了哪些数据？
- 关键函数是否正常执行？
- 哪里发生了异常？
- 异常发生在哪个文件、哪一行？
- 程序最终是否正常结束？

简单理解：

```python
print()   # 临时调试用
logging   # 正式项目记录运行过程用
```

---

## 2. 为什么不要长期使用 print？

`print()` 的问题主要有：

1. 没有日志级别；
2. 不知道信息来自哪个模块；
3. 不方便保存到文件；
4. 不方便控制输出格式；
5. 不适合长期运行项目；
6. 上线或提交项目时容易遗留调试信息。

例如：

```python
print("开始加载数据")
print("数据加载完成")
print("出错了")
```

这些信息对于小脚本还能勉强使用，但项目变复杂后，很难判断：

- 这是哪个文件输出的？
- 什么时候输出的？
- 是普通信息还是错误信息？
- 是否需要保存下来？

因此，正式项目应该使用 `logging`。

---

## 3. logging 模块基础

Python 内置了 `logging` 模块，不需要额外安装。

```python
import logging
```

最简单用法：

```python
import logging

logging.basicConfig(level=logging.INFO)

logging.info("程序开始")
logging.warning("这是一个警告")
logging.error("这是一个错误")
```

输出类似：

```text
INFO:root:程序开始
WARNING:root:这是一个警告
ERROR:root:这是一个错误
```

这种写法适合很小的脚本，不太适合正式项目。

---

## 4. 日志级别

`logging` 常用 5 个日志级别。

| 级别 | 含义 | 使用场景 |
|---|---|---|
| `DEBUG` | 调试信息 | 开发阶段，记录详细变量和中间过程 |
| `INFO` | 普通信息 | 程序正常运行流程 |
| `WARNING` | 警告 | 程序还能继续运行，但存在风险 |
| `ERROR` | 错误 | 某个操作失败 |
| `CRITICAL` | 严重错误 | 程序可能无法继续运行 |

示例：

```python
logger.debug("当前变量值: %s", value)
logger.info("开始训练模型")
logger.warning("配置缺失，使用默认值")
logger.error("模型保存失败")
logger.critical("数据库连接失败，程序终止")
```

推荐理解：

| 场景 | 推荐级别 |
|---|---|
| 程序启动、结束 | `INFO` |
| 加载配置、加载数据 | `INFO` |
| 每轮训练结果 | `INFO` 或 `DEBUG` |
| 数据为空但程序还能运行 | `WARNING` |
| 文件不存在、请求失败 | `ERROR` |
| 捕获异常并记录堆栈 | `EXCEPTION` |
| 数据库完全无法连接 | `CRITICAL` |

---

## 5. 项目中推荐的 logger 写法

正式项目中，不建议直接写：

```python
logging.info("开始执行")
```

更推荐在每个模块中创建自己的 `logger`：

```python
import logging

logger = logging.getLogger(__name__)
```

然后使用：

```python
logger.info("开始执行")
```

`__name__` 会自动对应当前模块名。

例如文件结构如下：

```text
src/
├── data.py
├── train.py
└── model.py
```

在 `src/data.py` 中：

```python
logger = logging.getLogger(__name__)
```

日志里可能会显示：

```text
src.data
```

这样你就能知道日志来自哪个模块。

---

## 6. 什么时候应该打日志？

日志不是越多越好，而是应该打在**关键节点**。

### 6.1 程序启动和结束

```python
logger.info("程序启动")
logger.info("程序结束")
```

适合放在程序入口，例如 `main()` 函数。

---

### 6.2 配置加载

例如配置文件路径、模型路径、数据路径。

```python
logger.info("加载配置文件: %s", config_path)
logger.info("模型保存目录: %s", model_dir)
```

注意：不要记录密码、token、密钥等敏感信息。

---

### 6.3 数据读取

```python
logger.info("开始读取数据: %s", data_path)
logger.info("数据读取完成，共 %d 条样本", len(data))
```

数据分析、机器学习项目尤其应该记录：

- 数据文件路径；
- 样本数量；
- 特征数量；
- 缺失值处理结果；
- 数据划分比例。

---

### 6.4 关键函数开始和结束

```python
logger.info("开始数据预处理")
logger.info("数据预处理完成")

logger.info("开始训练模型")
logger.info("模型训练完成")
```

这类日志可以帮助你快速判断程序卡在哪一步。

---

### 6.5 关键参数

例如训练参数、模型参数、运行参数。

```python
logger.info(
    "训练参数: epochs=%d, batch_size=%d, lr=%.5f",
    epochs,
    batch_size,
    lr
)
```

推荐使用 `%s`、`%d`、`%.4f` 这种格式化方式，而不是字符串拼接。

不推荐：

```python
logger.info("epochs=" + str(epochs))
```

推荐：

```python
logger.info("epochs=%d", epochs)
```

---

### 6.6 重要中间结果

例如训练过程中的 loss、accuracy、验证集指标。

```python
logger.info(
    "Epoch %d/%d | train_loss=%.4f | val_loss=%.4f | val_acc=%.4f",
    epoch,
    epochs,
    train_loss,
    val_loss,
    val_acc
)
```

---

### 6.7 文件保存

```python
logger.info("模型已保存到: %s", model_path)
logger.info("结果文件已保存到: %s", output_path)
```

这可以避免后续找不到输出文件。

---

### 6.8 异常捕获处

只要写了 `try-except`，通常就应该记录日志。

```python
try:
    data = load_data(data_path)
except FileNotFoundError:
    logger.exception("数据文件不存在: %s", data_path)
    raise
```

`logger.exception()` 会自动记录完整的异常堆栈，适合在 `except` 中使用。

---

### 6.9 外部资源访问

例如：

- 数据库；
- API；
- 文件系统；
- 网络请求；
- 第三方模型；
- 远程服务器。

```python
logger.info("开始请求接口: %s", url)
logger.info("开始连接数据库: %s", db_name)
logger.info("开始加载模型: %s", model_path)
```

不要记录数据库密码、API token、cookie 等敏感内容。

---

## 7. 不建议打日志的位置

### 7.1 不要每一行都打日志

不推荐：

```python
logger.info("进入 if")
logger.info("进入 for")
logger.info("变量 a 存在")
logger.info("变量 b 存在")
```

这种日志没有排查价值，只会制造噪音。

---

### 7.2 不要记录敏感信息

不推荐：

```python
logger.info("password=%s", password)
logger.info("token=%s", token)
```

推荐：

```python
logger.info("数据库配置加载完成")
```

---

### 7.3 不要用日志掩盖错误

不推荐：

```python
try:
    run()
except Exception:
    logger.exception("程序出错")
```

这段代码的问题是：异常被吞掉了，程序可能继续运行，导致更隐蔽的问题。

更推荐：

```python
try:
    run()
except Exception:
    logger.exception("程序出错")
    raise
```

这样既记录错误，又保留异常抛出。

---

## 8. 日志格式

推荐日志至少包含：

- 时间；
- 日志级别；
- 模块名；
- 文件名；
- 行号；
- 日志内容。

推荐格式：

```python
"%(asctime)s | %(levelname)s | %(name)s | %(filename)s:%(lineno)d | %(message)s"
```

输出示例：

```text
2026-06-15 21:30:12 | INFO | src.data | data.py:15 | 开始读取数据: data/train.csv
```

字段含义：

| 字段 | 含义 |
|---|---|
| `asctime` | 日志时间 |
| `levelname` | 日志级别 |
| `name` | logger 名称，通常是模块名 |
| `filename` | 文件名 |
| `lineno` | 日志所在行号 |
| `message` | 日志正文 |

---

## 9. 推荐项目结构

中小型 Python 项目可以采用以下结构：

```text
my_project/
├── configs/
│   └── train.yaml
├── data/
│   └── train.csv
├── logs/
│   └── app.log
├── scripts/
│   └── run_train.py
├── src/
│   ├── __init__.py
│   ├── data.py
│   ├── model.py
│   ├── train.py
│   └── utils/
│       ├── __init__.py
│       └── logger.py
└── README.md
```

推荐职责：

| 文件 | 作用 |
|---|---|
| `src/utils/logger.py` | 封装日志配置 |
| `scripts/run_train.py` | 程序入口 |
| `src/data.py` | 数据读取与预处理 |
| `src/train.py` | 模型训练 |
| `logs/` | 保存日志文件 |

---

## 10. 最小可用日志模板

新建文件：

```text
src/utils/logger.py
```

内容如下：

```python
import logging
import sys
from pathlib import Path


def setup_logger(
    name: str = None,
    log_file: str = "logs/app.log",
    level: int = logging.INFO,
) -> logging.Logger:
    """
    创建项目 logger。

    功能:
    1. 输出日志到控制台
    2. 输出日志到文件
    3. 自动创建 logs 目录
    4. 显示时间、级别、模块、文件和行号
    """

    logger = logging.getLogger(name)
    logger.setLevel(level)
    logger.propagate = False

    # 避免重复添加 handler
    if logger.handlers:
        return logger

    log_path = Path(log_file)
    log_path.parent.mkdir(parents=True, exist_ok=True)

    formatter = logging.Formatter(
        fmt="%(asctime)s | %(levelname)s | %(name)s | %(filename)s:%(lineno)d | %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S"
    )

    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(level)
    console_handler.setFormatter(formatter)

    file_handler = logging.FileHandler(log_file, encoding="utf-8")
    file_handler.setLevel(level)
    file_handler.setFormatter(formatter)

    logger.addHandler(console_handler)
    logger.addHandler(file_handler)

    return logger
```

---

## 11. 在其他模块中使用日志

### 11.1 数据读取模块

文件：

```text
src/data.py
```

示例：

```python
from src.utils.logger import setup_logger

logger = setup_logger(__name__)


def load_data(path: str):
    logger.info("开始读取数据: %s", path)

    try:
        with open(path, "r", encoding="utf-8") as f:
            data = f.readlines()
    except FileNotFoundError:
        logger.exception("数据文件不存在: %s", path)
        raise

    logger.info("数据读取完成，共 %d 行", len(data))
    return data
```

---

### 11.2 训练模块

文件：

```text
src/train.py
```

示例：

```python
from src.utils.logger import setup_logger

logger = setup_logger(__name__)


def train_model(model, train_data, epochs: int):
    logger.info("开始训练模型")
    logger.info("训练样本数: %d", len(train_data))
    logger.info("训练轮数: %d", epochs)

    for epoch in range(1, epochs + 1):
        loss = 0.1234

        logger.info(
            "Epoch %d/%d | loss=%.4f",
            epoch,
            epochs,
            loss
        )

    logger.info("模型训练完成")
```

---

### 11.3 程序入口

文件：

```text
scripts/run_train.py
```

示例：

```python
from src.data import load_data
from src.train import train_model
from src.utils.logger import setup_logger

logger = setup_logger(__name__)


def main():
    logger.info("训练脚本启动")

    data_path = "data/train.txt"

    data = load_data(data_path)

    model = None
    train_model(model, data, epochs=10)

    logger.info("训练脚本结束")


if __name__ == "__main__":
    main()
```

运行后，日志会同时输出到：

```text
控制台
logs/app.log
```

---

## 12. logger.exception() 的使用

`logger.exception()` 只能在 `except` 代码块中使用。

示例：

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    logger.exception("计算失败")
```

它会记录完整 traceback：

```text
ERROR | 计算失败
Traceback (most recent call last):
  File "main.py", line 2, in <module>
    result = 10 / 0
ZeroDivisionError: division by zero
```

这比普通的 `logger.error()` 更适合排查异常。

---

## 13. 日志文件过大怎么办？

如果程序长期运行，不能一直写入同一个日志文件，否则 `app.log` 会越来越大。

可以使用 `RotatingFileHandler`，按文件大小切分日志。

```python
from logging.handlers import RotatingFileHandler
```

示例：

```python
import logging
from logging.handlers import RotatingFileHandler

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

handler = RotatingFileHandler(
    filename="logs/app.log",
    maxBytes=10 * 1024 * 1024,
    backupCount=5,
    encoding="utf-8"
)

formatter = logging.Formatter(
    "%(asctime)s | %(levelname)s | %(name)s | %(message)s"
)

handler.setFormatter(formatter)
logger.addHandler(handler)

logger.info("程序启动")
```

参数含义：

| 参数 | 含义 |
|---|---|
| `maxBytes` | 单个日志文件最大大小 |
| `backupCount` | 最多保留多少个历史日志文件 |

例如：

```python
maxBytes=10 * 1024 * 1024
backupCount=5
```

表示：

- 单个日志文件最大 10MB；
- 最多保留 5 个历史日志文件。

生成文件可能如下：

```text
app.log
app.log.1
app.log.2
app.log.3
```

---

## 14. 按日期切分日志

如果希望每天生成一个日志文件，可以使用 `TimedRotatingFileHandler`。

```python
from logging.handlers import TimedRotatingFileHandler
```

示例：

```python
import logging
from logging.handlers import TimedRotatingFileHandler

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

handler = TimedRotatingFileHandler(
    filename="logs/app.log",
    when="midnight",
    interval=1,
    backupCount=7,
    encoding="utf-8"
)

formatter = logging.Formatter(
    "%(asctime)s | %(levelname)s | %(name)s | %(message)s"
)

handler.setFormatter(formatter)
logger.addHandler(handler)

logger.info("程序启动")
```

参数含义：

| 参数 | 含义 |
|---|---|
| `when="midnight"` | 每天凌晨切分 |
| `interval=1` | 每 1 天切分一次 |
| `backupCount=7` | 保留 7 个历史日志文件 |

---

## 15. 更实用的完整 logger.py 模板

对于中小型项目，可以直接使用下面这个版本。

```python
import logging
import sys
from pathlib import Path
from logging.handlers import RotatingFileHandler


def setup_logger(
    name: str = None,
    log_file: str = "logs/app.log",
    level: int = logging.INFO,
    max_bytes: int = 10 * 1024 * 1024,
    backup_count: int = 5,
) -> logging.Logger:
    """
    项目通用日志配置。

    功能:
    1. 同时输出到控制台和文件
    2. 自动创建 logs 目录
    3. 支持日志文件按大小切分
    4. 避免重复添加 handler
    5. 日志包含时间、级别、模块、文件名和行号
    """

    logger = logging.getLogger(name)
    logger.setLevel(level)
    logger.propagate = False

    if logger.handlers:
        return logger

    log_path = Path(log_file)
    log_path.parent.mkdir(parents=True, exist_ok=True)

    formatter = logging.Formatter(
        fmt="%(asctime)s | %(levelname)s | %(name)s | %(filename)s:%(lineno)d | %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S"
    )

    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setLevel(level)
    console_handler.setFormatter(formatter)

    file_handler = RotatingFileHandler(
        filename=log_file,
        maxBytes=max_bytes,
        backupCount=backup_count,
        encoding="utf-8"
    )
    file_handler.setLevel(level)
    file_handler.setFormatter(formatter)

    logger.addHandler(console_handler)
    logger.addHandler(file_handler)

    return logger
```

使用方式：

```python
from src.utils.logger import setup_logger

logger = setup_logger(__name__)

logger.info("程序启动")
logger.warning("发现异常情况")
logger.error("执行失败")
```

---

## 16. 机器学习项目中的日志设计

机器学习训练项目通常建议记录：

```text
程序启动
配置文件路径
数据路径
训练集大小
验证集大小
测试集大小
模型名称
模型参数
训练轮数
batch_size
learning_rate
每轮训练 loss
每轮验证指标
最佳模型指标
模型保存路径
异常信息
程序结束
```

示例：

```python
logger.info("程序启动")
logger.info("配置文件: %s", config_path)
logger.info("训练数据: %s", train_path)
logger.info("验证数据: %s", val_path)

logger.info("训练集样本数: %d", len(train_dataset))
logger.info("验证集样本数: %d", len(val_dataset))

logger.info(
    "模型参数: model=%s, lr=%.5f, batch_size=%d, epochs=%d",
    model_name,
    lr,
    batch_size,
    epochs
)

best_acc = 0.0

for epoch in range(1, epochs + 1):
    logger.info(
        "Epoch %d/%d | train_loss=%.4f | val_loss=%.4f | val_acc=%.4f",
        epoch,
        epochs,
        train_loss,
        val_loss,
        val_acc
    )

    if val_acc > best_acc:
        best_acc = val_acc
        logger.info("发现更优模型，当前 best_acc=%.4f", best_acc)

logger.info("模型已保存到: %s", model_path)
logger.info("程序结束")
```

---

## 17. 数据分析项目中的日志设计

数据分析项目通常建议记录：

```text
读取了哪个文件
原始数据维度
缺失值数量
重复值数量
清洗后数据维度
使用了哪些变量
输出了哪些图表
结果保存到了哪里
```

示例：

```python
logger.info("读取数据文件: %s", data_path)
logger.info("原始数据维度: rows=%d, cols=%d", df.shape[0], df.shape[1])

missing_count = df.isna().sum().sum()
logger.info("缺失值总数: %d", missing_count)

df = df.drop_duplicates()
logger.info("去重后数据维度: rows=%d, cols=%d", df.shape[0], df.shape[1])

logger.info("分析变量: %s", selected_columns)
logger.info("结果保存到: %s", output_path)
```

---

## 18. Web 项目中的日志设计

Web 项目通常建议记录：

```text
服务启动
请求路径
请求方法
用户 ID
响应状态码
请求耗时
异常堆栈
数据库操作失败
外部接口调用失败
```

示例：

```python
logger.info("服务启动，监听端口: %d", port)
logger.info("收到请求: method=%s path=%s", method, path)
logger.warning("请求参数缺失: user_id")
logger.error("数据库查询失败")
```

注意：不要在日志中记录用户密码、身份证号、完整手机号、token 等敏感数据。

---

## 19. 日志级别推荐策略

### 开发阶段

开发阶段可以使用：

```python
level=logging.DEBUG
```

记录更多细节，方便调试。

### 正常运行阶段

普通项目运行时可以使用：

```python
level=logging.INFO
```

记录主要流程。

### 长期运行或线上项目

长期运行项目可以使用：

```python
level=logging.WARNING
```

减少日志数量，只记录警告和错误。

---

## 20. 常见错误写法

### 20.1 重复添加 handler

如果多次执行：

```python
logger.addHandler(handler)
```

可能导致一条日志重复输出多次。

因此在封装时要写：

```python
if logger.handlers:
    return logger
```

---

### 20.2 到处使用 basicConfig

不推荐在多个模块里反复写：

```python
logging.basicConfig(...)
```

推荐只在程序入口或统一的 `logger.py` 中配置一次。

---

### 20.3 捕获异常但不抛出

不推荐：

```python
try:
    run()
except Exception:
    logger.exception("运行失败")
```

推荐：

```python
try:
    run()
except Exception:
    logger.exception("运行失败")
    raise
```

---

### 20.4 日志内容过于模糊

不推荐：

```python
logger.error("出错了")
```

推荐：

```python
logger.exception("读取配置文件失败: %s", config_path)
```

日志要尽量说明：

- 做什么事失败；
- 涉及哪个文件或参数；
- 是否有异常堆栈。

---

## 21. 日志管理最小实践清单

一个 Python 项目至少应该做到：

- [ ] 不再用 `print()` 做正式运行输出；
- [ ] 每个模块使用 `logger = setup_logger(__name__)`；
- [ ] 程序入口记录启动和结束；
- [ ] 数据读取处记录路径和数据规模；
- [ ] 关键流程开始和结束处记录日志；
- [ ] 关键参数记录到日志；
- [ ] 异常捕获处使用 `logger.exception()`；
- [ ] 日志同时输出到控制台和文件；
- [ ] 日志包含时间、级别、模块、文件名和行号；
- [ ] 不记录密码、token、cookie、身份证号等敏感信息；
- [ ] 长期运行项目使用日志切分。

---

## 22. 推荐使用方式总结

日常写项目时，可以形成固定习惯：

```python
from src.utils.logger import setup_logger

logger = setup_logger(__name__)
```

然后在关键位置使用：

```python
logger.debug("调试细节")
logger.info("正常流程")
logger.warning("可疑情况")
logger.error("普通错误")
logger.exception("异常堆栈")
logger.critical("严重错误")
```

最常用的是：

```python
logger.info()
logger.warning()
logger.exception()
```

对于学习项目和中小型项目来说，掌握这些已经足够。

---

## 23. 最终建议

日志管理的核心不是“多写日志”，而是：

> 在关键位置，用合适的级别，记录对排查问题有价值的信息。

好的日志应该做到：

1. 看得懂；
2. 找得到问题；
3. 能定位文件和行号；
4. 不泄露敏感信息；
5. 不制造无用噪音；
6. 能长期保存和切分。

如果你正在做 Python 学习项目、机器学习训练项目或数据分析项目，建议直接使用本文中的 `setup_logger()` 模板，把它放到：

```text
src/utils/logger.py
```

以后每个模块都用：

```python
logger = setup_logger(__name__)
```

这就是一个比较规范、够用、容易维护的日志管理方案。
