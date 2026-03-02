# 第22章：最佳实践和常见问题

## 22.1 代码组织最佳实践

### 命名规范

#### 类名

```csharp
// 使用PascalCase
public class PlayerController { }
public class GameManager { }
public class EnemyAI { }
```

#### 变量和方法名

```csharp
// 私有变量：camelCase，下划线前缀
private int health;
private float moveSpeed;

// 公共变量：camelCase
public int maxHealth;
public float jumpForce;

// 方法：PascalCase
public void TakeDamage() { }
public int GetHealth() { return health; }
```

#### 常量

```csharp
// 全大写，下划线分隔
public const float GRAVITY = 9.81f;
public const int MAX_ENEMIES = 10;
```

### 代码结构

#### 单一职责原则

```csharp
// ❌ 不好：一个类做太多事情
public class Player : MonoBehaviour
{
    void Move() { }
    void Attack() { }
    void UpdateUI() { }
    void PlaySound() { }
}

// ✅ 好：职责分离
public class PlayerController : MonoBehaviour
{
    void Move() { }
}

public class PlayerCombat : MonoBehaviour
{
    void Attack() { }
}

public class UIManager : MonoBehaviour
{
    void UpdateUI() { }
}
```

#### 使用接口

```csharp
public interface IDamageable
{
    void TakeDamage(int damage);
}

public class Player : MonoBehaviour, IDamageable
{
    public void TakeDamage(int damage) { }
}

public class Enemy : MonoBehaviour, IDamageable
{
    public void TakeDamage(int damage) { }
}
```

## 22.2 性能优化最佳实践

### 避免在Update中执行昂贵操作

```csharp
// ❌ 不好：每帧查找对象
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

### 减少Draw Call

1. **合并网格**：使用Mesh.CombineMeshes
2. **使用图集**：合并纹理
3. **批处理**：使用Static Batching和Dynamic Batching
4. **GPU Instancing**：实例化相同对象

### 优化物理

```csharp
// 使用FixedUpdate进行物理计算
void FixedUpdate()
{
    rb.velocity = movement * speed;
}

// 合理设置物理迭代次数
// Edit > Project Settings > Physics
// Velocity Iterations: 4-6
// Position Iterations: 1-2
```

## 22.3 内存管理最佳实践

### 避免不必要的分配

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

### 及时销毁对象

```csharp
// 使用Destroy而非SetActive(false)
Destroy(gameObject);

// 延迟销毁
Destroy(gameObject, 5f);
```

### 使用StringBuilder

```csharp
using System.Text;

// ❌ 不好：字符串拼接
string text = "Score: " + score + " / " + maxScore;

// ✅ 好：使用StringBuilder
StringBuilder sb = new StringBuilder();
sb.Append("Score: ");
sb.Append(score);
sb.Append(" / ");
sb.Append(maxScore);
string text = sb.ToString();
```

## 22.4 常见问题和解决方案

### 问题1：对象移动不平滑

**原因**：在Update中使用transform.position直接赋值

**解决方案**：
```csharp
// ❌ 不好
transform.position += Vector3.forward * speed;

// ✅ 好：使用Time.deltaTime
transform.position += Vector3.forward * speed * Time.deltaTime;

// ✅ 更好：使用Rigidbody
rb.velocity = Vector3.forward * speed;
```

### 问题2：碰撞检测不准确

**原因**：物理更新和逻辑更新不同步

**解决方案**：
```csharp
// 物理相关操作在FixedUpdate中
void FixedUpdate()
{
    rb.MovePosition(transform.position + movement * Time.fixedDeltaTime);
}
```

### 问题3：UI元素位置不正确

**原因**：锚点设置不正确

**解决方案**：
1. 正确设置RectTransform的锚点
2. 使用Canvas Scaler适配不同分辨率
3. 测试不同分辨率

### 问题4：音频播放延迟

**原因**：音频加载方式不当

**解决方案**：
```csharp
// 短音效：Decompress On Load
// 中等长度：Compressed In Memory
// 长音频：Streaming
```

### 问题5：场景加载慢

**原因**：资源过大或同步加载

**解决方案**：
```csharp
// 使用异步加载
IEnumerator LoadSceneAsync(string sceneName)
{
    AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);
    
    while (!operation.isDone)
    {
        float progress = operation.progress;
        // 更新加载进度UI
        yield return null;
    }
}
```

## 22.5 调试技巧

### 使用Debug.Log

```csharp
// 条件编译
#if UNITY_EDITOR
    Debug.Log("仅在编辑器中显示");
#endif

#if DEVELOPMENT_BUILD
    Debug.Log("仅在开发版本中显示");
#endif
```

### 使用断点

1. 在代码行左侧点击设置断点
2. 运行游戏
3. 执行到断点时暂停
4. 查看变量值

### 使用Profiler

1. 菜单：`Window > Analysis > Profiler`
2. 运行游戏
3. 查看性能数据：
   - CPU使用率
   - 内存使用
   - 渲染统计
   - 物理统计

### 使用Console窗口

- 查看日志、警告、错误
- 过滤日志类型
- 双击错误跳转到代码

## 22.6 版本控制最佳实践

### .gitignore配置

```
[Ll]ibrary/
[Tt]emp/
[Oo]bj/
[Bb]uild/
[Bb]uilds/
[Ll]ogs/
[Uu]user[Ss]ettings/

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

