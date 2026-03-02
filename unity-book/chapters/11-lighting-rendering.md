# 第11章：光照和渲染

## 11.1 光照系统概述

光照系统决定了场景的视觉效果，影响游戏的氛围和真实感。

### 光照类型

- **Directional Light**：平行光（太阳光）
- **Point Light**：点光源
- **Spot Light**：聚光灯
- **Area Light**：区域光

## 11.2 光源类型

### Directional Light（平行光）

- 模拟太阳光
- 无限远光源
- 平行光线
- 适合室外场景

**属性**：
- **Color**：颜色
- **Intensity**：强度
- **Shadows**：阴影类型

### Point Light（点光源）

- 从一点向四周发光
- 有衰减
- 适合灯泡、火把等

**属性**：
- **Range**：范围
- **Intensity**：强度
- **Attenuation**：衰减

### Spot Light（聚光灯）

- 圆锥形光照
- 有方向和角度
- 适合手电筒、舞台灯

**属性**：
- **Range**：范围
- **Spot Angle**：角度
- **Inner Spot Angle**：内角

### Area Light（区域光）

- 从区域发光
- 只用于烘焙
- 适合大范围柔和光照

## 11.3 阴影系统

### 阴影类型

- **No Shadows**：无阴影
- **Hard Shadows**：硬阴影
- **Soft Shadows**：软阴影

### 阴影设置

在光源的Shadows属性中设置：
- **Type**：阴影类型
- **Strength**：强度
- **Resolution**：分辨率
- **Bias**：偏移

### 接收阴影

在Mesh Renderer中：
- **Cast Shadows**：投射阴影
- **Receive Shadows**：接收阴影

## 11.4 光照模式

### Realtime（实时光照）

- 运行时计算
- 性能消耗大
- 可以动态变化

### Baked（烘焙光照）

- 预计算光照
- 性能好
- 不能动态变化

### Mixed（混合光照）

- 结合实时和烘焙
- 平衡性能和效果

## 11.5 光照贴图

### 什么是光照贴图？

光照贴图是预计算的光照信息，存储在纹理中。

### 烘焙光照贴图

1. 菜单：`Window > Rendering > Lighting`
2. 设置光照模式为Baked或Mixed
3. 点击"Generate Lighting"
4. Unity自动生成光照贴图

### 光照贴图设置

- **Lightmap Resolution**：分辨率
- **Lightmap Size**：大小
- **Lightmap Padding**：填充
- **Compress Lightmaps**：压缩

## 11.6 环境光照

### Skybox（天空盒）

- 场景背景
- 影响环境光照
- 创建：`Create > Material`，Shader选择Skybox

### Ambient Light（环境光）

在Lighting窗口中设置：
- **Sky Color**：天空颜色
- **Equator Color**：赤道颜色
- **Ground Color**：地面颜色
- **Intensity**：强度

## 11.7 渲染管线

### Built-in Render Pipeline（内置渲染管线）

- Unity默认渲染管线
- 兼容性好
- 功能完整

### Universal Render Pipeline（URP）

- 通用渲染管线
- 性能优化
- 适合移动平台

### High Definition Render Pipeline（HDRP）

- 高清渲染管线
- 高质量效果
- 适合PC和主机

## 11.8 后处理效果

### Post Processing Stack

1. 安装Post Processing包
2. 创建Post Process Volume
3. 添加效果：
   - **Bloom**：泛光
   - **Color Grading**：颜色分级
   - **Vignette**：暗角
   - **Depth of Field**：景深
   - **Motion Blur**：运动模糊

### 使用后处理

1. 创建Post Process Volume
2. 添加Profile
3. 设置效果参数
4. 摄像机添加Post Process Layer组件

## 11.9 实践项目：光照场景

### 项目目标

创建一个展示不同光照效果的场景：
- 不同光源类型
- 阴影效果
- 光照贴图
- 后处理效果

### 步骤

1. 创建场景
2. 添加不同光源
3. 设置阴影
4. 烘焙光照
5. 添加后处理效果

## 11.10 性能优化

### 光照优化

1. **使用光照贴图**：静态物体使用烘焙
2. **减少实时光源**：限制实时光源数量
3. **优化阴影**：降低阴影分辨率
4. **使用光照探针**：动态物体使用光照探针

### 渲染优化

1. **减少Draw Call**：合并网格
2. **使用LOD**：距离细节级别
3. **遮挡剔除**：使用Occlusion Culling
4. **合理使用后处理**：避免过度使用

## 11.11 本章小结

在本章中，我们学习了：

- ✅ 光源类型和属性
- ✅ 阴影系统
- ✅ 光照模式
- ✅ 光照贴图
- ✅ 环境光照
- ✅ 渲染管线
- ✅ 后处理效果
- ✅ 性能优化

## 11.12 实践练习

1. **创建光源**：在场景中添加不同类型的光源
2. **设置阴影**：配置阴影效果
3. **烘焙光照**：烘焙场景光照
4. **添加后处理**：添加后处理效果
5. **优化性能**：优化光照和渲染性能

## 11.13 下一步

下一章我们将完成一个完整的3D游戏项目，整合所有学到的知识。

---

**提示**：好的光照能大大提升游戏的视觉效果。多实验不同的设置，找到最佳效果。
