[![PyPI](https://img.shields.io/pypi/v/kea2-python.svg)](https://pypi.python.org/pypi/kea2-python)
[![PyPI Downloads](https://static.pepy.tech/badge/kea2-python)](https://pepy.tech/projects/kea2-python)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)

<div>
    <img src="https://github.com/user-attachments/assets/1a64635b-a8f2-40f1-8f16-55e47b1d74e7" style="border-radius: 14px; width: 20%; height: 20%;"/> 
</div>

## 关于

Kea2 是一个易于使用的 Python 库，用于支持、定制和改进移动应用的自动化 UI 测试。Kea2 的创新之处在于它能够融合由人类编写的脚本与自动化 UI 测试工具，从而实现许多有趣且强大的功能。

Kea2 目前构建在 [Fastbot](https://github.com/bytedance/Fastbot_Android) 和 [uiautomator2](https://github.com/openatx/uiautomator2) 之上，目标是 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 应用。

## 重要功能
- **功能1**(查找稳定性问题)：具备完整的 [Fastbot](https://github.com/bytedance/Fastbot_Android) 能力，用于压力测试和发现*稳定性问题*（即*崩溃错误*）；

- **功能2**(自定义测试场景\事件序列\黑白名单\黑白控件[^1])：在运行 Fastbot 时自定义测试场景（如测试特定应用功能、执行特定事件序列、进入特定 UI 页面、达到特定应用状态、屏蔽特定活动/UI 控件/UI 区域），凭借 *python* 语言和 [uiautomator2](https://github.com/openatx/uiautomator2) 的完整能力和灵活性实现；

- **功能3**(支持断言机制[^2])：基于继承自 [Kea](https://github.com/ecnusse/Kea) 的[基于性质的测试](https://en.wikipedia.org/wiki/Software_testing#Property_testing)理念，在运行 Fastbot 时支持自动断言，用于发现*逻辑错误*（即*非崩溃错误*）。


**Kea2 三大功能的能力一览**
|  | **功能1** | **功能2** | **功能3** |
| --- | --- | --- | ---- |
| **发现崩溃** | :+1: | :+1: | :+1: |
| **深层状态下发现崩溃** |  | :+1: | :+1: |
| **发现非崩溃的功能（逻辑）错误** |  |  | :+1: |


<div align="center">
    <div style="max-width:80%; max-height:80%">
    <img src="docs/intro.png" style="border-radius: 14px; width: 80%; height: 80%;"/> 
    </div>
</div>



## 设计与规划
Kea2 作为一个 Python 库发布，目前配合使用：
- 作为测试框架的 [unittest](https://docs.python.org/3/library/unittest.html)；
- 作为 UI 测试驱动的 [uiautomator2](https://github.com/openatx/uiautomator2)；
- 作为后台自动化 UI 测试工具的 [Fastbot](https://github.com/bytedance/Fastbot_Android)。

未来，Kea2 将扩展支持：
- [pytest](https://docs.pytest.org/en/stable/)
- [Appium](https://github.com/appium/appium)、[Hypium](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/hypium-python-guidelines)（用于 HarmonyOS/Open Harmony）
- 其他自动化 UI 测试工具（不限于 Fastbot）


## 安装

运行环境：
- 支持 Windows、MacOS 和 Linux
- python 3.8+，Android 4.4+（已安装 Android SDK）
- **关闭 VPN**（功能2和功能3所需）

通过 `pip` 安装 Kea2：
```bash
python3 -m pip install kea2-python
```

通过运行获取 Kea2 的选项：
```bash
kea2 -h
```

## 快速测试

Kea2 连接并运行于 Android 设备。我们推荐您进行快速测试，以确保 Kea2 与您的设备兼容。

1. 连接一台真实 Android 设备或一个 Android 模拟器（仅需一个设备），并通过运行 `adb devices` 确认设备已连接。

2. 运行 `quicktest.py` 测试示例应用 `omninotes`（作为 `omninotes.apk` 发布于 Kea2 仓库中）。脚本 `quicktest.py` 会自动安装并短时间测试该示例应用。

在您首选的工作目录下初始化 Kea2：
```python
kea2 init
```

> 如果是首次运行 Kea2，需要执行此步骤。

运行快速测试：
```python
python3 quicktest.py
```

若看到应用 `omninotes` 成功运行并接受测试，则 Kea2 工作正常！  
否则，请帮助我们[提交错误报告](https://github.com/ecnusse/Kea2/issues)，附上错误信息。感谢！


## 功能1(运行基础版 Fastbot：查找稳定性错误)

使用 Fastbot 的完整能力对您的应用进行压力测试并发现*稳定性问题*（即*崩溃错误*）；

```bash
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent native --running-minutes 10 --throttle 200
```

理解上述选项含义请查看[文档](docs/manual_en.md#launching-kea2)

> 使用方法类似于原始 Fastbot 的[shell 命令](https://github.com/bytedance/Fastbot_Android?tab=readme-ov-file#run-fastbot-with-shell-command)。

通过以下命令查看更多选项：
```bash
kea2 run -h
```

## 功能2(运行增强版 Fastbot：自定义测试场景\事件序列\黑白控件)

当使用 Fastbot 等自动化 UI 测试工具测试您的应用时，你可能会发现某些特定 UI 页面或功能难以达到或覆盖。原因是 Fastbot 缺乏对您应用的了解。幸运的是，脚本测试恰恰善于解决此问题。在功能2中，Kea2 支持编写小脚本引导 Fastbot 探索我们想要的任何地方。你还可以使用此类小脚本在 UI 测试过程中屏蔽特定控件。

在 Kea2 中，一个脚本由两个元素组成：
- **前置条件（Precondition）：** 何时执行该脚本。
- **交互场景（Interaction scenario）：** 脚本的测试方法中指定的交互逻辑，用于达到目标页面。

### 简单示例

假设 `Privacy` 是一个在自动化 UI 测试中难以到达的页面。Kea2 可以轻松引导 Fastbot 到达该页面。

```python
    @prob(0.5)
    # precondition: 当我们处于 `Home` 页面时
    @precondition(lambda self: 
        self.d(text="Home").exists
    )
    def test_goToPrivacy(self):
        """
        通过打开 `Drawer`，点击选项 `Settings`，再点击 `Privacy` 
        来引导 Fastbot 到达 `Privacy` 页面。
        """
        self.d(description="Drawer").click()
        self.d(text="Settings").click()
        self.d(text="Privacy").click()
```

- 通过装饰器 `@precondition`，我们指定前置条件——当处于 `Home` 页面时执行。  
此场景中，`Home` 页面是 `Privacy` 页的入口，且 Fastbot 容易达到 `Home` 页面。因此脚本会在检测到唯一控件 `Home` 存在时激活。  
- 在脚本的测试方法 `test_goToPrivacy` 中，指定交互逻辑（打开 `Drawer`，点击 `Settings`，点击 `Privacy`）引导 Fastbot 到达 `Privacy` 页面。  
- 通过装饰器 `@prob`，指定在处于 `Home` 页面时执行该引导的概率（本例为 50%）。因此 Kea2 仍允许 Fastbot 探索其他页面。

你可在脚本 `quicktest.py` 中找到完整示例，并通过命令 `kea2 run` 运行此脚本：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py。
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
```

## 功能3(运行增强版 Fastbot：加入自动断言)

Kea2 支持在运行 Fastbot 时加入自动断言，用于发现*逻辑错误*（即*非崩溃错误*）。实现方式是您可以在脚本中添加断言。自动化 UI 测试过程中断言失败即发现潜在功能错误。

在功能3中，脚本由三个元素组成：

- **前置条件（Precondition）：** 何时执行脚本。
- **交互场景（Interaction scenario）：** 脚本的测试方法中的交互逻辑。
- **断言（Assertion）：** 期望的应用行为。

### 示例

在社交媒体应用中，消息发送是常见功能。在消息发送页面，当输入框非空时，`发送`按钮应该始终存在。

<div align="center" >
    <div >
        <img src="docs/socialAppBug.png" style="border-radius: 14px; width:30%; height:40%;"/>
    </div>
    <p>期望行为（上图）与错误行为（下图）。
<p/>
</div>

针对上述总是保持的性质，我们可以编写如下脚本验证功能正确性：当消息发送页面存在名为 `input_box` 的控件时，向输入框输入任意非空字符串，并断言 `send_button` 始终存在。

```python
    @precondition(
        lambda self: self.d(description="input_box").exists
    )
    def test_input_box(self):
        from hypothesis.strategies import text, ascii_letters
        random_str = text(alphabet=ascii_letters).example()
        self.d(description="input_box").set_text(random_str)
        assert self.d(description="send_button").exist

        # 我们甚至可以做更多断言，例如：
        # 输入的字符串应该在发送消息页面显示
        assert self.d(text=random_str).exist
```
> 这里使用了 [hypothesis](https://github.com/HypothesisWorks/hypothesis) 来生成随机文本。

你可以通过功能2中类似的命令行运行此示例。

## 文档（更多文档）

[更多文档](docs/manual_en.md)，包括：  
- Kea2 的案例教程（基于微信介绍）  
- Kea2 脚本的定义方式，支持的脚本装饰器（如 `@precondition`、`@prob`、`@max_tries`）  
- Kea2 的启动方式、命令行选项  
- 查看/理解 Kea2 的运行结果（如界面截图、测试覆盖率、脚本执行成功与否）  
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

Kea2 由 [ecnusse](https://github.com/ecnusse) 团队积极开发维护：

- [Xixian Liang](https://xixianliang.github.io/resume/) ([@XixianLiang][])
- Bo Ma ([@majuzi123][])
- Chen Peng ([@Drifterpc][])
- [Ting Su](https://tingsu.github.io/) ([@tingsu][])

[@XixianLiang]: https://github.com/XixianLiang  
[@majuzi123]: https://github.com/majuzi123  
[@Drifterpc]: https://github.com/Drifterpc  
[@tingsu]: https://github.com/tingsu

[Zhendong Su](https://people.inf.ethz.ch/suz/), [Yiheng Xiong](https://xyiheng.github.io/), [Xiangchen Shen](https://xiangchenshen.github.io/), [Mengqian Xu](https://mengqianx.github.io/), [Haiying Sun](https://faculty.ecnu.edu.cn/_s43/shy/main.psp), [Jingling Sun](https://jinglingsun.github.io/), [Jue Wang](https://cv.juewang.info/) 也积极参与该项目并做出大量贡献！

Kea2 还获得了来自字节跳动（[Zhao Zhang](https://github.com/zhangzhao4444)、Fastbot 团队的 Yuhui Su）、OPay（Tiesong Liu）、微信（Haochuan Lu、Yuetang Deng）、华为、小米等多家工业界人士的宝贵见解、建议、反馈和经验分享。致敬！

[^1]: 不少 UI 自动化测试工具提供“自定义事件序列”能力（如 [Fastbot](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%BA%8F%E5%88%97) 和 [AppCrawler](https://github.com/seveniruby/AppCrawler)），但实际使用中存在诸多问题，如自定义能力有限、使用不灵活等。此前不少年多 Fastbot 用户曾抱怨其“自定义事件序列”的使用问题，如[#209](https://github.com/bytedance/Fastbot_Android/issues/209), [#225](https://github.com/bytedance/Fastbot_Android/issues/225), [#286](https://github.com/bytedance/Fastbot_Android/issues/286) 等。

[^2]: 在 UI 自动化测试过程中支持自动断言是一个非常重要的能力，但几乎没有测试工具提供该功能。我们注意到 [AppCrawler](https://ceshiren.com/t/topic/15801/5) 开发者曾希望实现断言机制，获得用户广泛响应，许多用户自2021年起催促更新，但始终未实现。