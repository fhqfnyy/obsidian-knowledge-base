以下是一个简单的示例程序，演示如何使用Light组件来控制灯光的属性：

```csharp
using UnityEngine;

public class LightExample : MonoBehaviour
{
    // 获取Light组件
    private Light lightComponent;

    void Start()
    {
        // 获取游戏对象上的Light组件
        lightComponent = GetComponent<Light>();
    }

    void Update()
    {
        // 检查是否按下空格键
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 切换灯光的开关状态
            lightComponent.enabled = !lightComponent.enabled;
        }

        // 控制灯光的颜色随时间变化
        float colorValue = Mathf.PingPong(Time.time, 1f);
        lightComponent.color = new Color(colorValue, 1f - colorValue, 0f);
    }
}
```

在这个示例中：

- `Start()` 方法在游戏对象首次被激活时调用，用于获取游戏对象上的Light组件。
- `Update()` 方法每帧都会被调用，用于检查用户输入并控制游戏对象的行为。
- 使用 `Input.GetKeyDown(KeyCode.Space)` 检查是否按下了空格键。
- 使用 `lightComponent.enabled` 属性来控制灯光的开关状态。当按下空格键时，切换灯光的开关状态。
- 使用 `Mathf.PingPong()` 方法来控制灯光的颜色随时间变化。这个方法可以产生一个在指定范围内循环变化的值。在这里，我们用这个值来控制灯光的颜色从红色到绿色再到蓝色的渐变。