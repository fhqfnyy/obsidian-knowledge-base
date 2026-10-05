以下是一个简单的使用欧拉角实现旋转的示例代码，使用Unity中的C#语言实现：

```csharp
using UnityEngine;

public class EulerRotationExample : MonoBehaviour
{
    // 旋转速度
    public float rotationSpeed = 50f;

    void Update()
    {
        // 获取当前游戏对象的欧拉角
        Vector3 currentEulerAngles = transform.eulerAngles;

        // 计算新的欧拉角
        float newRotationX = currentEulerAngles.x + rotationSpeed * Time.deltaTime;
        float newRotationY = currentEulerAngles.y + rotationSpeed * Time.deltaTime;
        float newRotationZ = currentEulerAngles.z + rotationSpeed * Time.deltaTime;

        // 使用新的欧拉角更新游戏对象的旋转
        transform.eulerAngles = new Vector3(newRotationX, newRotationY, newRotationZ);
    }
}
```

在这个示例中：

- `rotationSpeed` 定义了旋转的速度，单位是度/秒。
- `Update()` 方法每帧被调用，用于更新游戏对象的旋转。
- `transform.eulerAngles` 用于获取当前游戏对象的欧拉角。
- 计算新的欧拉角时，将当前欧拉角的每个分量分别加上旋转速度乘以时间增量 `Time.deltaTime`，以实现平滑的旋转效果。
- 使用 `transform.eulerAngles` 属性直接设置游戏对象的欧拉角，从而实现旋转。

通过修改 `rotationSpeed` 可以控制旋转的速度，从而实现不同的旋转效果。