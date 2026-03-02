# 第17章：UI系统

## 17.1 UI系统概述

Unity的UI系统（uGUI）用于创建游戏界面，包括：
- 菜单界面
- HUD（抬头显示）
- 对话框
- 设置界面

## 17.2 Canvas（画布）

### 什么是Canvas？

Canvas是所有UI元素的容器，UI元素必须是Canvas的子对象。

### 创建Canvas

1. 菜单：`GameObject > UI > Canvas`
2. Unity自动创建：
   - Canvas对象
   - EventSystem对象（处理输入事件）

### Canvas组件

- **Render Mode**：渲染模式
  - **Screen Space - Overlay**：屏幕空间覆盖（默认）
  - **Screen Space - Camera**：屏幕空间摄像机
  - **World Space**：世界空间

- **Canvas Scaler**：画布缩放器
  - **Constant Pixel Size**：固定像素大小
  - **Scale With Screen Size**：随屏幕缩放（推荐）
  - **Constant Physical Size**：固定物理大小

### Canvas Scaler设置

**Scale With Screen Size模式**：
- **Reference Resolution**：参考分辨率（如1920x1080）
- **Match**：匹配方式（Width/Height/Both）
- **Match Width Or Height**：匹配宽度或高度（0=宽度，1=高度）

## 17.3 基本UI元素

### Text（文本）

创建：`GameObject > UI > Text`

**主要属性**：
- **Text**：文本内容
- **Font**：字体
- **Font Size**：字体大小
- **Alignment**：对齐方式
- **Color**：颜色
- **Best Fit**：自动调整大小

### Image（图像）

创建：`GameObject > UI > Image`

**主要属性**：
- **Source Image**：源图像（Sprite）
- **Color**：颜色叠加
- **Material**：材质
- **Image Type**：
  - **Simple**：简单
  - **Sliced**：切片（九宫格）
  - **Tiled**：平铺
  - **Filled**：填充

### Button（按钮）

创建：`GameObject > UI > Button`

**结构**：
- Button对象（Button组件）
- Text子对象（显示文字）

**主要属性**：
- **Interactable**：是否可交互
- **Transition**：过渡效果
  - **None**：无
  - **Color Tint**：颜色变化
  - **Sprite Swap**：精灵交换
  - **Animation**：动画

**点击事件**：
- 在Inspector底部添加OnClick事件
- 拖拽对象和方法到事件列表

### Toggle（开关）

创建：`GameObject > UI > Toggle`

**结构**：
- Toggle对象
- Background子对象
- Checkmark子对象
- Label子对象（文本）

**主要属性**：
- **Is On**：是否选中
- **Toggle Transition**：切换过渡

### Slider（滑块）

创建：`GameObject > UI > Slider`

**结构**：
- Slider对象
- Background子对象
- Fill Area子对象
- Handle Slide Area子对象

**主要属性**：
- **Min Value**：最小值
- **Max Value**：最大值
- **Whole Numbers**：整数
- **Value**：当前值

### Input Field（输入框）

创建：`GameObject > UI > Input Field`

**主要属性**：
- **Text Component**：文本组件
- **Placeholder**：占位符
- **Content Type**：内容类型（Standard、Integer Number等）
- **Character Limit**：字符限制

### Dropdown（下拉菜单）

创建：`GameObject > UI > Dropdown`

**主要属性**：
- **Options**：选项列表
- **Value**：当前选中值

## 17.4 RectTransform组件

所有UI元素都有RectTransform组件（继承自Transform）。

### 锚点（Anchors）

锚点定义UI元素相对于父对象的位置关系。

**预设锚点**：
- 点击RectTransform左上角的锚点预设
- 选择9个预设位置之一

**自定义锚点**：
- 拖拽锚点图标自定义位置
- 按住Shift+Alt同时移动锚点和位置

### 轴心点（Pivot）

轴心点影响旋转和缩放的中心。

### 尺寸（Size）

