# 第14章：Unity脚本API详解

## 14.1 Unity API概述

Unity提供了丰富的API（应用程序编程接口），用于访问引擎的各种功能。

### 主要命名空间

- **UnityEngine**：核心Unity功能
- **UnityEngine.UI**：UI系统
- **UnityEngine.SceneManagement**：场景管理
- **UnityEngine.Audio**：音频系统
- **System.Collections**：集合类

## 14.2 GameObject类

### 创建和销毁

```csharp
// 创建空对象
GameObject obj = new GameObject("MyObject");

// 从预制体实例化
GameObject instance = Instantiate(prefab, position, rotation);

// 销毁对象
Destroy(gameObject);
Destroy(gameObject, 2f);  // 2秒后销毁

// 立即销毁（下一帧）
DestroyImmediate(gameObject);
```

### 查找对象

```csharp
// 按名称查找
GameObject obj = GameObject.Find("ObjectName");

// 按标签查找
GameObject player = GameObject.FindWithTag("Player");
GameObject[] enemies = GameObject.FindGameObjectsWithTag("Enemy");

// 按类型查找组件
PlayerController player = FindObjectOfType<PlayerController>();
PlayerController[] players = FindObjectsOfType<PlayerController>();
```

### 对象激活

```csharp
// 激活/禁用对象
gameObject.SetActive(true);
gameObject.SetActive(false);

// 检查是否激活
bool isActive = gameObject.activeSelf;
bool isActiveInHierarchy = gameObject.activeInHierarchy;
```

## 14.3 Transform类

### 位置、旋转、缩放

```csharp
// 位置
transform.position = new Vector3(0, 0, 0);
Vector3 pos = transform.position;
transform.localPosition = new Vector3(0, 0, 0);

// 旋转
transform.rotation = Quaternion.identity;
transform.eulerAngles = new Vector3(0, 90, 0);
transform.localRotation = Quaternion.Euler(0, 90, 0);

// 缩放
transform.localScale = new Vector3(2, 2, 2);
Vector3 scale = transform.localScale;
```

### 移动和旋转

```csharp
// 移动
transform.Translate(Vector3.forward * Time.deltaTime * speed);
transform.Translate(Vector3.right * speed, Space.World);

// 旋转
transform.Rotate(Vector3.up * 90f * Time.deltaTime);
transform.RotateAround(target.position, Vector3.up, 90f * Time.deltaTime);

// 看向目标
transform.LookAt(target);
transform.LookAt(target.position);
```

### 父子关系

```csharp
// 设置父对象
transform.SetParent(parentTransform);
transform.SetParent(null);  // 移除父对象

// 获取子对象
Transform child = transform.GetChild(0);
int childCount = transform.childCount;

// 遍历子对象
foreach (Transform child in transform)
{
    Debug.Log(child.name);
}
```

## 14.4 Time类

### 时间属性

```csharp
// 时间缩放（用于暂停）
Time.timeScale = 0f;  // 暂停
Time.timeScale = 1f;  // 正常

// 时间信息
float time = Time.time;  // 游戏开始后的时间
float deltaTime = Time.deltaTime;  // 上一帧到这一帧的时间
float fixedDeltaTime = Time.fixedDeltaTime;  // 固定时间步长
float unscaledTime = Time.unscaledTime;  // 不受timeScale影响的时间
float realtimeSinceStartup = Time.realtimeSinceStartup;  // 真实时间

// 帧率
int frameCount = Time.frameCount;  // 总帧数
float timeSinceLevelLoad = Time.timeSinceLevelLoad;  // 场景加载后的时间
```

### 使用示例

```csharp
void Update()
{
    // 每秒执行一次
    if (Time.time % 1f < Time.deltaTime)
    {
        Debug.Log("每秒执行");
    }
    
    // 平滑移动
    transform.position += Vector3.forward * speed * Time.deltaTime;
}
```

## 14.5 Input类

### 键盘输入

```csharp
// 按键检测
if (Input.GetKeyDown(KeyCode.Space))
{
    // 按下瞬间
}

if (Input.GetKey(KeyCode.Space))
{
    // 按住
}

if (Input.GetKeyUp(KeyCode.Space))
{
    // 释放瞬间
}

// 虚拟轴
float horizontal = Input.GetAxis("Horizontal");  // -1 到 1，平滑
float vertical = Input.GetAxis("Vertical");

float horizontalRaw = Input.GetAxisRaw("Horizontal");  // -1, 0, 1，不平滑
```

### 鼠标输入

```csharp
// 鼠标按键
if (Input.GetMouseButtonDown(0))  // 左键
{
    // 点击
}

if (Input.GetMouseButton(1))  // 右键
{
    // 按住
}

// 鼠标位置
Vector3 mousePos = Input.mousePosition;  // 屏幕坐标
Vector3 worldPos = Camera.main.ScreenToWorldPoint(mousePos);  // 世界坐标

// 鼠标移动
float mouseX = Input.GetAxis("Mouse X");
float mouseY = Input.GetAxis("Mouse Y");
```

