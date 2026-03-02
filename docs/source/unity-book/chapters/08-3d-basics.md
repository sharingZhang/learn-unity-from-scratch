# 第8章：3D游戏开发基础

## 8.1 3D vs 2D

### 3D游戏的特点

- **三维空间**：X、Y、Z三个维度
- **透视效果**：距离影响物体大小
- **更复杂**：需要处理更多概念（光照、阴影、材质等）
- **更真实**：可以创建更真实的游戏世界

### Unity的3D支持

Unity最初就是为3D游戏开发的，提供了完整的3D工具集：
- 3D模型导入和渲染
- 物理系统
- 光照和阴影系统
- 后处理效果
- 地形系统

## 8.2 创建3D项目

### 创建新项目

1. 打开Unity Hub
2. 点击"新建项目"
3. 选择"3D"模板
4. 设置项目名称和位置
5. 点击"创建"

### 3D项目设置

创建3D项目后，Unity会自动设置：
- **摄像机**：设置为透视（Perspective）模式
- **光照**：默认场景光照
- **物理**：3D物理系统

## 8.3 3D坐标系统

### 世界坐标

- **X轴**：左右（右为正）
- **Y轴**：上下（上为正）
- **Z轴**：前后（前为正）

### 本地坐标

- 相对于父对象的坐标
- 子对象使用父对象的坐标系

### 坐标转换

```csharp
using UnityEngine;

public class CoordinateExample : MonoBehaviour
{
    void Update()
    {
        // 世界坐标转本地坐标
        Vector3 localPos = transform.InverseTransformPoint(worldPos);
        
        // 本地坐标转世界坐标
        Vector3 worldPos = transform.TransformPoint(localPos);
        
        // 方向转换
        Vector3 worldDir = transform.TransformDirection(localDir);
    }
}
```

## 8.4 3D基本对象

### 创建3D对象

菜单：`GameObject > 3D Object`

#### 基本几何体

- **Cube**：立方体
- **Sphere**：球体
- **Capsule**：胶囊体
- **Cylinder**：圆柱体
- **Plane**：平面
- **Quad**：四边形

### 基本对象用途

- **Cube**：墙壁、平台、箱子
- **Sphere**：球、角色头部
- **Capsule**：角色身体
- **Cylinder**：柱子、树干
- **Plane**：地面、水面
- **Quad**：广告牌、UI背景

## 8.5 Transform组件

Transform是所有游戏对象都有的组件，控制对象的位置、旋转和缩放。

### 位置（Position）

```csharp
// 设置位置
transform.position = new Vector3(0, 0, 0);

// 移动
transform.Translate(Vector3.forward * Time.deltaTime * 5f);

// 获取位置
Vector3 pos = transform.position;
```

### 旋转（Rotation）

```csharp
// 设置旋转（欧拉角）
transform.rotation = Quaternion.Euler(0, 90, 0);

// 旋转
transform.Rotate(Vector3.up * 90f * Time.deltaTime);

// 看向目标
transform.LookAt(target.position);

// 平滑旋转
transform.rotation = Quaternion.Slerp(transform.rotation, targetRotation, Time.deltaTime);
```

### 缩放（Scale）

```csharp
// 设置缩放
transform.localScale = new Vector3(2, 2, 2);

// 缩放
transform.localScale *= 1.1f;
```

### 父子关系

```csharp
// 设置父对象
child.transform.SetParent(parent.transform);

// 移除父对象
transform.SetParent(null);

// 获取子对象
Transform child = transform.GetChild(0);

// 遍历所有子对象
foreach (Transform child in transform)
{
    Debug.Log(child.name);
}
```

## 8.6 3D摄像机

### 透视摄像机

3D游戏使用透视（Perspective）摄像机：
- 有透视效果
- 物体大小随距离变化
- 更真实

### 摄像机属性

选中Main Camera，在Inspector中：

- **Projection**：投影模式
  - **Perspective**：透视（3D游戏）
  - **Orthographic**：正交（2D游戏）
  
- **Field of View**：视野角度（FOV）
  - 影响可见范围
  - 通常60-90度
  
