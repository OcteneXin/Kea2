[![PyPI](https://img.shields.io/pypi/v/kea2-python.svg)](https://pypi.python.org/pypi/kea2-python)
[![PyPI Downloads](https://static.pepy.tech/badge/kea2-python)](https://pepy.tech/projects/kea2-python)
![Python](https://img.shields.io/badge/python-3.8%2B-blue)

<div>
    <img src="https://github.com/user-attachments/assets/1a64635b-a8f2-40f1-8f16-55e47b1d74e7" style="border-radius: 14px; width: 20%; height: 20%;"/> 
</div>

## 关于

Kea2 是一个易用的 Python 库，用于支持、定制和提升移动应用的自动化 UI 测试。Kea2 的创新点在于能够将脚本（通常由人编写）与自动化 UI 测试工具融合，从而实现许多有趣且强大的功能。

Kea2 目前基于 [Fastbot](https://github.com/bytedance/Fastbot_Android) 和 [uiautomator2](https://github.com/openatx/uiautomator2) 构建，目标应用为 [Android](https://en.wikipedia.org/wiki/Android_(operating_system)) 应用。

## 主要特性
- **特性1**（查找稳定性问题）：具备[Fastbot](https://github.com/bytedance/Fastbot_Android)完整的压力测试功能，能够查找*稳定性问题*（即*崩溃类Bug*）；

- **特性2**（自定义测试场景\事件序列\黑白名单\黑白控件[^1]）：通过 *python* 语言及 [uiautomator2](https://github.com/openatx/uiautomator2) 提供的强大能力和灵活性，自定义 Fastbot 运行时的测试场景（如测试特定应用功能、执行特定事件序列、进入特定 UI 页面、触达特定应用状态、黑名单特定 Activity/UI 控件/UI 区域）；

- **特性3**（支持断言机制[^2]）：在运行 Fastbot 时支持自动断言，基于从 [Kea](https://github.com/ecnusse/Kea) 继承的[基于性质的测试](https://en.wikipedia.org/wiki/Software_testing#Property_testing)思想，以查找*逻辑错误*（即*非崩溃类Bug*）。

**Kea2 三大特性的能力对比**
|  | **特性1** | **特性2** | **特性3** |
| --- | --- | --- | ---- |
| **发现崩溃问题** | :+1: | :+1: | :+1: |
| **发现深层状态下的崩溃问题** |  | :+1: | :+1: |
| **发现非崩溃功能性（逻辑）Bug** |  |  | :+1: |


<div align="center">
    <div style="max-width:80%; max-height:80%">
    <img src="docs/intro.png" style="border-radius: 14px; width: 80%; height: 80%;"/> 
    </div>
</div>



## 设计与路线图
Kea2 作为 Python 库发布，当前配合以下组件工作：
- 采用 [unittest](https://docs.python.org/3/library/unittest.html) 作为测试框架；
- 采用 [uiautomator2](https://github.com/openatx/uiautomator2) 作为 UI 测试驱动器；
- 采用 [Fastbot](https://github.com/bytedance/Fastbot_Android) 作为后端自动化 UI 测试工具。

未来，Kea2 将扩展支持：
- [pytest](https://docs.pytest.org/en/stable/)
- [Appium](https://github.com/appium/appium)、[Hypium](https://developer.huawei.com/consumer/cn/doc/harmonyos-guides/hypium-python-guidelines)（面向 HarmonyOS/Open Harmony）
- 其他自动化 UI 测试工具（不限于 Fastbot）

## 安装

运行环境：
- 支持 Windows、MacOS 和 Linux
- python 3.8+，Android 4.4+（已安装 Android SDK）
- **关闭 VPN**（特性 2 和 3 需要）

通过 `pip` 安装 Kea2：
```bash
python3 -m pip install kea2-python
```

通过运行以下命令查看 Kea2 的选项：
```bash
kea2 -h
```

## 快速测试

Kea2 会连接并运行于 Android 设备上。建议先做快速测试，确保 Kea2 与设备兼容。

1. 连接一台真实 Android 设备或 Android 模拟器（仅需一台），执行 `adb devices` 确认设备已连接。

2. 运行 `quicktest.py` 测试示例应用 `omninotes`（在 Kea2 仓库中以 `omninotes.apk` 发布）。该脚本会自动安装并短时测试该示例应用。

在你首选的工作目录下初始化 Kea2：
```python
kea2 init
```

> 如果是第一次运行 Kea2，该步骤必需。

执行快速测试：
```python
python3 quicktest.py
```

如果成功看到应用 `omninotes` 运行及测试，则说明 Kea2 工作正常！
否则，请帮助我们[提交Bug报告](https://github.com/ecnusse/Kea2/issues)，并附上错误信息。谢谢！

## 特性1（运行基础版Fastbot：查找稳定性错误）

使用 Fastbot 的完整能力对你的应用进行压力测试，查找*稳定性问题*（即*崩溃Bug*）；

```bash
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent native --running-minutes 10 --throttle 200
```

理解上述选项的含义请参考[文档](docs/manual_en.md#launching-kea2)

> 用法与原生 Fastbot 的 [shell 命令](https://github.com/bytedance/Fastbot_Android?tab=readme-ov-file#run-fastbot-with-shell-command) 类似。

查看更多选项：
```bash
kea2 run -h
```

## 特性2（运行增强版Fastbot：自定义测试场景\事件序列\黑白控件）

当使用像 Fastbot 这样的自动化 UI 测试工具测试应用时，你可能会发现某些特定的 UI 页面或功能很难触达或覆盖。原因是 Fastbot 对你的应用缺乏理解。而这正是脚本测试的优势。通过特性 2，Kea2 支持编写小脚本，指导 Fastbot 探索我们希望进入的页面。你也可用这些小脚本在 UI 测试时屏蔽特定控件。

在 Kea2 中，一个脚本由两部分组成：
- **前置条件（Precondition）**：何时执行该脚本；
- **交互场景**：实现交互逻辑（脚本的测试方法内编写）以达成预期目标。

### 简单示例

假定 `Privacy` 是自动化测试中难以触达的页面。Kea2 可轻松指导 Fastbot 达到该页面。

```python
    @prob(0.5)
    # 前置条件：当处于 `Home` 页面时
    @precondition(lambda self: 
        self.d(text="Home").exists
    )
    def test_goToPrivacy(self):
        """
        指导 Fastbot 通过打开 `Drawer`， 
        点击选项 `Setting`，再点击 `Privacy` 来进入 `Privacy` 页面。
        """
        self.d(description="Drawer").click()
        self.d(text="Settings").click()
        self.d(text="Privacy").click()
```

- 利用装饰器 `@precondition` 指定前置条件 —— 当在 `Home` 页时。
  在此示例中，`Home` 页面是 `Privacy` 页的入口页面，且 Fastbot 容易触达。脚本会通过检查是否存在唯一 Widget `Home` 来判断当前是否处于该页面并激活。
- 在测试方法 `test_goToPrivacy` 内指定交互逻辑（即打开 `Drawer`，点击 `Setting`，点击 `Privacy`）指导 Fastbot 进入 `Privacy` 页面。
- 利用装饰器 `@prob` 指定该指导动作执行的概率（本例中为 50%），从而允许 Kea2 同时让 Fastbot 探索其它页面。

完整示例可见脚本 `quicktest.py`，并通过下面命令载入该脚本并配合 Fastbot 运行：

```bash
# 启动 Kea2 并加载单个脚本 quicktest.py。
kea2 run -s "emulator-5554" -p it.feio.android.omninotes.alpha --agent u2 --running-minutes 10 --throttle 200 --driver-name d unittest discover -p quicktest.py
```

## 特性3（运行增强版Fastbot：加入自动断言）

Kea2 在运行 Fastbot 时支持自动断言，以查找*逻辑错误*（即*非崩溃型Bug*）。实现方式是在脚本中加入断言。当自动化 UI 测试过程中断言失败，即发现潜在的功能性 Bug。

特性 3 中，脚本由以下三部分组成：

- **前置条件**：何时执行脚本
- **交互场景**：交互逻辑（脚本测试方法内描述）
- **断言**：期望的应用行为

### 示例

在一个社交媒体应用中，发送消息很常见。在消息发送页面，当输入框非空时，`send` 按钮应始终显示。

<div align="center" >
    <div >
        <img src="docs/socialAppBug.png" style="border-radius: 14px; width:30%; height:40%;"/>
    </div>
    <p>预期行为（上图）与错误行为（下图）。<p/>
</div>

针对上述“始终满足”的性质，我们可以编写如下脚本验证功能正确性：只要消息发送页存在 `input_box` 控件，就随机输入非空字符串，并断言 `send_button` 应一直存在。

```python
    @precondition(
        lambda self: self.d(description="input_box").exists
    )
    def test_input_box(self):
        from hypothesis.strategies import text, ascii_letters
        random_str = text(alphabet=ascii_letters).example()
        self.d(description="input_box").set_text(random_str)
        assert self.d(description="send_button").exist

        # 还可以做更多断言，例如：
        # 输入的字符串应出现在消息发送页面
        assert self.d(text=random_str).exist
```
> 这里使用了 [hypothesis](https://github.com/HypothesisWorks/hypothesis) 生成随机文本。

你可以用与特性 2 类似的命令行运行该示例。

## 文档（更多文档）

[更多文档](docs/manual_en.md)，包括：
- Kea2 案例教程（基于微信介绍）
- Kea2 脚本定义方法，支持的脚本装饰器（如 `@precondition`、`@prob`、`@max_tries`）
- Kea2 启动方式及命令行选项
- 查看/理解 Kea2 运行结果（界面截图、测试覆盖率、脚本执行成败等）
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

另外，[Zhendong Su](https://people.inf.ethz.ch/suz/)、[Yiheng Xiong](https://xyiheng.github.io/)、[Xiangchen Shen](https://xiangchenshen.github.io/)、[Mengqian Xu](https://mengqianx.github.io/)、[Haiying Sun](https://faculty.ecnu.edu.cn/_s43/shy/main.psp)、[Jingling Sun](https://jinglingsun.github.io/)、[Jue Wang](https://cv.juewang.info/) 等也积极参与并贡献良多。

Kea2 还获得了多个来自业内人士的宝贵见解、建议、反馈和经验分享，部分来自字节跳动（[Zhao Zhang](https://github.com/zhangzhao4444)、Fastbot 团队 Yuhui Su）、OPay（Tiesong Liu）、微信（Haochuan Lu、Yuetang Deng）、华为、小米等。特此感谢！

[^1]: 许多 UI 自动化测试工具提供“自定义事件序列”功能（如[Fastbot](https://github.com/bytedance/Fastbot_Android/blob/main/handbook-cn.md#%E8%87%AA%E5%AE%9A%E4%B9%89%E4%BA%8B%E4%BB%B6%E5%BA%8F%E5%88%97) 和 [AppCrawler](https://github.com/seveniruby/AppCrawler)），但实际使用中存在诸多问题，如定制能力有限、使用不灵活等。早前不少 Fastbot 用户抱怨其“自定义事件序列”存在的使用问题，如[#209](https://github.com/bytedance/Fastbot_Android/issues/209)、[#225](https://github.com/bytedance/Fastbot_Android/issues/225)、[#286](https://github.com/bytedance/Fastbot_Android/issues/286)等。

[^2]: 在 UI 自动化测试过程中支持自动断言是十分重要的能力，但几乎无测试工具实现。我们注意到[AppCrawler](https://ceshiren.com/t/topic/15801/5) 开发者曾希望引入断言机制，用户反响热烈，早在 2021 年便开始催更，但始终未实现。