# 第12章：3D游戏实战项目

## 12.1 项目概述

本章我们将创建一个完整的3D第三人称动作游戏，整合之前学到的所有3D开发知识。

### 项目目标

创建一个名为"3D Adventure"的游戏，包含：
- 第三人称角色控制器
- 敌人AI系统
- 战斗系统
- 关卡设计
- UI系统
- 音效系统

## 12.2 项目设置

### 创建项目

1. 创建新3D项目
2. 组织文件夹结构
3. 导入必要资源

### 文件夹结构

```
Assets/
├── Scenes/
├── Scripts/
│   ├── Player/
│   ├── Enemy/
│   ├── Combat/
│   └── Managers/
├── Prefabs/
├── Models/
├── Materials/
└── Audio/
```

## 12.3 创建玩家角色

### 角色设置

1. 导入或创建角色模型
2. 添加Character Controller组件
3. 添加Animator组件
4. 设置动画控制器

### 第三人称控制器

```csharp
using UnityEngine;

public class ThirdPersonController : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float sprintSpeed = 8f;
    public float rotationSpeed = 10f;
    public float jumpHeight = 2f;
    public float gravity = -9.81f;
    
    private CharacterController controller;
    private Animator animator;
    private Vector3 velocity;
    private bool isGrounded;
    private float currentSpeed;

    void Start()
    {
        controller = GetComponent<CharacterController>();
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        HandleMovement();
        HandleJump();
        UpdateAnimator();
    }

    void HandleMovement()
    {
        // 地面检测
        isGrounded = controller.isGrounded;
        if (isGrounded && velocity.y < 0)
        {
            velocity.y = -2f;
        }

        // 输入
        float horizontal = Input.GetAxis("Horizontal");
        float vertical = Input.GetAxis("Vertical");
        Vector3 direction = new Vector3(horizontal, 0, vertical).normalized;

        // 移动
        if (direction.magnitude >= 0.1f)
        {
            // 旋转角色
            float targetAngle = Mathf.Atan2(direction.x, direction.z) * Mathf.Rad2Deg + Camera.main.transform.eulerAngles.y;
            float angle = Mathf.SmoothDampAngle(transform.eulerAngles.y, targetAngle, ref rotationSpeed, 0.1f);
            transform.rotation = Quaternion.Euler(0f, angle, 0f);

            // 移动方向
            Vector3 moveDir = Quaternion.Euler(0f, targetAngle, 0f) * Vector3.forward;
            
            // 冲刺
            currentSpeed = Input.GetKey(KeyCode.LeftShift) ? sprintSpeed : moveSpeed;
            controller.Move(moveDir.normalized * currentSpeed * Time.deltaTime);
        }

        // 重力
        velocity.y += gravity * Time.deltaTime;
        controller.Move(velocity * Time.deltaTime);
    }

    void HandleJump()
    {
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            velocity.y = Mathf.Sqrt(jumpHeight * -2f * gravity);
            animator.SetTrigger("Jump");
        }
    }

    void UpdateAnimator()
    {
        float speed = new Vector3(controller.velocity.x, 0, controller.velocity.z).magnitude;
        animator.SetFloat("Speed", speed);
        animator.SetBool("IsGrounded", isGrounded);
    }
}
```

## 12.4 摄像机系统

### 第三人称摄像机

```csharp
using UnityEngine;

public class ThirdPersonCamera : MonoBehaviour
{
    public Transform target;
    public float distance = 5f;
    public float height = 2f;
    public float rotationSpeed = 2f;
    
    private float currentRotationX;
    private float currentRotationY;

    void LateUpdate()
    {
        // 鼠标输入
        currentRotationX += Input.GetAxis("Mouse X") * rotationSpeed;
        currentRotationY -= Input.GetAxis("Mouse Y") * rotationSpeed;
        currentRotationY = Mathf.Clamp(currentRotationY, -20f, 60f);

        // 计算位置
        Quaternion rotation = Quaternion.Euler(currentRotationY, currentRotationX, 0);
        Vector3 direction = rotation * Vector3.back;
        Vector3 position = target.position + direction * distance + Vector3.up * height;

        // 设置摄像机
        transform.position = position;
        transform.LookAt(target.position + Vector3.up * height);
    }
}
```

## 12.5 战斗系统

### 攻击脚本

