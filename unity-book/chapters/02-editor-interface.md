# 第2章：Unity Editor界面详解

## 2.1 窗口概述

Unity Editor由多个功能窗口组成，每个窗口都有特定的用途。理解这些窗口是掌握Unity开发的关键。

## 2.2 Scene视图（场景视图）

Scene视图是编辑游戏场景的主要工作区域。

### 视图控制

#### 视角导航
- **旋转**：按住鼠标右键拖动（Orbit模式）
- **平移**：按住鼠标中键拖动，或Alt+鼠标左键
- **缩放**：滚动鼠标滚轮，或Alt+鼠标右键拖动
- **聚焦**：选中对象后按F键，或双击Hierarchy中的对象

#### 视图模式
- **Shaded**：带光照的渲染模式（默认）
- **Wireframe**：线框模式
- **Shaded Wireframe**：带线框的着色模式
- **Shadow Cascades**：阴影级联可视化
- **Render Paths**：渲染路径可视化
- **Alpha Channel**：Alpha通道可视化
- **Overdraw**：过度绘制可视化
- **Mipmaps**：Mipmap级别可视化

### Gizmos（辅助图标）

Gizmos是场景中显示的小图标，帮助识别对象类型：
- 摄像机图标
- 光源图标
- 音频源图标
- 碰撞体轮廓

可以通过Scene视图右上角的Gizmos菜单控制显示。

### 2D/3D模式切换

- **2D模式**：点击Scene视图左上角的2D按钮
- **3D模式**：再次点击切换回3D模式

## 2.3 Game视图（游戏视图）

Game视图显示游戏运行时的画面，模拟最终玩家看到的效果。

### 视图设置

- **分辨率**：可以设置不同的屏幕分辨率进行测试
- **宽高比**：测试不同宽高比的显示效果
- **缩放**：调整视图缩放比例
- **最大化**：点击右上角的最大化按钮

### 视图选项

- **Stats**：显示性能统计信息（FPS、Draw Calls等）
- **Gizmos**：显示Gizmos图标

## 2.4 Hierarchy窗口（层级窗口）

Hierarchy窗口显示当前场景中所有游戏对象的层级结构。

### 基本操作

- **创建对象**：右键点击空白处，选择创建选项
- **选择对象**：点击对象名称
- **重命名**：选中后按F2，或双击名称
- **删除对象**：选中后按Delete键
- **复制对象**：Ctrl+D（Windows）或Cmd+D（Mac）
- **搜索**：使用顶部的搜索框

### 层级关系

- 对象可以嵌套在其他对象下，形成父子关系
- 子对象会跟随父对象移动、旋转、缩放
- 拖拽对象可以改变层级关系

### 常用对象类型

- **Empty GameObject**：空对象，用于组织其他对象
- **3D Object**：Cube、Sphere、Plane、Cylinder等
- **2D Object**：Sprite、Tilemap等
- **Light**：光源
- **Camera**：摄像机
- **Audio**：音频源

## 2.5 Inspector窗口（检视窗口）

Inspector窗口显示选中对象的详细信息和所有组件。

### 对象信息

- **名称和标签**：对象名称、标签、图层
- **Transform组件**：位置、旋转、缩放（所有对象都有）
- **其他组件**：根据对象类型显示不同组件

### 组件操作

- **添加组件**：点击"Add Component"按钮
- **移除组件**：点击组件右上角的三个点，选择Remove
- **重置组件**：点击组件右上角的三个点，选择Reset
- **复制组件**：点击组件右上角的三个点，选择Copy，然后在其他对象上Paste

### 属性编辑

- **数值输入**：直接输入数值
- **滑块**：拖动滑块调整数值
- **颜色选择器**：点击颜色块选择颜色
- **材质选择**：拖拽材质到材质槽
- **对象引用**：拖拽对象到引用槽

## 2.6 Project窗口（项目窗口）

Project窗口是项目资源的文件浏览器。

### 视图模式

- **One Column Layout**：单列布局
- **Two Column Layout**：两列布局（默认）
- **搜索**：使用搜索框查找资源

### 资源操作

- **创建资源**：右键点击文件夹，选择Create
- **导入资源**：拖拽文件到Project窗口
- **删除资源**：选中后按Delete键
- **重命名**：选中后按F2
- **复制**：Ctrl+D
- **查找引用**：右键选择"Find References In Scene"

### 资源类型

- **Scenes**：场景文件（.unity）
- **Prefabs**：预制体
- **Scripts**：C#脚本
- **Materials**：材质
- **Textures**：纹理
- **Models**：3D模型
- **Audio**：音频文件
- **Animations**：动画文件

## 2.7 Console窗口（控制台窗口）

Console窗口显示日志、警告和错误信息。

### 日志类型

- **Info**：普通信息（白色）
- **Warning**：警告（黄色）
- **Error**：错误（红色）
- **Exception**：异常（红色）

### 功能

