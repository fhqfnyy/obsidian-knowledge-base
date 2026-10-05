以下是一个简单的示例程序，演示如何使用AudioSource组件来播放音频：

```csharp
using UnityEngine;

public class AudioSourceExample : MonoBehaviour
{
    // 定义音频剪辑变量
    public AudioClip soundClip;

    // 获取AudioSource组件
    private AudioSource audioSource;

    void Start()
    {
        // 获取游戏对象上的AudioSource组件
        audioSource = GetComponent<AudioSource>();
    }

    void Update()
    {
        // 检查是否按下空格键
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 播放音频剪辑
            audioSource.PlayOneShot(soundClip);
        }
    }
}
```

在这个示例中：

- `Start()` 方法在游戏对象首次被激活时调用，用于获取游戏对象上的AudioSource组件。
- `Update()` 方法每帧都会被调用，用于检查用户输入并控制游戏对象的行为。
- 使用 `Input.GetKeyDown(KeyCode.Space)` 检查是否按下了空格键。
- 使用 `audioSource.PlayOneShot(soundClip)` 方法来播放音频剪辑。`PlayOneShot()` 方法允许我们播放一次性音效，不会中断当前正在播放的音频。