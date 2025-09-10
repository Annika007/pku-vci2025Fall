## VCI-2025 / VCX-Labs 代码运行与开发指南（第一次搭环境的血泪教训）
本项目包含课程实验的引擎、示例与 Lab0 入门案例。你只需按本文档一步一步做，即可在 Windows、macOS 或 Linux 上成功编译与运行。

你现在使用的是 Windows（PowerShell），本文首先给出 Windows 的最简步骤，其次提供通用说明与常见问题排查。

---

## 一、快速开始（Windows，新手优先）

1) 安装编译器（选一个即可）
   - 推荐 Visual Studio 2022（勾选“使用 C++ 的桌面开发”工作负载）。
   - 或安装 Visual Studio Build Tools（同样勾选 C++ 开发组件）。

2) 安装 Git（包管理会用到）
   - 前往 `https://git-scm.com/download/win` 下载安装。

3) 安装 xmake（本项目构建工具）
   - 用 winget（推荐）：在 PowerShell 执行：
     ```powershell
     winget install -e --id xmake-io.xmake
     ```
   - 若未装 winget，可用 Chocolatey：
     ```powershell
     choco install xmake -y
     ```

4) 打开 PowerShell，进入项目目录（请按你的实际路径修改）
   ```powershell
   cd "D:\wangziyou\北京大学课程\大二上\pku-vcl\vci-2025"
   ```

5) 首次构建（自动下载依赖、编译所有目标）
   ```powershell
   xmake
   ```
   - 首次会下载依赖（glfw、glad、imgui、glm、spdlog、stb、fmt、tinyobjloader、yaml-cpp、eigen 等），耗时取决于网络。

6) 运行 Lab0（入门案例）
   ```powershell
   xmake run lab0
   ```
   看到窗口后可在界面中切换展示不同三角形。

7) 运行示例（三角形 / ImGui）
   ```powershell
   xmake run example-triangle
   xmake run example-imgui
   ```

如果以上步骤任一步失败，请直接看「常见问题」；99% 的问题都能快速解决。

---

## 二、项目结构与目标

- `src/VCX/Engine/`：跨平台渲染/应用框架（窗口、OpenGL 上下文、ImGui 集成等）。
- `src/VCX/Examples/`：示例程序（如 `Triangle`、`ImGui`）。
- `src/VCX/Labs/0-GettingStarted/`：Lab0 入口与 UI。
- `assets/`：资源与着色器，构建后会自动复制到可执行文件同级的 `assets` 目录。
- `xmake.lua`：构建脚本（定义了 `engine`、`lab-common`、`lab0`、`example-*` 等目标）。

主要可执行目标：
- `lab0`：课程 Lab0 程序
- `example-triangle`：最小三角形示例
- `example-imgui`：ImGui 示例

---

## 三、构建与运行（详细版）

你可以在 Debug 或 Release 模式构建：

- Debug（默认）：
  ```powershell
  xmake f -m debug -c
  xmake
  ```

- Release：
  ```powershell
  xmake f -m release -c
  xmake
  ```

运行任意目标：
```powershell
xmake run lab0
xmake run example-triangle
xmake run example-imgui
```

清理：
```powershell
xmake clean
```

生成 IDE 工程：
- Visual Studio：
  ```powershell
  xmake project -a x64 -k vsxmake ./build
  ```
  生成的解决方案在 `build/vsxmake20xx/`。

- VS Code（智能提示）：
  ```powershell
  xmake project -k compile_commands ./.vscode
  ```
  安装「C/C++」与「XMake」扩展，并将 C++ 标准设为 C++20；
  让 VS Code 使用生成的 `./.vscode/compile_commands.json`。

---

## 四、OpenGL 与驱动

本项目请求 OpenGL 4.1 Core Profile。若窗口创建失败或黑屏：
- 更新独显/核显驱动（NVIDIA/AMD/Intel 官网）。
- 确认在独立显卡上运行（笔记本可在显卡控制面板中设置）。

---

## 五、常见问题（Windows）

- xmake 不被识别（命令未找到）
  - 确认已通过 winget 或 choco 安装，并重启终端。

- 找不到编译器或「cannot get program for cxx」
  - 安装 Visual Studio 2022（含「使用 C++ 的桌面开发」）。
  - 或使用 MinGW：
    ```powershell
    xmake f -p mingw -c
    xmake
    ```
  - 或使用 Clang：
    ```powershell
    xmake f --toolchain=clang -c
    xmake
    ```

- 下载依赖失败（网络报错、`Recv failure: Connection was reset`）
  - 有代理时，在 PowerShell 设置：
    ```powershell
    $env:HTTPS_PROXY = "127.0.0.1:7890"
    xmake
    ```
  - 无代理时，可按 xmake 输出提示手动下载依赖，并告知 xmake 本地包目录：
    ```powershell
    xmake g --pkg_searchdirs=你的下载文件夹路径
    xmake
    ```

- 已装 Visual Studio 仍被提示未找到
  - 确认 Visual Studio 安装路径不包含中文；安装器中勾选最新的生成工具与 C++ 组件。
 
- 关于将项目上传到github上遇到的问题
  - 本项目是从gitee上经过如下步骤克隆到本地的
    - git clone https://gitee.com/pku-vcl/vci-2025
    - git checkout lab0
   
  - gitee已经占用了origin分支，需要重新建立github仓库分支的联系
    - git remote add github https://github.com/Annika007/pku-vci2025Fall.git
    - 
    - 本项目体积过大，用https方式传送会占用很多线程，速度慢且项目推送过程中会中断
    - # 切换到 SSH 方式
git remote set-url github git@github.com:Annika007/pku-vci2025Fall.git
    - # 增加缓冲区大小
git config http.postBuffer 524288000
    - # 尝试再次推送
git push -u github lab0
    - 网络无法连接到 GitHub（端口 443 连接失败）如何解决
       - 使用VPN时调整Git代理设置
若在使用VPN后出现此问题，可能是系统端口号与Git端口号不一致。可以查询代理端口，然后设置Git端口号：
         git config --global http.proxy 127.0.0.1:****
         git config --global https.proxy 127.0.0.1:****
         验证设置：git config --global -L


---

## 六、平台补充

- macOS：
  - 安装 Xcode（建议最新版），命令行工具 `xcode-select --install`。
  - 安装 xmake：`brew install xmake` 或 `curl -fsSL https://xmake.io/shget.text | bash`。
  - 构建运行：
    ```bash
    xmake
    xmake run lab0
    ```

- Linux（Debian/Ubuntu 举例）：
  ```bash
  sudo apt update
  sudo apt install build-essential git
  bash <(curl -fsSL https://xmake.io/shget.text)
  xmake
  xmake run lab0
  ```

---

## 七、命令速查

- 首次/重新配置：`xmake f -c`
- 构建：`xmake`
- 运行：`xmake run <target>`（如 `lab0`、`example-triangle`、`example-imgui`）
- 清理：`xmake clean`
- 生成 VS 工程：`xmake project -a x64 -k vsxmake ./build`
- 生成 VS Code 提示：`xmake project -k compile_commands ./.vscode`

---

## 八、后续改进想法

- 在 `README.md` 中加入每个 Lab 的运行与评测说明链接，统一入口。
- 为中国大陆网络环境提供一键镜像/代理脚本，减少首次构建时间。
- 在应用启动时检测 GPU/OpenGL 能力，给出更友好的提示。
