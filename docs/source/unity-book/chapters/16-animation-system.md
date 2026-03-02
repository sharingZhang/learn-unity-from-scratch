# 第16章：动画系统

## 16.1 动画系统概述

Unity的动画系统用于创建和控制角色和对象的动画。

### 动画组件

- **Animation**：旧版动画系统
- **Animator**：新版动画系统（推荐）
- **Animation Clip**：动画剪辑

## 16.2 Animation Clip

### 创建Animation Clip

1. 选中游戏对象
2. 打开Animation窗口：`Window > Animation > Animation`
3. 点击"Create"创建新Clip
4. 命名并保存

### 编辑动画

#### 添加属性

1. 点击"Add Property"
2. 选择要动画的属性（Transform、Material等）
3. 设置关键帧

#### 关键帧操作

- **添加关键帧**：修改属性值，自动添加
- **选择关键帧**：点击关键帧
- **移动关键帧**：拖拽关键帧
- **删除关键帧**：选中后按Delete

### 动画曲线

- 点击属性名称查看曲线
- 调整曲线控制缓动效果
- Unity提供预设曲线

## 16.3 Animator Controller

### 创建Animator Controller

1. 右键Project窗口：`Create > Animator Controller`
2. 命名Controller
3. 双击打开Animator窗口

### 动画状态

#### 创建状态

1. 在Animator窗口中右键
2. 选择"Create State > Empty"
3. 或拖拽Animation Clip到窗口

#### 默认状态

- 第一个状态自动成为默认状态（橙色）
- 游戏开始时播放默认状态

### 状态转换

#### 创建转换

1. 右键源状态
2. 选择"Make Transition"
3. 点击目标状态

#### 转换设置

- **Has Exit Time**：等待当前动画完成
- **Exit Time**：退出时间点
- **Transition Duration**：转换持续时间
- **Conditions**：转换条件

### 动画参数

#### 参数类型

- **Float**：浮点数
- **Int**：整数
- **Bool**：布尔值
- **Trigger**：触发器

#### 创建参数

1. 在Animator窗口左侧点击"+"
2. 选择参数类型
3. 命名参数

## 16.4 脚本控制动画

### 获取Animator

```csharp
using UnityEngine;

public class AnimationController : MonoBehaviour
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

public class PlayerAnimation : MonoBehaviour
{
    private Animator animator;
    private float moveSpeed;

    void Start()
    {
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        // 获取移动速度
        moveSpeed = Input.GetAxis("Horizontal");
        
        // 设置动画参数
        animator.SetFloat("Speed", Mathf.Abs(moveSpeed));
        
        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space))
        {
            animator.SetTrigger("Jump");
        }
    }
}
```

## 16.5 动画事件

### 添加动画事件

1. 在Animation窗口中选中关键帧
2. 点击"Add Animation Event"
3. 设置函数名和参数

### 接收事件

```csharp
public void OnFootstep()
{
    // 播放脚步声
    AudioSource.PlayClipAtPoint(footstepSound, transform.position);
}

public void OnAttackHit()
{
    // 攻击命中
    DealDamage();
}
```

## 16.6 动画层

### 什么是动画层？

动画层允许同时播放多个动画。

### 创建动画层

1. 在Animator窗口中点击"Layers"
2. 点击"+"添加新层
3. 设置权重和混合模式

### 使用动画层

```csharp
// 设置层的权重
animator.SetLayerWeight(1, 1f);

// 在特定层设置参数
animator.SetFloat("ArmAnimation", value);
```

## 16.7 混合树（Blend Trees）

### 什么是混合树？

混合树根据参数值混合多个动画。

### 创建混合树

1. 在Animator窗口中右键
2. 选择"Create State > From New Blend Tree"
3. 双击混合树进入编辑

### 1D混合树

- 参数：Speed
- 动画：Idle（0）、Walk（1）、Run（2）

### 2D混合树

- 参数：X、Y（方向）
- 动画：8方向移动动画

## 16.8 人形动画（Humanoid）

### 设置人形动画

1. 导入人形模型
2. 在Rig标签设置Animation Type为Humanoid
3. Unity自动创建Avatar

### Avatar

- 定义骨骼映射
- 允许动画重定向
- 不同模型可以共享动画

### 使用人形动画

```csharp
// 人形动画可以直接应用到不同模型
animator.avatar = avatar;
```

## 16.9 动画优化

### 性能优化

1. **减少动画数量**：只创建必要的动画
2. **优化关键帧**：删除不必要的关键帧
3. **使用动画压缩**：在Animation Clip设置中启用
4. **合理使用层**：不要创建过多层

### 内存优化

1. **共享动画**：多个对象共享Animation Clip
2. **使用Animator Override Controller**：创建变体
3. **及时销毁**：不使用的Animator及时销毁

## 16.10 实践项目：完整的动画系统

### 项目目标

创建完整的角色动画系统：
- Idle、Walk、Run动画
- Jump动画
- Attack动画
- 动画状态机
- 脚本控制

### 步骤

1. 准备动画资源
2. 创建Animation Clips
3. 创建Animator Controller
4. 设置状态和转换
5. 编写控制脚本

## 16.11 本章小结

在本章中，我们学习了：

- ✅ Animation Clip创建和编辑
- ✅ Animator Controller使用
- ✅ 动画状态和转换
- ✅ 动画参数控制
- ✅ 动画事件
- ✅ 动画层
- ✅ 混合树
- ✅ 人形动画
- ✅ 动画优化

## 16.12 实践练习

1. **创建动画**：为角色创建基本动画
2. **设置状态机**：创建Animator Controller
3. **添加控制**：编写脚本控制动画
4. **添加事件**：在动画中添加事件
5. **创建混合树**：创建移动混合树

## 16.13 下一步

下一章我们将学习UI系统，创建游戏界面。

---

**提示**：流畅的动画能让游戏更加生动。花时间创建高质量的动画。