```csharp
using UnityEngine;

public class CombatSystem : MonoBehaviour
{
    public int damage = 10;
    public float attackRange = 2f;
    public LayerMask enemyLayer;
    public Transform attackPoint;
    
    private Animator animator;

    void Start()
    {
        animator = GetComponent<Animator>();
    }

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            Attack();
        }
    }

    void Attack()
    {
        animator.SetTrigger("Attack");
        
        // 检测敌人
        Collider[] hitEnemies = Physics.OverlapSphere(attackPoint.position, attackRange, enemyLayer);
        
        foreach (Collider enemy in hitEnemies)
        {
            enemy.GetComponent<EnemyHealth>()?.TakeDamage(damage);
        }
    }

    void OnDrawGizmosSelected()
    {
        if (attackPoint == null) return;
        Gizmos.DrawWireSphere(attackPoint.position, attackRange);
    }
}
```

## 12.6 敌人AI

### 敌人控制器

```csharp
using UnityEngine;
using UnityEngine.AI;

public class EnemyAI : MonoBehaviour
{
    public float detectionRange = 10f;
    public float attackRange = 2f;
    public int damage = 5;
    public float attackCooldown = 2f;
    
    private NavMeshAgent agent;
    private Transform player;
    private float lastAttackTime;
    private Animator animator;

    void Start()
    {
        agent = GetComponent<NavMeshAgent>();
        animator = GetComponent<Animator>();
        player = GameObject.FindGameObjectWithTag("Player")?.transform;
    }

    void Update()
    {
        if (player == null) return;

        float distanceToPlayer = Vector3.Distance(transform.position, player.position);

        if (distanceToPlayer <= attackRange)
        {
            // 攻击
            if (Time.time >= lastAttackTime + attackCooldown)
            {
                Attack();
                lastAttackTime = Time.time;
            }
            agent.SetDestination(transform.position);
        }
        else if (distanceToPlayer <= detectionRange)
        {
            // 追逐
            agent.SetDestination(player.position);
            animator.SetBool("IsChasing", true);
        }
        else
        {
            // 巡逻
            animator.SetBool("IsChasing", false);
        }
    }

    void Attack()
    {
        animator.SetTrigger("Attack");
        player.GetComponent<PlayerHealth>()?.TakeDamage(damage);
    }
}
```

## 12.7 健康系统

### 健康组件

```csharp
using UnityEngine;
using UnityEngine.Events;

public class Health : MonoBehaviour
{
    public int maxHealth = 100;
    public UnityEvent OnDeath;
    public UnityEvent<int> OnHealthChanged;
    
    private int currentHealth;

    void Start()
    {
        currentHealth = maxHealth;
    }

    public void TakeDamage(int damage)
    {
        currentHealth -= damage;
        currentHealth = Mathf.Max(0, currentHealth);
        
        OnHealthChanged?.Invoke(currentHealth);
        
        if (currentHealth <= 0)
        {
            Die();
        }
    }

    void Die()
    {
        OnDeath?.Invoke();
        Destroy(gameObject);
    }
}
```

## 12.8 关卡设计

### 创建关卡

1. 设计关卡布局
2. 放置障碍物和平台
3. 设置敌人位置
4. 添加收集物品
5. 设置检查点

### 检查点系统

```csharp
using UnityEngine;

public class Checkpoint : MonoBehaviour
{
    public Transform spawnPoint;
    private bool activated = false;

    void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player") && !activated)
        {
            activated = true;
            GameManager.instance.SetCheckpoint(spawnPoint.position);
        }
    }
}
```

## 12.9 完善游戏

### 添加功能

1. **UI系统**：生命值、分数显示
2. **音效系统**：背景音乐、音效
3. **粒子效果**：攻击、爆炸效果
4. **优化**：性能优化、代码优化

### 游戏管理器

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class GameManager : MonoBehaviour
{
    public static GameManager instance;
    public Vector3 checkpointPosition;

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

    public void SetCheckpoint(Vector3 position)
    {
        checkpointPosition = position;
    }

    public void RespawnPlayer(GameObject player)
    {
        player.transform.position = checkpointPosition;
    }

    public void GameOver()
    {
        // 游戏结束逻辑
        SceneManager.LoadScene("GameOver");
    }
}
```

## 12.10 项目总结

### 学到的技能

- ✅ 3D游戏开发流程
- ✅ 第三人称控制器
- ✅ 摄像机系统
- ✅ 战斗系统
- ✅ AI系统
- ✅ 关卡设计
- ✅ 系统整合

### 扩展建议

- 添加更多敌人类型
- 添加技能系统
- 添加装备系统
- 添加更多关卡
- 添加存档系统

## 12.11 本章小结

通过这个完整的3D游戏项目，我们实践了：

- ✅ 完整的3D游戏开发流程
- ✅ 各个系统的整合
- ✅ 代码组织和架构
- ✅ 游戏设计思维

## 12.12 下一步

继续完善这个项目，或开始新的项目。实践是学习的最好方式！

---

**提示**：完成一个完整的3D项目是很好的学习经历，这个项目可以作为你的作品集。
