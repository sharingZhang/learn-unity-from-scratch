# 第15章：输入系统

## 15.1 输入系统概述

Unity提供了两种输入系统：
- **旧输入系统**：Input类（简单易用）
- **新输入系统**：Input System包（更灵活强大）

## 15.2 旧输入系统（Input类）

### 键盘输入

```csharp
using UnityEngine;

public class OldInputExample : MonoBehaviour
{
    void Update()
    {
        // 按键检测
        if (Input.GetKeyDown(KeyCode.Space))
        {
            Debug.Log("空格键按下");
        }
        
        if (Input.GetKey(KeyCode.W))
        {
            Debug.Log("W键按住");
        }
        
        // 虚拟轴
        float horizontal = Input.GetAxis("Horizontal");  // A/D 或 方向键
        float vertical = Input.GetAxis("Vertical");  // W/S 或 方向键
    }
}
```

### 鼠标输入

```csharp
void Update()
{
    // 鼠标按键
    if (Input.GetMouseButtonDown(0))  // 左键
    {
        Vector3 mousePos = Input.mousePosition;
        Debug.Log($"鼠标点击位置：{mousePos}");
    }
    
    // 鼠标移动
    float mouseX = Input.GetAxis("Mouse X");
    float mouseY = Input.GetAxis("Mouse Y");
}
```

### 触摸输入

```csharp
void Update()
{
    if (Input.touchCount > 0)
    {
        Touch touch = Input.GetTouch(0);
        
        switch (touch.phase)
        {
            case TouchPhase.Began:
                Debug.Log("触摸开始");
                break;
            case TouchPhase.Moved:
                Debug.Log("触摸移动");
                break;
            case TouchPhase.Ended:
                Debug.Log("触摸结束");
                break;
        }
    }
}
```

## 15.3 新输入系统

### 安装Input System

1. 菜单：`Window > Package Manager`
2. 选择"Unity Registry"
3. 搜索"Input System"
4. 点击"Install"

### 创建Input Actions

1. 右键Project窗口：`Create > Input Actions`
2. 命名（如：PlayerControls）
3. 双击打开Input Actions编辑器

### 配置Input Actions

#### 创建Action Map

1. 点击"+"添加Action Map
2. 命名（如：Player）

#### 创建Actions

1. 在Action Map中点击"+"添加Action
2. 命名（如：Move、Jump、Attack）
3. 设置Action Type：
   - **Value**：值类型（用于移动等）
   - **Button**：按钮类型（用于跳跃、攻击等）
   - **Pass Through**：直通类型

#### 绑定输入

1. 点击Action
2. 点击"+"添加绑定
3. 选择输入类型：
   - **Keyboard**：键盘
   - **Mouse**：鼠标
   - **Gamepad**：手柄
   - **Touch**：触摸

### 生成C#脚本

1. 选中Input Actions资源
2. 在Inspector中勾选"Generate C# Class"
3. 点击"Apply"
4. Unity自动生成C#脚本

## 15.4 使用新输入系统

### 方法1：使用生成的C#类

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class NewInputExample : MonoBehaviour
{
    private PlayerControls controls;
    private Vector2 moveInput;

    void Awake()
    {
        controls = new PlayerControls();
    }

    void OnEnable()
    {
        controls.Enable();
        controls.Player.Move.performed += OnMove;
        controls.Player.Jump.performed += OnJump;
    }

    void OnDisable()
    {
        controls.Disable();
        controls.Player.Move.performed -= OnMove;
        controls.Player.Jump.performed -= OnJump;
    }

    void OnMove(InputAction.CallbackContext context)
    {
        moveInput = context.ReadValue<Vector2>();
    }

    void OnJump(InputAction.CallbackContext context)
    {
        Debug.Log("跳跃");
    }

    void Update()
    {
        // 使用输入
        transform.Translate(new Vector3(moveInput.x, 0, moveInput.y) * Time.deltaTime * 5f);
    }
}
```

### 方法2：直接读取

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class DirectInputExample : MonoBehaviour
{
    private PlayerControls controls;
    private Vector2 moveInput;

    void Awake()
    {
        controls = new PlayerControls();
    }

    void OnEnable()
    {
        controls.Enable();
    }

    void OnDisable()
    {
        controls.Disable();
    }

    void Update()
    {
        // 直接读取值
        moveInput = controls.Player.Move.ReadValue<Vector2>();
        
        if (controls.Player.Jump.WasPressedThisFrame())
        {
            Debug.Log("跳跃");
        }
    }
}
```

## 15.5 输入处理模式

### 事件驱动模式

```csharp
void OnEnable()
{
    controls.Player.Jump.performed += OnJump;
    controls.Player.Jump.canceled += OnJumpCanceled;
}

void OnJump(InputAction.CallbackContext context)
{
    // 按下时
    if (context.performed)
    {
        Jump();
    }
}

void OnJumpCanceled(InputAction.CallbackContext context)
{
    // 释放时
    if (context.canceled)
    {
        StopJump();
    }
}
```

### 轮询模式

```csharp
void Update()
{
    // 每帧检查
    if (controls.Player.Jump.WasPressedThisFrame())
    {
        Jump();
    }
    
    if (controls.Player.Jump.IsPressed())
    {
        // 按住中
    }
    
    if (controls.Player.Jump.WasReleasedThisFrame())
    {
        StopJump();
    }
}
```

## 15.6 多设备支持

