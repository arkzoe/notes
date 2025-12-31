# 查看文件目录

| 特征               | Mono 包（托管编译）                                                                               | IL2CPP 包（AOT 原生编译）                                                                                                                                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Managed 目录文件** | 存在大量 `.dll` 文件（核心是 `Assembly-CSharp.dll`，这是游戏逻辑的托管程序集，还有 `UnityEngine.dll` 等 Unity 核心 DLL） | 无 `.dll` 文件（或仅残留少量非逻辑 DLL），取而代之的是 **原生二进制文件**                                                                                                                                                                                               |
| **原生文件位置**       | 无专门的 IL2CPP 原生文件                                                                           | Windows：`游戏根目录\GameAssembly.dll`（核心特征）；<br><br>macOS：`Contents\Frameworks\GameAssembly.dylib`；<br><br>Linux：`libGameAssembly.so`；<br><br>Android：`lib\armeabi-v7a/arm64-v8a\libil2cpp.so`（核心）；<br><br>iOS：`Payload\游戏名.app\libil2cpp.dylib` |
# 工具反编译
#### 1. dnSpy（Windows/macOS/Linux）
- 用途：.NET 程序集反编译工具，仅能识别 Mono 包的托管 DLL。
- 操作：打开 dnSpy → 拖拽游戏 `Managed` 目录下的 `Assembly-CSharp.dll`（或任意游戏相关 DLL）。
    - 能正常加载、看到命名空间 / 类 / 方法 → 是 Mono 包（DLL 是真实托管代码）；
    - 提示 “不是有效的 .NET 程序集” 或无法加载 → 大概率是 IL2CPP（该目录无真实托管 DLL）。
#### 2. IL2CPP Dumper（跨平台）
- 用途：专门检测 IL2CPP 包，提取元数据（验证是否为 IL2CPP 编译）。
- 操作：
    1. 下载工具：[GitHub 开源地址](https://github.com/Perfare/Il2CppDumper)（支持 Windows/macOS/Linux）；
    2. 找到游戏的 IL2CPP 核心文件（如 Windows 的 `GameAssembly.dll`、Android 的 `libil2cpp.so`）；
    3. 同时找到游戏目录下的 `global-metadata.dat`（IL2CPP 编译后会生成的元数据文件，路径通常在 `Data\Managed\Metadata\global-metadata.dat`）；
    4. 用 IL2CPP Dumper 加载上述两个文件，能成功提取出类、方法信息 → 确认是 IL2CPP 包；无法提取 → 是 Mono 包。
#### 3. ExeInfoPE（Windows 专用）
- 用途：查看可执行文件的编译信息。
- 操作：拖拽游戏主程序（`.exe`）到 ExeInfoPE。
    - 如果在 “模块” 或 “导入表” 中看到 `mono-2.0-bdwgc.dll` 等 Mono 相关模块 → 是 Mono 包；
    - 如果看到 `GameAssembly.dll` 或 `il2cpp` 相关导入 → 是 IL2CPP 包。
### 三、补充场景：移动平台（Android/iOS）特殊判断
#### Android（APK 包）
1. 解压 APK 后，进入 `lib` 目录（通常有 `armeabi-v7a`、`arm64-v8a` 等子目录）；
2. 若子目录中存在 `libil2cpp.so` → 100% 是 IL2CPP 包；
3. 若 `assets\bin\Data\Managed\` 目录有 `Assembly-CSharp.dll` 等 → 是 Mono 包。
#### iOS（IPA 包）
1. 解压 IPA 后，进入 `Payload\游戏名.app\`；
2. 若存在 `libil2cpp.dylib` → 是 IL2CPP 包；
3. 若 `Data\Managed\` 目录有 `.dll` 文件 → 是 Mono 包。