- **Width**：宽度
- **Height**：高度
- 或使用Left、Right、Top、Bottom

## 17.5 UI布局组件

### Horizontal Layout Group（水平布局）

自动水平排列子对象。

**属性**：
- **Spacing**：间距
- **Padding**：内边距
- **Child Alignment**：子对象对齐
- **Child Force Expand**：强制扩展

### Vertical Layout Group（垂直布局）

自动垂直排列子对象。

### Grid Layout Group（网格布局）

网格排列子对象。

**属性**：
- **Cell Size**：单元格大小
- **Spacing**：间距
- **Start Corner**：起始角
- **Start Axis**：起始轴
- **Constraint**：约束（固定行数/列数）

### Content Size Fitter（内容大小适配）

根据内容自动调整大小。

**属性**：
- **Horizontal Fit**：水平适配（Unconstrained/Preferred/MinSize）
- **Vertical Fit**：垂直适配

### Layout Element（布局元素）

覆盖布局组的设置。

**属性**：
- **Min Width/Height**：最小尺寸
- **Preferred Width/Height**：首选尺寸
- **Flexible Width/Height**：灵活尺寸

## 17.6 UI事件系统

### EventSystem

EventSystem处理UI输入事件，创建Canvas时自动创建。

### 事件接口

实现Unity的事件接口处理UI事件：

```csharp
using UnityEngine;
using UnityEngine.EventSystems;

public class ButtonHandler : MonoBehaviour, IPointerClickHandler, IPointerEnterHandler, IPointerExitHandler
{
    public void OnPointerClick(PointerEventData eventData)
    {
        Debug.Log("按钮被点击");
    }

    public void OnPointerEnter(PointerEventData eventData)
    {
        Debug.Log("鼠标进入");
    }

    public void OnPointerExit(PointerEventData eventData)
    {
        Debug.Log("鼠标离开");
    }
}
```

### 常用事件接口

- **IPointerClickHandler**：点击
- **IPointerEnterHandler**：鼠标进入
- **IPointerExitHandler**：鼠标离开
- **IPointerDownHandler**：按下
- **IPointerUpHandler**：释放
- **IDragHandler**：拖拽
- **IScrollHandler**：滚动

## 17.7 UI脚本控制

### 获取UI组件

```csharp
using UnityEngine;
using UnityEngine.UI;

public class UIController : MonoBehaviour
{
    public Text healthText;
    public Slider healthSlider;
    public Button attackButton;
    public InputField nameInput;

    void Start()
    {
        // 获取组件
        healthText = GameObject.Find("HealthText").GetComponent<Text>();
        healthSlider = GetComponentInChildren<Slider>();
        
        // 设置按钮点击事件
        attackButton.onClick.AddListener(OnAttackButtonClicked);
        
        // 设置输入框事件
        nameInput.onEndEdit.AddListener(OnNameEntered);
    }

    void OnAttackButtonClicked()
    {
        Debug.Log("攻击按钮被点击");
    }

    void OnNameEntered(string name)
    {
        Debug.Log("输入的名字：" + name);
    }

    public void UpdateHealth(int currentHealth, int maxHealth)
    {
        healthText.text = $"Health: {currentHealth}/{maxHealth}";
        healthSlider.value = (float)currentHealth / maxHealth;
    }
}
```

### 动态创建UI

```csharp
using UnityEngine;
using UnityEngine.UI;

public class DynamicUI : MonoBehaviour
{
    public GameObject buttonPrefab;
    public Transform buttonParent;

    void Start()
    {
        // 创建按钮
        GameObject newButton = Instantiate(buttonPrefab, buttonParent);
        Button button = newButton.GetComponent<Button>();
        button.onClick.AddListener(() => Debug.Log("动态按钮被点击"));
    }
}
```

## 17.8 UI动画

### Animation组件

为UI元素添加动画：

1. 选中UI对象
2. 添加Animation组件
3. 创建Animation Clip
4. 在Animation窗口中编辑动画

