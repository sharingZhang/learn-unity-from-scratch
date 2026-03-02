# 第19章：场景管理

## 19.1 场景管理概述

场景管理是游戏开发中的重要部分，包括场景加载、切换、数据传递等。

## 19.2 场景基础

### 什么是场景？

场景是Unity中组织游戏内容的基本单位，包含：
- 游戏对象
- 光照设置
- 摄像机设置
- 物理设置

### 场景操作

- **创建场景**：`File > New Scene`
- **保存场景**：`Ctrl+S`
- **打开场景**：`File > Open Scene`
- **添加场景到构建**：Build Settings中添加

## 19.3 场景加载

### 同步加载

```csharp
using UnityEngine.SceneManagement;

// 按名称加载
SceneManager.LoadScene("SceneName");

// 按索引加载
SceneManager.LoadScene(0);

// 加载并替换当前场景
SceneManager.LoadScene("SceneName", LoadSceneMode.Single);
```

### 异步加载

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneLoader : MonoBehaviour
{
    public void LoadSceneAsync(string sceneName)
    {
        StartCoroutine(LoadSceneCoroutine(sceneName));
    }

    IEnumerator LoadSceneCoroutine(string sceneName)
    {
        AsyncOperation operation = SceneManager.LoadSceneAsync(sceneName);
        
        while (!operation.isDone)
        {
            float progress = Mathf.Clamp01(operation.progress / 0.9f);
            Debug.Log($"加载进度：{progress * 100}%");
            
            // 更新加载进度UI
            UpdateLoadingUI(progress);
            
            yield return null;
        }
    }

    void UpdateLoadingUI(float progress)
    {
        // 更新UI显示加载进度
    }
}
```

### 叠加加载

```csharp
// 加载场景但不卸载当前场景
SceneManager.LoadScene("SceneName", LoadSceneMode.Additive);
```

## 19.4 场景切换

### 场景切换管理器

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneTransitionManager : MonoBehaviour
{
    public static SceneTransitionManager instance;
    
    public float fadeDuration = 1f;
    public CanvasGroup fadeCanvasGroup;

    void Awake()
    {
        if (instance == null)
        {
            instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
        }
    }

    public void TransitionToScene(string sceneName)
    {
        StartCoroutine(TransitionCoroutine(sceneName));
    }

    IEnumerator TransitionCoroutine(string sceneName)
    {
        // 淡出
        yield return StartCoroutine(FadeOut());
        
        // 加载场景
        SceneManager.LoadScene(sceneName);
        
        // 淡入
        yield return StartCoroutine(FadeIn());
    }

    IEnumerator FadeOut()
    {
        float elapsed = 0f;
        while (elapsed < fadeDuration)
        {
            elapsed += Time.deltaTime;
            fadeCanvasGroup.alpha = Mathf.Lerp(1f, 0f, elapsed / fadeDuration);
            yield return null;
        }
        fadeCanvasGroup.alpha = 0f;
    }

    IEnumerator FadeIn()
    {
        float elapsed = 0f;
        while (elapsed < fadeDuration)
        {
            elapsed += Time.deltaTime;
            fadeCanvasGroup.alpha = Mathf.Lerp(0f, 1f, elapsed / fadeDuration);
            yield return null;
        }
        fadeCanvasGroup.alpha = 1f;
    }
}
```

## 19.5 场景间数据传递

### 使用静态类

```csharp
public static class GameData
{
    public static int playerScore;
    public static int playerHealth;
    public static string playerName;
    
    public static void Reset()
    {
        playerScore = 0;
        playerHealth = 100;
        playerName = "";
    }
}

// 使用
GameData.playerScore = 100;
int score = GameData.playerScore;
```

### 使用单例管理器

```csharp
public class GameManager : MonoBehaviour
{
    public static GameManager instance;
    
    public int score;
    public int health;

    void Awake()
    {
        if (instance == null)
        {
            instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
        }
    }
}
```

### 使用PlayerPrefs

```csharp
// 保存数据
PlayerPrefs.SetInt("Score", score);
PlayerPrefs.SetString("PlayerName", name);
PlayerPrefs.Save();

// 读取数据
int score = PlayerPrefs.GetInt("Score", 0);
string name = PlayerPrefs.GetString("PlayerName", "Player");
```

## 19.6 场景加载事件

### 场景加载完成事件

```csharp
void OnEnable()
{
    SceneManager.sceneLoaded += OnSceneLoaded;
}

void OnDisable()
{
    SceneManager.sceneLoaded -= OnSceneLoaded;
}

void OnSceneLoaded(Scene scene, LoadSceneMode mode)
{
    Debug.Log($"场景加载完成：{scene.name}");
    
    // 场景加载后的初始化
    InitializeScene();
}

void InitializeScene()
{
    // 初始化场景对象
    // 设置摄像机位置
    // 生成玩家等
}
```

## 19.7 场景管理最佳实践

### 场景组织

1. **按功能分离**：主菜单、游戏场景、设置场景分开
2. **使用场景名称**：使用描述性的场景名称
3. **场景索引**：在Build Settings中按顺序排列

### 性能优化

1. **异步加载**：使用异步加载避免卡顿
2. **预加载**：提前加载下一个场景
3. **资源管理**：及时卸载不需要的资源

### 代码组织

```csharp
public class SceneManager : MonoBehaviour
{
    // 场景名称常量
    public const string MAIN_MENU = "MainMenu";
    public const string GAME_SCENE = "GameScene";
    public const string SETTINGS = "Settings";
    
    public void LoadMainMenu()
    {
        SceneManager.LoadScene(MAIN_MENU);
    }
    
    public void LoadGameScene()
    {
        SceneManager.LoadScene(GAME_SCENE);
    }
}
```

## 19.8 实践项目：场景管理系统

### 项目目标

创建一个完整的场景管理系统：
- 场景切换
- 加载进度显示
- 淡入淡出效果
- 数据传递

### 实现步骤

1. 创建SceneTransitionManager
2. 创建加载UI
3. 实现淡入淡出效果
4. 添加数据传递功能
5. 测试场景切换

## 19.9 本章小结

在本章中，我们学习了：

- ✅ 场景基础操作
- ✅ 同步和异步加载
- ✅ 场景切换
- ✅ 场景间数据传递
- ✅ 场景加载事件
- ✅ 场景管理最佳实践

## 19.10 实践练习

1. **创建多个场景**：创建主菜单、游戏场景等
2. **实现场景切换**：添加场景切换功能
3. **添加加载进度**：显示加载进度条
4. **实现数据传递**：在场景间传递数据
5. **完善场景管理**：创建完整的场景管理系统

## 19.11 下一步

下一章我们将学习性能优化，提高游戏的运行效率。

---

**提示**：良好的场景管理能让游戏流程更加顺畅，提升用户体验。
