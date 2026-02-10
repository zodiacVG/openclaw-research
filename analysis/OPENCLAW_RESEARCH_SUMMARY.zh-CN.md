# OpenClaw 研究总结

## 研究目标

本仓库用于研究 OpenClaw：

- 保留 OpenClaw 源码与官方文档，便于追踪实现细节。
- 增加独立研究文档，沉淀接入、配置、架构与运维结论。

## 当前研究基线

- Upstream 仓库：`OpenClaw/OpenClaw`
- 本地研究时间：2026-02-10
- 代码版本：`2026.2.9`
- 参考提交：`8933010e84ffbbb1f30c1a96fb28203f9af12a4f`

## 一、OpenClaw 是什么

OpenClaw 是一个个人 AI 助手网关（Gateway）：

- 通过一个统一控制平面连接多消息渠道（WhatsApp / Telegram / Slack / Discord / Signal / iMessage / WebChat 等）。
- Gateway 通过 WebSocket + HTTP 暴露控制与服务接口。
- CLI、Web 控制台、macOS/iOS/Android 节点都围绕同一 Gateway 协同。

## 二、接入方式（怎么开始）

推荐路径：

1. 安装并运行向导：`openclaw onboard --install-daemon`
2. 启动/检查网关：`openclaw gateway status`
3. 打开控制台：`openclaw dashboard`

最小可用路径：

- 即使未接聊天渠道，也可先用 Dashboard/WebChat 与 agent 交互。

渠道接入：

- 各渠道通过 `channels.*` 配置。
- 常见方式：Telegram/Discord 使用 token；WhatsApp 走登录/配对。

## 三、配置模型

默认配置与状态目录：

- 配置：`~/.openclaw/openclaw.json`
- 状态：`~/.openclaw/`

关键环境变量：

- `OPENCLAW_HOME`
- `OPENCLAW_STATE_DIR`
- `OPENCLAW_CONFIG_PATH`

配置能力要点：

- 支持 JSON5 配置。
- 支持 `$include` 进行分文件拆分与合并。
- 支持 `${ENV_VAR}` 环境变量替换。
- 支持多 agent 路由（`agents.list + bindings`）。
- 支持 sandbox 安全隔离（尤其适合 non-main/group 场景）。

## 四、架构与项目结构

运行架构（逻辑）：

- 多渠道消息 → Gateway → Agent Runtime / Tools / Session Store
- 客户端（CLI、Web、Node）通过 WS 与 Gateway 通信。

代码结构（核心）：

- `src/`：核心逻辑（gateway/channels/agents/config/tools）
- `docs/`：官方文档
- `extensions/`：扩展渠道与插件能力
- `apps/`：macOS / iOS / Android 端
- `ui/`：Web 控制台前端
- `scripts/`：构建与运维脚本

## 五、构建与运行验证

本次研究实测：

- `pnpm install`：成功
- `pnpm build`：成功
- `node openclaw.mjs --help`：成功

说明：

- 源码可在本机完成构建。
- 渠道实连（如 WhatsApp/Telegram）需要真实账号与密钥，不在无凭据状态下自动完成。

## 六、安全与运维重点

- DM 默认建议采用配对/allowlist，避免陌生输入直接触达 agent。
- 网关远程暴露需启用 token/password，并限制网络边界。
- 非主会话建议开启 sandbox，降低 prompt injection 风险影响面。

## 七、研究仓库定位

本仓库不是生产部署仓库，而是研究仓库：

- 一部分是源码与官方文档（用于证据链与源码级分析）。
- 一部分是研究结论（用于知识沉淀与后续方案设计）。

建议后续把新增研究按专题继续沉淀到 `analysis/`：

- 接入专题（渠道逐一）
- 配置专题（多 agent / 路由 / 安全）
- 架构专题（协议 / 会话 / 工具）
- 运维专题（升级 / 迁移 / 监控）
