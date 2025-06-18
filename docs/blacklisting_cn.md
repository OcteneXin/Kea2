## Blacklisting specific UI widgets/regions (黑白名单/控件/界面特定区域)

我们支持黑名单功能，可以将特定的 UI 控件/区域加入黑名单，Fastbot 在模糊测试时将避开与这些控件的交互。

黑名单支持两个层级：

- 控件屏蔽（Widget Blocking）：仅将传入控件的指定属性（clickable、long-clickable、scrollable、checkable、enabled、focusable）设置为 false。当你只想禁用某个单独控件时使用此方式。

- 树屏蔽（Tree Blocking）：将传入控件视为子树的根节点，将该根节点及其所有子孙节点的上述属性设置为 false。当你想通过只传入某区域的根节点禁用该区域内所有控件时使用此方法。

我们支持(1) `全局黑名单`（始终生效），和(2) `条件黑名单`（仅在满足特定条件时生效）。

黑名单元素列表在 Kea2 的配置文件 `configs/widget.block.py` 中指定（运行 `kea2 init` 时生成）。  
需要屏蔽的元素可灵活使用 u2 selector（如 `text`、`description`）或 `xpath` 等方式指定。

#### 控件屏蔽
##### 全局黑名单
定义函数 `global_block_widgets` 来指定全局黑名单控件。该屏蔽始终生效。

```python
# file: configs/widget.block.py

def global_block_widgets(d: "Device"):
    """
    全局黑名单。
    返回应该被全局屏蔽的控件列表
    """
    return [d(text="widgets to block"), 
            d.xpath(".//node[@text='widget to block']"),
            d(description="widgets to block")]
```
##### 条件黑名单
可以定义任意函数名以 "block_" 开头（不要求“block_tree_”前缀），并用 `@precondition` 装饰器修饰，实现条件黑名单。  
此时，屏蔽仅在前置条件满足时生效。
```python
# file: configs/widget.block.py

# 条件黑名单
@precondition(lambda d: d(text="In the home page").exists)
def block_sth(d: "Device"):
    # 注意：函数名应以 "block_" 开头
    return [d(text="widgets to block"), 
            d.xpath(".//node[@text='widget to block']"),
            d(description="widgets to block")]
```

#### 树屏蔽
##### 全局黑名单
定义函数 `global_block_tree` 来指定全局屏蔽的 UI 控件树。该屏蔽始终生效。

```python
# file: configs/widget.block.py

def global_block_tree(d: "Device"):
    """
    指定测试期间全局屏蔽的 UI 控件树。
    返回根节点列表，根节点及其整个子树将不被探索。
    该函数仅在 'u2 agent' 模式下可用。
    """
    return [d(text="trees to block"), d.xpath(".//node[@text='tree to block']")]
```
##### 条件黑名单
可定义任意函数名以 "block_tree_" 开头，并用 `@precondition` 装饰，实现条件树黑名单。  
仅当前置条件满足时该屏蔽生效。
```python
# file: configs/widget.block.py

# 条件树黑名单示例

@precondition(lambda d: d(text="In the home page").exists)
def block_tree_sth(d: "Device"):
    # 注意：函数名必须以 "block_tree_" 开头
    return [d(text="trees to block"), 
            d.xpath(".//node[@text='tree to block']"),
            d(description="trees to block")]
```

### UI 元素定位支持的方法

配置黑名单时，可通过组合多种属性精确定位当前窗口中的特定 UI 元素。  
属性可灵活组合使用，实现精准屏蔽。

例如，定位 text 为 "Alarm" 且 className 为 `android.widget.Button` 的元素：

```python
d(text="Alarm", className="android.widget.Button")
```

#### 支持的属性列表

常用属性如下，详细用法请查阅官方 Android UiSelector 文档：

- **与文本相关**  
  `text`, `textContains`, `textStartsWith`

- **与类名相关**  
  `className`

- **与描述相关**  
  `description`, `descriptionContains`, `descriptionStartsWith`

- **状态相关**  
  `checkable`, `checked`, `clickable`, `longClickable`, `scrollable`, `enabled`, `focusable`, `focused`, `selected`

- **包名相关**  
  `packageName`

- **资源ID相关**  
  `resourceId`

- **索引相关**  
  `index`

#### 定位子元素和同级元素

除直接定位目标元素外，也可定位子元素或兄弟元素，用于更复杂的查询。

- **定位子元素或孙子元素**  
  例如，在列表控件内定位名为 "Wi-Fi" 的项：

  ```python
  d(className="android.widget.ListView").child(text="Wi-Fi")
  ```

- **定位兄弟元素**  
  例如，定位 text 为 "Settings" 的元素旁边的 `android.widget.ImageView` 兄弟元素：

  ```python
  d(text="Settings").sibling(className="android.widget.ImageView")
  ```

---

### 不支持的方法

> ⚠️ 请避免使用以下方法，这些方法 **不支持** 用于黑名单配置：

- 位置关系查询方法：

  ```python
  d(A).left(B)    # 选中位于 A 左侧的 B
  d(A).right(B)   # 选中位于 A 右侧的 B
  d(A).up(B)      # 选中位于 A 上方的 B
  d(A).down(B)    # 选中位于 A 下方的 B
  ```

- 部分子节点查询方法，例如 `child_by_text`、`child_by_description`、`child_by_instance`。示例：

  ```python
  d(className="android.widget.ListView", resourceId="android:id/list") \
    .child_by_text("Bluetooth", className="android.widget.LinearLayout")
  
  d(className="android.widget.ListView", resourceId="android:id/list") \
    .child_by_text(
      "Bluetooth",
      allow_scroll_search=True,  # 默认 False
      className="android.widget.LinearLayout"
    )
  ```
- 使用 `instance` 参数定位元素。比如避免：

 ```python
 d(className="android.widget.Button", instance=2)
 ```

- 正则表达式匹配参数：  
  `textMatches`, `classNameMatches`, `descriptionMatches`, `packageNameMatches`, `resourceIdMatches`

请避免使用以上不支持的方法，以确保你的黑名单配置能被正确应用。