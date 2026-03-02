# 第4章：2D游戏开发基础

## 4.1 2D vs 3D

### 2D游戏的特点

- **平面视图**：游戏在二维平面上进行
- **精灵（Sprite）**：使用2D图像作为游戏对象
- **简化开发**：通常比3D游戏更容易开发
- **经典风格**：像素艺术、手绘风格等

### Unity的2D支持

Unity提供了完整的2D游戏开发工具集：
- 2D Sprite渲染器
- 2D物理系统
- 2D动画系统
- Tilemap系统（瓦片地图）

## 4.2 创建2D项目

### 创建新项目

1. 打开Unity Hub
2. 点击"新建项目"
3. 选择"2D"模板
4. 设置项目名称和位置
5. 点击"创建"

### 2D项目设置

创建2D项目后，Unity会自动设置：
- **摄像机**：设置为正交（Orthographic）模式
- **Scene视图**：默认2D模式
- **Sprite导入设置**：自动配置为Sprite类型

## 4.3 精灵（Sprite）

### 什么是精灵？

精灵是2D游戏中使用的图像对象。可以是：
- 角色图像
- 背景图像
- 道具图像
- UI元素

### 导入精灵

1. 准备图像文件（PNG格式，支持透明通道）
2. 将图像拖拽到Project窗口
3. Unity自动导入为Sprite

### 精灵导入设置

选中图像后，在Inspector中设置：

- **Texture Type**：选择"Sprite (2D and UI)"
- **Sprite Mode**：
  - **Single**：单个精灵
  - **Multiple**：多个精灵（精灵表）
  - **Polygon**：多边形精灵
- **Pixels Per Unit**：每单位像素数（默认100）
- **Filter Mode**：过滤模式（Point适合像素艺术）

### 创建精灵对象

1. 在Project窗口选中精灵图像
2. 拖拽到Scene或Hierarchy窗口
3. Unity自动创建带Sprite Renderer组件的游戏对象

## 4.4 Sprite Renderer组件

Sprite Renderer负责渲染2D精灵。

### 主要属性

- **Sprite**：要渲染的精灵图像
- **Color**：颜色叠加（可用于调色）
- **Flip**：水平/垂直翻转
- **Sorting Layer**：排序图层
- **Order in Layer**：图层内排序顺序

### Sorting Layer（排序图层）

控制精灵的渲染顺序：
- 高排序图层的精灵显示在前面
- 同一图层内，Order in Layer值大的显示在前面

#### 创建排序图层

1. 菜单：`Edit > Project Settings > Tags and Layers`
2. 在Sorting Layers部分添加新图层

## 4.5 2D摄像机

### 正交摄像机

2D游戏使用正交（Orthographic）摄像机：
- 没有透视效果
- 物体大小不随距离变化
- 适合2D游戏

### 摄像机设置

选中Main Camera，在Inspector中：

- **Projection**：设置为"Orthographic"
- **Size**：摄像机大小（控制可见范围）
- **Clear Flags**：清除标志（通常使用Solid Color）
- **Background**：背景颜色

### 摄像机移动

创建脚本控制摄像机跟随玩家：

```csharp
using UnityEngine;

public class CameraFollow : MonoBehaviour
{
    public Transform target;  // 跟随的目标
    public float smoothSpeed = 0.125f;  // 平滑速度
    public Vector3 offset;  // 偏移量

    void LateUpdate()
    {
        Vector3 desiredPosition = target.position + offset;
        Vector3 smoothedPosition = Vector3.Lerp(transform.position, desiredPosition, smoothSpeed);
        transform.position = smoothedPosition;
    }
}
```

## 4.6 2D坐标系统

### Unity的2D坐标

- **X轴**：水平方向（右为正）
- **Y轴**：垂直方向（上为正）
- **Z轴**：深度（用于排序，不影响2D显示）

### 坐标转换

- **世界坐标**：场景中的绝对位置
- **本地坐标**：相对于父对象的位置
- **屏幕坐标**：屏幕像素坐标

## 4.7 精灵动画

### Animation组件

Unity的Animation组件可以播放2D动画：

1. 选中游戏对象
2. 添加Animation组件
3. 创建Animation Clip（动画剪辑）
4. 在Animation窗口中编辑关键帧

### Animator组件

Animator组件用于控制动画状态机：

1. 添加Animator组件
2. 创建Animator Controller
3. 在Animator窗口中设置状态和过渡

### 精灵表动画

使用多个精灵创建动画：

1. 导入精灵表（多个精灵在一张图上）
2. 设置Sprite Mode为"Multiple"
3. 点击Sprite Editor进行切片
4. 创建Animation Clip
5. 将各个精灵帧添加到动画中

## 4.8 2D物理系统

### Rigidbody2D组件

为2D对象添加物理属性：

