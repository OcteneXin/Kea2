[![PyPI](https://img.shields.io/pypi/v/kea2-python.svg)](https://pypi.python.org/pypi/kea2-python)
[![PyPI Downloads](https://static.pepy.tech/badge/kea2-python)](https://pepy.tech/projects/kea2-python)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)

<div>
    <img src="https://github.com/user-attachments/assets/1a64635b-a8f2-40f1-8f16-55e47b1d74e7" style="border-radius: 14px; width: 20%; height: 20%;"/> 
</div>

## 关于

Kea2 是一个易用的 Python 库，用于支持、自定义和提升移动应用的自动化 UI 测试。Kea2 的新颖之处在于能够融合由人工编写的脚本与自动化 UI 测试工具，从而提供许多有趣且强大的特性。

Kea2 目前基于 [Fastbot](https://github.com/bytedance/Fastbot_Android) 和 [uiautomator2](https://github.com/openatx/uiautomator2) 构建，目标是 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 应用。

## 主要特性
- **特性 1** (查找稳定性问题)：具备 [Fastbot](https://github.com/bytedance/Fastbot_Android) 完整能力，用于压力测试和发现*稳定性问题*（即*崩溃错误*）；

- **特性 2** (自定义测试场景\事件序列\黑白名单\黑白控件[^1])：运行 Fastbot 时可自定义测试场景（如测试特定功能、执行特定事件序列、进入特定 UI 页面、达到特定状态、黑名单特定活动/UI 控件/UI 区域），并通过 *python* 语言与 [uiautomator2](https://github.com/openatx/uiautomator2) 提供的完整能力和灵活性实现；

- **特性 3** (支持断言机制[^2])：支持运行 Fastbot 时自动断言，基于继承自 [Kea](https://github.com/ecnusse/Kea) 的[基于性质的测试](https://en.wikipedia.org/wiki/Software_testing#Property_testing)理念，用于发现*逻辑错误*（即*非崩溃错误*）。

**Kea2 三大特性的能力对比**
|  | **特性 1** | **特性 2** | **特性 3** |
| --- | --- | --- | ---- |
| **发现崩溃错误** | :+1: | :+1: | :+1: |
| **发现深层状态的崩溃错误** |  | :+1: | :+1: |
| **发现非崩溃功能（逻辑）错误** |  |  | :+1: |


<div align="center">
    <div style="max-width:80%; max-height:80%">
    <img src="docs/intro.png" style="border-radius: 14px; width: 80%; height: 80%;"/> 
    </div>
</div>

## 设计与路线图
Kea2 作为 Python 库发布，当前支持：
- 以 [unittest](https://docs.python.org/3/library/unittest.html) 作为测试框架；
- 以 [uiautomator2](https://github.com/openatx/uiautomator2) 作为 UI 测试驱动；
- 以 [Fastbot](https://github.com/bytedance/Fastbot_Android) 作为后端自动化 UI 测试工具。

未来，Kea2 将扩展以支持：
- [pytest](https://docs.pytest.org/en/stable/)
- [Appium](https://github.com/appium/appium)、[Hypium](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/hypium-python-guidelines)（面向 HarmonyOS/Open Harmony）
- 其他自动化 UI 测试工具（不限于 Fastbot）

## 安装

运行环境：
- 支持 Windows、MacOS 和 Linux
- python 3.8+，Android 4.4+（需安装 Android SDK）
- **VPN 关闭**（特性 2 和 3 需要）

使用 `pip` 安装 Kea2：
```bash
python3 -m pip install kea2-python
```

通过运行以下命令查看 Kea2 选项：
```bash
kea2 -h
```

## 快速测试

Kea2 连接并运行于 Android 设备。建议先进行快速测试以确保 Kea2 与您的设备兼容。

1. 连接真实 Android 设备或 Android 模拟器（仅需连接一个设备），并运行 `adb devices` 确认设备被识别。

2. 运行 `quicktest.py` 测试示例应用 `omninotes`（该应用作为 `omninotes.apk` 存放在 Kea2 仓库中）。脚本 `quicktest.py` 会自动安装并短时间测试该示例应用。

在您首选的工作目录下初始化 Kea2：
```python
kea2 init
```

> 如果是首次运行 Kea2，此步骤必需。

运行快速测试：
```python
python3 quicktest.py
```

若您看到应用 `omninotes` 成功运行并经过测试，说明 Kea2 可用！
否则请帮忙通过[提 Bug](https://github.com/ecnusse/Kea2/issues)并附上错误信息告诉我们，非常感谢！

## 特性 1（运行基础版Fastbot：查找稳定性错误）

使用 Fastbot 的全部能力测试您的应用，进行压力测试及查找*稳定性问题*（即*崩溃错误*）；

```bash
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent native --running-minutes 10 --throttle 200
```

理解以上选项请参见[文档](docs/manual_en.md#launching-kea2)

> 语法用法类似于原生 Fastbot 的[shell 命令](https://github.com/bytedance/Fastbot_Android?tab=readme-ov-file#run-fastbot-with-shell-command)。

更多选项，请使用：
```bash
kea2 run -h
```

## 特性 2（运行增强版Fastbot：自定义测试场景\事件序列\黑白控件）

当使用类似 Fastbot 的自动化 UI 测试工具测试您的应用时，您可能发现某些特定 UI 页面或功能很难触达或覆盖。原因在于 Fastbot 缺乏对您应用的内部知识。幸运的是，这正是脚本测试的优势。在特性 2 中，Kea2 支持编写小脚本，引导 Fastbot 探索我们想去的任意地方。您还可以利用这些小脚本在测试过程中屏蔽特定控件。

在 Kea2 中，一个脚本由两个要素组成：
- **前置条件（Precondition）：** 何时执行脚本。
- **交互场景（Interaction scenario）：** 到达目标位置的交互逻辑（在脚本测试方法内指定）。

### 简单示例

假设 `Privacy` 是自动化 UI 测试时难以触及的 UI 页面。Kea2 可以轻松引导 Fastbot 到达该页面。

```python
    @prob(0.5)
    # 前置条件：当处于页面 `Home`
    @precondition(lambda self: 
        self.d(text="Home").exists
    )
    def test_goToPrivacy(self):
        """
        通过打开“Drawer”，点击“Settings”选项然后点击“Privacy”，引导 Fastbot 到达页面 `Privacy`。
        """
        self.d(description="Drawer").click()
        self.d(text="Settings").click()
        self.d(text="Privacy").click()
```

- 通过装饰器 `@precondition` 指定执行条件 —— 当处于 `Home` 页面时。
  在此案例中，`Home` 页面是进入 `Privacy` 页的入口页面，并且 Fastbot 容易到达 `Home`。脚本会通过判断唯一 Widget `Home` 是否存在，来激活。
- 在脚本测试方法 `test_goToPrivacy` 内，指定交互逻辑（打开 Drawer，点击 Setting，再点击 Privacy）以引导 Fastbot 到达 `Privacy` 页面。
- 通过装饰器 `@prob` 指定在处于 `Home` 页面时执行此指引的概率（这里为 50%），从而允许 Fastbot 继续探索其他页面。

您可在脚本 `quicktest.py` 中找到完整示例， 并通过命令 `kea2 run` 运行该脚本：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py。
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
```

## 特性 3（运行增强版Fastbot：加入自动断言）

Kea2 支持运行 Fastbot 时自动断言机制，用于发现*逻辑错误*（即*非崩溃错误*）。为此，您可在脚本中添加断言。当自动化测试过程中断言失败，即发现了潜在功能性缺陷。

特性 3 的脚本由三部分组成：

- **前置条件（Precondition）：** 何时执行脚本。
- **交互场景（Interaction scenario）：** 交互逻辑（在脚本测试方法内指定）。
- **断言（Assertion）：** 期望的应用行为。

### 示例

在社交媒体应用中，发送消息是常用功能。在发送消息页面，当输入框不为空（即有消息）时，`send` 按钮应始终显示。

<div align="center" >
    <div >
        <img src="docs/socialAppBug.png" style="border-radius: 14px; width:30%; height:40%;"/>
    </div>
    <p>预期行为（上图）与有缺陷行为（下图）。</p>
</div>

针对上述始终成立的性质，我们可以写如下脚本验证功能正确性：当页面存在 `input_box` 控件时，输入任意非空字符串，并断言 `send_button` 始终存在。

```python
    @precondition(
        lambda self: self.d(description="input_box").exists
    )
    def test_input_box(self):
        from hypothesis.strategies import text, ascii_letters
        random_str = text(alphabet=ascii_letters).example()
        self.d(description="input_box").set_text(random_str)
        assert self.d(description="send_button").exist

        # 我们还可以做更多断言，例如：
        #       输入的字符串应出现于发送消息页面上
        assert self.d(text=random_str).exist
```
> 这里使用了 [hypothesis](https://github.com/HypothesisWorks/hypothesis) 生成随机文本。

您可使用特性 2 相关命令行类似方式运行此示例。

## 文档（更多文档）

[更多文档](docs/manual_en.md)，包括：
- Kea2 案例教程（基于微信介绍）
- Kea2 脚本定义方法，支持的脚本装饰器（如 `@precondition`、`@prob`、`@max_tries`）
- Kea2 启动方式、命令行选项
- 查看/理解 Kea2 运行结果（如界面截图、测试覆盖率、脚本执行是否成功）
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

此外，[Zhendong Su](https://people.inf.ethz.ch/suz/), [Yiheng Xiong](https://xyiheng.github.io/), [Xiangchen Shen](https://xiangchenshen.github.io/), [Mengqian Xu](https://mengqianx.github.io/), [Haiying Sun](https://faculty.ecnu.edu.cn/_s43/shy/main.psp), [Jingling Sun](https://jinglingsun.github.io/), [Jue Wang](https://cv.juewang.info/) 等也积极参与并贡献良多！

同时，Kea2 得到了来自字节跳动（Fastbot 团队的赵章、苏玉辉）、OPay（刘铁松）、微信（陆浩川、邓岳棠）、华为、小米等多家业界人士的宝贵见解、建议和反馈。非常感谢！

[^1]: 许多 UI 自动化测试工具提供了“自定义事件序列”能力（如[Fastbot](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%BA%8F%E5%88%97)和[AppCrawler](https://github.com/seveniruby/AppCrawler)），但实际使用中存在限制，如自定义能力有限、不够灵活等。此前不少 Fastbot 用户抱怨其“自定义事件序列”功能有诸多问题，见[#209](https://github.com/bytedance/Fastbot_Android/issues/209), [#225](https://github.com/bytedance/Fastbot_Android/issues/225), [#286](https://github.com/bytedance/Fastbot_Android/issues/286)等。

[^2]: UI 自动化测试过程中支持自动断言是重要能力，但几乎无测试工具具备该功能。我们注意到[AppCrawler](https://ceshiren.com/t/topic/15801/5)开发者曾想实现断言机制，用户反响热烈，早自 2021 年起多次催促，但始终未实现。