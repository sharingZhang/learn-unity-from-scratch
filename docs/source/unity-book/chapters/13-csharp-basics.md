# 第13章：C#脚本编程基础

## 13.1 C#简介

### 什么是C#？

C#是Microsoft开发的面向对象编程语言，Unity使用C#作为主要脚本语言。

### 为什么使用C#？

- **类型安全**：编译时检查类型错误
- **面向对象**：支持类、继承、多态等
- **性能好**：编译为IL，运行时JIT编译
- **Unity支持**：Unity官方推荐

## 13.2 创建脚本

### 创建脚本文件

1. 右键Project窗口：`Create > C# Script`
2. 命名脚本（使用PascalCase，如：PlayerController）
3. 双击打开脚本编辑器

### 脚本模板

Unity自动生成的脚本模板：

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    void Start()
    {
        // 初始化代码，游戏开始时调用一次
    }

    void Update()
    {
        // 每帧调用的代码
    }
}
```

### 附加脚本到对象

1. 选中游戏对象
2. 在Inspector中点击"Add Component"
3. 搜索脚本名称
4. 或直接拖拽脚本到Inspector窗口

## 13.3 基本语法

### 变量

#### 变量声明

```csharp
// 基本类型
int health = 100;
float speed = 5.5f;  // float需要f后缀
double precision = 3.14159;
bool isAlive = true;
string name = "Player";
char letter = 'A';

// Unity类型
Vector3 position = new Vector3(0, 0, 0);
GameObject player;
Transform transform;
```

#### 访问修饰符

- **public**：公共，可在Inspector中编辑
- **private**：私有，只能在类内访问
- **protected**：受保护，子类可访问
- **internal**：程序集内可访问

```csharp
public int publicVariable;      // 在Inspector中可见
private int privateVariable;    // 仅在类内可见
[SerializeField] private int serializedVariable;  // 私有但Inspector可见
```

### 常量

```csharp
public const float GRAVITY = 9.81f;
private const int MAX_HEALTH = 100;
```

### 数组和列表

```csharp
// 数组
public int[] numbers = new int[5];
public GameObject[] enemies;

// 列表（需要using System.Collections.Generic）
using System.Collections.Generic;

public List<GameObject> items = new List<GameObject>();

// 添加元素
items.Add(newItem);
items.Remove(oldItem);
```

## 13.4 Unity生命周期方法

### MonoBehaviour生命周期

Unity脚本继承自MonoBehaviour，有以下生命周期方法：

#### Awake()

```csharp
void Awake()
{
    // 对象创建时调用，早于Start
    // 用于初始化，不依赖其他对象
}
```

#### Start()

```csharp
void Start()
{
    // 第一帧更新前调用一次
    // 用于初始化，可以访问其他对象
}
```

#### Update()

```csharp
void Update()
{
    // 每帧调用一次
    // 用于游戏逻辑、输入处理
}
```

#### FixedUpdate()

```csharp
void FixedUpdate()
{
    // 固定时间间隔调用（默认0.02秒）
    // 用于物理计算
}
```

#### LateUpdate()

```csharp
void LateUpdate()
{
    // Update之后调用
    // 用于摄像机跟随等
}
```

#### OnEnable() / OnDisable()

```csharp
void OnEnable()
{
    // 对象启用时调用
}

void OnDisable()
{
    // 对象禁用时调用
}
```

#### OnDestroy()

```csharp
void OnDestroy()
{
    // 对象销毁时调用
    // 用于清理资源
}
```

### 执行顺序

```
Awake → OnEnable → Start → FixedUpdate → Update → LateUpdate → OnDisable → OnDestroy
```

## 13.5 获取组件

### GetComponent

```csharp
using UnityEngine;

public class ComponentExample : MonoBehaviour
{
    private Rigidbody rb;
    private Collider col;

    void Start()
    {
        // 获取组件
        rb = GetComponent<Rigidbody>();
        col = GetComponent<Collider>();

        // 获取其他对象的组件
        GameObject otherObject = GameObject.Find("OtherObject");
        Rigidbody otherRb = otherObject.GetComponent<Rigidbody>();

        // 如果组件不存在，返回null
        if (rb != null)
        {
            rb.mass = 10f;
        }
    }
}
```

### GetComponentInChildren / GetComponentInParent

```csharp
// 获取子对象的组件
Rigidbody childRb = GetComponentInChildren<Rigidbody>();

