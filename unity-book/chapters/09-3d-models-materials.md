# 第9章：3D模型和材质

## 9.1 3D模型概述

3D模型是3D游戏的基础，定义了游戏中的物体形状。

### 模型格式

- **FBX**：Unity推荐格式，支持动画
- **OBJ**：通用格式，简单易用
- **DAE**：Collada格式
- **Blend**：Blender文件（需要Blender安装）

## 9.2 导入3D模型

### 导入步骤

1. 将模型文件拖拽到Project窗口
2. Unity自动导入
3. 在Inspector中调整导入设置

### 模型导入设置

#### Model标签

- **Scale Factor**：缩放因子（调整模型大小）
- **Mesh Compression**：网格压缩（减小文件大小）
- **Read/Write Enabled**：允许运行时修改网格
- **Generate Colliders**：自动生成碰撞体
- **Import Materials**：导入材质
- **Import Animation**：导入动画

#### 优化设置

- **Optimize Mesh**：优化网格
- **Generate Secondary UV**：生成第二UV（用于光照贴图）
- **Keep Quads**：保持四边形

## 9.3 材质系统

### 什么是材质？

材质定义物体的外观：颜色、纹理、光照属性等。

### 创建材质

1. 右键Project窗口：`Create > Material`
2. 命名材质
3. 在Inspector中设置属性

### 标准材质属性

#### Albedo（基础颜色）

- **Color**：基础颜色
- **Texture**：基础纹理贴图
- **Tiling**：平铺次数
- **Offset**：偏移量

#### Metallic（金属度）

- **Metallic**：金属度（0=非金属，1=金属）
- **Smoothness**：光滑度（0=粗糙，1=光滑）

#### Normal Map（法线贴图）

- 增加表面细节
- 不增加多边形数量
- 创建凹凸效果

#### Emission（自发光）

- 物体自己发光
- 不受光照影响
- 适合发光物体

## 9.4 纹理类型

### 基础纹理

- **Albedo/Diffuse**：基础颜色纹理
- **Normal Map**：法线贴图
- **Specular**：高光贴图
- **Metallic**：金属度贴图

### 纹理导入设置

- **Texture Type**：纹理类型
- **Max Size**：最大尺寸
- **Compression**：压缩格式
- **Generate Mip Maps**：生成Mipmap

## 9.5 使用材质

### 应用材质

1. 创建材质
2. 设置纹理和属性
3. 拖拽材质到模型对象
4. 或拖拽到Mesh Renderer组件

### 代码中修改材质

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
        }
    }
}
```

## 9.6 材质实例

### 创建材质实例

材质实例允许修改材质而不影响原始材质：

```csharp
// 创建材质实例
Material instanceMaterial = meshRenderer.material;

// 修改实例属性
instanceMaterial.color = Color.red;
```

## 9.7 着色器（Shader）

### 什么是着色器？

着色器定义如何渲染材质，控制光照和渲染效果。

### Unity内置着色器

- **Standard**：标准PBR着色器
- **Unlit**：无光照着色器
- **Transparent**：透明着色器
- **Sprite**：2D精灵着色器

### 选择着色器

在材质的Shader下拉菜单中选择。

## 9.8 实践项目：创建材质库

### 项目目标

创建一个常用材质库：
- 金属材质
- 木材材质
- 石材材质
- 玻璃材质

### 步骤

1. 创建Materials文件夹
2. 创建各种材质
3. 设置合适的纹理和属性
4. 保存为预制体或资源

## 9.9 本章小结

在本章中，我们学习了：

- ✅ 3D模型导入和设置
- ✅ 材质创建和使用
- ✅ 纹理类型和应用
- ✅ 材质属性设置
- ✅ 着色器选择

## 9.10 实践练习

1. **导入模型**：导入一个3D模型到项目
2. **创建材质**：为模型创建合适的材质
3. **应用纹理**：为材质添加纹理贴图
4. **调整属性**：调整材质的光照属性
5. **创建材质库**：创建常用材质库

## 9.11 下一步

下一章我们将学习3D物理系统，了解3D碰撞和物理模拟。

---

**提示**：好的材质能让模型看起来更真实。多实验不同的设置，找到最佳效果。
