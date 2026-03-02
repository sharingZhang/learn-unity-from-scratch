# 第6章：2D物理和碰撞

## 6.1 2D物理系统概述

Unity的2D物理系统基于Box2D物理引擎，提供了完整的2D物理模拟功能。

### 2D物理组件

- **Rigidbody2D**：刚体组件，提供物理属性
- **Collider2D**：碰撞体组件，定义碰撞形状
- **Joint2D**：关节组件，连接物体
- **Effector2D**：效果器组件，产生物理效果

## 6.2 Rigidbody2D组件

### 添加Rigidbody2D

1. 选中游戏对象
2. 在Inspector中点击"Add Component"
3. 搜索"Rigidbody2D"
4. 添加组件

### 主要属性

#### Body Type（物体类型）

- **Dynamic**：动态物体
  - 受重力和力影响
  - 可以与其他物体碰撞
  - 用于玩家、敌人等可移动物体
  
- **Kinematic**：运动学物体
  - 不受力影响
  - 可以通过代码移动
  - 可以推动Dynamic物体
  - 用于平台、移动障碍物等
  
- **Static**：静态物体
  - 完全不动
  - 性能最优
  - 用于地面、墙壁等固定物体

#### 物理属性

- **Mass**：质量（影响惯性）
- **Linear Drag**：线性阻力（空气阻力）
- **Angular Drag**：角阻力（旋转阻力）
- **Gravity Scale**：重力缩放（0=不受重力，1=正常重力）
- **Freeze Rotation**：冻结旋转（防止物体旋转）

### 通过代码控制Rigidbody2D

```csharp
using UnityEngine;

public class PhysicsController : MonoBehaviour
{
    private Rigidbody2D rb;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // 添加力
        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.AddForce(Vector2.up * 10f, ForceMode2D.Impulse);
        }

        // 设置速度
        rb.velocity = new Vector2(5f, rb.velocity.y);

        // 添加扭矩（旋转力）
        rb.AddTorque(10f);

        // 设置角速度
        rb.angularVelocity = 90f;
    }
}
```

## 6.3 Collider2D组件

### 碰撞体类型

#### Box Collider 2D（矩形碰撞体）

- 最常用的碰撞体
- 适合矩形物体
- 性能好

**属性**：
- **Size**：碰撞体大小
- **Offset**：偏移量
- **Is Trigger**：是否为触发器

#### Circle Collider 2D（圆形碰撞体）

- 适合圆形物体
- 性能最好
- 适合角色、球等

**属性**：
- **Radius**：半径
- **Offset**：偏移量

#### Capsule Collider 2D（胶囊碰撞体）

- 适合角色
- 结合了矩形和圆形的优点

**属性**：
- **Size**：大小
- **Direction**：方向（Vertical/Horizontal）

#### Polygon Collider 2D（多边形碰撞体）

- 可以自定义形状
- 适合复杂形状
- 性能较差

**属性**：
- **Points**：顶点数组
- **Path Count**：路径数量

#### Edge Collider 2D（边缘碰撞体）

- 用于平台边缘
- 只有边缘有碰撞
- 适合平台游戏

**属性**：
- **Points**：边缘点数组

### 添加碰撞体

1. 选中游戏对象
2. 添加Component > Physics2D > 选择碰撞体类型
3. 调整大小和位置

### 碰撞体设置

- **Is Trigger**：触发器模式
  - 勾选：不产生物理碰撞，只触发事件
  - 不勾选：产生物理碰撞
  
- **Material**：物理材质（影响摩擦和弹性）
- **Used by Effector**：是否被效果器使用

## 6.4 物理材质（Physics Material 2D）

### 创建物理材质

1. 右键Project窗口：`Create > Physics2D Material`
2. 命名材质（如：Ice、Bouncy等）

### 材质属性

- **Friction**：摩擦力（0-1）
  - 0：无摩擦（冰面）
  - 1：最大摩擦（粗糙表面）
  
- **Bounciness**：弹性（0-1）
  - 0：无弹性
  - 1：完全弹性（弹球）

### 使用物理材质

1. 创建物理材质
2. 设置属性
3. 在Collider2D的Material槽中拖拽材质

## 6.5 碰撞检测

### 碰撞事件

Unity提供三种碰撞事件：

#### OnCollisionEnter2D

物体开始碰撞时调用：

```csharp
void OnCollisionEnter2D(Collision2D collision)
{
    Debug.Log("碰撞开始：" + collision.gameObject.name);
    
    // 获取碰撞点
    ContactPoint2D contact = collision.contacts[0];
    Vector2 point = contact.point;
    
    // 获取碰撞法线
    Vector2 normal = contact.normal;
}
```

#### OnCollisionStay2D