### 触摸输入

```csharp
// 触摸数量
int touchCount = Input.touchCount;

// 触摸信息
if (Input.touchCount > 0)
{
    Touch touch = Input.GetTouch(0);
    Vector2 touchPos = touch.position;
    
    if (touch.phase == TouchPhase.Began)
    {
        // 触摸开始
    }
}
```

## 14.6 Physics类

### 2D物理

```csharp
using UnityEngine;

// 射线检测
RaycastHit2D hit = Physics2D.Raycast(origin, direction, distance);
RaycastHit2D[] hits = Physics2D.RaycastAll(origin, direction, distance);

// 圆形检测
Collider2D collider = Physics2D.OverlapCircle(position, radius);
Collider2D[] colliders = Physics2D.OverlapCircleAll(position, radius);

// 矩形检测
Collider2D collider = Physics2D.OverlapArea(pointA, pointB);

// 忽略碰撞
Physics2D.IgnoreLayerCollision(8, 9);
Physics2D.IgnoreCollision(collider1, collider2);
```

### 3D物理

```csharp
// 射线检测
RaycastHit hit;
if (Physics.Raycast(origin, direction, out hit, distance))
{
    Debug.Log("击中：" + hit.collider.name);
}

// 球形检测
Collider[] colliders = Physics.OverlapSphere(position, radius);

// 盒形检测
Collider[] colliders = Physics.OverlapBox(position, size, rotation);
```

## 14.7 Random类

### 随机数生成

```csharp
// 随机浮点数（0到1）
float value = Random.value;

// 随机整数
int randomInt = Random.Range(0, 10);  // 0到9
float randomFloat = Random.Range(0f, 10f);  // 0到10

// 随机方向
Vector3 randomDirection = Random.insideUnitSphere;
Vector2 randomDirection2D = Random.insideUnitCircle;

// 随机颜色
Color randomColor = Random.ColorHSV();

// 设置随机种子
Random.InitState(12345);
```

## 14.8 Mathf类

### 数学函数

```csharp
// 绝对值
float abs = Mathf.Abs(-5f);  // 5

// 限制范围
float clamped = Mathf.Clamp(value, 0f, 100f);
float clamped01 = Mathf.Clamp01(value);  // 0到1

// 插值
float lerped = Mathf.Lerp(a, b, t);  // 线性插值
float smooth = Mathf.SmoothStep(0f, 1f, t);  // 平滑插值

// 最大值/最小值
float max = Mathf.Max(a, b, c);
float min = Mathf.Min(a, b, c);

// 幂和平方根
float power = Mathf.Pow(2f, 3f);  // 8
float sqrt = Mathf.Sqrt(16f);  // 4

// 三角函数
float sin = Mathf.Sin(angle);
float cos = Mathf.Cos(angle);
float tan = Mathf.Tan(angle);

// 角度转换
float degrees = Mathf.Rad2Deg * radians;
float radians = Mathf.Deg2Rad * degrees;
```

## 14.9 Vector3和Vector2

### Vector3操作

```csharp
// 创建向量
Vector3 v1 = new Vector3(1, 2, 3);
Vector3 v2 = Vector3.zero;  // (0, 0, 0)
Vector3 v3 = Vector3.one;  // (1, 1, 1)
Vector3 forward = Vector3.forward;  // (0, 0, 1)
Vector3 up = Vector3.up;  // (0, 1, 0)
Vector3 right = Vector3.right;  // (1, 0, 0)

// 向量运算
Vector3 sum = v1 + v2;
Vector3 diff = v1 - v2;
Vector3 scaled = v1 * 2f;
float dot = Vector3.Dot(v1, v2);  // 点积
Vector3 cross = Vector3.Cross(v1, v2);  // 叉积

// 向量属性
float magnitude = v1.magnitude;  // 长度
float sqrMagnitude = v1.sqrMagnitude;  // 长度的平方（性能更好）
Vector3 normalized = v1.normalized;  // 归一化向量

// 距离
float distance = Vector3.Distance(pos1, pos2);
float sqrDistance = Vector3.SqrMagnitude(pos1 - pos2);  // 性能更好

// 插值
Vector3 lerped = Vector3.Lerp(start, end, t);
Vector3 slerped = Vector3.Slerp(start, end, t);  // 球面插值
```

### Vector2操作

Vector2的操作与Vector3类似，只是没有Z分量。

## 14.10 Quaternion类

### 四元数操作

```csharp
// 创建旋转
Quaternion rotation = Quaternion.identity;  // 无旋转
Quaternion rot = Quaternion.Euler(0, 90, 0);  // 欧拉角转四元数

// 旋转运算
Quaternion combined = rot1 * rot2;  // 组合旋转
Vector3 rotated = rotation * Vector3.forward;  // 旋转向量

// 插值
Quaternion lerped = Quaternion.Lerp(start, end, t);
Quaternion slerped = Quaternion.Slerp(start, end, t);  // 球面插值（推荐）

// 看向目标
Quaternion lookRotation = Quaternion.LookRotation(direction);
transform.rotation = Quaternion.LookRotation(target.position - transform.position);
```

