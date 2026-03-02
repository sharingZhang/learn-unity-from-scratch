# 第18章：音频系统

## 18.1 音频系统概述

Unity的音频系统用于播放音效和背景音乐，包括：
- 3D空间音频
- 2D音频
- 音频混合
- 音频效果

## 18.2 音频组件

### Audio Source组件

Audio Source用于播放音频剪辑。

**创建Audio Source**：
1. 选中游戏对象
2. 添加Component > Audio > Audio Source

**主要属性**：
- **Audio Clip**：音频剪辑
- **Output**：输出到Audio Mixer
- **Mute**：静音
- **Play On Awake**：唤醒时播放
- **Loop**：循环播放
- **Volume**：音量（0-1）
- **Pitch**：音调（1=正常，>1=高，<1=低）
- **Spatial Blend**：空间混合（0=2D，1=3D）
- **3D Sound Settings**：3D音效设置

### Audio Listener组件

Audio Listener接收音频，通常附加在主摄像机上。

**注意**：场景中只能有一个Audio Listener。

## 18.3 导入音频

### 支持的格式

- **WAV**：未压缩，高质量，文件大
- **MP3**：压缩，质量好，文件小
- **OGG**：压缩，开源，Unity推荐

### 导入设置

选中音频文件，在Inspector中设置：

**Load Type**：
- **Decompress On Load**：加载时解压（适合短音效）
- **Compressed In Memory**：内存中压缩（适合中等长度）
- **Streaming**：流式播放（适合长音频，如背景音乐）

**Compression Format**：
- **PCM**：未压缩，质量最好
- **ADPCM**：压缩，质量好
- **Vorbis**：压缩，文件小

**Quality**：质量设置（Vorbis格式）

**Sample Rate Setting**：
- **Preserve Sample Rate**：保持采样率
- **Optimize Sample Rate**：优化采样率
- **Override Sample Rate**：覆盖采样率

## 18.4 播放音频

### 基本播放

```csharp
using UnityEngine;

public class AudioPlayer : MonoBehaviour
{
    public AudioClip soundEffect;
    private AudioSource audioSource;

    void Start()
    {
        audioSource = GetComponent<AudioSource>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 播放音频剪辑
            audioSource.Play();
            
            // 或播放一次（不需要AudioSource组件）
            AudioSource.PlayClipAtPoint(soundEffect, transform.position);
        }
    }
}
```

### 控制播放

```csharp
// 播放
audioSource.Play();

// 暂停
audioSource.Pause();

// 停止
audioSource.Stop();

// 检查是否正在播放
if (audioSource.isPlaying)
{
    // 正在播放
}
```

### 设置音频剪辑

```csharp
audioSource.clip = newClip;
audioSource.Play();
```

## 18.5 3D空间音频

### 3D音频设置

在Audio Source组件中：

1. **Spatial Blend**：设置为1（3D）
2. **3D Sound Settings**：
   - **Min Distance**：最小距离（开始衰减的距离）
   - **Max Distance**：最大距离（完全听不到的距离）
   - **Rolloff Mode**：衰减模式
     - **Logarithmic**：对数衰减
     - **Linear**：线性衰减
     - **Custom**：自定义衰减曲线

### 3D音频示例

```csharp
using UnityEngine;

public class SpatialAudio : MonoBehaviour
{
    public AudioClip footstepSound;
    private AudioSource audioSource;

    void Start()
    {
        audioSource = GetComponent<AudioSource>();
        audioSource.spatialBlend = 1f;  // 3D音频
        audioSource.minDistance = 1f;
        audioSource.maxDistance = 20f;
        audioSource.rolloffMode = AudioRolloffMode.Logarithmic;
    }

    void PlayFootstep()
    {
        audioSource.PlayOneShot(footstepSound);
    }
}
```

## 18.6 Audio Mixer（音频混合器）

### 创建Audio Mixer