物体持续碰撞时每帧调用：

```csharp
void OnCollisionStay2D(Collision2D collision)
{
    // 持续碰撞中的逻辑
}
```

#### OnCollisionExit2D

物体停止碰撞时调用：

```csharp
void OnCollisionExit2D(Collision2D collision)
{
    Debug.Log("碰撞结束：" + collision.gameObject.name);
}
```

### 触发器事件

当Collider2D的Is Trigger为true时，使用触发器事件：

#### OnTriggerEnter2D

```csharp
void OnTriggerEnter2D(Collider2D other)
{
    Debug.Log("进入触发器：" + other.gameObject.name);
    
    // 例如：收集道具
    if (other.CompareTag("Collectible"))
    {
        Destroy(other.gameObject);
    }
}
```

#### OnTriggerStay2D

```csharp
void OnTriggerStay2D(Collider2D other)
{
    // 持续在触发器中的逻辑
}
```

#### OnTriggerExit2D

```csharp
void OnTriggerExit2D(Collider2D other)
{
    Debug.Log("离开触发器：" + other.gameObject.name);
}
```

## 6.6 碰撞过滤

### 图层碰撞矩阵

控制哪些图层之间可以发生碰撞：

1. 菜单：`Edit > Project Settings > Physics2D`
2. 找到"Layer Collision Matrix"
3. 取消勾选不需要碰撞的图层组合

### 使用图层

1. 创建图层：`Edit > Project Settings > Tags and Layers`
2. 为对象设置图层
3. 在碰撞矩阵中配置

### 代码中设置碰撞

```csharp
// 忽略特定图层的碰撞
Physics2D.IgnoreLayerCollision(8, 9);  // 图层8和9不碰撞

// 忽略特定对象的碰撞
Physics2D.IgnoreCollision(collider1, collider2);
```

## 6.7 2D关节（Joints）

### 关节类型

#### Distance Joint 2D（距离关节）

保持两个物体之间的固定距离：

```csharp
DistanceJoint2D joint = gameObject.AddComponent<DistanceJoint2D>();
joint.connectedBody = otherRigidbody;
joint.distance = 5f;
```

#### Spring Joint 2D（弹簧关节）

用弹簧连接两个物体：

```csharp
SpringJoint2D joint = gameObject.AddComponent<SpringJoint2D>();
joint.connectedBody = otherRigidbody;
joint.distance = 5f;
joint.frequency = 2f;  // 弹簧频率
joint.dampingRatio = 0.5f;  // 阻尼比
```

#### Hinge Joint 2D（铰链关节）

允许物体围绕一个点旋转：

```csharp
HingeJoint2D joint = gameObject.AddComponent<HingeJoint2D>();
joint.connectedBody = otherRigidbody;
joint.anchor = Vector2.zero;
```

#### Slider Joint 2D（滑动关节）

允许物体沿一个轴滑动：

```csharp
SliderJoint2D joint = gameObject.AddComponent<SliderJoint2D>();
joint.connectedBody = otherRigidbody;
joint.angle = 0f;  // 滑动角度
```

## 6.8 2D效果器（Effectors）

### 效果器类型

#### Area Effector 2D（区域效果器）

在区域内产生力：

- **Force Angle**：力的角度
- **Force Magnitude**：力的大小
- **Force Variation**：力的变化
- **Drag**：阻力
- **Angular Drag**：角阻力

#### Buoyancy Effector 2D（浮力效果器）

模拟浮力效果：

- **Surface Level**：表面高度
- **Density**：密度
- **Linear Drag**：线性阻力
- **Angular Drag**：角阻力

#### Platform Effector 2D（平台效果器）

用于单向平台（只能从下方通过）：

- **Use One Way**：使用单向碰撞
- **Surface Arc**：表面角度
- **Side Friction**：侧面摩擦
- **Side Bounce**：侧面弹跳

#### Point Effector 2D（点效果器）

从一点产生力：

- **Force Magnitude**：力的大小
- **Force Variation**：力的变化
- **Distance Scale**：距离缩放
- **Drag**：阻力

## 6.9 射线检测（Raycasting）

### 2D射线检测

用于检测特定方向上的物体：

```csharp
using UnityEngine;

public class Raycast2D : MonoBehaviour
{
    void Update()
    {
        // 发射射线
        RaycastHit2D hit = Physics2D.Raycast(transform.position, Vector2.right, 10f);
        
        if (hit.collider != null)
        {
            Debug.Log("击中：" + hit.collider.gameObject.name);
            Debug.Log("距离：" + hit.distance);
            Debug.Log("点：" + hit.point);
        }
    }
}
```

### 射线检测方法

#### Raycast（单次检测）

```csharp
RaycastHit2D hit = Physics2D.Raycast(origin, direction, distance, layerMask);
```

