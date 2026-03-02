# 第5章：2D精灵和动画

## 5.1 精灵表（Sprite Sheet）

### 什么是精灵表？

精灵表是将多个精灵图像组合在一张图片中的技术。常用于：
- 角色动画帧
- 游戏元素集合
- 减少Draw Call，提高性能

### 导入精灵表

1. 准备精灵表图像（PNG格式）
2. 拖拽到Project窗口
3. 在Inspector中设置：
   - **Texture Type**：Sprite (2D and UI)
   - **Sprite Mode**：Multiple
   - **Pixels Per Unit**：根据需求设置

### 切片精灵表

使用Sprite Editor进行切片：

1. 选中精灵表图像
2. 点击Inspector中的"Sprite Editor"按钮
3. 在Sprite Editor中：
   - **Slice**：自动切片
   - **Type**：选择切片类型（Grid、Automatic等）
   - **Pivot**：设置轴心点
   - **Apply**：应用切片

#### 手动切片

- 在Sprite Editor中手动调整每个精灵的边界
- 适合不规则排列的精灵表

#### 自动切片

- 使用Grid模式自动切片
- 设置Cell Size（单元格大小）
- Unity自动识别并切片

## 5.2 精灵编辑器（Sprite Editor）

### 打开Sprite Editor

- 选中精灵图像
- 点击Inspector中的"Sprite Editor"按钮
- 或菜单：`Window > 2D > Sprite Editor`

### Sprite Editor功能

- **编辑边界**：调整精灵的边界框
- **设置轴心点**：设置旋转和缩放的轴心
- **编辑多边形**：编辑多边形碰撞体形状
- **预览**：预览精灵效果

### 轴心点设置

轴心点影响精灵的旋转和缩放中心：

- **Center**：中心
- **Top Left**：左上角
- **Top Right**：右上角
- **Bottom Left**：左下角
- **Bottom Right**：右下角
- **Custom**：自定义位置

## 5.3 Animation窗口

### 打开Animation窗口

- 菜单：`Window > Animation > Animation`
- 快捷键：`Ctrl+6`（Windows）或`Cmd+6`（Mac）

### 创建动画剪辑

1. 选中要添加动画的游戏对象
2. 打开Animation窗口
3. 点击"Create"按钮
4. 命名并保存Animation Clip
5. Unity自动添加Animation组件

### 编辑动画

#### 添加关键帧

1. 将时间轴移动到目标时间
2. 修改对象的属性（位置、旋转、缩放等）
3. 点击"Add Property"或直接修改属性
4. Unity自动记录关键帧

#### 编辑关键帧

- **选择关键帧**：点击关键帧
- **移动关键帧**：拖拽关键帧
- **删除关键帧**：选中后按Delete键
- **复制关键帧**：Ctrl+C，Ctrl+V

#### 动画曲线

- 点击属性名称查看动画曲线
- 调整曲线控制动画的缓动效果
- Unity提供预设曲线：Linear、Ease In、Ease Out等

### 精灵动画

使用多个精灵创建动画：

1. 创建Animation Clip
2. 添加Sprite Renderer的Sprite属性
3. 在不同时间点设置不同的精灵
4. Unity自动在精灵之间插值

#### 示例：创建行走动画

1. 准备行走动画的精灵帧（4-8帧）
2. 创建Animation Clip命名为"Walk"
3. 在时间轴上设置关键帧：
   - 0秒：第1帧精灵
   - 0.1秒：第2帧精灵
   - 0.2秒：第3帧精灵
   - ...
4. 设置动画为循环播放

## 5.4 Animator组件和Animator Controller

### Animator组件

Animator组件控制动画的播放：

- **Controller**：Animator Controller资源
- **Avatar**：2D游戏通常不需要
- **Apply Root Motion**：应用根运动（2D通常关闭）

### Animator Controller

Animator Controller定义动画状态机：

1. 创建：右键Project窗口 `Create > Animator Controller`
2. 打开：双击Animator Controller
3. 打开Animator窗口：`Window > Animation > Animator`

### 动画状态

#### 创建状态

1. 在Animator窗口中右键
2. 选择"Create State > Empty"
3. 或从Project窗口拖拽Animation Clip到Animator窗口

#### 默认状态

- 第一个创建的状态自动成为默认状态（橙色）
- 游戏开始时播放默认状态

### 状态转换（Transitions）

#### 创建转换

1. 右键源状态
2. 选择"Make Transition"
3. 点击目标状态

#### 转换条件

- **Has Exit Time**：等待当前动画播放完成
- **Exit Time**：退出时间点
- **Transition Duration**：转换持续时间
- **Conditions**：转换条件（参数）

### 动画参数

#### 参数类型

- **Float**：浮点数
- **Int**：整数
- **Bool**：布尔值
- **Trigger**：触发器

#### 创建参数

1. 在Animator窗口左侧点击"+"按钮
2. 选择参数类型
3. 命名参数

#### 使用参数

在转换的Conditions中添加条件：
- `Speed > 0.1`：速度大于0.1时转换
- `IsGrounded == true`：在地面时转换
- `Jump`：触发时转换

## 5.5 动画脚本控制

### 获取Animator组件

```csharp
using UnityEngine;

public class PlayerAnimation : MonoBehaviour
{
    private Animator animator;

    void Start()
    {
        animator = GetComponent<Animator>();
    }
}
```

### 设置参数

```csharp
// 设置Float参数
animator.SetFloat("Speed", moveSpeed);

// 设置Bool参数
animator.SetBool("IsGrounded", isGrounded);

// 设置Int参数
animator.SetInteger("Health", currentHealth);

// 触发Trigger参数
animator.SetTrigger("Jump");
```