1. 右键Project窗口：`Create > Audio > Audio Mixer`
2. 命名Mixer（如：MasterMixer）
3. 双击打开Audio Mixer窗口

### Audio Mixer窗口

- **Groups**：音频组
- **Snapshots**：快照（不同音频状态）
- **Attenuation**：衰减视图

### 创建音频组

1. 在Audio Mixer窗口中右键Master
2. 选择"Add child group"
3. 创建组：Music、SFX、Voice等

### 使用Audio Mixer

```csharp
using UnityEngine;
using UnityEngine.Audio;

public class AudioManager : MonoBehaviour
{
    public AudioMixer masterMixer;
    
    public void SetMusicVolume(float volume)
    {
        // 音量范围：-80dB 到 0dB
        masterMixer.SetFloat("MusicVolume", volume);
    }
    
    public void SetSFXVolume(float volume)
    {
        masterMixer.SetFloat("SFXVolume", volume);
    }
}
```

### Audio Mixer快照

快照保存音频组的状态，可以快速切换：

1. 在Audio Mixer窗口中点击"Snapshots"
2. 点击"+"创建快照
3. 调整音频组音量
4. 在代码中切换快照：

```csharp
public AudioMixerSnapshot normalSnapshot;
public AudioMixerSnapshot pausedSnapshot;

void PauseGame()
{
    pausedSnapshot.TransitionTo(0.5f);  // 0.5秒过渡
}

void ResumeGame()
{
    normalSnapshot.TransitionTo(0.5f);
}
```

## 18.7 音频效果

### Audio Mixer效果

在Audio Mixer组中可以添加效果：

- **Attenuation**：衰减
- **Send**：发送
- **Receive**：接收
- **Duck Volume**：闪避音量
- **Effect**：各种音频效果

### 常用效果

- **Low Pass Filter**：低通滤波器（模拟距离）
- **High Pass Filter**：高通滤波器
- **Echo**：回声
- **Reverb**：混响
- **Distortion**：失真

## 18.8 音频管理器

### 创建音频管理器

```csharp
using UnityEngine;
using System.Collections.Generic;

public class AudioManager : MonoBehaviour
{
    public static AudioManager instance;
    
    [System.Serializable]
    public class Sound
    {
        public string name;
        public AudioClip clip;
        [Range(0f, 1f)]
        public float volume = 1f;
        [Range(0.1f, 3f)]
        public float pitch = 1f;
        public bool loop = false;
        
        [HideInInspector]
        public AudioSource source;
    }
    
    public Sound[] sounds;
    
    void Awake()
    {
        // 单例模式
        if (instance == null)
        {
            instance = this;
            DontDestroyOnLoad(gameObject);
        }
        else
        {
            Destroy(gameObject);
            return;
        }
        
        // 为每个声音创建AudioSource
        foreach (Sound s in sounds)
        {
            s.source = gameObject.AddComponent<AudioSource>();
            s.source.clip = s.clip;
            s.source.volume = s.volume;
            s.source.pitch = s.pitch;
            s.source.loop = s.loop;
        }
    }
    
    public void Play(string soundName)
    {
        Sound s = System.Array.Find(sounds, sound => sound.name == soundName);
        if (s == null)
        {
            Debug.LogWarning("Sound: " + soundName + " not found!");
            return;
        }
        s.source.Play();
    }
    
    public void Stop(string soundName)
    {
        Sound s = System.Array.Find(sounds, sound => sound.name == soundName);
        if (s != null)
        {
            s.source.Stop();
        }
    }
    
    public void SetVolume(string soundName, float volume)
    {
        Sound s = System.Array.Find(sounds, sound => sound.name == soundName);
        if (s != null)
        {
            s.source.volume = volume;
        }
    }
}
```

### 使用音频管理器

```csharp
// 播放音效
AudioManager.instance.Play("Jump");

// 停止音效
AudioManager.instance.Stop("BackgroundMusic");

// 设置音量
AudioManager.instance.SetVolume("BackgroundMusic", 0.5f);
```