// 获取父对象的组件
Rigidbody parentRb = GetComponentInParent<Rigidbody>();
```

### GetComponents（多个组件）

```csharp
// 获取所有匹配的组件
Rigidbody[] rbs = GetComponents<Rigidbody>();

// 获取子对象的所有组件
Rigidbody[] childRbs = GetComponentsInChildren<Rigidbody>();
```

## 13.6 查找对象

### GameObject.Find()

```csharp
// 按名称查找（性能较差，不推荐频繁使用）
GameObject player = GameObject.Find("Player");
```

### GameObject.FindWithTag()

```csharp
// 按标签查找（推荐）
GameObject player = GameObject.FindWithTag("Player");

// 查找所有带标签的对象
GameObject[] enemies = GameObject.FindGameObjectsWithTag("Enemy");
```

### 通过引用

```csharp
// 在Inspector中拖拽赋值（推荐）
public GameObject player;

// 或通过Transform
public Transform playerTransform;
```

## 13.7 条件语句

### if-else

```csharp
if (health > 0)
{
    // 健康
}
else if (health == 0)
{
    // 死亡
}
else
{
    // 异常情况
}
```

### switch

```csharp
switch (state)
{
    case "Idle":
        // 待机状态
        break;
    case "Walk":
        // 行走状态
        break;
    case "Run":
        // 跑步状态
        break;
    default:
        // 默认情况
        break;
}
```

### 三元运算符

```csharp
int maxHealth = isPlayer ? 100 : 50;
string status = health > 0 ? "Alive" : "Dead";
```

## 13.8 循环

### for循环

```csharp
// 遍历数组
for (int i = 0; i < enemies.Length; i++)
{
    Debug.Log(enemies[i].name);
}

// 倒序
for (int i = enemies.Length - 1; i >= 0; i--)
{
    Destroy(enemies[i]);
}
```

### foreach循环

```csharp
// 遍历列表
foreach (GameObject enemy in enemies)
{
    Debug.Log(enemy.name);
}
```

### while循环

```csharp
int count = 0;
while (count < 10)
{
    Debug.Log(count);
    count++;
}
```

## 13.9 函数

### 函数定义

```csharp
// 无返回值
void MovePlayer()
{
    transform.Translate(Vector3.forward);
}

// 有返回值
int GetHealth()
{
    return health;
}

// 带参数
void TakeDamage(int damage)
{
    health -= damage;
}

// 带默认参数
void SetSpeed(float speed = 5f)
{
    moveSpeed = speed;
}
```

### 函数重载

```csharp
void Attack()
{
    Attack(10);  // 调用重载版本
}

void Attack(int damage)
{
    // 攻击逻辑
}
```

## 13.10 类和对象

### 类定义

```csharp
public class Player
{
    public int health;
    public string name;

    public void TakeDamage(int damage)
    {
        health -= damage;
    }
}
```

### 继承

```csharp
public class Enemy : MonoBehaviour
{
    public int health = 100;

    public virtual void TakeDamage(int damage)
    {
        health -= damage;
    }
}

public class Boss : Enemy
{
    public override void TakeDamage(int damage)
    {
        // Boss的特殊逻辑
        base.TakeDamage(damage / 2);  // 只受一半伤害
    }
}
```

### 静态成员

```csharp
public class GameManager : MonoBehaviour
{
    public static int score = 0;
    public static GameManager instance;

    void Awake()
    {
        instance = this;  // 单例模式
    }

    public static void AddScore(int points)
    {
        score += points;
    }
}

// 使用
GameManager.AddScore(10);
int currentScore = GameManager.score;
```

## 13.11 协程（Coroutines）

### 什么是协程？

协程允许暂停执行，在下一帧或指定时间后继续。

### 创建协程

```csharp
using System.Collections;

IEnumerator WaitAndDoSomething()
{
    yield return new WaitForSeconds(2f);  // 等待2秒
    Debug.Log("2秒后执行");
}

// 启动协程
StartCoroutine(WaitAndDoSomething());
```

### 协程用途

```csharp
// 延迟执行
IEnumerator DelayedDestroy()
{
    yield return new WaitForSeconds(5f);
    Destroy(gameObject);
}

