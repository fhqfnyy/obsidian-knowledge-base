以下是一个简单的示例程序，演示如何使用Rigidbody组件来模拟游戏对象的物理行为：

```csharp
using UnityEngine;

public class RigidbodyExample : MonoBehaviour
{
    // 定义速度变量
    public float moveSpeed = 5f;

    // 获取Rigidbody组件
    private Rigidbody rb;

    void Start()
    {
        // 获取游戏对象上的Rigidbody组件
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        // 获取用户输入
        float horizontalInput = Input.GetAxis("Horizontal");
        float verticalInput = Input.GetAxis("Vertical");

        // 计算移动方向
        Vector3 moveDirection = new Vector3(horizontalInput, 0f, verticalInput).normalized;

        // 使用Rigidbody组件模拟物理运动
        rb.MovePosition(transform.position + moveDirection * moveSpeed * Time.fixedDeltaTime);
    }
}
```

在这个示例中：

- `Start()` 方法在游戏对象首次被激活时调用，用于获取游戏对象上的Rigidbody组件。
- `FixedUpdate()` 方法在固定的时间间隔内调用，用于更新游戏对象的物理行为。在处理物理行为时，推荐使用`FixedUpdate()`方法。
- `Input.GetAxis("Horizontal")` 和 `Input.GetAxis("Vertical")` 用于获取水平和垂直方向上的用户输入。
- 创建一个新的 `Vector3` 对象来表示移动方向，其 x 和 z 分量分别由水平和垂直输入决定，y 分量设为 0。使用 `normalized` 方法将向量标准化，以确保对象在任何方向上移动时速度一致。
- 使用 `Rigidbody.MovePosition()` 方法来更新游戏对象的位置，以模拟物体的运动。