# Changelog

本文件记录项目的所有重要变更。


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
- 删除 `claim_key_shard` 工具

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