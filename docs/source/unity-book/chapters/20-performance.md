# 第20章：性能优化

## 20.1 性能优化概述

性能优化是游戏开发的重要部分，直接影响游戏的流畅度和用户体验。

### 性能指标

- **FPS**：帧率（目标60 FPS）
- **Draw Call**：绘制调用（越少越好）
- **内存使用**：内存占用
- **CPU使用率**：CPU占用

## 20.2 使用Profiler

### 打开Profiler

菜单：`Window > Analysis > Profiler`
快捷键：`Ctrl+7`（Windows）或`Cmd+7`（Mac）

### Profiler功能

- **CPU Usage**：CPU使用情况
- **Rendering**：渲染统计
- **Memory**：内存使用
- **Audio**：音频统计
- **Physics**：物理统计

### 分析性能

1. 运行游戏
2. 查看Profiler数据
3. 找出性能瓶颈
4. 优化问题代码

## 20.3 渲染优化

### 减少Draw Call

#### 合并网格

```csharp
using UnityEngine;

public class MeshCombiner : MonoBehaviour
{
    void CombineMeshes()
    {
        MeshFilter[] meshFilters = GetComponentsInChildren<MeshFilter>();
        CombineInstance[] combine = new CombineInstance[meshFilters.Length];

        for (int i = 0; i < meshFilters.Length; i++)
        {
            combine[i].mesh = meshFilters[i].sharedMesh;
            combine[i].transform = meshFilters[i].transform.localToWorldMatrix;
        }

        Mesh combinedMesh = new Mesh();
        combinedMesh.CombineMeshes(combine);
        GetComponent<MeshFilter>().mesh = combinedMesh;
    }
}
```

#### 使用图集

- 合并多个纹理到一个图集
- 减少纹理切换
- 使用Sprite Atlas（2D）

#### 批处理

- **Static Batching**：静态批处理
- **Dynamic Batching**：动态批处理
- **GPU Instancing**：GPU实例化

### 优化纹理

1. **使用合适的尺寸**：不要使用过大的纹理
2. **压缩纹理**：使用压缩格式
3. **使用Mipmap**：自动生成Mipmap
4. **减少纹理数量**：合并纹理

### LOD（细节级别）

1. 创建不同细节级别的模型
2. 添加LOD Group组件
3. 根据距离自动切换

```csharp
// LOD Group自动处理
// 距离越远，使用越简单的模型
```

## 20.4 脚本优化

### 避免在Update中执行昂贵操作

```csharp
// ❌ 不好
void Update()
{
    GameObject player = GameObject.Find("Player");
}

// ✅ 好：缓存引用
private GameObject player;

void Start()
{
    player = GameObject.Find("Player");
}
```

### 使用对象池

```csharp
public class ObjectPool : MonoBehaviour
{
    public GameObject prefab;
    public int poolSize = 10;
    private Queue<GameObject> pool = new Queue<GameObject>();

    void Start()
    {
        for (int i = 0; i < poolSize; i++)
        {
            GameObject obj = Instantiate(prefab);
            obj.SetActive(false);
            pool.Enqueue(obj);
        }
    }

    public GameObject Get()
    {
        if (pool.Count > 0)
        {
            GameObject obj = pool.Dequeue();
            obj.SetActive(true);
            return obj;
        }
        return Instantiate(prefab);
    }

    public void Return(GameObject obj)
    {
        obj.SetActive(false);
        pool.Enqueue(obj);
    }
}
```

### 减少GC分配

```csharp
// ❌ 不好：每帧创建新对象
void Update()
{
    Vector3 pos = new Vector3(0, 0, 0);
}

// ✅ 好：重用对象
private Vector3 pos = Vector3.zero;

void Update()
{
    pos.Set(0, 0, 0);
}
```

### 使用协程替代Update

```csharp
// ❌ 不好：每帧检查
void Update()
{
    if (Time.time % 1f < Time.deltaTime)
    {
        DoSomething();
    }
}

// ✅ 好：使用协程
void Start()
{
    StartCoroutine(DoSomethingEverySecond());
}

IEnumerator DoSomethingEverySecond()
{
    while (true)
    {
        DoSomething();
        yield return new WaitForSeconds(1f);
    }
}
```

## 20.5 物理优化

### 优化物理设置

1. **减少物理对象**：只对需要的对象使用物理
2. **使用Static**：固定物体使用Static
3. **优化碰撞体**：使用简单碰撞体
4. **减少迭代次数**：在可接受范围内降低

### 物理设置

菜单：`Edit > Project Settings > Physics`

- **Solver Iteration Count**：求解器迭代次数（4-6）
- **Solver Velocity Iterations**：速度迭代次数（1-2）

## 20.6 音频优化

### 音频设置

1. **使用压缩格式**：OGG Vorbis
2. **合理设置Load Type**：
   - 短音效：Decompress On Load
   - 中等长度：Compressed In Memory
   - 长音频：Streaming
3. **限制同时播放**：避免同时播放太多音频

## 20.7 内存优化

### 及时销毁对象

```csharp
// 使用Destroy而非SetActive(false)
Destroy(gameObject);

// 延迟销毁
Destroy(gameObject, 5f);
```

### 卸载未使用的资源

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

// 卸载未使用的资源
Resources.UnloadUnusedAssets();
System.GC.Collect();
```

### 使用Resources.Load谨慎

- Resources文件夹会在启动时加载所有资源
- 使用AssetBundle替代

## 20.8 光照优化

### 使用光照贴图

- 静态物体使用烘焙光照
- 减少实时光源数量
- 使用光照探针

### 阴影优化

1. **降低阴影分辨率**
2. **限制阴影距离**
3. **使用软阴影**（性能更好）

## 20.9 移动平台优化

### 移动平台特殊优化

1. **降低纹理分辨率**
2. **减少Draw Call**
3. **优化着色器**：使用移动平台优化的着色器
4. **减少粒子效果**
5. **优化物理**：减少物理对象

### 构建设置

- **Managed Stripping Level**：代码剥离
- **Compression Format**：压缩格式
- **Script Call Optimization**：脚本调用优化

## 20.10 性能检查清单

### 优化检查清单

- [ ] 使用Profiler分析性能
- [ ] 减少Draw Call
- [ ] 优化纹理大小
- [ ] 使用对象池
- [ ] 减少GC分配
- [ ] 优化物理设置
- [ ] 使用光照贴图
- [ ] 优化阴影
- [ ] 测试不同设备
- [ ] 监控内存使用

## 20.11 实践项目：性能优化

### 项目目标

优化一个现有项目：
- 分析性能瓶颈
- 应用优化技巧
- 测试优化效果

### 步骤

1. 使用Profiler分析
2. 找出性能问题
3. 应用优化方案
4. 测试优化效果
5. 对比优化前后

## 20.12 本章小结

在本章中，我们学习了：

- ✅ 使用Profiler分析性能
- ✅ 渲染优化技巧
- ✅ 脚本优化方法
- ✅ 物理优化
- ✅ 音频优化
- ✅ 内存优化
- ✅ 光照优化
- ✅ 移动平台优化
- ✅ 性能检查清单

## 20.13 实践练习

1. **使用Profiler**：分析你的项目性能
2. **优化渲染**：减少Draw Call
3. **优化脚本**：使用对象池和缓存
4. **优化物理**：调整物理设置
5. **测试优化**：对比优化前后效果

## 20.14 下一步

下一章我们将学习项目发布，将游戏发布到不同平台。

---

**提示**：性能优化是一个持续的过程，要在效果和性能之间找到平衡。
