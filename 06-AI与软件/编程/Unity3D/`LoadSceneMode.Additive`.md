下面是一个示例程序，演示如何使用`LoadSceneMode.Additive`参数将新场景加载到当前场景中：

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class AdditiveSceneLoader : MonoBehaviour
{
    // 定义要加载的附加场景名称
    public string sceneName;

    // 监听用户输入
    void Update()
    {
        // 检测用户是否按下了空格键
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 加载附加场景
            SceneManager.LoadScene(sceneName, LoadSceneMode.Additive);
        }
    }
}
```

在这个示例中：

- 在 `Update()` 方法中监听用户输入，检测是否按下了空格键。
- 当用户按下空格键时，调用 `SceneManager.LoadScene(sceneName, LoadSceneMode.Additive)` 方法加载指定名称的场景，并使用 `LoadSceneMode.Additive` 参数将新场景加载到当前场景中。

要使用这个示例，将该脚本附加到任何游戏对象上，并在Unity编辑器中将要加载的附加场景名称赋给 `sceneName` 变量。然后，在游戏运行时，按下空格键就可以将新场景加载到当前场景中。