- **Clipping Planes**：裁剪平面
  - **Near**：近裁剪平面
  - **Far**：远裁剪平面
  
- **Viewport Rect**：视口矩形
  - 用于分屏或多摄像机

### 摄像机控制脚本

```csharp
using UnityEngine;

public class CameraController : MonoBehaviour
{
    public Transform target;
    public float distance = 10f;
    public float height = 5f;
    public float rotationSpeed = 2f;
    
    private float currentRotationX;
    private float currentRotationY;

    void LateUpdate()
    {
        // 鼠标旋转
        currentRotationX += Input.GetAxis("Mouse X") * rotationSpeed;
        currentRotationY -= Input.GetAxis("Mouse Y") * rotationSpeed;
        currentRotationY = Mathf.Clamp(currentRotationY, -20f, 60f);

        // 计算位置
        Quaternion rotation = Quaternion.Euler(currentRotationY, currentRotationX, 0);
        Vector3 direction = rotation * Vector3.back;
        Vector3 position = target.position + direction * distance + Vector3.up * height;

        // 设置摄像机
        transform.position = position;
        transform.LookAt(target.position);
    }
}
```

## 8.7 3D模型导入

### 支持的格式

- **FBX**：最常用，Unity推荐
- **OBJ**：通用格式
- **DAE**：Collada格式
- **3DS**：3ds Max格式
- **Blend**：Blender文件（需要Blender安装）

### 导入模型

1. 将模型文件拖拽到Project窗口
2. Unity自动导入
3. 在Inspector中调整导入设置

### 模型导入设置

#### Model标签

- **Scale Factor**：缩放因子
- **Mesh Compression**：网格压缩
- **Read/Write Enabled**：允许读写（运行时修改网格）
- **Generate Colliders**：自动生成碰撞体
- **Import Materials**：导入材质
- **Import Animation**：导入动画

#### Rig标签（动画）

- **Animation Type**：
  - **None**：无动画
  - **Legacy**：旧版动画
  - **Generic**：通用动画
  - **Humanoid**：人形动画
  
- **Avatar Definition**：Avatar定义

#### Animation标签

- **Import Animation**：导入动画
- **Animations**：动画剪辑列表
- **Clips**：设置每个动画剪辑

### 使用模型

1. 从Project窗口拖拽模型到Scene
2. 或拖拽到Hierarchy窗口
3. Unity创建包含模型的游戏对象

## 8.8 材质和纹理

### 材质（Material）

材质定义物体的外观：
- 颜色
- 纹理
- 光照属性
- 渲染属性

#### 创建材质

1. 右键Project窗口：`Create > Material`
2. 命名材质
3. 在Inspector中设置属性

#### 材质属性

- **Albedo**：基础颜色/纹理
- **Metallic**：金属度
- **Smoothness**：光滑度
- **Normal Map**：法线贴图
- **Emission**：自发光
- **Tiling**：平铺
- **Offset**：偏移

### 纹理（Texture）

纹理是贴在模型表面的图像。

#### 导入纹理

1. 拖拽图像文件到Project窗口
2. 在Inspector中设置：
   - **Texture Type**：纹理类型
   - **Max Size**：最大尺寸
   - **Compression**：压缩格式

#### 纹理类型

- **Default**：默认纹理
- **Normal Map**：法线贴图
- **Sprite**：2D精灵
- **Cursor**：光标
- **Cookie**：Cookie纹理
- **Lightmap**：光照贴图

### 应用材质

1. 创建材质
2. 设置纹理和属性
3. 拖拽材质到模型对象
4. 或拖拽到模型的Mesh Renderer组件

## 8.9 Mesh Renderer组件

Mesh Renderer负责渲染3D模型。

### 主要属性

- **Materials**：材质数组
- **Shadow Casting**：投射阴影
- **Receive Shadows**：接收阴影
- **Light Probes**：光照探针
- **Reflection Probes**：反射探针

### 使用示例

