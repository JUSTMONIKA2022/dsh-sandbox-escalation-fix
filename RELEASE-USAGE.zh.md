# dsh-sandbox-escalation-fix 0.1.6-alpha2-win-linux 使用说明

## 版本内容

- 支持 DSH `0.1.6-alpha.2`。官方该版本让请求模式等于本次调用有效模式时直接返回，避免同模式请求重复进入审批。
- 官方 `0.1.6-alpha.2` 仍使用注册表全局静态升级 Schema，固定暴露 `workspace-write` 与 `danger-full-access` 目标；真正升级仍由执行期严格变宽校验和审批处理，因此本插件按 Session 投影模型可见 Schema 的作用仍然存在。
- 本版继续兼容 DSH `0.1.6-alpha.1` 的 PTC Runtime、Agent 异步串行初始化和沙箱后端接口；插件核心 Supervisor、Wrapper、Schema 投影和参数正规化逻辑无需修改。
- 已对 `0.1.6-alpha.1` 与 `0.1.6-alpha.2` 的新代际 15 个官方 npm 包执行逐文件 SHA-256 比较，并针对变化包复核 Sandbox 升级、Agent、Tools、Sandbox Policy 与 Pwsh 契约。
- 完整真实 `0.1.6-alpha.2` npm 开发依赖下运行 40 项测试与 TypeScript 构建。
- 正式支持 Linux：插件本体为纯 JavaScript、无平台限制；已在 Ubuntu 24.04 实机（Landlock 沙箱后端）完成安装、Schema 投影与沙箱/审批行为验证。`.sh` 脚本预期同样适用于 macOS，但尚未在真实 Mac 上测试。
- 支持 DSH Desktop `2.0.3` 隐藏宿主包清单时的严格结构校验回退。
- 支持通过 `link:`、工作区软链接或外部插件目录加载插件。
- 在 `workspace-write` 下，若模型对经真实路径边界确认位于当前工作区内的 `write` / `edit` 错误申请 `danger-full-access`，插件会移除该误提权参数并按现有权限执行；工作区外路径、Shell 调用和其他无法确认的路径仍保留正常审批。
- npm 的 `latest` 与 `next` 标签均指向当前最新兼容版本；包名安装与 GitHub Release `.tgz` 使用同一份构建产物。

## 安装前准备

1. 完全退出正在运行的 DSH 或 DSH Desktop。
2. 解压 Release ZIP，确认本说明、四个安装/卸载脚本和 `.tgz` 文件位于同一目录。
3. 执行 `dsh --version`，确认 `dsh` 命令可用。
4. 当前支持的 DSH 版本为 `0.1.0-rc.5`、`0.1.0-rc.6`、`0.1.0-rc.7`、`0.1.0-rc.8`、`0.1.1-rc.1`、`0.1.1-rc.2`、`0.1.2-alpha.1`、`0.1.2-alpha.2`、`0.1.2-alpha.3`、`0.1.2-alpha.4`、`0.1.2-alpha.5`、`0.1.2-rc.1`、`0.1.3-alpha.1`、`0.1.3-alpha.2`、`0.1.5-alpha.1`、`0.1.5-alpha.2`、`0.1.5-rc.1`、`0.1.5-rc.2`、`0.1.6-alpha.1` 和 `0.1.6-alpha.2`。

> DSH `0.1.6-alpha.2` 仍使用注册表全局静态 Schema；Session 当前模式没有用于收窄模型可见字段，真正升级仍在执行期处理。建议只在实际遇到空 justification、不可能执行的升级选项或重复重试问题后安装。

## 通过 npm Registry 安装（推荐）

```sh
dsh plugin --profile web add dsh-sandbox-escalation-fix@latest
```

其他 Profile 将 `web` 替换为实际名称。`@next` 当前指向同一版本，也可以锁定具体版本：

```sh
dsh plugin --profile web add dsh-sandbox-escalation-fix@0.1.6-alpha2-win-linux
```

## 通过 Release ZIP 安装

Windows：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File ".\install-release.ps1"
```

Linux/macOS：

```sh
sh ./install-release.sh
```

安装到其他 Profile 时，Windows 使用 `-Profile headless`，Linux/macOS 将 `headless` 作为第一个参数。

## 卸载

Windows：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File ".\uninstall-release.ps1"
```

Linux/macOS：

```sh
sh ./uninstall-release.sh
```

等效命令：

```sh
dsh plugin --profile web remove dsh-sandbox-escalation-fix
```

## 注意事项

- 安装、升级或卸载后都应完全重启 DSH，并在对应 Profile 中新建 Session 验证。
- 不需要手动编辑插件包内的 `cordis.patch.yml`；DSH CLI 会管理 Profile 依赖和 Bundle 层。
- Linux/macOS 上沙箱实际生效依赖 DSH 宿主可用的沙箱后端；后端不可用时 DSH 会拒绝执行，不会绕过沙箱。