- **清除日志**：点击Clear按钮
- **过滤**：使用下拉菜单过滤日志类型
- **折叠**：相同消息会折叠显示
- **双击跳转**：双击错误可以跳转到代码位置

### 打开方式

- 菜单：`Window > General > Console`
- 快捷键：Ctrl+Shift+C（Windows）或Cmd+Shift+C（Mac）

## 2.8 工具栏

### 变换工具

- **手形工具**（Q）：平移场景视图
- **移动工具**（W）：移动对象
- **旋转工具**（E）：旋转对象
- **缩放工具**（R）：缩放对象
- **矩形工具**（T）：2D对象的矩形变换

### 播放控制

- **播放**（▶）：开始播放游戏
- **暂停**（⏸）：暂停游戏
- **单步**（⏭）：单步执行

### 图层和布局

- **图层下拉菜单**：选择显示的图层
- **布局下拉菜单**：切换窗口布局

## 2.9 菜单栏

### File（文件）

- **New Scene**：新建场景
- **Open Scene**：打开场景
- **Save**：保存场景
- **Save As**：另存为
- **Build Settings**：构建设置
- **Build And Run**：构建并运行

### Edit（编辑）

- **Preferences**：偏好设置
- **Project Settings**：项目设置
- **Graphics Emulation**：图形模拟
- **Snap Settings**：对齐设置

### GameObject（游戏对象）

- **Create Empty**：创建空对象
- **3D Object**：创建3D对象
- **2D Object**：创建2D对象
- **Light**：创建光源
- **Audio**：创建音频对象
- **UI**：创建UI元素

### Component（组件）

- **Add Component**：添加组件（与Inspector中的功能相同）
- 各种组件分类菜单

### Window（窗口）

- **Package Manager**：包管理器
- **Asset Store**：资源商店
- **Profiler**：性能分析器
- **Animation**：动画窗口
- **Animator**：动画控制器窗口
- **Audio Mixer**：音频混合器
- **Console**：控制台
- **Layouts**：窗口布局

### Help（帮助）

- **Unity Manual**：Unity手册
- **Scripting API**：脚本API文档
- **Unity Forum**：Unity论坛
- **Unity Answers**：Unity问答

## 2.10 自定义布局

### 保存自定义布局

1. 调整窗口位置和大小
2. 菜单：`Window > Layouts > Save Layout...`
3. 输入布局名称
4. 点击Save

### 加载布局

- 菜单：`Window > Layouts`，选择已保存的布局

### 重置布局

- 菜单：`Window > Layouts > Revert Factory Settings`

## 2.11 快捷键参考

### 通用快捷键

- **Ctrl+S / Cmd+S**：保存场景
- **Ctrl+N / Cmd+N**：新建场景
- **Ctrl+O / Cmd+O**：打开场景
- **Ctrl+Z / Cmd+Z**：撤销
- **Ctrl+Y / Cmd+Shift+Z**：重做
- **Ctrl+D / Cmd+D**：复制对象
- **Delete**：删除对象
- **F2**：重命名
- **F**：聚焦选中对象

### 视图快捷键

- **Q**：手形工具
- **W**：移动工具
- **E**：旋转工具
- **R**：缩放工具
- **T**：矩形工具
- **Ctrl+P / Cmd+P**：播放/停止
- **Ctrl+Shift+P / Cmd+Shift+P**：暂停

### 窗口快捷键

- **Ctrl+1 / Cmd+1**：Scene视图
- **Ctrl+2 / Cmd+2**：Game视图
- **Ctrl+3 / Cmd+3**：Inspector窗口
- **Ctrl+4 / Cmd+4**：Hierarchy窗口
- **Ctrl+5 / Cmd+5**：Project窗口
- **Ctrl+6 / Cmd+6**：Animation窗口
- **Ctrl+7 / Cmd+7**：Profiler窗口
- **Ctrl+9 / Cmd+9**：Asset Store
- **Ctrl+Shift+C / Cmd+Shift+C**：Console窗口

## 2.12 本章小结

在本章中，我们详细学习了：

- ✅ Scene视图的操作和功能
- ✅ Game视图的设置和使用
- ✅ Hierarchy窗口的层级管理
- ✅ Inspector窗口的组件编辑
- ✅ Project窗口的资源管理
- ✅ Console窗口的日志查看
- ✅ 工具栏和菜单栏的功能
- ✅ 自定义布局和快捷键

## 2.13 实践练习

1. **熟悉窗口**：打开Unity，熟悉各个窗口的位置和功能
2. **创建对象**：在场景中创建不同类型的对象
3. **调整布局**：尝试不同的窗口布局，找到最适合自己的
4. **练习快捷键**：使用快捷键进行操作，提高效率
5. **自定义布局**：创建并保存自己的窗口布局

## 2.14 下一步

现在你已经熟悉了Unity Editor的界面，下一章我们将学习项目管理和资源系统。

---

**提示**：熟练使用快捷键可以大大提高开发效率，建议多练习常用快捷键。