```csharp
using UnityEngine;

public class MaterialChanger : MonoBehaviour
{
    public Material newMaterial;
    private MeshRenderer meshRenderer;

    void Start()
    {
        meshRenderer = GetComponent<MeshRenderer>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 更换材质
            meshRenderer.material = newMaterial;
            
            // 或更换材质数组
            Material[] materials = meshRenderer.materials;
            materials[0] = newMaterial;
            meshRenderer.materials = materials;
        }
    }
}
```

## 8.10 3D物理系统

### Rigidbody组件

3D物理使用Rigidbody组件（不是Rigidbody2D）。

#### 主要属性

- **Mass**：质量
- **Drag**：阻力
- **Angular Drag**：角阻力
- **Use Gravity**：使用重力
- **Is Kinematic**：是否为运动学
- **Constraints**：约束（冻结位置/旋转）

### Collider组件

3D碰撞体类型：

- **Box Collider**：盒形碰撞体
- **Sphere Collider**：球形碰撞体
- **Capsule Collider**：胶囊碰撞体
- **Mesh Collider**：网格碰撞体（复杂形状）
- **Wheel Collider**：车轮碰撞体
- **Terrain Collider**：地形碰撞体

### 物理材质

创建3D物理材质：

1. 右键Project窗口：`Create > Physic Material`
2. 设置属性：
   - **Dynamic Friction**：动摩擦
   - **Static Friction**：静摩擦
   - **Bounciness**：弹性
   - **Friction Combine**：摩擦组合方式
   - **Bounce Combine**：弹性组合方式

## 8.11 3D输入和移动

### 获取输入

```csharp
using UnityEngine;

public class PlayerController3D : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    private Rigidbody rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        // 获取输入
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");

        // 移动（相对于摄像机方向）
        Vector3 moveDirection = new Vector3(horizontal, 0, vertical).normalized;
        transform.Translate(moveDirection * moveSpeed * Time.deltaTime);

        // 跳跃
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
    }

    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }
}
```

### 使用物理移动

```csharp
using UnityEngine;

public class PhysicsMovement : MonoBehaviour
{
    public float moveSpeed = 5f;
    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        // 获取输入
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");

        // 移动
        Vector3 movement = new Vector3(horizontal, 0, vertical);
        rb.MovePosition(transform.position + movement * moveSpeed * Time.fixedDeltaTime);
    }
}
```

## 8.12 实践项目：简单的3D场景

### 项目目标

创建一个简单的3D场景：
- 地面
- 几个3D对象
- 可控制的角色
- 摄像机跟随

### 步骤1：创建场景

1. 创建新场景
2. 创建Plane作为地面
3. 缩放Plane（Scale: 10, 1, 10）
4. 添加材质和纹理

### 步骤2：添加对象

1. 创建几个Cube、Sphere等对象
2. 布置在场景中
3. 添加不同的材质

### 步骤3：创建玩家

1. 创建Capsule作为玩家
2. 添加Rigidbody组件
3. 添加Capsule Collider组件
4. 添加移动脚本（参考8.11节）

### 步骤4：设置摄像机

1. 调整摄像机位置
2. 添加摄像机跟随脚本（参考8.6节）

## 8.13 本章小结

在本章中，我们学习了：

- ✅ 3D游戏开发的基础概念
- ✅ 3D坐标系统
- ✅ 3D基本对象
- ✅ Transform组件
- ✅ 3D摄像机设置
- ✅ 3D模型导入
- ✅ 材质和纹理
- ✅ Mesh Renderer组件
- ✅ 3D物理系统
- ✅ 3D输入和移动

## 8.14 实践练习

1. **创建3D场景**：创建一个包含多个3D对象的场景
2. **导入模型**：导入一个3D模型到项目中
3. **创建材质**：创建几个不同的材质并应用到对象
4. **添加物理**：为对象添加3D物理组件
5. **创建控制器**：创建一个可以移动的3D角色

## 8.15 下一步

下一章我们将深入学习3D模型、材质和光照系统，创建更真实的3D场景。

---

**提示**：3D开发比2D复杂，需要理解空间概念。多实践，熟悉3D坐标系统和变换操作。
