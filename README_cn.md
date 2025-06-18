[![PyPI](https://img.shields.io/pypi/v/kea2-python.svg)](https://pypi.python.org/pypi/kea2-python)
[![PyPI Downloads](https://static.pepy.tech/badge/kea2-python)](https://pepy.tech/projects/kea2-python)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)


<div>
    <img src="https://github.com/user-attachments/assets/aa5839fc-4542-46f6-918b-c9f891356c84" style="border-radius: 14px; width: 20%; height: 20%;"/> 
</div>

[中文文档](README_cn.md)

## 关于

Kea2 是一个易于使用的 Python 库，用于支持、自定义和改进移动应用的自动化 UI 测试。Kea2 的创新点在于能够将脚本（通常由人编写）与自动化 UI 测试工具融合，从而实现许多有趣且强大的功能。

Kea2 目前基于 [Fastbot](https://github.com/bytedance/Fastbot_Android) 和 [uiautomator2](https://github.com/openatx/uiautomator2) 构建，目标平台为 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 应用。

## 主要特性
- **特性 1**（查找稳定性问题）：集成[Fastbot](https://github.com/bytedance/Fastbot_Android) 的全部能力，用于压力测试与发现*稳定性问题*（即*崩溃错误*）；

- **特性 2**（自定义测试场景\事件序列\黑白名单\黑白控件[^1]）：在运行 Fastbot 时自定义测试场景（例如，测试特定应用功能、执行指定事件序列、进入特定 UI 页面、达到特定应用状态、黑名单指定活动/UI 组件/UI 区域），并充分利用 *python* 语言和 [uiautomator2](https://github.com/openatx/uiautomator2) 提供的灵活性；

- **特性 3**（支持断言机制[^2]）：基于继承自 [Kea](https://github.com/ecnusse/Kea) 的[基于性质的测试](https://en.wikipedia.org/wiki/Software_testing#Property_testing)理念，支持在运行 Fastbot 时自动断言，以发现*逻辑错误*（即*非崩溃错误*）。

**Kea2 三大特性的能力对比**
|  | **特性 1** | **特性 2** | **特性 3** |
| --- | --- | --- | ---- |
| **发现崩溃** | :+1: | :+1: | :+1: |
| **在深层状态发现崩溃** |  | :+1: | :+1: |
| **发现非崩溃的功能性（逻辑）错误** |  |  | :+1: |


<div align="center">
    <div style="max-width:80%; max-height:80%">
    <img src="docs/intro.png" style="border-radius: 14px; width: 80%; height: 80%;"/> 
    </div>
</div>



## 设计与路线图
Kea2 作为 Python 库发布，目前依赖：
- [unittest](https://docs.python.org/3/library/unittest.html) 作为测试框架；
- [uiautomator2](https://github.com/openatx/uiautomator2) 作为 UI 测试驱动；
- [Fastbot](https://github.com/bytedance/Fastbot_Android) 作为后台自动化 UI 测试工具。

未来，Kea2 计划支持
- [pytest](https://docs.pytest.org/en/stable/)
- [Appium](https://github.com/appium/appium)、[Hypium](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/hypium-python-guidelines)（面向 HarmonyOS/Open Harmony）
- 以及其他自动化 UI 测试工具（不限于 Fastbot）


## 安装

运行环境：
- 支持 Windows、MacOS 和 Linux
- python 3.8+，Android 5.0+（需安装 Android SDK）
- **关闭 VPN**（运行特性 2 和 3 时需）

通过 `pip` 安装 Kea2：
```bash
python3 -m pip install kea2-python
```

查看 Kea2 的选项：
```bash
kea2 -h
```

## 快速测试

Kea2 连接并运行于 Android 设备。建议进行快速测试以确保 Kea2 兼容您的设备。

1. 连接真实 Android 设备或 Android 模拟器（仅需一个设备），并通过运行 `adb devices` 确认设备已连接。

2. 运行 `quicktest.py` 测试示例应用 `omninotes`（在 Kea2 仓库中已发布为 `omninotes.apk`）。`quicktest.py` 会自动安装并短时测试该示例应用。

在您喜好的工作目录下初始化 Kea2：
```python
kea2 init
```

> 如果是第一次运行 Kea2，则必须执行此步骤。

运行快速测试：
```python
python3 quicktest.py
```

如果看到应用 `omninotes` 成功运行并被测试，则表明 Kea2 运行正常！
否则，请[提交 bug 报告](https://github.com/ecnusse/Kea2/issues)并附上错误信息。感谢支持！



## 特性 1（运行基础版 Fastbot：查找稳定性错误）

利用 Fastbot 全能力对您的应用进行压力测试，发现*稳定性问题*（即*崩溃错误*）；

```bash
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent native --running-minutes 10 --throttle 200
```

理解上述参数含义请参考[文档](docs/manual_en.md#launching-kea2)

> 使用方式与原始 Fastbot 的[shell 命令](https://github.com/bytedance/Fastbot_Android?tab=readme-ov-file#run-fastbot-with-shell-command)类似。

查看更多选项：
```bash
kea2 run -h
```

## 特性 2（运行增强版 Fastbot：自定义测试场景\事件序列\黑白控件）

运行 Fastbot 等任何自动化 UI 测试工具测试应用时，您可能发现某些特定 UI 页面或功能较难覆盖，这主要因为 Fastbot 对您的应用缺乏了解。幸运的是，脚本测试正好可以弥补这一不足。特性 2 中，Kea2 支持编写小脚本来引导 Fastbot 探索到我们想去的地方。您也可以用这些小脚本来阻止某些 UI 组件在测试中被触发。

在 Kea2 中，脚本由两个部分组成：
- **前置条件（Precondition）**：何时执行脚本。
- **交互场景（Interaction scenario）**：脚本测试方法中指定的交互逻辑，达到期望的页面或状态。

### 简单示例

假设 `Privacy` 页面在自动化 UI 测试中难以触达。Kea2 可以轻松引导 Fastbot 到达该页面。

```python
    @prob(0.5)
    # precondition: 当我们处于页面 `Home`
    @precondition(lambda self: 
        self.d(text="Home").exists
    )
    def test_goToPrivacy(self):
        """
        通过打开 `Drawer`，点击 `Settings` 选项，再点击 `Privacy`，
        引导 Fastbot 到达 `Privacy` 页面。
        """
        self.d(description="Drawer").click()
        self.d(text="Settings").click()
        self.d(text="Privacy").click()
```

- 使用装饰器 `@precondition` 指定前置条件 —— 当处于 `Home` 页面时激活脚本。此处，`Home` 页面是进入 `Privacy` 页的入口页，且 Fastbot 容易到达该页面。因此通过判断唯一 UI 组件 `Home` 是否存在，来判断当前页面是否满足前置条件。
- 在脚本测试方法 `test_goToPrivacy` 中，指定交互逻辑（打开 `Drawer`、点击 `Settings` 选项、点击 `Privacy`），引导 Fastbot 到达目标页面。
- 装饰器 `@prob` 指定该引导行为的执行概率（此例中为 50%），确保 Kea2 同时允许 Fastbot 探索其他页面。

完整示例可见脚本 `quicktest.py`，并通过命令 `kea2 run` 将该脚本与 Fastbot 一起运行：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
```

## 特性 3（运行增强版 Fastbot：加入自动断言）

Kea2 支持在运行 Fastbot 时自动断言，以发现*逻辑错误*（即*非崩溃错误*）。为此，您可在脚本中添加断言语句，当自动化 UI 测试期间断言失败时，即发现了潜在的功能性错误。

特性 3 中，脚本包含三个要素：
- **前置条件**：何时执行脚本。
- **交互场景**：脚本测试方法中指定的交互逻辑。
- **断言**：期望的应用行为。

### 示例

在社交媒体应用中，消息发送是常见功能。在消息发送页面，当输入框非空时应始终显示发送按钮。

<div align="center" >
    <div >
        <img src="docs/socialAppBug.png" style="border-radius: 14px; width:30%; height:40%;"/>
    </div>
    <p>期望行为（上图）与异常行为（下图）。
<p/>
</div>

为验证上述始终保持的性质，我们可以编写如下脚本：当消息发送页面存在 `input_box` 组件时，向输入框输入任意非空字符串，并断言 `send_button` 应始终存在。

```python
    @precondition(
        lambda self: self.d(description="input_box").exists
    )
    def test_input_box(self):
        from hypothesis.strategies import text, ascii_letters
        random_str = text(alphabet=ascii_letters).example()
        self.d(description="input_box").set_text(random_str)
        assert self.d(description="send_button").exists

        # 我们甚至可以做更多断言，比如：
        #       输入的字符串应当在消息发送页上显示
        assert self.d(text=random_str).exists
```
>  我们使用 [hypothesis](https://github.com/HypothesisWorks/hypothesis) 生成随机文本。

您可使用与特性 2 类似的命令行运行该示例。

## 文档（更多文档）

[更多文档](docs/manual_en.md)，包括：
- Kea2 案例教程（基于微信介绍）
- Kea2 脚本定义方式，支持的脚本装饰器（如 `@precondition`、`@prob`、`@max_tries`）
- Kea2 启动方式、命令行选项
- 查看与理解 Kea2 运行结果（界面截图、测试覆盖率、脚本执行成功情况）
- [如何黑白控件/区域](docs/blacklisting.md)

## Kea2 使用的开源项目

- [Fastbot](https://github.com/bytedance/Fastbot_Android)
- [uiautomator2](https://github.com/openatx/uiautomator2)
- [hypothesis](https://github.com/HypothesisWorks/hypothesis)

## Kea2 相关论文

> General and Practical Property-based Testing for Android Apps. ASE 2024. [pdf](https://dl.acm.org/doi/10.1145/3691620.3694986)

> An Empirical Study of Functional Bugs in Android Apps. ISSTA 2023. [pdf](https://dl.acm.org/doi/10.1145/3597926.3598138)

> Fastbot2: Reusable Automated Model-based GUI Testing for Android Enhanced by Reinforcement Learning. ASE 2022. [pdf](https://dl.acm.org/doi/10.1145/3551349.3559505)

> Guided, Stochastic Model-Based GUI Testing of Android Apps. ESEC/FSE 2017.  [pdf](https://dl.acm.org/doi/10.1145/3106237.3106298)

### 维护者/贡献者

Kea2 由 [ecnusse](https://github.com/ecnusse) 团队积极开发和维护：

- [Xixian Liang](https://xixianliang.github.io/resume/) ([@XixianLiang][])
- Bo Ma ([@majuzi123][])
- Chen Peng ([@Drifterpc][])
- [Ting Su](https://tingsu.github.io/) ([@tingsu][])

[@XixianLiang]: https://github.com/XixianLiang
[@majuzi123]: https://github.com/majuzi123
[@Drifterpc]: https://github.com/Drifterpc
[@tingsu]: https://github.com/tingsu

此外，[Zhendong Su](https://people.inf.ethz.ch/suz/), [Yiheng Xiong](https://xyiheng.github.io/), [Xiangchen Shen](https://xiangchenshen.github.io/), [Mengqian Xu](https://mengqianx.github.io/), [Haiying Sun](https://faculty.ecnu.edu.cn/_s43/shy/main.psp), [Jingling Sun](https://jinglingsun.github.io/), [Jue Wang](https://cv.juewang.info/) 等也积极参与了该项目并做出重要贡献！

Kea2 同时也得到快手（Bytedance，包含 Fastbot 团队成员赵张、苏雨辉）、OPay（刘铁松）、微信（陆浩川、邓月塘）、华为、小米等工业界人士的宝贵见解、建议、反馈及经验分享。致敬！

[^1]: 许多 UI 自动化测试工具提供“自定义事件序列”能力（如[Fastbot](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%BA%8F%E5%88%97) 和[AppCrawler](https://github.com/seveniruby/AppCrawler)），但实际使用中存在诸多问题，如自定义能力有限、使用不灵活等。许多 Fastbot 用户曾抱怨其“自定义事件序列”的使用问题，见[#209](https://github.com/bytedance/Fastbot_Android/issues/209)、[#225](https://github.com/bytedance/Fastbot_Android/issues/225)、[#286](https://github.com/bytedance/Fastbot_Android/issues/286) 等。

[^2]: UI 自动化测试过程中支持自动断言是重要能力，但几乎无测试工具具备。我们注意到[AppCrawler](https://ceshiren.com/t/topic/15801/5)开发者曾有断言机制愿景，用户反响热烈，早自 2021 年起不断催促，但未曾实现。