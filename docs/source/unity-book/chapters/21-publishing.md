# 第21章：项目发布

## 21.1 发布概述

发布是将Unity项目构建为可执行文件的过程，可以在不同平台上运行。

### 支持的平台

- **PC**：Windows、Mac、Linux
- **移动设备**：iOS、Android
- **游戏主机**：PlayStation、Xbox、Nintendo Switch
- **Web**：WebGL
- **VR/AR**：Oculus、HTC Vive等

## 21.2 构建设置

### 打开构建设置

菜单：`File > Build Settings`
快捷键：`Ctrl+Shift+B`（Windows）或`Cmd+Shift+B`（Mac）

### Build Settings窗口

- **Platform**：目标平台列表
- **Scenes In Build**：构建中包含的场景
- **Player Settings**：玩家设置按钮
- **Build**：构建按钮
- **Build And Run**：构建并运行按钮

### 添加场景到构建

1. 打开要构建的场景
2. 点击"Add Open Scenes"按钮
3. 或拖拽场景文件到Scenes In Build列表
4. 调整场景顺序（第一个是启动场景）

## 21.3 Player Settings（玩家设置）

### 打开Player Settings

- 菜单：`Edit > Project Settings > Player`
- 或在Build Settings中点击"Player Settings"

### 重要设置

#### Company Name和Product Name

- **Company Name**：公司名称
- **Product Name**：产品名称（显示在窗口标题）

#### Icon（图标）

- **Default Icon**：默认图标
- 不同平台可以设置不同图标

#### Resolution and Presentation

**Windows/Mac/Linux**：
- **Default Screen Width/Height**：默认分辨率
- **Fullscreen Mode**：全屏模式
- **Run In Background**：后台运行

**Android/iOS**：
- **Default Orientation**：默认方向
- **Allowed Orientations**：允许的方向

#### Other Settings

**Rendering**：
- **Color Space**：颜色空间（Gamma/Linear）
- **Auto Graphics API**：自动图形API

**Configuration**：
- **Scripting Backend**：脚本后端（Mono/IL2CPP）
- **Api Compatibility Level**：API兼容级别

## 21.4 发布到PC平台

### Windows发布

1. **选择平台**：在Build Settings中选择"PC, Mac & Linux Standalone"
2. **选择Target Platform**：Windows
3. **设置Player Settings**：
   - 设置图标
   - 设置分辨率
   - 设置公司名和产品名
4. **构建**：点击"Build"
5. **选择保存位置**：选择输出文件夹
6. **等待构建完成**

### Mac发布

1. 选择平台：PC, Mac & Linux Standalone
2. 选择Target Platform：Mac OS X
3. 构建步骤同Windows

### Linux发布

1. 选择平台：PC, Mac & Linux Standalone
2. 选择Target Platform：Linux
3. 构建步骤同Windows

## 21.5 发布到移动平台

### Android发布

#### 前置要求

1. **安装Android SDK**：
   - 下载Android Studio
   - 安装Android SDK
   - 在Unity Hub中配置SDK路径

2. **安装JDK**：
   - 下载并安装JDK
   - 在Unity中配置路径：`Edit > Preferences > External Tools`

#### 构建步骤

1. **选择平台**：在Build Settings中选择"Android"
2. **Switch Platform**：点击"Switch Platform"（首次需要）
3. **Player Settings**：
   - **Package Name**：包名（如：com.company.game）
   - **Minimum API Level**：最低API级别
   - **Target API Level**：目标API级别
   - **Scripting Backend**：IL2CPP（推荐）
4. **Keystore**：设置签名密钥
5. **构建**：点击"Build"
6. **生成APK**：选择保存位置，生成APK文件

#### 签名设置

1. 在Player Settings中找到"Publishing Settings"
2. 创建或选择Keystore
3. 设置密钥别名和密码

### iOS发布

#### 前置要求

1. **Mac电脑**：iOS开发需要Mac
2. **Xcode**：安装Xcode
3. **Apple Developer账号**：发布到App Store需要

#### 构建步骤

1. **选择平台**：在Build Settings中选择"iOS"
2. **Switch Platform**：点击"Switch Platform"
3. **Player Settings**：
   - **Bundle Identifier**：包标识符
   - **Target minimum iOS Version**：最低iOS版本
   - **Scripting Backend**：IL2CPP
4. **构建**：点击"Build"
5. **生成Xcode项目**：选择保存位置
6. **在Xcode中打开**：打开生成的Xcode项目
7. **在Xcode中构建**：配置签名和证书，构建并发布

