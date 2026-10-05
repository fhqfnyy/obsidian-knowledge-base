以下是一个简单的使用四元数实现旋转的示例代码，使用Unity中的C#语言实现：

```csharp
using UnityEngine;

public class QuaternionRotationExample : MonoBehaviour
{
    // 旋转速度
    public float rotationSpeed = 50f;

    void Update()
    {
        // 获取当前游戏对象的旋转四元数
        Quaternion currentRotation = transform.rotation;

        // 计算旋转角度
        float angle = rotationSpeed * Time.deltaTime;

        // 使用四元数来表示旋转
        Quaternion rotationQuaternion = Quaternion.Euler(0f, angle, 0f);

        // 将当前旋转四元数与新的旋转四元数相乘，得到新的旋转四元数
        Quaternion newRotation = rotationQuaternion * currentRotation;

        // 使用新的旋转四元数更新游戏对象的旋转
        transform.rotation = newRotation;
    }
}
```

在这个示例中：

- `rotationSpeed` 定义了旋转的速度，单位是度/秒。
- `Update()` 方法每帧被调用，用于更新游戏对象的旋转。
- `transform.rotation` 用于获取当前游戏对象的旋转四元数。
- `Quaternion.Euler()` 方法用于创建一个绕Y轴旋转的四元数，表示每帧旋转的角度。
- 将当前的旋转四元数与新的旋转四元数相乘，得到新的旋转四元数。
- 使用新的旋转四元数更新游戏对象的旋转。

通过修改 `rotationSpeed` 可以控制旋转的速度，从而实现不同的旋转效果。