# Bilingual distribution / 双语发行说明

Distribution revision / 发行修订：`FIRST_PUBLIC_BILINGUAL_20261024`.

English is the default README; Simplified Chinese is provided in README.zh-CN.md. Both packages include the formal public contracts, installation guidance, third-party notices, and optional WorkBuddy v1.0.1 component.

默认 README 为英文，README.zh-CN.md 为简体中文；内外包保留正式公开合同、安装说明、第三方声明与可选 WorkBuddy v1.0.1 组件。

Core bytecode, embedded runtime contracts, launcher, adapter and installer retain their verified bytes. The wheel version remains 1.0.0+rc20261024 (historical internal version 1.0.0). Its license, descriptive METADATA and RECORD were updated; its archive SHA256 therefore differs. The version suffix is retained build provenance, not a separate distribution identity.

Core 字节码、内嵌运行合同、launcher、适配器和安装工具保持已验证字节。wheel 保留 1.0.0+rc20261024（内部历史版本 1.0.0），仅更新许可、描述性 METADATA 与 RECORD，因此归档 SHA256 改变。版本后缀是构建来源，不代表另一发行身份。

Existing functional acceptance is reused; this documentation and packaging revision does not rerun Core, pip or WorkBuddy E2E. WorkBuddy evidence covers Windows / WorkBuddy 5.8.3 / Node v22.22.2 / CPython 3.12.14, one real new session and controlled repeat probes in that session. Automatic renewal of the 30-minute TTL, clean machines, other versions and cross-device SSD use remain unverified.

复用既有功能验收，本次不重跑 Core、pip 或 WorkBuddy E2E。WorkBuddy 证据覆盖上述环境、一个真实新会话及同会话受控重复探针；30 分钟 TTL 自动续期、干净机器、其他版本和跨设备 SSD 仍未验证。

LICENSE contains the governing Chinese license and an English convenience translation. Complete, unmodified official distributions may be freely redistributed; modification and public distribution of repackaged versions are not authorized. Existing lawful grants and third-party licenses remain unaffected.

LICENSE 同时包含适用中文许可及英文便利性翻译；允许完整、未经修改的官方成品自由转发，不授权修改或重新包装后公开发行；既有合法授权与第三方许可不受影响。