## 21.6 发布到WebGL

### WebGL概述

WebGL允许在浏览器中运行Unity游戏，无需插件。

### 构建步骤

1. **选择平台**：在Build Settings中选择"WebGL"
2. **Switch Platform**：点击"Switch Platform"
3. **Player Settings**：
   - **Compression Format**：压缩格式（Gzip/Brotli）
   - **Data Caching**：数据缓存
4. **构建**：点击"Build"
5. **生成文件**：生成HTML、JS、数据文件
6. **部署**：上传到Web服务器

### WebGL限制

- **文件大小**：需要压缩，减小文件大小
- **性能**：性能不如原生应用
- **功能限制**：某些功能不可用（如文件系统）

## 21.7 构建优化

### 减小构建大小

1. **压缩纹理**：使用压缩纹理格式
2. **优化模型**：减少多边形数量
3. **音频压缩**：使用压缩音频格式
4. **移除未使用资源**：使用Asset Bundle或Resources清理
5. **代码剥离**：启用Managed Code Stripping

### 性能优化

1. **优化Draw Call**：合并网格，使用批处理
2. **优化光照**：使用光照贴图
3. **优化物理**：减少物理对象
4. **优化脚本**：避免每帧执行昂贵操作

### 构建设置优化

**Player Settings > Other Settings**：
- **Managed Stripping Level**：代码剥离级别
- **Compression Format**：压缩格式
- **Script Call Optimization**：脚本调用优化

## 21.8 版本管理

### 版本号

在Player Settings中设置：
- **Version**：版本号（如：1.0.0）
- **Build**：构建号（自动递增）

### 版本命名规范

通常使用：主版本号.次版本号.修订号（如：1.2.3）

## 21.9 发布检查清单

### 发布前检查

- [ ] 测试所有功能
- [ ] 检查所有场景
- [ ] 测试不同分辨率
- [ ] 检查音频设置
- [ ] 优化性能
- [ ] 设置图标和启动画面
- [ ] 设置公司名和产品名
- [ ] 检查构建设置
- [ ] 测试构建版本

### 平台特定检查

**PC**：
- [ ] 测试全屏和窗口模式
- [ ] 测试不同分辨率
- [ ] 检查输入设备支持

**移动设备**：
- [ ] 测试不同屏幕尺寸
- [ ] 测试横屏和竖屏
- [ ] 检查触摸输入
- [ ] 测试性能（低端设备）

**WebGL**：
- [ ] 测试不同浏览器
- [ ] 检查文件大小
- [ ] 测试加载时间

## 21.10 发布流程示例

### 完整发布流程

1. **准备阶段**：
   - 完成所有功能开发
   - 进行充分测试
   - 优化性能

2. **配置阶段**：
   - 设置Player Settings
   - 配置图标和启动画面
   - 设置版本号

3. **构建阶段**：
   - 选择目标平台
   - 添加场景到构建
   - 执行构建

4. **测试阶段**：
   - 测试构建版本
   - 修复问题
   - 重新构建

5. **发布阶段**：
   - 上传到平台（Steam、App Store等）
   - 填写商店信息
   - 提交审核

## 21.11 常见问题

### 构建失败

- **检查错误信息**：查看Console窗口的错误
- **检查平台模块**：确保安装了目标平台的模块
- **检查设置**：检查Player Settings配置

### 构建文件过大

- **压缩资源**：使用压缩格式
- **移除未使用资源**：清理项目
- **优化设置**：调整压缩和剥离设置

### 性能问题

- **性能分析**：使用Profiler分析性能
- **优化资源**：优化纹理、模型等
- **代码优化**：优化脚本性能

## 21.12 本章小结

在本章中，我们学习了：

- ✅ 发布概述和平台支持
- ✅ 构建设置和场景管理
- ✅ Player Settings配置
- ✅ PC平台发布
- ✅ 移动平台发布（Android/iOS）
- ✅ WebGL发布
- ✅ 构建优化
- ✅ 版本管理
- ✅ 发布检查清单
- ✅ 发布流程

## 21.13 实践练习

1. **配置Player Settings**：设置图标、公司名等
2. **构建PC版本**：构建Windows/Mac/Linux版本
3. **测试构建版本**：测试构建的可执行文件
4. **优化构建**：尝试减小构建文件大小
5. **准备发布**：完成发布检查清单

## 21.14 下一步

恭喜！你已经完成了Unity游戏开发的学习。继续实践，创建更多项目，不断提升技能！

---

**提示**：发布前一定要充分测试，确保游戏在不同设备和环境下都能正常运行。
