[![PyPI](https://img.shields.io/pypi/v/kea2-python.svg)](https://pypi.python.org/pypi/kea2-python)
[![PyPI Downloads](https://static.pepy.tech/badge/kea2-python)](https://pepy.tech/projects/kea2-python)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)

<div>
    <img src="https://github.com/user-attachments/assets/1a64635b-a8f2-40f1-8f16-55e47b1d74e7" style="border-radius: 14px; width: 20%; height: 20%;"/> 
</div>

## 关于

Kea2 是一个易用的 Python 库，用于支持、自定义和提升移动应用的自动化 UI 测试。Kea2 的新颖之处在于能够融合人类编写的脚本与自动化 UI 测试工具，从而实现许多有趣且强大的功能。

Kea2 目前基于 [Fastbot](https://github.com/bytedance/Fastbot_Android) 和 [uiautomator2](https://github.com/openatx/uiautomator2) 构建，目标对象是 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 应用。

## 重要特性
- **特性 1**(查找稳定性问题)：具备 [Fastbot](https://github.com/bytedance/Fastbot_Android) 的全部功能，用于压力测试和查找*稳定性问题*（即*崩溃类错误*）；

- **特性 2**(自定义测试场景\事件序列\黑白名单\黑白控件[^1])：在运行 Fastbot 时，支持自定义测试场景（例如测试特定应用功能、执行特定事件序列、进入特定 UI 页面、达到特定应用状态、黑名单/白名单特定活动/UI 控件/UI 区域），所有自定义均基于 *python* 语言和 [uiautomator2](https://github.com/openatx/uiautomator2) 提供的全部能力和灵活性；

- **特性 3**(支持断言机制[^2])：支持在运行 Fastbot 时自动断言，基于继承自 [Kea](https://github.com/ecnusse/Kea) 的 [property-based testing](https://en.wikipedia.org/wiki/Software_testing#Property_testing) 思想，用于发现*逻辑错误*（即*非崩溃错误*）

**Kea2 三项特性的能力对比**
|  | **特性 1** | **特性 2** | **特性 3** |
| --- | --- | --- | ---- |
| **发现崩溃** | :+1: | :+1: | :+1: |
| **在深层状态发现崩溃** |  | :+1: | :+1: |
| **发现非崩溃功能性（逻辑）错误** |  |  | :+1: |


<div align="center">
    <div style="max-width:80%; max-height:80%">
    <img src="docs/intro.png" style="border-radius: 14px; width: 80%; height: 80%;"/> 
    </div>
</div>

## 设计与规划
Kea2 作为 Python 库发布，目前支持：
- 以 [unittest](https://docs.python.org/3/library/unittest.html) 作为测试框架；
- 以 [uiautomator2](https://github.com/openatx/uiautomator2) 作为 UI 测试驱动；
- 以 [Fastbot](https://github.com/bytedance/Fastbot_Android) 作为后端自动化 UI 测试工具。

未来 Kea2 计划扩展支持：
- [pytest](https://docs.pytest.org/en/stable/)
- [Appium](https://github.com/appium/appium)、[Hypium](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/hypium-python-guidelines)（适配 HarmonyOS/Open Harmony）
- 其他自动化 UI 测试工具（不局限于 Fastbot）

## 安装

运行环境：
- 支持 Windows、MacOS 和 Linux
- python 3.8+，Android 4.4+（需安装 Android SDK）
- **VPN 关闭**（特性 2 和特性 3 需要）

通过 `pip` 安装 Kea2：
```bash
python3 -m pip install kea2-python
```

通过运行以下命令查看 Kea2 的选项
```bash
kea2 -h
```

## 快速测试

Kea2 连接并运行于 Android 设备上。建议先进行快速测试以确保 Kea2 与您的设备兼容。

1. 连接真实 Android 设备或 Android 模拟器（只需一台设备），并通过执行 `adb devices` 确认设备已连接。

2. 运行 `quicktest.py` 对示例应用 `omninotes`（以 `omninotes.apk` 形式发布于 Kea2 仓库中）进行测试。此脚本会自动安装并短时间运行测试该示例应用。

在您偏好的工作目录下初始化 Kea2：
```python
kea2 init
```

> 如果是首次运行 Kea2，始终需要执行此步骤。

运行快速测试：
```python
python3 quicktest.py
```

如果您能看到应用 `omninotes` 成功运行并测试，则表示 Kea2 工作正常！
否则，请帮忙[提交问题报告](https://github.com/ecnusse/Kea2/issues)并附带错误信息。谢谢！

## 特性 1（运行基础版 Fastbot：查找稳定性错误）

使用 Fastbot 的全部能力对您的应用进行压力测试，查找*稳定性问题*（即*崩溃类错误*）；

```bash
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent native --running-minutes 10 --throttle 200
```

理解上述选项含义请查看[文档](docs/manual_en.md#launching-kea2)

> 该使用方式类似原版 Fastbot 的 [shell 命令](https://github.com/bytedance/Fastbot_Android?tab=readme-ov-file#run-fastbot-with-shell-command)。

查看更多选项：
```bash
kea2 run -h
```

## 特性 2（运行增强版 Fastbot：自定义测试场景\事件序列\黑白控件）

在使用 Fastbot 等自动化 UI 测试工具测试应用时，您可能会发现某些特定 UI 页面或功能难以达到或覆盖。原因是 Fastbot 对您的应用缺乏知识。幸运的是，脚本测试对此有优势。在特性 2 中，Kea2 支持编写小脚本以引导 Fastbot 探索任意目标地。您也可用这类小脚本在测试时屏蔽特定控件。

在 Kea2 中，一个脚本由两个元素组成：
- **前提条件（Precondition）**：何时执行该脚本。
- **交互场景**：在脚本测试方法中指定的交互逻辑，用于达到期望位置。

### 简单示例

假设 `Privacy` 页面是自动化 UI 测试中难以到达的页面，Kea2 能轻松引导 Fastbot 到达该页面。

```python
    @prob(0.5)
    # precondition: when we are at the page `Home`
    @precondition(lambda self: 
        self.d(text="Home").exists
    )
    def test_goToPrivacy(self):
        """
        Guide Fastbot to the page `Privacy` by opening `Drawer`, 
        clicking the option `Setting` and clicking `Privacy`.
        """
        self.d(description="Drawer").click()
        self.d(text="Settings").click()
        self.d(text="Privacy").click()
```

- 通过装饰器 `@precondition` 指定前提条件──当处于 `Home` 页面时激活脚本。此时，`Home` 页面是 `Privacy` 页面的入口页面，且 Fastbot 能轻易到达 `Home` 页面，因此脚本会通过检查是否存在唯一控件 `Home` 来激活；
- 在脚本测试方法 `test_goToPrivacy` 中，指定交互逻辑（打开 `Drawer`、点击 `Setting` 选项、再点击 `Privacy`）以引导 Fastbot 到达 `Privacy` 页面；
- 通过装饰器 `@prob` 指定在处于 `Home` 页面时执行该引导的概率（此处为 50%），因此 Kea2 依然允许 Fastbot 探索其他页面。

您可以在脚本 `quicktest.py` 中查看完整示例，并通过以下命令使用 Fastbot 运行该脚本：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py。
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
```

## 特性 3（运行增强版 Fastbot：加入自动断言）

Kea2 支持在运行 Fastbot 时加入自动断言，用于发现*逻辑错误*（即*非崩溃错误*）。为此，您可以在脚本中添加断言。自动化 UI 测试过程中断言失败即发现可能的功能性错误。

特性 3 中，脚本包含三部分元素：

- **前提条件**：何时执行脚本。
- **交互场景**：脚本测试方法中指定的交互逻辑。
- **断言**：预期的应用行为。

### 示例

在社交媒体应用中，发送消息是常见功能。在消息发送页面，当输入框非空时，发送按钮应始终出现。

<div align="center" >
    <div >
        <img src="docs/socialAppBug.png" style="border-radius: 14px; width:30%; height:40%;"/>
    </div>
    <p>期望行为（上图）与出错行为（下图）。
<p/>
</div>

针对这一总是应持有的性质，我们可编写如下脚本验证功能的正确性：当消息发送页面存在 `input_box` 控件时，向输入框输入任意非空字符串，并断言 `send_button` 应始终存在。

```python
    @precondition(
        lambda self: self.d(description="input_box").exists
    )
    def test_input_box(self):
        from hypothesis.strategies import text, ascii_letters
        random_str = text(alphabet=ascii_letters).example()
        self.d(description="input_box").set_text(random_str)
        assert self.d(description="send_button").exist

        # we can even do more assertions, e.g.,
        #       the input string should exist on the message sending page
        assert self.d(text=random_str).exist
```
> 我们使用 [hypothesis](https://github.com/HypothesisWorks/hypothesis) 来生成随机文本。

您可以通过特性 2 中类似的命令行运行此示例。

## 文档（更多文档）

[更多文档](docs/manual_en.md)，内容包括：
- Kea2 的案例教程（基于微信介绍）；
- Kea2 脚本的定义方法，支持的脚本装饰器（如 `@precondition`、`@prob`、`@max_tries`）；
- Kea2 的启动方式、命令行选项；
- 查看/理解 Kea2 运行结果（如界面截图、测试覆盖率、脚本是否执行成功）；
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

此外，[Zhendong Su](https://people.inf.ethz.ch/suz/)、[Yiheng Xiong](https://xyiheng.github.io/)、[Xiangchen Shen](https://xiangchenshen.github.io/)、[Mengqian Xu](https://mengqianx.github.io/)、[Haiying Sun](https://faculty.ecnu.edu.cn/_s43/shy/main.psp)、[Jingling Sun](https://jinglingsun.github.io/)、[Jue Wang](https://cv.juewang.info/) 等也积极参与并为项目贡献巨大！

Kea2 还收到了字节跳动（Fastbot 团队的赵张（Zhao Zhang）、苏雨晖（Yuhui Su））、OPay（刘铁松）、微信（陆浩川、邓岳棠）、华为、小米等多家业内人士的宝贵见解、建议、反馈和经验分享。感谢所有支持者！

[^1]: 许多 UI 自动化测试工具提供“自定义事件序列”能力（如[Fastbot](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%BA%8F%E5%88%97)和[AppCrawler](https://github.com/seveniruby/AppCrawler)），但实际使用中存在不少问题，如自定义能力有限、使用不灵活等。此前许多 Fastbot 用户曾抱怨其“自定义事件序列”功能的问题，如[#209](https://github.com/bytedance/Fastbot_Android/issues/209), [#225](https://github.com/bytedance/Fastbot_Android/issues/225), [#286](https://github.com/bytedance/Fastbot_Android/issues/286)等。

[^2]: 在 UI 自动化测试中支持自动断言是非常重要的能力，但几乎没有测试工具提供。我们注意到 [AppCrawler](https://ceshiren.com/t/topic/15801/5) 开发者曾希望实现断言机制，并获得用户热切响应，许多用户自 21 年起催更，但至今未实现。