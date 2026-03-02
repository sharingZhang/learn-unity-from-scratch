# 第7章：2D游戏实战项目

## 7.1 项目概述

本章我们将创建一个完整的2D平台跳跃游戏，整合之前学到的所有知识。

### 项目目标

创建一个名为"Jump Adventure"的2D平台游戏，包含：
- 玩家角色（移动、跳跃）
- 平台和障碍物
- 可收集物品
- 敌人（简单AI）
- 游戏UI（分数、生命值）
- 音效和背景音乐
- 多个关卡

## 7.2 项目设置

### 创建项目

1. 打开Unity Hub
2. 创建新项目，选择"2D"模板
3. 项目名称：JumpAdventure
4. 创建项目

### 组织文件夹结构

```
Assets/
├── Scenes/
│   ├── MainMenu.unity
│   ├── Level01.unity
│   └── Level02.unity
├── Scripts/
│   ├── Player/
│   ├── Enemy/
│   ├── Collectibles/
│   └── Managers/
├── Prefabs/
│   ├── Player.prefab
│   ├── Platform.prefab
│   ├── Enemy.prefab
│   └── Collectible.prefab
├── Sprites/
│   ├── Player/
│   ├── Environment/
│   └── UI/
├── Audio/
│   ├── Music/
│   └── SFX/
└── Materials/
```

## 7.3 创建玩家角色

### 步骤1：准备精灵

1. 导入玩家精灵（或使用Unity默认Sprite）
2. 创建Sprite对象作为玩家
3. 设置Sorting Layer为"Player"

### 步骤2：添加组件

1. **Rigidbody2D**：
   - Body Type: Dynamic
   - Gravity Scale: 3
   - Freeze Rotation Z: true

2. **Collider2D**：
   - 添加Capsule Collider 2D
   - 调整大小适合角色

3. **Animator**：
   - 创建Animator Controller
   - 设置动画状态

### 步骤3：玩家控制器脚本

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    [Header("Movement")]
    public float moveSpeed = 5f;
    public float jumpForce = 10f;
    public float groundCheckRadius = 0.2f;
    public LayerMask groundLayer;
    
    [Header("Health")]
    public int maxHealth = 3;
    private int currentHealth;
    
    private Rigidbody2D rb;
    private Animator animator;
    private bool isGrounded;
    private bool facingRight = true;
    
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        animator = GetComponent<Animator>();
        currentHealth = maxHealth;
    }
    
    void Update()
    {
        HandleMovement();
        HandleJump();
        CheckGrounded();
        UpdateAnimator();
    }
    
    void HandleMovement()
    {
        float horizontal = Input.GetAxis("Horizontal");
        rb.velocity = new Vector2(horizontal * moveSpeed, rb.velocity.y);
        
        // 翻转角色
        if (horizontal > 0 && !facingRight)
            Flip();
        else if (horizontal < 0 && facingRight)
            Flip();
    }
    
    void HandleJump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector2.up * jumpForce, ForceMode2D.Impulse);
            animator.SetTrigger("Jump");
        }
    }
    
    void CheckGrounded()
    {
        isGrounded = Physics2D.OverlapCircle(transform.position, groundCheckRadius, groundLayer);
        animator.SetBool("IsGrounded", isGrounded);
    }
    
    void UpdateAnimator()
    {
        animator.SetFloat("Speed", Mathf.Abs(rb.velocity.x));
    }
    
    void Flip()
    {
        facingRight = !facingRight;
        Vector3 scale = transform.localScale;
        scale.x *= -1;
        transform.localScale = scale;
    }
    
    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        if (currentHealth <= 0)
        {
            Die();
        }
    }
    
    void Die()
    {
        // 死亡逻辑
        GameManager.instance.GameOver();
    }
    
    void OnDrawGizmos()
    {
        Gizmos.color = isGrounded ? Color.green : Color.red;
        Gizmos.DrawWireSphere(transform.position, groundCheckRadius);
    }
}
```

## 7.4 创建平台

### 平台预制体

1. 创建Sprite对象
2. 添加Box Collider 2D
3. 设置标签为"Ground"
4. 创建预制体：Platform.prefab

### 移动平台脚本

```csharp
using UnityEngine;

