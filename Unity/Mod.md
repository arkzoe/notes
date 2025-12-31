# BeplnPlugin
1. 类库.net framework
	- 有`netstandard.dll`net472
	- 有`mscorlib.dll`版本大于等于`4.0.0`net46
	- 否则net35
2. 引用`UnityEngine.CoreModule.dll`、`UnityEngine.dll`、`BeplnEx.Core.dll`、`BeplnEx.Unity,dll`
3. `[BepInPlugin("id","插件名","版本")]`
4. `[BepInProcess("xxx.exe")]`指定mod运行的程序
5. `[BepIncompatibility("id")]`指定插件加载就不加载
# harmony
```C#
public class OriginalCode
{
    public void Test(int counter, string name)
    {
        // ...
    }
}
[HarmonyPrefix,HarmonyPatch(typeof(OriginalCode), nameof(OriginalCode.Test))]
//前置，post后置                    类                    方法
public static bool OriginalCode_Test_PrePatch()
{
	return false;//原函数不执行
}
```
- 访问私有成员Traverse
```csharp
// starting from a type or instance
public static Traverse Create(Type type)
public static Traverse Create<T>()
public static Traverse CreateWithType(string name)

// digging deeper
public Traverse Type(string name)
public Traverse Field(string name)
public Traverse Property(string name, object[] index = null)
public Traverse Method(string name, params object[] arguments)
public Traverse Method(string name, Type[] paramTypes, object[] arguments = null)

// calling getter or method
public object GetValue()
public T GetValue<T>()
public object GetValue(params object[] arguments)
public T GetValue<T>(params object[] arguments)
public override string ToString()

// calling setter
public Traverse SetValue(object value)

// iterating
public static void IterateFields(object source, Action<Traverse> action)
public static void IterateFields(object source, object target, Action<Traverse, Traverse> action)
public static void IterateProperties(object source, Action<Traverse> action)
public static void IterateProperties(object source, object target, Action<Traverse, Traverse> action)
```
- patch协程
# NStrip
- 将整个程序集public
# 调试Unity
# IMGUI制作UI