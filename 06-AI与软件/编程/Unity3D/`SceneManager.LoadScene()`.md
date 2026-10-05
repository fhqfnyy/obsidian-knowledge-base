以下是一个简单的示例程序，演示如何使用`SceneManager.LoadScene()`方法加载新场景：

```csharp
using UnityEngine;
using UnityEngine.SceneManagement;

public class SceneLoader : MonoBehaviour
{
    // 定义要加载的场景名称
    public string sceneName;

    // 监听用户输入
    void Update()
    {
        // 检测用户是否按下了空格键
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // 加载新场景
            SceneManager.LoadScene(sceneName);
        }
    }
}
```

在这个示例中：

- 在 `Update()` 方法中监听用户输入，检测是否按下了空格键。
- 当用户按下空格键时，调用 `SceneManager.LoadScene(sceneName)` 方法加载指定名称的场景。`sceneName` 是一个公共变量，可以在Unity编辑器中指定要加载的场景名称。

要使用这个示例，只需将该脚本附加到任何游戏对象上，并在Unity编辑器中将要加载的场景名称赋给 `sceneName` 变量即可。然后，在游戏运行时，按下空格键就可以加载新场景。