# Changelog

本文件记录项目的所有重要变更。

## [v1.0.10] - 2026-10-26

### Security
- **[macOS] 修复钥匙串密钥可被任意程序无提示读取的严重漏洞。**
  旧版本使用 `security` CLI 并带有 `-A` 参数写入密钥，导致同用户下的任何进程均可通过命令行静默读取明文密钥。新版本移除了 `-A` 参数，并将底层实现切换为 `Security.framework` API（cgo），使钥匙串访问控制（ACL）严格绑定到 App 自身。
- **[macOS] 消除密钥在进程参数（argv）中泄露的风险。**
  不再通过 `security -w <secret>` 的方式在命令行传递密钥，避免被 `ps` 或其他进程监控工具捕获。

### Changed
- macOS 平台底层密钥存储由子进程调用 `/usr/bin/security` 改为基于 cgo 直接调用原生 `Security.framework` (`SecItemAdd` / `SecItemCopyMatching`)。
- 针对非 cgo 构建环境（`CGO_ENABLED=0`），提供降级实现，但移除了 `-A` 参数并改为通过 stdin 传输密钥，大幅降低了攻击面。

### ⚠️ 升级注意 / 迁移指南
- 由于底层实现改变，旧版本写入的钥匙串条目（已被 `-A` 标记为完全开放）不会自动收紧权限。
- **用户必须在升级后重新触发一次密钥的写入**（执行一次 `Store` 操作）。新实现会在写入前自动删除旧条目并重建，从而将 ACL 绑定至新的 App 进程。


## [v1.0.9] - 2026-10-19

### Added
- 新增 `get_gen_address_requests` 工具：查询 `add_address` 发起的派生审批状态（PENDING_APPROVAL / APPROVED / REJECTED / TIMEOUT 等），解决审批超时后 agent 无法感知结果的问题
- `get_wallet` / `get_wallets` 返回 `custodianSize`（成员数）与 `threshold`（审批门槛）
- `-clean-data` 支持 `-y` 参数跳过交互确认（便于脚本化），警告文案明确标注该操作会永久删除 agent 密钥分片
- `send_transaction` / `send_nft_transaction` 的 `toAddress` 参数新增格式校验（宽松模式：仅拦截空地址、字符集/长度明显非法等高置信错误，未知链与新格式不拦截，最终以后端链上校验兜底）
- 添加代币 `ARB1 - USDT0 0xFd086bC7CD5C481DCC9C85ebE478A1C0b69FCbb9`（仅新安装 MCP 服务有效，旧版本不受影响）

### Fixed
- 修复枚举参数 jsonschema 生成为 `"enum":["1|2"]` 单字符串的问题（netType / nftState / currencyType / status，共涉及 10 个工具），输出真正的数字/字符串枚举数组，兼容 Hermes 等严格 schema 校验的客户端
- 修复 `search_chains` 误用 `get_currencies` 参数 schema 导致 `currency` 参数缺失的问题
- `send_transaction` 返回结果补全 `netType` / `txType` 字段
- `query_address_balance` 修复 `addressId` 恒为 0 的问题
- `search_protocols` 修复搜索 ERC20 等非 NFT 协议返回空的问题
- search_currencies 支持传 `flag`（合约地址）模糊查询
- `join_mpc_wallet` 修复加入 MPC 钱包后本地 `status` 为空的问题（立即回填服务端返回状态）
- `add_address` 输出 schema 修正为实际返回结构，工具描述如实说明主币（走门限审批）与代币（复用 index 0 地址）的不同派生行为
- 启动失败提示移除不存在的 `[-r]` 参数，改为按 secure storage 故障场景给出准确的恢复指引

### Changed
- 安全存储跨平台改进（升级后首次启动可能仍有一次迁移提示，完成迁移后永久生效）：
  - macOS：Keychain 条目改用无应用访问限制的方式存取，彻底消除钥匙串授权弹窗；仅版本升级后首次启动可能弹出一次旧条目授权框，点击「始终允许 / Allow All」后自动迁移，此后不再弹窗；全新安装全程无窗
  - Linux / Docker：主密钥优先读取 `COINSDO_MASTER_KEY_FILE` 或 Docker secret `/run/secrets/coinsdo_master_key`，两者均未配置时首次启动自动生成；未配置时旧版本密文不兼容，需运行 `-clean-data` 清除数据后重新初始化；新增主密钥指纹检测，机器码变化或密钥文件被改动时启动即给出明确报错与恢复指引，不再输出晦涩的解密失败
  - Windows：DPAPI 加密与解密统一为用户范围（修复旧版加密用机器范围、解密用用户范围的不一致），旧格式密文首次读取时自动迁移；全程无任何弹窗，正常修改系统登录密码不影响存储的持久性
- `send_transaction` 的 `memo` 参数描述明确列出支持链与格式要求

### Deleted
- 删除 `OKT`、`HECO/HT`、`Fantom`、`DOT_OLD` 链（仅新安装 MCP 服务有效，旧版本不受影响）
- 删除 `Tron-USDC` 默认代币（仅新安装 MCP 服务有效，旧版本不受影响）

## [v1.0.8] - 2026-10-09

### Added
- 添加 `approval_transaction` 工具
- 添加 `custom_address_path` 工具

### Changed
- 调整部分工具请求参数描述信息、README.md


## [v1.0.7] - 2026-10-05

### Added
- 添加日志存储功能
- `send_transaction`工具添加`evmNonce`参数

### Changed
- 运行指令`-r`改成`-clean-data`
- `status`工具多返回`dataDir` 字段

### Fixed
- 修正 `send_transaction`工具`TRON`链交易失败问题


## [v1.0.6] - 2026-09-30

### Removed
- 删除 `claim_key_shard` 工具。移除原因：MCP服务定时任务自动处理key shard，移除该工具。

### Changed
- 优化工具返回列表数据结构

### Added
- tools工具参数添加验证逻辑


## [v1.0.5] - 2026-09-23

### Fixed
- 修正linux安全存储路径

## [v1.0.4] - 2026-09-23

### Removed
- 删除 `user_login` 工具

### Changed
- 修改服务名称成 `coinsdo-wallet-mcp`

### Fixed
- 修正交叉编译

## [v1.0.0] - 2026-09-18

### Added
- 首个正式版本