## 18.9 音频最佳实践

### 性能优化

1. **使用压缩格式**：OGG Vorbis适合大多数情况
2. **合理设置Load Type**：
   - 短音效：Decompress On Load
   - 中等长度：Compressed In Memory
   - 长音频：Streaming
3. **限制同时播放**：避免同时播放太多音频
4. **使用对象池**：重复使用的音效使用对象池

### 音频设计

1. **音量平衡**：确保不同音效音量平衡
2. **避免重复**：不要重复播放相同音效
3. **使用Audio Mixer**：统一管理音频
4. **测试不同设备**：在不同设备上测试音频效果

### 3D音频技巧

1. **合理设置距离**：根据游戏场景设置Min/Max Distance
2. **使用衰减曲线**：调整Rolloff Mode
3. **Audio Listener位置**：确保在正确位置（通常是摄像机）

## 18.10 实践项目：完整的音频系统

### 项目目标

创建一个完整的音频系统：
- 背景音乐
- 音效系统
- 音量控制
- 3D空间音频

### 步骤1：创建AudioManager

使用18.8节的AudioManager代码

### 步骤2：设置音频

1. 在场景中创建空对象，命名为"AudioManager"
2. 添加AudioManager脚本
3. 在Inspector中设置sounds数组：
   - BackgroundMusic（循环）
   - Jump
   - Collect
   - Hit
   - 等

### 步骤3：创建Audio Mixer

1. 创建Audio Mixer
2. 创建音频组：Music、SFX
3. 在AudioManager中引用Mixer

### 步骤4：添加音量控制UI

```csharp
using UnityEngine;
using UnityEngine.UI;

public class VolumeControl : MonoBehaviour
{
    public Slider musicSlider;
    public Slider sfxSlider;
    public AudioMixer audioMixer;

    void Start()
    {
        // 加载保存的音量设置
        musicSlider.value = PlayerPrefs.GetFloat("MusicVolume", 0.75f);
        sfxSlider.value = PlayerPrefs.GetFloat("SFXVolume", 0.75f);
        
        // 设置初始音量
        SetMusicVolume(musicSlider.value);
        SetSFXVolume(sfxSlider.value);
    }

    public void SetMusicVolume(float volume)
    {
        audioMixer.SetFloat("MusicVolume", Mathf.Log10(volume) * 20);
        PlayerPrefs.SetFloat("MusicVolume", volume);
    }

    public void SetSFXVolume(float volume)
    {
        audioMixer.SetFloat("SFXVolume", Mathf.Log10(volume) * 20);
        PlayerPrefs.SetFloat("SFXVolume", volume);
    }
}
```

### 步骤5：在游戏中使用

```csharp
// 播放背景音乐
AudioManager.instance.Play("BackgroundMusic");

// 播放音效
AudioManager.instance.Play("Jump");

// 3D音效
AudioSource.PlayClipAtPoint(footstepSound, transform.position);
```

## 18.11 本章小结

在本章中，我们学习了：

- ✅ 音频组件（Audio Source、Audio Listener）
- ✅ 音频导入和设置
- ✅ 播放音频的方法
- ✅ 3D空间音频
- ✅ Audio Mixer的使用
- ✅ 音频效果
- ✅ 音频管理器
- ✅ 音频最佳实践

## 18.12 实践练习

1. **导入音频**：导入一些音效和背景音乐
2. **创建AudioManager**：创建音频管理器系统
3. **添加音效**：在游戏中添加音效（跳跃、收集等）
4. **设置背景音乐**：添加循环播放的背景音乐
5. **创建音量控制**：创建UI控制音量

## 18.13 下一步

下一章我们将学习场景管理和项目发布，完成游戏开发的最后步骤。

---

**提示**：音频是游戏体验的重要组成部分，合适的音效能大大增强游戏的沉浸感。
