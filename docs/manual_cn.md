# 文档

[中文文档](manual_cn.md)

## Kea2 教程

1. 在 [WeChat](Scenario_Examples_zh.md) 上应用 Kea2 的功能 2 和 3 的小教程。

## Kea2 脚本

Kea2 使用 [Unittest](https://docs.python.org/3/library/unittest.html) 来管理脚本。所有 Kea2 的脚本都可以在 unittest 规则中找到（即测试方法需以 `test_` 开头，测试类需继承自 `unittest.TestCase`）。

Kea2 使用 [Uiautomator2](https://github.com/openatx/uiautomator2) 操控 android 设备。详情请参考 [Uiautomator2 的文档](https://github.com/openatx/uiautomator2?tab=readme-ov-file#quick-start)。

基本上，你可以按照以下两步编写 Kea2 脚本：

1. 创建一个继承自 `unittest.TestCase` 的测试类。

```python
import unittest

class MyFirstTest(unittest.TestCase):
    ...
```

2. 通过定义测试方法编写你自己的脚本

默认情况下，只有以 `test_` 开头的测试方法会被 unittest 发现。你可以用 `@precondition` 装饰函数。装饰器 `@precondition` 接受一个返回布尔值的函数作为参数。当该函数返回 `True` 时，前置条件满足，脚本将被激活，Kea2 会基于 `@prob` 装饰器定义的概率运行该脚本。

注意，如果测试方法没有被 `@precondition` 装饰，
该测试方法在自动化 UI 测试期间不会被激活，会被视为普通的 `unittest` 测试方法。
因此，当你希望测试方法总是执行时，需要显式加上 `@precondition(lambda self: True)`。如果没有被 `@prob` 装饰，默认概率为 1（即满足前置条件时总执行）。

```python
import unittest
from kea2 import precondition

class MyFirstTest(unittest.TestCase):

    @prob(0.7)
    @precondition(lambda self: ...)
    def test_func1(self):
        ...
```

更多细节可参考 [Kea - 写你的第一个 property](https://kea-docs.readthedocs.io/en/latest/part-keaUserManuel/first_property.html)。

## 装饰器

### `@precondition`

```python
@precondition(lambda self: ...)
def test_func1(self):
    ...
```

装饰器 `@precondition` 接受一个返回布尔值的函数作为参数。当该函数返回 `True`，前置条件满足，函数 `test_func1` 将被激活，且 Kea2 会基于装饰器 `@prob` 定义的概率值运行它。
若未指定 `@prob`，概率默认值为 1，此时满足前置条件时函数 `test_func1` 始终被执行。

### `@prob`

```python
@prob(0.7)
@precondition(lambda self: ...)
def test_func1(self):
    ...
```

装饰器 `@prob` 需要一个浮点数作为参数。该数字表示前置条件满足时执行函数 `test_func1` 的概率。概率值应在 0 到 1 之间。
若未指定 `@prob`，默认概率为 1，此时满足前置条件时函数始终执行。

当多个函数的前置条件都满足时，Kea2 会根据概率值随机选择其中一个执行。
具体来说，Kea2 会随机生成一个 0 到 1 之间的值 `p`，并根据函数的概率值决定选择哪个函数。

举例来说，若三个函数 `test_func1`、`test_func2` 和 `test_func3` 的前置条件均满足，概率值分别为 `0.2`、`0.4` 和 `0.6`：
- 情况 1：若 `p` 随机为 `0.3`，`test_func1` 因概率值 `0.2 < 0.3` 失去被选机会，Kea2 将从 `test_func2` 和 `test_func3` 中随机选择执行一个。
- 情况 2：若 `p` 随机为 `0.1`，Kea2 将从三者中随机选一个执行。
- 情况 3：若 `p` 随机为 `0.7`，Kea2 会忽略这三个函数，均不执行。

### `@max_tries`

```python
@max_tries(1)
@precondition(lambda self: ...)
def test_func1(self):
    ...
```

装饰器 `@max_tries` 接受一个整数参数，表示前置条件满足时函数 `test_func1` 最多执行的次数。默认值为 `inf`（无限次）。

## 启动 Kea2

我们提供两种方式启动 Kea2。

### 1. 通过 shell 命令启动

Kea2 兼容 `unittest` 框架，你可以按 unittest 风格管理测试用例。可通过 `kea run` 命令结合驱动参数和子命令 `unittest`（支持 unittest 选项）启动 Kea2。

命令格式：
```
kea2 run <Kea2 cmds> unittest <unittest cmds> 
```

示例命令：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py

# 启动 Kea2 并加载目录 mytests/omni_notes 中的多个脚本
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -s mytests/omni_notes -p test*.py
```

### `kea2 run` 参数

| 参数 | 说明 | 默认值 | 
| --- | --- | --- |
| -s | 设备序列号，可用 `adb devices` 查询 | |
| -p | 被测应用的包名（如 com.example.app） | 
| -o | 日志及结果输出目录 | `output` |
| --agent | {native, u2}。默认使用 `u2`，支持 Kea2 三大关键特性。如需运行原版 Fastbot，使用 `native`。| `u2` |
| --running-minutes | 运行 Kea2 时长（分钟） | `10` |
| --max-step | 触发的最大 monkey 事件数（仅限 `--agent u2`） | `inf`（无限） |
| --throttle | 两个 monkey 事件之间的延迟（毫秒） | `200` |
| --driver-name | Kea2 脚本中使用的驱动名称。如指定为 `d`，则用 `self.d(..).click()` 操作设备。 |
| --log-stamp | 日志及结果文件的时间戳标记（如 `--log-stamp 123`，日志名为 `fastbot_123.log` 和 `result_123.json`） | 当前时间戳 |
| --profile-period | 采集覆盖率及 UI 截图的周期（以 monkey 事件数计）。UI 截图存储在设备 SD 卡中，请根据设备存储情况设置合适值。 | `25` |
| --take-screenshots | 每个 Monkey 事件截取截屏。截屏文件会被周期性自动拉取到主机（周期由 `--profile-period` 指定）。 |  |
| --device-output-root | 设备端输出根目录。Kea2 会在 `"<device-output-root>/output_*********/"` 临时保存截屏和结果日志，确保该目录可访问。 | `/sdcard` |
| unittest | 指定加载哪些脚本。子命令 `unittest` 完全兼容 unittest。详见 `python3 -m unittest -h` 更多选项。仅在 `--agent u2` 下可用。 |

### `kea` 参数

| 参数 | 说明 | 默认值 | 
| --- | --- | --- |
| -d | 启用调试模式 | |

> ```bash
> # 添加 -d 启用调试模式
> kea2 -d run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
> ```

### 2. 通过 `unittest.main` 启动

和 unittest 一样，我们可以通过 `unittest.main` 方法启动 Kea2。

示例脚本（命名为 `mytest.py`），其中选项直接在脚本内定义。

```python
import unittest

from kea2 import KeaTestRunner, Options
from kea2.u2Driver import U2Driver

class MyTest(unittest.TestCase):
    ...
    # <你的测试方法写在这里>

if __name__ == "__main__":
    KeaTestRunner.setOptions(
        Options(
            driverName="d",
            Driver=U2Driver,
            packageNames=[PACKAGE_NAME],
            # serial="emulator-5554",   # 指定设备序列号
            maxStep=100,
            # running_mins=10,  # 指定最长运行时间，默认 10 分钟
            # throttle=200,   # 指定等待时长，默认 200 毫秒
            # agent='native'  # 若运行原版 Fastbot，使用 'native'
        )
    )
    # 声明 KeaTestRunner
    unittest.main(testRunner=KeaTestRunner)
```

可直接运行脚本启动 Kea2，例如：
```python
python3 mytest.py
```

以下是 `Options` 中所有可用的选项。

```python
# 脚本中的 driver_name（如果是 self.d，则为 d）
driverName: str
# 驱动（目前仅支持 U2Driver）
Driver: U2Driver
# 被测应用包名列表
packageNames: List[str]
# 目标设备序列号
serial: str = None
# 测试 agent，默认 "u2"
agent: "u2" | "native" = "u2"
# 探索的最大步数（阶段2~3可用）
maxStep: int # 默认 "inf"
# 探索时长（分钟）
running_mins: int = 10
# 探索时等待时长（毫秒）
throttle: int = 200
# 日志及结果的输出目录
output_dir: str = "output"
# 日志和结果文件的时间戳，默认当前时间戳
log_stamp: str = None
# 获取覆盖率的采样周期
profile_period: int = 25
# 是否每步截屏
take_screenshots: bool = False
# 设备端输出根路径
device_output_root: str = "/sdcard"
# 是否启用调试模式
debug: bool = False
```

## 查看脚本运行统计

若想查看测试期间脚本是否被执行及执行次数，请在测试结束后打开 `result.json` 文件。

示例：

```json
{
    "test_goToPrivacy": {
        "precond_satisfied": 8,
        "executed": 2,
        "fail": 0,
        "error": 1
    },
    ...
}
```

**如何解读 `result.json`**

字段 | 描述 | 意义
--- | --- | --- |
precond_satisfied | 探索期间测试方法的前置条件被满足的次数 | 是否达到测试状态？
executed | UI 测试期间测试方法被执行的次数 | 测试方法是否被执行过？
fail | 测试方法在 UI 测试期间断言失败的次数 | 失败时，测试方法找到了可能的功能性缺陷。
error | 测试方法在 UI 测试期间因意外错误中止的次数（如测试方法中用到的某个 UI 组件未找到） | 出现错误时，脚本需要更新/修复，因为导致了意外错误。

## 配置文件

执行 `Kea2 init` 后，会在 `configs` 目录生成一些配置文件。
这些配置文件属于 `Fastbot`，具体介绍请见 [配置文件介绍](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E4%B8%93%E5%AE%B6%E7%B3%BB%E7%BB%9F)。

## 应用崩溃缺陷

Kea2 会将触发的崩溃缺陷记录在由 `-o` 指定输出目录中的 `fastbot_*.log` 文件中。你可以在 `fastbot_*.log` 文件中搜索关键词 `FATAL EXCEPTION` 来获取崩溃缺陷的具体信息。

这些崩溃缺陷也会记录在你的设备上。[详细请参见 Fastbot 手册](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E7%BB%93%E6%9E%9C%E8%AF%B4%E6%98%8E)。