### 检测输入设备

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class DeviceDetection : MonoBehaviour
{
    void OnEnable()
    {
        InputSystem.onDeviceChange += OnDeviceChange;
    }

    void OnDisable()
    {
        InputSystem.onDeviceChange -= OnDeviceChange;
    }

    void OnDeviceChange(InputDevice device, InputDeviceChange change)
    {
        switch (change)
        {
            case InputDeviceChange.Added:
                Debug.Log($"设备连接：{device}");
                break;
            case InputDeviceChange.Removed:
                Debug.Log($"设备断开：{device}");
                break;
        }
    }
}
```

### 获取当前设备

```csharp
void Update()
{
    // 获取当前使用的设备
    InputDevice device = Keyboard.current;
    if (device != null)
    {
        Debug.Log("使用键盘");
    }
    
    device = Gamepad.current;
    if (device != null)
    {
        Debug.Log("使用手柄");
    }
}
```

## 15.7 输入重映射

### 运行时重映射

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class InputRemapping : MonoBehaviour
{
    private PlayerControls controls;

    void Start()
    {
        controls = new PlayerControls();
        controls.Enable();
    }

    public void RemapJump(InputAction action)
    {
        // 开始重映射
        action.Disable();
        var rebindOperation = action.PerformInteractiveRebinding()
            .WithControlsExcluding("Mouse")
            .OnMatchWaitForAnother(0.1f)
            .OnComplete(operation => {
                Debug.Log($"新按键：{action.bindings[0].effectivePath}");
                action.Enable();
                operation.Dispose();
            })
            .Start();
    }
}
```

## 15.8 移动平台输入

### 触摸输入

```csharp
using UnityEngine;
using UnityEngine.InputSystem;
using UnityEngine.InputSystem.EnhancedTouch;

public class TouchInputExample : MonoBehaviour
{
    void OnEnable()
    {
        EnhancedTouchSupport.Enable();
    }

    void OnDisable()
    {
        EnhancedTouchSupport.Disable();
    }

    void Update()
    {
        if (Touch.activeTouches.Count > 0)
        {
            Touch touch = Touch.activeTouches[0];
            Vector2 touchPos = touch.screenPosition;
            
            switch (touch.phase)
            {
                case UnityEngine.InputSystem.TouchPhase.Began:
                    Debug.Log("触摸开始");
                    break;
                case UnityEngine.InputSystem.TouchPhase.Moved:
                    Debug.Log("触摸移动");
                    break;
                case UnityEngine.InputSystem.TouchPhase.Ended:
                    Debug.Log("触摸结束");
                    break;
            }
        }
    }
}
```

### 加速度计

```csharp
void Update()
{
    Vector3 acceleration = Accelerometer.current.acceleration.ReadValue();
    // 使用加速度数据
}
```

## 15.9 输入系统最佳实践

### 性能优化

1. **使用事件而非轮询**：事件驱动性能更好
2. **及时取消订阅**：避免内存泄漏
3. **使用ReadValue而非WasPressed**：需要连续值时

### 代码组织

1. **创建输入管理器**：统一管理输入
2. **使用接口**：抽象输入处理
3. **支持重映射**：允许玩家自定义按键

### 示例：输入管理器

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class InputManager : MonoBehaviour
{
    public static InputManager instance;
    
    private PlayerControls controls;
    
    public Vector2 MoveInput { get; private set; }
    public bool JumpPressed { get; private set; }
    public bool AttackPressed { get; private set; }

    void Awake()
    {
        if (instance == null)
        {
            instance = this;
            controls = new PlayerControls();
        }
        else
        {
            Destroy(gameObject);
        }
    }

    void OnEnable()
    {
        controls.Enable();
        controls.Player.Move.performed += OnMove;
        controls.Player.Move.canceled += OnMoveCanceled;
        controls.Player.Jump.performed += OnJump;
        controls.Player.Attack.performed += OnAttack;
    }

    void OnDisable()
    {
        controls.Disable();
        controls.Player.Move.performed -= OnMove;
        controls.Player.Move.canceled -= OnMoveCanceled;
        controls.Player.Jump.performed -= OnJump;
        controls.Player.Attack.performed -= OnAttack;
    }

    void OnMove(InputAction.CallbackContext context)
    {
        MoveInput = context.ReadValue<Vector2>();
    }

    void OnMoveCanceled(InputAction.CallbackContext context)
    {
        MoveInput = Vector2.zero;
    }

    void OnJump(InputAction.CallbackContext context)
    {
        JumpPressed = context.performed;
    }

    void OnAttack(InputAction.CallbackContext context)
    {
        AttackPressed = context.performed;
    }

    void LateUpdate()
    {
        // 重置单帧输入
        JumpPressed = false;
        AttackPressed = false;
    }
}
```

## 15.10 实践项目：完整的输入系统

### 项目目标

创建一个支持多种输入方式的玩家控制器：
- 键盘输入
- 手柄输入
- 触摸输入（移动平台）
- 输入重映射

### 实现步骤

1. 安装Input System包
2. 创建Input Actions资源
3. 配置Actions和绑定
4. 生成C#脚本
5. 创建InputManager
6. 在玩家控制器中使用InputManager

## 15.11 本章小结

在本章中，我们学习了：

- ✅ 旧输入系统（Input类）
- ✅ 新输入系统（Input System）
- ✅ Input Actions配置
- ✅ 输入处理模式
- ✅ 多设备支持
- ✅ 输入重映射
- ✅ 移动平台输入
- ✅ 输入系统最佳实践

## 15.12 实践练习

1. **配置Input Actions**：创建并配置Input Actions资源
2. **实现输入处理**：在玩家控制器中使用新输入系统
3. **添加手柄支持**：添加手柄输入绑定
4. **创建输入管理器**：创建统一的输入管理器
5. **实现重映射**：添加输入重映射功能

## 15.13 下一步

下一章我们将学习动画系统，创建更生动的游戏角色。

---

**提示**：新输入系统更强大，但学习曲线较陡。建议从简单项目开始，逐步掌握。