#### RaycastAll（检测所有）

```csharp
RaycastHit2D[] hits = Physics2D.RaycastAll(origin, direction, distance);
```

#### Linecast（线段检测）

```csharp
RaycastHit2D hit = Physics2D.Linecast(start, end);
```

### 可视化射线

```csharp
void Update()
{
    RaycastHit2D hit = Physics2D.Raycast(transform.position, Vector2.down, 2f);
    
    // 绘制射线（仅在Scene视图中可见）
    if (hit.collider != null)
    {
        Debug.DrawLine(transform.position, hit.point, Color.red);
    }
    else
    {
        Debug.DrawRay(transform.position, Vector2.down * 2f, Color.green);
    }
}
```

## 6.10 物理查询

### Overlap检测

检测区域内是否有碰撞体：

```csharp
// 圆形检测
Collider2D collider = Physics2D.OverlapCircle(transform.position, 2f);

// 矩形检测
Collider2D collider = Physics2D.OverlapArea(new Vector2(0, 0), new Vector2(5, 5));

// 检测所有
Collider2D[] colliders = Physics2D.OverlapCircleAll(transform.position, 2f);
```

### 使用示例：地面检测

```csharp
using UnityEngine;

public class GroundCheck : MonoBehaviour
{
    public float checkRadius = 0.2f;
    public LayerMask groundLayer;
    
    private bool isGrounded;

    void Update()
    {
        // 检测脚下是否有地面
        isGrounded = Physics2D.OverlapCircle(transform.position, checkRadius, groundLayer);
    }

    void OnDrawGizmos()
    {
        // 可视化检测范围
        Gizmos.color = isGrounded ? Color.green : Color.red;
        Gizmos.DrawWireSphere(transform.position, checkRadius);
    }
}
```

## 6.11 物理设置

### 全局物理设置

菜单：`Edit > Project Settings > Physics2D`

#### 重要设置

- **Gravity**：全局重力（默认Y=-9.81）
- **Default Material**：默认物理材质
- **Velocity Iterations**：速度迭代次数（影响精度）
- **Position Iterations**：位置迭代次数（影响精度）
- **Velocity Threshold**：速度阈值
- **Max Linear Correction**：最大线性修正
- **Max Angular Correction**：最大角度修正

### 性能优化

- **减少迭代次数**：在可接受范围内降低迭代次数
- **使用简单碰撞体**：Circle和Box性能最好
- **合理使用Static**：固定物体使用Static类型
- **减少碰撞体数量**：合并碰撞体，使用复合碰撞体

## 6.12 实践项目：2D平台游戏物理

### 项目目标

创建一个完整的2D平台游戏物理系统：
- 玩家移动和跳跃
- 地面检测
- 平台碰撞
- 单向平台（从下方通过）

### 步骤1：创建玩家

```csharp
using UnityEngine;

public class PlayerPhysics : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    public float groundCheckRadius = 0.2f;
    public LayerMask groundLayer;
    
    private Rigidbody2D rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        // 地面检测
        isGrounded = Physics2D.OverlapCircle(transform.position, groundCheckRadius, groundLayer);

        // 移动
        float horizontal = Input.GetAxis("Horizontal");
        rb.velocity = new Vector2(horizontal * moveSpeed, rb.velocity.y);

        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Impulse);
        }
    }

    void OnDrawGizmos()
    {
        Gizmos.color = isGrounded ? Color.green : Color.red;
        Gizmos.DrawWireSphere(transform.position, groundCheckRadius);
    }
}
```

### 步骤2：创建平台

1. 创建Sprite对象
2. 添加Box Collider 2D
3. 添加Platform Effector 2D组件
4. 设置Use One Way为true

## 6.13 本章小结

在本章中，我们学习了：

- ✅ Rigidbody2D组件的使用
- ✅ 各种Collider2D类型
- ✅ 物理材质的创建和使用
- ✅ 碰撞检测和事件
- ✅ 触发器系统
- ✅ 碰撞过滤和图层
- ✅ 2D关节和效果器
- ✅ 射线检测和物理查询
- ✅ 物理设置和优化

## 6.14 实践练习

1. **创建物理对象**：创建不同Body Type的物体，观察行为
2. **添加碰撞体**：为对象添加不同类型的碰撞体
3. **创建物理材质**：创建冰面和弹跳材质
4. **实现碰撞检测**：编写碰撞和触发器事件处理
5. **完成平台游戏**：实现完整的2D平台游戏物理

## 6.15 下一步

下一章我们将学习3D游戏开发的基础知识，了解3D和2D开发的异同。

---

**提示**：理解物理系统是游戏开发的关键，多实验不同的设置，观察效果。
