以下是一个简单的示例程序，演示如何使用Camera组件来控制摄像机的行为：

```csharp
using UnityEngine;

public class CameraExample : MonoBehaviour
{
    // 定义移动速度和旋转速度
    public float moveSpeed = 5f;
    public float rotateSpeed = 90f;

    void Update()
    {
        // 获取用户输入
        float horizontalInput = Input.GetAxis("Horizontal");
        float verticalInput = Input.GetAxis("Vertical");
        float rotationInput = Input.GetAxis("Rotation");

        // 移动摄像机
        Vector3 moveDirection = new Vector3(horizontalInput, 0f, verticalInput).normalized;
        transform.Translate(moveDirection * moveSpeed * Time.deltaTime);

        // 旋转摄像机
        transform.Rotate(Vector3.up, rotationInput * rotateSpeed * Time.deltaTime);
    }
}
```

在这个示例中：

- `Update()` 方法每帧都会被调用，用于更新摄像机的位置和旋转。
- 使用 `Input.GetAxis("Horizontal")` 和 `Input.GetAxis("Vertical")` 获取水平和垂直方向上的用户输入，用于控制摄像机的移动。
- 创建一个新的 `Vector3` 对象来表示移动方向，其 x 和 z 分量分别由水平和垂直输入决定，y 分量设为 0。使用 `normalized` 方法将向量标准化，以确保摄像机在任何方向上移动时速度一致。
- 使用 `Input.GetAxis("Rotation")` 获取旋转的输入，根据输入值旋转摄像机。
继续上述示例，让我们为摄像机添加一些额外的功能，比如限制移动范围和视角的倾斜：

```csharp
using UnityEngine;

public class CameraExample : MonoBehaviour
{
    // 定义移动速度和旋转速度
    public float moveSpeed = 5f;
    public float rotateSpeed = 90f;

    // 定义移动范围
    public float minX = -10f;
    public float maxX = 10f;
    public float minZ = -10f;
    public float maxZ = 10f;

    // 定义视角的倾斜角度
    public float minAngle = 30f;
    public float maxAngle = 80f;

    void Update()
    {
        // 获取用户输入
        float horizontalInput = Input.GetAxis("Horizontal");
        float verticalInput = Input.GetAxis("Vertical");
        float rotationInput = Input.GetAxis("Rotation");

        // 移动摄像机
        Vector3 moveDirection = new Vector3(horizontalInput, 0f, verticalInput).normalized;
        transform.Translate(moveDirection * moveSpeed * Time.deltaTime);

        // 限制摄像机移动范围
        Vector3 newPosition = transform.position;
        newPosition.x = Mathf.Clamp(newPosition.x, minX, maxX);
        newPosition.z = Mathf.Clamp(newPosition.z, minZ, maxZ);
        transform.position = newPosition;

        // 旋转摄像机
        float angle = transform.eulerAngles.x - rotationInput * rotateSpeed * Time.deltaTime;
        angle = Mathf.Clamp(angle, minAngle, maxAngle);
        transform.eulerAngles = new Vector3(angle, transform.eulerAngles.y, transform.eulerAngles.z);
    }
}
```

在这个示例中，我们添加了以下功能：

- 定义了移动范围的最小和最大值，以确保摄像机不会移动超出指定范围。
- 定义了视角的倾斜角度的最小和最大值，以确保摄像机的视角在指定的范围内旋转。
- 使用 `Mathf.Clamp()` 方法来限制摄像机的移动范围和视角的倾斜角度。这个方法可以确保值在指定的范围内。