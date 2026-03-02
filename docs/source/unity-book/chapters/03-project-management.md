# 第3章：项目管理和资源系统

## 3.1 项目结构

理解Unity项目的文件结构是有效管理项目的基础。

### 项目文件夹

```
YourProject/
├── Assets/              # 游戏资源（最重要）
│   ├── Scenes/          # 场景文件
│   ├── Scripts/         # C#脚本
│   ├── Materials/       # 材质
│   ├── Textures/        # 纹理
│   ├── Models/          # 3D模型
│   ├── Prefabs/         # 预制体
│   ├── Animations/      # 动画
│   ├── Audio/           # 音频
│   └── Resources/       # 特殊资源文件夹
├── Packages/            # Unity包
├── ProjectSettings/     # 项目设置文件
└── UserSettings/        # 用户设置文件
```

### Assets文件夹

- **最重要**：所有游戏资源都在这里
- **版本控制**：需要提交到版本控制系统
- **组织方式**：建议按类型或功能组织子文件夹

### Packages文件夹

- Unity包管理系统
- 包含项目依赖的包
- 通常不需要手动编辑

### ProjectSettings文件夹

- 项目配置文件
- 包含项目设置、标签、图层等
- 需要提交到版本控制

## 3.2 场景管理

### 什么是场景？

场景（Scene）是Unity中组织游戏内容的基本单位。一个场景包含：
- 游戏对象
- 光照设置
- 摄像机设置
- 物理设置
- 其他场景特定的配置

### 创建场景

1. 菜单：`File > New Scene`
2. 选择场景模板（Basic、Empty等）
3. 保存场景：`File > Save` 或 `Ctrl+S`

### 场景操作

- **打开场景**：`File > Open Scene` 或双击Project窗口中的场景文件
- **保存场景**：`Ctrl+S`
- **另存为**：`File > Save As`
- **新建场景**：`Ctrl+N`

### 场景组织建议

- 为每个关卡创建单独的场景
- 使用描述性的场景名称（如：Level01、MainMenu）
- 将场景保存在 `Assets/Scenes/` 文件夹中

## 3.3 资源导入

### 支持的资源类型

#### 3D模型
- `.fbx`（推荐）
- `.obj`
- `.dae`
- `.3ds`
- `.blend`（Blender文件）

#### 纹理
- `.png`（推荐，支持透明）
- `.jpg`
- `.tga`
- `.psd`（Photoshop文件）

#### 音频
- `.wav`（未压缩，高质量）
- `.mp3`（压缩）
- `.ogg`（压缩，开源）

#### 视频
- `.mp4`
- `.mov`

### 导入资源

#### 方法1：拖拽导入
1. 从文件管理器拖拽文件到Project窗口
2. Unity自动导入并处理资源

#### 方法2：复制导入
1. 将文件复制到Assets文件夹
2. Unity自动检测并导入

### 导入设置

选中资源后，Inspector窗口会显示导入设置：

#### 纹理导入设置
- **Texture Type**：纹理类型（Default、Normal Map、Sprite等）
- **Max Size**：最大尺寸
- **Compression**：压缩格式
- **Generate Mip Maps**：生成Mipmap

#### 模型导入设置
- **Scale Factor**：缩放因子
- **Mesh Compression**：网格压缩
- **Generate Colliders**：自动生成碰撞体
- **Import Materials**：导入材质

#### 音频导入设置
- **Load Type**：加载类型（Decompress On Load、Compressed In Memory、Streaming）
- **Compression Format**：压缩格式
- **Quality**：质量设置

## 3.4 预制体（Prefabs）

### 什么是预制体？

预制体是可重用的游戏对象模板。创建一次后，可以在多个场景中使用。

### 创建预制体

1. 在场景中设置好游戏对象
2. 将对象从Hierarchy拖拽到Project窗口
3. 预制体创建完成

### 使用预制体

- **实例化**：从Project窗口拖拽预制体到Scene或Hierarchy
- **修改实例**：修改场景中的实例不会影响预制体
- **修改预制体**：在Project窗口选中预制体，在Inspector中修改

### 预制体变体（Prefab Variants）

- 基于现有预制体创建变体
- 变体继承原预制体的属性
- 可以覆盖部分属性

### 预制体模式（Prefab Mode）

- 双击预制体进入预制体模式
- 在预制体模式下编辑预制体本身
- 所有实例会自动更新

## 3.5 标签和图层

### 标签（Tags）

标签用于标识和分类游戏对象。

#### 创建标签
1. 菜单：`Edit > Project Settings > Tags and Layers`
2. 在Tags部分添加新标签

#### 使用标签
- 在Inspector中为对象设置标签
- 在代码中通过标签查找对象：`GameObject.FindGameObjectWithTag("TagName")`

### 图层（Layers）

图层用于组织对象，主要用于：
- 摄像机渲染控制
- 光照剔除
- 物理碰撞过滤

#### 创建图层
1. 菜单：`Edit > Project Settings > Tags and Layers`
2. 在Layers部分添加新图层