### Animator组件

使用Animator Controller控制UI动画状态机。

### DOTween（推荐）

DOTween是流行的动画插件，非常适合UI动画：

```csharp
using DG.Tweening;

// 淡入
image.DOFade(1f, 0.5f);

// 移动
button.transform.DOMoveX(100f, 1f);

// 缩放
icon.transform.DOScale(1.2f, 0.3f).SetLoops(-1, LoopType.Yoyo);
```

## 17.9 UI最佳实践

### 性能优化

1. **减少Draw Call**：合并UI元素，使用图集
2. **禁用不可见UI**：不使用的UI设置为SetActive(false)
3. **使用对象池**：重复使用的UI元素使用对象池
4. **避免每帧更新**：只在需要时更新UI

### 分辨率适配

1. **使用Canvas Scaler**：设置合适的参考分辨率
2. **使用锚点**：正确设置锚点确保适配
3. **测试多分辨率**：在Game视图中测试不同分辨率

### 组织UI

1. **使用空对象分组**：用空对象组织UI元素
2. **命名规范**：使用清晰的命名
3. **创建预制体**：常用UI创建为预制体

## 17.10 实践项目：游戏菜单系统

### 项目目标

创建一个完整的游戏菜单系统：
- 主菜单
- 设置菜单
- 暂停菜单
- 游戏HUD

### 步骤1：创建主菜单

1. 创建Canvas
2. 添加背景Image
3. 添加标题Text
4. 添加按钮：
   - 开始游戏
   - 设置
   - 退出游戏

### 步骤2：创建菜单管理器

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class MenuManager : MonoBehaviour
{
    public GameObject mainMenuPanel;
    public GameObject settingsPanel;

    public void OnStartGameClicked()
    {
        SceneManager.LoadScene("GameScene");
    }

    public void OnSettingsClicked()
    {
        mainMenuPanel.SetActive(false);
        settingsPanel.SetActive(true);
    }

    public void OnBackClicked()
    {
        settingsPanel.SetActive(false);
        mainMenuPanel.SetActive(true);
    }

    public void OnQuitClicked()
    {
        Application.Quit();
    }
}
```

### 步骤3：创建游戏HUD

1. 创建Canvas（设置为Screen Space - Overlay）
2. 添加：
   - 生命值显示
   - 分数显示
   - 小地图
   - 技能栏

### 步骤4：创建暂停菜单

```csharp
using UnityEngine;

public class PauseMenu : MonoBehaviour
{
    public GameObject pausePanel;
    private bool isPaused = false;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Escape))
        {
            TogglePause();
        }
    }

    void TogglePause()
    {
        isPaused = !isPaused;
        pausePanel.SetActive(isPaused);
        Time.timeScale = isPaused ? 0f : 1f;  // 暂停游戏
    }

    public void OnResumeClicked()
    {
        TogglePause();
    }

    public void OnMainMenuClicked()
    {
        Time.timeScale = 1f;
        SceneManager.LoadScene("MainMenu");
    }
}
```

## 17.11 本章小结

在本章中，我们学习了：

- ✅ Canvas和UI系统基础
- ✅ 基本UI元素（Text、Image、Button等）
- ✅ RectTransform和锚点系统
- ✅ UI布局组件
- ✅ UI事件系统
- ✅ UI脚本控制
- ✅ UI动画
- ✅ UI最佳实践

## 17.12 实践练习

1. **创建菜单**：创建一个主菜单界面
2. **添加按钮**：添加功能按钮并实现点击事件
3. **创建HUD**：创建游戏内的HUD界面
4. **实现设置菜单**：创建设置菜单，包含音量、画质等选项
5. **添加动画**：为UI元素添加动画效果

## 17.13 下一步

下一章我们将学习音频系统，为游戏添加音效和背景音乐。

---

**提示**：UI设计要注重用户体验，保持界面简洁清晰，响应迅速。