## 14.11 Camera类

### 摄像机操作

```csharp
// 获取主摄像机
Camera mainCam = Camera.main;

// 坐标转换
Vector3 screenPos = mainCam.WorldToScreenPoint(worldPos);
Vector3 worldPos = mainCam.ScreenToWorldPoint(screenPos);
Ray ray = mainCam.ScreenPointToRay(screenPos);

// 视口坐标（0到1）
Vector3 viewportPos = mainCam.WorldToViewportPoint(worldPos);
Vector3 worldFromViewport = mainCam.ViewportToWorldPoint(viewportPos);

// 摄像机属性
float fov = mainCam.fieldOfView;
float orthographicSize = mainCam.orthographicSize;
Rect viewport = mainCam.rect;
```

## 14.12 Debug类

### 调试输出

```csharp
// 日志
Debug.Log("普通信息");
Debug.LogWarning("警告");
Debug.LogError("错误");

// 带对象引用（点击可定位）
Debug.Log("消息", gameObject);

// 绘制线条（仅在Scene视图可见）
Debug.DrawLine(start, end, Color.red);
Debug.DrawRay(origin, direction, Color.green, 2f);  // 持续2秒

// 断言
Debug.Assert(condition, "条件不满足");
```

## 14.13 协程和IEnumerator

### 协程基础

```csharp
using System.Collections;

IEnumerator MyCoroutine()
{
    yield return null;  // 等待一帧
    yield return new WaitForSeconds(2f);  // 等待2秒
    yield return new WaitForFixedUpdate();  // 等待固定更新
    yield return new WaitForEndOfFrame();  // 等待帧结束
    yield return new WaitUntil(() => condition);  // 等待条件
    yield return new WaitWhile(() => condition);  // 等待条件为false
    
    // 启动其他协程
    yield return StartCoroutine(OtherCoroutine());
}

// 启动协程
StartCoroutine(MyCoroutine());
```

## 14.14 场景管理

### SceneManager

```csharp
using UnityEngine.SceneManagement;

// 加载场景
SceneManager.LoadScene("SceneName");
SceneManager.LoadScene(0);  // 按索引加载
SceneManager.LoadScene("SceneName", LoadSceneMode.Additive);  // 叠加加载

// 异步加载
StartCoroutine(LoadSceneAsync("SceneName"));

IEnumerator LoadSceneAsync(string sceneName)
{
    AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);
    
    while (!operation.isDone)
    {
        float progress = operation.progress;
        Debug.Log($"加载进度：{progress * 100}%");
        yield return null;
    }
}

// 获取当前场景
Scene currentScene = SceneManager.GetActiveScene();
string sceneName = currentScene.name;
int sceneIndex = currentScene.buildIndex;
```

## 14.15 实践示例

### 完整的工具类

```csharp
using UnityEngine;

public static class UnityUtils
{
    // 查找最近的敌人
    public static Transform FindNearestEnemy(Vector3 position, float range)
    {
        Transform nearest = null;
        float nearestDistance = float.MaxValue;
        
        GameObject[] enemies = GameObject.FindGameObjectsWithTag("Enemy");
        foreach (GameObject enemy in enemies)
        {
            float distance = Vector3.Distance(position, enemy.transform.position);
            if (distance < range && distance < nearestDistance)
            {
                nearest = enemy.transform;
                nearestDistance = distance;
            }
        }
        
        return nearest;
    }
    
    // 平滑移动到目标
    public static void SmoothMove(Transform transform, Vector3 target, float speed)
    {
        transform.position = Vector3.MoveTowards(transform.position, target, speed * Time.deltaTime);
    }
    
    // 检查是否在范围内
    public static bool IsInRange(Vector3 pos1, Vector3 pos2, float range)
    {
        return Vector3.SqrMagnitude(pos1 - pos2) <= range * range;
    }
}
```

## 14.16 本章小结

在本章中，我们学习了：

- ✅ GameObject和Transform操作
- ✅ Time类的时间管理
- ✅ Input类的输入处理
- ✅ Physics类的物理查询
- ✅ Random和Mathf工具类
- ✅ Vector和Quaternion数学运算
- ✅ Camera坐标转换
- ✅ Debug调试工具
- ✅ 协程使用
- ✅ 场景管理

## 14.17 实践练习

1. **创建工具类**：创建包含常用功能的工具类
2. **实现对象池**：使用Unity API实现对象池系统
3. **创建管理器**：创建游戏管理器使用各种API
4. **实现功能**：使用API实现具体游戏功能
5. **查阅文档**：熟悉Unity API文档的使用

## 14.18 下一步

继续实践使用Unity API，查阅官方文档了解更多功能。API文档是学习Unity的重要资源。

---

**提示**：Unity API非常丰富，建议查阅官方文档：https://docs.unity3d.com/ScriptReference/