public class MovingPlatform : MonoBehaviour
{
    public Transform[] waypoints;
    public float speed = 2f;
    public float waitTime = 1f;
    
    private int currentWaypoint = 0;
    private float waitTimer = 0f;
    private bool isWaiting = false;
    
    void Update()
    {
        if (waypoints.Length == 0) return;
        
        if (isWaiting)
        {
            waitTimer += Time.deltaTime;
            if (waitTimer >= waitTime)
            {
                isWaiting = false;
                waitTimer = 0f;
                currentWaypoint = (currentWaypoint + 1) % waypoints.Length;
            }
            return;
        }
        
        Vector2 target = waypoints[currentWaypoint].position;
        transform.position = Vector2.MoveTowards(transform.position, target, speed * Time.deltaTime);
        
        if (Vector2.Distance(transform.position, target) < 0.1f)
        {
            isWaiting = true;
        }
    }
    
    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player"))
        {
            collision.transform.SetParent(transform);
        }
    }
    
    void OnCollisionExit2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player"))
        {
            collision.transform.SetParent(null);
        }
    }
}
```

## 7.5 创建可收集物品

### 收集物品脚本

```csharp
using UnityEngine;

public class Collectible : MonoBehaviour
{
    public int scoreValue = 10;
    public AudioClip collectSound;
    
    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Player"))
        {
            Collect();
        }
    }
    
    void Collect()
    {
        // 播放音效
        if (collectSound != null)
            AudioSource.PlayClipAtPoint(collectSound, transform.position);
        
        // 增加分数
        GameManager.instance.AddScore(scoreValue);
        
        // 销毁物品
        Destroy(gameObject);
    }
}
```

## 7.6 创建敌人

### 敌人AI脚本

```csharp
using UnityEngine;

public class Enemy : MonoBehaviour
{
    public float moveSpeed = 2f;
    public float detectionRange = 5f;
    public int damage = 1;
    
    private Transform player;
    private Rigidbody2D rb;
    private bool isMovingRight = true;
    private Vector2 startPosition;
    
    void Start()
    {
        rb = GetComponent<Rigidbody2D>();
        startPosition = transform.position;
        player = GameObject.FindGameObjectWithTag("Player")?.transform;
    }
    
    void Update()
    {
        if (player != null && Vector2.Distance(transform.position, player.position) < detectionRange)
        {
            // 追逐玩家
            ChasePlayer();
        }
        else
        {
            // 巡逻
            Patrol();
        }
    }
    
    void ChasePlayer()
    {
        Vector2 direction = (player.position - transform.position).normalized;
        rb.velocity = new Vector2(direction.x * moveSpeed, rb.velocity.y);
    }
    
    void Patrol()
    {
        if (isMovingRight)
        {
            rb.velocity = new Vector2(moveSpeed, rb.velocity.y);
            if (transform.position.x > startPosition.x + 3f)
                isMovingRight = false;
        }
        else
        {
            rb.velocity = new Vector2(-moveSpeed, rb.velocity.y);
            if (transform.position.x < startPosition.x - 3f)
                isMovingRight = true;
        }
    }
    