- **Body Type**：
  - **Dynamic**：动态物体（受物理影响）
  - **Kinematic**：运动学物体（不受力影响，但可以移动）
  - **Static**：静态物体（完全不动）
- **Mass**：质量
- **Gravity Scale**：重力缩放（0表示不受重力）
- **Drag**：阻力
- **Angular Drag**：角阻力

### Collider2D组件

2D碰撞体类型：

- **Box Collider 2D**：矩形碰撞体
- **Circle Collider 2D**：圆形碰撞体
- **Capsule Collider 2D**：胶囊碰撞体
- **Polygon Collider 2D**：多边形碰撞体
- **Edge Collider 2D**：边缘碰撞体（用于平台）

### 物理材质

创建2D物理材质：

1. 右键Project窗口：`Create > Physics2D Material`
2. 设置属性：
   - **Friction**：摩擦力
   - **Bounciness**：弹性

## 4.9 2D输入系统

### 获取输入

使用Input类获取输入：

```csharp
using UnityEngine;

public class PlayerController2D : MonoBehaviour
{
    public float moveSpeed = 5f;
    private Rigidbody2D rb;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // 获取输入
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");

        // 移动
        Vector2 movement = new Vector2(horizontal, vertical);
        rb.velocity = movement * moveSpeed;
    }
}
```

### 新输入系统（Input System）

Unity的新输入系统更灵活：

1. 安装Input System包
2. 创建Input Actions资源
3. 在代码中使用：

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerController2D : MonoBehaviour
{
    public float moveSpeed = 5f;
    private Vector2 moveInput;
    private Rigidbody2D rb;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    public void OnMove(InputValue value)
    {
        moveInput = value.Get<Vector2>();
    }

    void FixedUpdate()
    {
        rb.velocity = moveInput * moveSpeed;
    }
}
```

## 4.10 2D游戏对象创建

### 创建2D精灵对象

1. 菜单：`GameObject > 2D Object > Sprite`
2. 或从Project窗口拖拽精灵到场景

### 创建2D背景

1. 导入背景图像
2. 创建Sprite对象
3. 设置Sorting Layer为"Background"
4. 调整大小和位置

### 创建2D角色

1. 导入角色精灵
2. 创建Sprite对象
3. 添加Rigidbody2D组件
4. 添加Collider2D组件
5. 添加移动脚本

## 4.11 2D光照系统

### 2D光照类型

- **Point Light 2D**：点光源
- **Sprite Light 2D**：精灵光源
- **Global Light 2D**：全局光源

### 启用2D光照

1. 菜单：`Window > Rendering > Lighting`
2. 在Lighting窗口中启用2D光照
3. 创建2D光源对象

### 光照设置

- **Normal Maps**：使用法线贴图增强3D效果
- **Light Blend Styles**：光照混合样式

## 4.12 实践项目：简单的2D平台游戏

### 项目目标

创建一个简单的2D平台游戏，包含：
- 玩家角色（可以移动和跳跃）
- 平台（可以站立）
- 摄像机跟随

### 步骤1：设置场景

1. 创建新场景
2. 设置背景颜色
3. 调整摄像机大小

### 步骤2：创建玩家

1. 创建Sprite对象作为玩家
2. 添加Rigidbody2D组件
3. 添加Box Collider 2D组件
4. 创建移动脚本：

```csharp
using UnityEngine;

public class Player2D : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    private Rigidbody2D rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // 水平移动
        float horizontal = Input.GetAxis("Horizontal");
        rb.velocity = new Vector2(horizontal * moveSpeed, rb.velocity.y);

        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Impulse);
            isGrounded = false;
        }
    }

    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }
}
```

### 步骤3：创建平台

1. 创建Sprite对象作为平台
2. 添加Box Collider 2D组件
3. 设置标签为"Ground"
4. 复制多个平台，布置场景

### 步骤4：摄像机跟随

添加摄像机跟随脚本（参考4.5节）

## 4.13 本章小结

在本章中，我们学习了：

- ✅ 2D游戏开发的基础概念
- ✅ 精灵的导入和使用
- ✅ Sprite Renderer组件
- ✅ 2D摄像机的设置
- ✅ 2D坐标系统
- ✅ 精灵动画
- ✅ 2D物理系统
- ✅ 2D输入处理
- ✅ 创建2D游戏对象
- ✅ 2D光照系统

## 4.14 实践练习

1. **导入精灵**：导入一些2D图像作为精灵
2. **创建角色**：创建一个可以移动的2D角色
3. **创建平台**：创建一些平台对象
4. **添加物理**：为对象添加2D物理组件
5. **完成项目**：完成简单的2D平台游戏

## 4.15 下一步

下一章我们将深入学习2D精灵和动画系统，学习如何创建更复杂的2D动画效果。

---

**提示**：2D游戏开发的关键是理解精灵、排序图层和2D物理系统。多实践，多尝试不同的设置。