// 循环执行
IEnumerator SpawnEnemies()
{
    while (true)
    {
        SpawnEnemy();
        yield return new WaitForSeconds(2f);
    }
}

// 等待条件
IEnumerator WaitUntilCondition()
{
    yield return new WaitUntil(() => player != null);
    Debug.Log("玩家已生成");
}
```

### 停止协程

```csharp
Coroutine coroutine;

void Start()
{
    coroutine = StartCoroutine(MyCoroutine());
}

void Stop()
{
    StopCoroutine(coroutine);
    // 或
    StopAllCoroutines();
}
```

## 13.12 事件和委托

### 委托（Delegate）

```csharp
// 定义委托
public delegate void HealthChangedDelegate(int newHealth);

// 声明事件
public event HealthChangedDelegate OnHealthChanged;

// 触发事件
void TakeDamage(int damage)
{
    health -= damage;
    OnHealthChanged?.Invoke(health);  // ?. 空条件运算符
}
```

### Unity事件

```csharp
using UnityEngine.Events;

[System.Serializable]
public class HealthEvent : UnityEvent<int> { }

public HealthEvent OnHealthChanged;

void TakeDamage(int damage)
{
    health -= damage;
    OnHealthChanged.Invoke(health);
}
```

## 13.13 属性（Properties）

### 基本属性

```csharp
private int health;

public int Health
{
    get { return health; }
    set 
    { 
        health = Mathf.Clamp(value, 0, 100);  // 限制范围
    }
}

// 使用
player.Health = 150;  // 自动限制为100
int currentHealth = player.Health;
```

### 自动属性

```csharp
public int Health { get; set; }

// 只读属性
public int MaxHealth { get; private set; }
```

## 13.14 命名空间

### 使用命名空间

```csharp
using UnityEngine;
using System.Collections;
using System.Collections.Generic;

namespace MyGame
{
    public class Player : MonoBehaviour
    {
        // 代码
    }
}
```

## 13.15 调试

### Debug.Log()

```csharp
Debug.Log("普通信息");
Debug.LogWarning("警告信息");
Debug.LogError("错误信息");

// 带对象引用
Debug.Log("玩家位置", gameObject);
```

### 断点调试

1. 在代码行左侧点击设置断点
2. 运行游戏
3. 执行到断点时暂停
4. 查看变量值

### 条件编译

```csharp
#if UNITY_EDITOR
    Debug.Log("仅在编辑器中显示");
#endif

#if DEVELOPMENT_BUILD
    Debug.Log("仅在开发版本中显示");
#endif
```

## 13.16 实践项目：完整的玩家控制器

```csharp
using UnityEngine;

public class CompletePlayerController : MonoBehaviour
{
    [Header("Movement")]
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    
    [Header("Health")]
    public int maxHealth = 100;
    private int currentHealth;
    
    private Rigidbody rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        currentHealth = maxHealth;
    }

    void Update()
    {
        HandleMovement();
        HandleJump();
    }

    void HandleMovement()
    {
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        
        Vector3 movement = new Vector3(horizontal, 0, vertical).normalized;
        transform.Translate(movement * moveSpeed * Time.deltaTime);
    }

    void HandleJump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        currentHealth = Mathf.Max(0, currentHealth);
        
        if (currentHealth <= 0)
        {
            Die();
        }
    }

    void Die()
    {
        Debug.Log("玩家死亡");
        // 死亡逻辑
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

## 13.17 本章小结

在本章中，我们学习了：

- ✅ C#基本语法
- ✅ Unity生命周期方法
- ✅ 获取组件和查找对象
- ✅ 条件语句和循环
- ✅ 函数定义和使用
- ✅ 类和对象
- ✅ 协程
- ✅ 事件和委托
- ✅ 属性
- ✅ 调试技巧

## 13.18 实践练习

1. **创建脚本**：创建几个基本脚本，熟悉语法
2. **生命周期**：在不同生命周期方法中添加日志，观察执行顺序
3. **获取组件**：练习获取和操作组件
4. **创建控制器**：创建一个完整的玩家控制器
5. **使用协程**：创建延迟执行的协程

## 13.19 下一步

下一章我们将深入学习Unity的脚本API，学习如何使用Unity提供的各种功能。

---

**提示**：编程需要大量练习，多写代码，多实验，遇到问题查阅文档和社区。