### 提交规则

- **提交**：Assets文件夹、ProjectSettings文件夹
- **忽略**：Library文件夹、Temp文件夹、Build文件夹
- **协作**：使用Unity Collaborate或Git LFS处理大文件

## 22.7 项目组织最佳实践

### 文件夹结构

```
Assets/
├── Scenes/
├── Scripts/
│   ├── Player/
│   ├── Enemy/
│   ├── Managers/
│   └── Utils/
├── Prefabs/
├── Materials/
├── Textures/
├── Models/
├── Audio/
└── Resources/  # 谨慎使用
```

### 使用命名空间

```csharp
namespace MyGame.Player
{
    public class PlayerController : MonoBehaviour
    {
        // 代码
    }
}

namespace MyGame.Enemy
{
    public class EnemyAI : MonoBehaviour
    {
        // 代码
    }
}
```

## 22.8 测试最佳实践

### 单元测试

```csharp
using UnityEngine;
using UnityEngine.TestTools;
using NUnit.Framework;

public class PlayerTests
{
    [Test]
    public void PlayerTakesDamage()
    {
        GameObject playerObj = new GameObject();
        PlayerController player = playerObj.AddComponent<PlayerController>();
        player.maxHealth = 100;
        player.Start();
        
        player.TakeDamage(10);
        Assert.AreEqual(90, player.currentHealth);
    }
}
```

### 游戏测试检查清单

- [ ] 测试所有功能
- [ ] 测试不同分辨率
- [ ] 测试不同设备
- [ ] 测试边界情况
- [ ] 性能测试
- [ ] 内存泄漏测试

## 22.9 文档和注释

### 代码注释

```csharp
/// <summary>
/// 玩家控制器，处理玩家移动和输入
/// </summary>
public class PlayerController : MonoBehaviour
{
    /// <summary>
    /// 玩家移动速度
    /// </summary>
    public float moveSpeed = 5f;
    
    /// <summary>
    /// 处理玩家移动
    /// </summary>
    void HandleMovement()
    {
        // 实现代码
    }
}
```

### 项目文档

- README.md：项目说明
- 设计文档：游戏设计文档
- API文档：代码API文档
- 更新日志：版本更新记录

## 22.10 团队协作最佳实践

### 代码规范

1. 统一代码风格
2. 使用代码审查
3. 编写清晰的注释
4. 遵循命名规范

### 资源管理

1. 统一资源命名
2. 使用版本控制
3. 避免冲突
4. 及时沟通

### 沟通

1. 定期会议
2. 使用项目管理工具
3. 记录问题和解决方案
4. 分享知识

## 22.11 常见错误和避免方法

### 错误1：忘记取消事件订阅

```csharp
// ❌ 不好：可能导致内存泄漏
void OnEnable()
{
    SomeEvent += Handler;
}

// ✅ 好：在OnDisable中取消订阅
void OnEnable()
{
    SomeEvent += Handler;
}

void OnDisable()
{
    SomeEvent -= Handler;
}
```

### 错误2：在Update中查找对象

```csharp
// ❌ 不好：性能问题
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

### 错误3：忘记检查null

```csharp
// ❌ 不好：可能空引用异常
void Update()
{
    player.transform.position = target.position;
}

// ✅ 好：检查null
void Update()
{
    if (player != null)
    {
        player.transform.position = target.position;
    }
}
```

## 22.12 持续学习

### 学习资源

- **Unity官方文档**：https://docs.unity3d.com/
- **Unity Learn**：https://learn.unity.com/
- **Unity论坛**：https://forum.unity.com/
- **GitHub**：查看开源项目
- **YouTube**：Unity教程视频

### 实践建议

1. **完成项目**：完成完整的游戏项目
2. **阅读代码**：阅读优秀的开源项目
3. **参与社区**：参与Unity社区讨论
4. **持续更新**：关注Unity新版本和新功能

## 22.13 本章小结

在本章中，我们学习了：

- ✅ 代码组织最佳实践
- ✅ 性能优化技巧
- ✅ 内存管理方法
- ✅ 常见问题和解决方案
- ✅ 调试技巧
- ✅ 版本控制实践
- ✅ 项目组织方法
- ✅ 测试最佳实践
- ✅ 文档和注释
- ✅ 团队协作建议
- ✅ 常见错误避免
- ✅ 持续学习建议

## 22.14 总结

Unity游戏开发是一个持续学习的过程。掌握最佳实践可以帮助你：

- 编写更好的代码
- 提高开发效率
- 避免常见错误
- 创建更好的游戏

记住：**实践是最好的老师**。多写代码，多完成项目，不断学习和改进！

---

**恭喜你完成了Unity游戏开发的学习！现在开始创建你的第一个游戏吧！** 🎮