### 完整示例

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    
    private Rigidbody2D rb;
    private Animator animator;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        // 移动
        float horizontal = Input.GetAxis("Horizontal");
        rb.velocity = new Vector2(horizontal * moveSpeed, rb.velocity.y);

        // 设置动画参数
        animator.SetFloat("Speed", Mathf.Abs(horizontal));
        
        // 翻转角色
        if (horizontal > 0)
            transform.localScale = new Vector3(1, 1, 1);
        else if (horizontal < 0)
            transform.localScale = new Vector3(-1, 1, 1);

        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Impulse);
            animator.SetTrigger("Jump");
            isGrounded = false;
        }
    }

    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
            animator.SetBool("IsGrounded", true);
        }
    }

    void OnCollisionExit2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = false;
            animator.SetBool("IsGrounded", false);
        }
    }
}
```

## 5.6 动画事件（Animation Events）

### 什么是动画事件？

动画事件允许在动画的特定时间点触发函数调用。

### 添加动画事件

1. 在Animation窗口中选中关键帧
2. 点击"Add Animation Event"按钮（或右键关键帧）
3. 在Inspector中设置：
   - **Function**：要调用的函数名
   - **Float Parameter**：浮点参数（可选）
   - **Int Parameter**：整数参数（可选）
   - **String Parameter**：字符串参数（可选）
   - **Object Reference Parameter**：对象引用（可选）

### 接收动画事件

在脚本中创建与事件同名的函数：

```csharp
using UnityEngine;

public class PlayerAnimationEvents : MonoBehaviour
{
    public void OnFootstep()
    {
        // 播放脚步声
        Debug.Log("Footstep!");
    }

    public void OnJumpStart()
    {
        // 跳跃开始
        Debug.Log("Jump Start!");
    }

    public void OnLand()
    {
        // 落地
        Debug.Log("Land!");
    }
}
```

## 5.7 动画层（Animation Layers）

### 什么是动画层？

动画层允许同时播放多个动画，例如：
- 基础层：身体动画
- 上层：手臂动画
- 上层：面部表情

### 创建动画层

1. 在Animator窗口中点击"Layers"标签
2. 点击"+"按钮添加新层
3. 设置层的权重和混合模式

### 使用动画层

```csharp
// 设置层的权重
animator.SetLayerWeight(1, 1f);  // 第2层权重为1

// 在特定层设置参数
animator.SetFloat("ArmAnimation", value);
```

## 5.8 动画混合树（Blend Trees）

### 什么是混合树？

混合树根据参数值混合多个动画，常用于：
- 根据速度混合行走和跑步动画
- 根据方向混合不同方向的动画

### 创建混合树

1. 在Animator窗口中右键
2. 选择"Create State > From New Blend Tree"
3. 双击混合树进入编辑

### 配置混合树

1. 添加动画剪辑到混合树
2. 设置每个动画的位置（1D）或坐标（2D）
3. 设置混合参数

#### 1D混合树示例

- 参数：Speed
- 动画1：Idle（Speed = 0）
- 动画2：Walk（Speed = 1）
- 动画3：Run（Speed = 2）

#### 2D混合树示例

- 参数：X, Y（方向）
- 动画：上、下、左、右、左上、右上、左下、右下

## 5.9 动画优化技巧

### 性能优化

1. **减少动画数量**：只创建必要的动画
2. **优化关键帧**：删除不必要的关键帧
3. **使用动画压缩**：在Animation Clip设置中启用压缩
4. **合理使用动画层**：不要创建过多层

### 内存优化

1. **共享动画**：多个对象共享相同的Animation Clip
2. **使用Animator Override Controller**：基于现有Controller创建变体
3. **及时销毁**：不使用的Animator及时销毁

## 5.10 实践项目：完整的2D角色动画系统

### 项目目标

创建一个完整的2D角色动画系统，包含：
- Idle（待机）动画
- Walk（行走）动画
- Jump（跳跃）动画
- 动画状态机
- 脚本控制

### 步骤1：准备精灵

1. 准备角色精灵表
2. 导入并切片精灵表
3. 组织精灵帧

### 步骤2：创建动画剪辑

1. 创建Idle动画（使用待机精灵帧）
2. 创建Walk动画（使用行走精灵帧）
3. 创建Jump动画（使用跳跃精灵帧）

### 步骤3：创建Animator Controller

1. 创建Animator Controller
2. 添加动画状态
3. 设置状态转换和条件

### 步骤4：添加脚本控制

参考5.5节的完整示例代码

## 5.11 本章小结

在本章中，我们学习了：

- ✅ 精灵表的导入和切片
- ✅ Sprite Editor的使用
- ✅ Animation窗口创建动画
- ✅ Animator组件和Controller
- ✅ 动画状态和转换
- ✅ 动画参数控制
- ✅ 动画事件
- ✅ 动画层和混合树
- ✅ 动画优化技巧

## 5.12 实践练习

1. **切片精灵表**：导入并切片一个角色精灵表
2. **创建动画**：为角色创建Idle、Walk、Jump动画
3. **设置状态机**：创建Animator Controller并设置状态转换
4. **添加控制**：编写脚本控制动画播放
5. **添加事件**：在动画中添加事件触发音效

## 5.13 下一步

下一章我们将学习2D物理系统和碰撞检测，这是2D游戏开发的重要部分。

---

**提示**：动画是游戏体验的重要组成部分，花时间创建流畅的动画会让游戏更加生动有趣。
