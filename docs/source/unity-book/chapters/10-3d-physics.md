# 第10章：3D物理和碰撞

## 10.1 3D物理系统概述

Unity的3D物理系统基于NVIDIA PhysX引擎，提供完整的3D物理模拟。

### 3D物理组件

- **Rigidbody**：刚体组件
- **Collider**：碰撞体组件
- **Joint**：关节组件
- **Character Controller**：角色控制器

## 10.2 Rigidbody组件

### 添加Rigidbody

1. 选中游戏对象
2. 添加Component > Physics > Rigidbody

### 主要属性

- **Mass**：质量
- **Drag**：阻力
- **Angular Drag**：角阻力
- **Use Gravity**：使用重力
- **Is Kinematic**：运动学模式
- **Constraints**：约束（冻结位置/旋转）

### 通过代码控制

```csharp
using UnityEngine;

public class PhysicsController : MonoBehaviour
{
    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        // 添加力
        if (Input.GetKeyDown(KeyCode.Space))
        {
            rb.AddForce(Vector3.up * 10f, ForceMode.Impulse);
        }

        // 设置速度
        rb.velocity = new Vector3(5f, rb.velocity.y, 0f);

        // 添加扭矩
        rb.AddTorque(Vector3.up * 10f);
    }
}
```

## 10.3 Collider组件

### 碰撞体类型

#### Box Collider（盒形碰撞体）

- 最常用
- 性能好
- 适合方形物体

#### Sphere Collider（球形碰撞体）

- 性能最好
- 适合球形物体

#### Capsule Collider（胶囊碰撞体）

- 适合角色
- 性能好

#### Mesh Collider（网格碰撞体）

- 精确碰撞
- 性能较差
- 适合复杂形状

### 添加碰撞体

1. 选中游戏对象
2. 添加Component > Physics > 选择碰撞体类型
3. 调整大小和位置

### 碰撞体设置

- **Is Trigger**：触发器模式
- **Material**：物理材质
- **Center**：中心偏移
- **Size**：大小

## 10.4 物理材质

### 创建物理材质

1. 右键Project窗口：`Create > Physic Material`
2. 设置属性

### 材质属性

- **Dynamic Friction**：动摩擦
- **Static Friction**：静摩擦
- **Bounciness**：弹性
- **Friction Combine**：摩擦组合方式
- **Bounce Combine**：弹性组合方式

## 10.5 碰撞检测

### 碰撞事件

```csharp
void OnCollisionEnter(Collision collision)
{
    Debug.Log("碰撞开始：" + collision.gameObject.name);
    
    // 获取碰撞点
    ContactPoint contact = collision.contacts[0];
    Vector3 point = contact.point;
    
    // 获取碰撞法线
    Vector3 normal = contact.normal;
}

void OnCollisionStay(Collision collision)
{
    // 持续碰撞
}

void OnCollisionExit(Collision collision)
{
    Debug.Log("碰撞结束");
}
```

### 触发器事件

```csharp
void OnTriggerEnter(Collider other)
{
    Debug.Log("进入触发器：" + other.gameObject.name);
}

void OnTriggerStay(Collider other)
{
    // 持续在触发器中
}

void OnTriggerExit(Collider other)
{
    Debug.Log("离开触发器");
}
```

## 10.6 射线检测

### 3D射线检测

```csharp
void Update()
{
    // 发射射线
    RaycastHit hit;
    if (Physics.Raycast(transform.position, Vector3.forward, out hit, 10f))
    {
        Debug.Log("击中：" + hit.collider.name);
        Debug.Log("距离：" + hit.distance);
        Debug.Log("点：" + hit.point);
    }
}
```

### 射线检测方法

```csharp
// 单次检测
RaycastHit hit;
Physics.Raycast(origin, direction, out hit, distance);

// 检测所有
RaycastHit[] hits = Physics.RaycastAll(origin, direction, distance);

// 球形检测
Collider[] colliders = Physics.OverlapSphere(position, radius);

// 盒形检测
Collider[] colliders = Physics.OverlapBox(position, size, rotation);
```

## 10.7 Character Controller

### 什么是Character Controller？

Character Controller是专门用于角色的控制器，不依赖物理系统。

### 添加Character Controller

1. 选中角色对象
2. 添加Component > Physics > Character Controller

### 主要属性

- **Radius**：半径
- **Height**：高度
- **Center**：中心
- **Slope Limit**：坡度限制
- **Step Offset**：台阶高度
- **Skin Width**：皮肤宽度

### 使用Character Controller

```csharp
using UnityEngine;

public class CharacterMovement : MonoBehaviour
{
    private CharacterController controller;
    public float speed = 5f;
    public float jumpHeight = 2f;
    public float gravity = -9.81f;
    
    private Vector3 velocity;
    private bool isGrounded;

    void Start()
    {
        controller = GetComponent<CharacterController>();
    }

    void Update()
    {
        // 地面检测
        isGrounded = controller.isGrounded;
        if (isGrounded && velocity.y < 0)
        {
            velocity.y = -2f;
        }

        // 移动
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        Vector3 move = transform.right * horizontal + transform.forward * vertical;
        controller.Move(move * speed * Time.deltaTime);

        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            velocity.y = Mathf.Sqrt(jumpHeight * -2f * gravity);
        }

        // 重力
        velocity.y += gravity * Time.deltaTime;
        controller.Move(velocity * Time.deltaTime);
    }
}
```

## 10.8 物理设置

### 全局物理设置

菜单：`Edit > Project Settings > Physics`

#### 重要设置

- **Gravity**：全局重力（默认Y=-9.81）
- **Default Material**：默认物理材质
- **Solver Iteration Count**：求解器迭代次数
- **Solver Velocity Iterations**：速度迭代次数

## 10.9 实践项目：3D物理场景

### 项目目标

创建一个3D物理场景：
- 可控制的角色
- 可交互的物体
- 物理效果演示

### 步骤

1. 创建场景
2. 添加地面和障碍物
3. 创建可控制的角色
4. 添加可交互的物体
5. 测试物理效果

## 10.10 本章小结

在本章中，我们学习了：

- ✅ Rigidbody组件
- ✅ 各种Collider类型
- ✅ 物理材质
- ✅ 碰撞检测
- ✅ 射线检测
- ✅ Character Controller
- ✅ 物理设置

## 10.11 实践练习

1. **创建物理对象**：创建不同物理属性的物体
2. **添加碰撞体**：为对象添加碰撞体
3. **实现碰撞检测**：编写碰撞事件处理
4. **使用Character Controller**：创建可控制的角色
5. **完成物理场景**：创建完整的物理演示场景

## 10.12 下一步

下一章我们将学习光照和渲染系统，创建更真实的3D场景。

---

**提示**：理解物理系统是3D游戏开发的基础，多实验不同的设置。