    void OnCollisionEnter2D(Collision2D collision)
    {
        if (collision.gameObject.CompareTag("Player"))
        {
            collision.gameObject.GetComponent<PlayerController>()?.TakeDamage(damage);
        }
    }
}
```

## 7.7 游戏管理器

### GameManager脚本

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

public class GameManager : MonoBehaviour
{
    public static GameManager instance;
    
    [Header("UI")]
    public Text scoreText;
    public Text healthText;
    public GameObject gameOverPanel;
    
    private int score = 0;
    
    void Awake()
    {
        if (instance == null)
        {
            instance = this;
        }
        else
        {
            Destroy(gameObject);
        }
    }
    
    void Start()
    {
        UpdateUI();
        if (gameOverPanel != null)
            gameOverPanel.SetActive(false);
    }
    
    public void AddScore(int points)
    {
        score += points;
        UpdateUI();
    }
    
    public void UpdateHealth(int currentHealth, int maxHealth)
    {
        if (healthText != null)
            healthText.text = $"Health: {currentHealth}/{maxHealth}";
    }
    
    void UpdateUI()
    {
        if (scoreText != null)
            scoreText.text = $"Score: {score}";
    }
    
    public void GameOver()
    {
        if (gameOverPanel != null)
            gameOverPanel.SetActive(true);
        Time.timeScale = 0f;
    }
    
    public void Restart()
    {
        Time.timeScale = 1f;
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
    }
    
    public void LoadNextLevel()
    {
        SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex + 1);
    }
}
```

## 7.8 创建UI

### HUD设置

1. 创建Canvas
2. 添加Text显示分数
3. 添加Text显示生命值
4. 创建Game Over面板：
   - 背景Image
   - Game Over文本
   - Restart按钮
   - Main Menu按钮

### UI脚本

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

public class UIManager : MonoBehaviour
{
    public Button restartButton;
    public Button mainMenuButton;
    
    void Start()
    {
        if (restartButton != null)
            restartButton.onClick.AddListener(Restart);
        
        if (mainMenuButton != null)
            mainMenuButton.onClick.AddListener(LoadMainMenu);
    }
    
    void Restart()
    {
        GameManager.instance.Restart();
    }
    
    void LoadMainMenu()
    {
        Time.timeScale = 1f;
        SceneManager.LoadScene("MainMenu");
    }
}
```

## 7.9 添加音效

### 音频设置

1. 导入音效文件（跳跃、收集、受伤等）
2. 导入背景音乐
3. 创建AudioManager（参考第18章）
4. 在适当位置播放音效

## 7.10 关卡设计

### 关卡1：入门关卡

- 简单的平台跳跃
- 几个可收集物品
- 一个敌人
- 终点标记

### 关卡2：进阶关卡

- 移动平台
- 更多敌人
- 更多收集物品
- 更复杂的布局

### 关卡切换

```csharp
public class LevelExit : MonoBehaviour
{
    void OnTriggerEnter2D(Collider2D other)
    {
        if (other.CompareTag("Player"))
        {
            GameManager.instance.LoadNextLevel();
        }
    }
}
```

## 7.11 完善和优化

### 性能优化

1. 使用对象池管理敌人和收集物品
2. 优化物理设置
3. 减少Draw Call

### 游戏平衡

1. 调整玩家移动速度
2. 调整敌人AI难度
3. 平衡分数系统

### 测试

1. 测试所有功能
2. 测试不同分辨率
3. 修复bug

## 7.12 项目总结

### 学到的技能

- ✅ 2D游戏开发流程
- ✅ 玩家控制器实现
- ✅ 物理系统使用
- ✅ AI实现
- ✅ UI系统
- ✅ 音频系统
- ✅ 场景管理
- ✅ 游戏管理器模式

### 扩展建议

- 添加更多关卡
- 添加更多敌人类型
- 添加道具系统
- 添加存档系统
- 添加成就系统

## 7.13 本章小结

通过这个完整的2D游戏项目，我们实践了：

- ✅ 完整的游戏开发流程
- ✅ 各个系统的整合
- ✅ 代码组织和架构
- ✅ 游戏设计思维

## 7.14 下一步

继续完善这个项目，或开始新的项目。实践是学习的最好方式！

---

**提示**：完成一个完整的项目比做很多小练习更有价值。这个项目可以作为你的作品集的一部分。