#### 使用图层
- 在Inspector中为对象设置图层
- 摄像机可以设置渲染哪些图层
- 物理系统可以设置哪些图层之间发生碰撞

## 3.6 资源组织最佳实践

### 文件夹结构建议

```
Assets/
├── Scenes/
│   ├── MainMenu.unity
│   ├── Level01.unity
│   └── Level02.unity
├── Scripts/
│   ├── Player/
│   ├── Enemy/
│   ├── UI/
│   └── Managers/
├── Prefabs/
│   ├── Characters/
│   ├── Environment/
│   └── UI/
├── Materials/
├── Textures/
│   ├── Characters/
│   ├── Environment/
│   └── UI/
├── Models/
├── Audio/
│   ├── Music/
│   └── SFX/
└── Animations/
```

### 命名规范

- **场景**：使用PascalCase（如：MainMenu、Level01）
- **脚本**：使用PascalCase（如：PlayerController、GameManager）
- **预制体**：使用PascalCase（如：Player、Enemy_01）
- **材质**：使用描述性名称（如：Player_Material、Grass_Material）
- **纹理**：使用描述性名称（如：Player_Diffuse、Player_Normal）

### 资源优化

- **纹理**：使用合适的尺寸，不要过大
- **模型**：优化多边形数量
- **音频**：使用压缩格式，设置合适的质量
- **预制体**：重用预制体，减少重复资源

## 3.7 版本控制

### Git集成

Unity支持Git版本控制。

#### 设置Git

1. 菜单：`Edit > Project Settings > Version Control`
2. 选择版本控制系统（Visible Meta Files）
3. 创建 `.gitignore` 文件：

```
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
[Uu]ser[Ss]ettings/

*.csproj
*.unityproj
*.sln
*.suo
*.tmp
*.user
*.userprefs
*.pidb
*.booproj
*.svd
*.pdb
*.mdb
*.opendb
*.VC.db

*.pidb.meta
*.pdb.meta
*.mdb.meta

sysinfo.txt

*.apk
*.aab
*.unitypackage
*.app
```

#### 提交规则

- **提交**：Assets文件夹、ProjectSettings文件夹
- **忽略**：Library文件夹、Temp文件夹、Build文件夹

## 3.8 包管理器（Package Manager）

### 打开包管理器

- 菜单：`Window > Package Manager`
- 快捷键：`Ctrl+9`（Windows）或`Cmd+9`（Mac）

### 包类型

- **Unity Registry**：Unity官方包
- **My Assets**：你购买的资源
- **In Project**：项目中已安装的包

### 常用包

- **2D Sprite**：2D精灵工具
- **2D Tilemap Extras**：2D瓦片地图扩展
- **Cinemachine**：摄像机系统
- **Post Processing**：后处理效果
- **ProBuilder**：程序化建模工具
- **TextMeshPro**：高级文本渲染

### 安装包

1. 打开Package Manager
2. 选择包源
3. 找到需要的包
4. 点击"Install"

## 3.9 资源商店（Asset Store）

### 访问资源商店

- 菜单：`Window > Asset Store`
- 或访问 [assetstore.unity.com](https://assetstore.unity.com)

### 使用资源

1. 在Asset Store中购买或下载免费资源
2. 在Unity中打开Asset Store窗口
3. 点击"My Assets"
4. 下载并导入资源

### 资源类型

- **3D Models**：3D模型
- **2D Sprites**：2D精灵
- **Audio**：音频资源
- **Scripts**：脚本和工具
- **Templates**：项目模板
- **Editor Extensions**：编辑器扩展

## 3.10 项目设置

### 打开项目设置

菜单：`Edit > Project Settings`

### 重要设置

#### Player设置
- **Company Name**：公司名称
- **Product Name**：产品名称
- **Default Icon**：默认图标
- **Resolution and Presentation**：分辨率和显示设置

#### Graphics设置
- **Color Space**：颜色空间（Gamma或Linear）
- **Shader Stripping**：着色器剥离

#### Physics设置
- **Gravity**：重力设置
- **Default Material**：默认物理材质

#### Audio设置
- **DSP Buffer Size**：DSP缓冲区大小
- **Virtual Voice Count**：虚拟声音数量

## 3.11 本章小结

在本章中，我们学习了：

- ✅ Unity项目的文件结构
- ✅ 场景的创建和管理
- ✅ 资源的导入和设置
- ✅ 预制体的创建和使用
- ✅ 标签和图层的管理
- ✅ 资源组织最佳实践
- ✅ 版本控制设置
- ✅ 包管理器和资源商店的使用
- ✅ 项目设置的重要性

## 3.12 实践练习

1. **组织项目**：创建合理的文件夹结构
2. **导入资源**：导入一些测试资源（纹理、模型等）
3. **创建预制体**：创建几个常用对象的预制体
4. **设置标签**：创建并使用标签分类对象
5. **探索包管理器**：浏览并安装一些有用的包

## 3.13 下一步

现在你已经掌握了项目管理和资源系统，接下来我们将开始学习2D游戏开发的基础知识。

---

**提示**：良好的项目组织习惯会让后续开发更加顺利，建议从一开始就建立清晰的文件夹结构。
