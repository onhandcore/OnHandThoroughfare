# WorkBuddy 可选启动钩子

[English](README.md) | 简体中文

适用范围：Windows / WorkBuddy 5.8.3。适配器 v1.0.1 及安装工具已于 2026-10-24 完成真实 SessionStart → Node → Python → ESTABLISH_SESSION E2E PASS（Node v22.22.2 / CPython 3.12.14）。仅一个真实新会话样本，另有同会话受控重复探针；需要一次安装注册，不代表其他机器开箱即用，30 分钟 TTL 自动续期仍未验证。

## 一次安装

先安装中央大道及其 wheel，建立产品 `.venv`。需要自行取得的 CPython 3.12.x 和 Node.js 18 或以上；Node 18 是本组件支持下限，不表示每个 Node 版本均已实测。不捆绑 Node、Python 或 WorkBuddy。使用目标 WorkBuddy 界面显示的真实版本，安装工具仅接受 5.8.3；其他版本需独立验证。

用自己的绝对路径替换所有占位符。`--entry` 必须由用户选择，不能填别人的盘符。`--node` 选择真实 node.exe，钩子记录完整路径，无需依赖 WorkBuddy 的 PATH。

```powershell
& '<Python3.12完整路径>' .\install.py install --entry '<技能区>\OnHandThoroughfare\SKILL.md' --node '<Node完整路径>\node.exe' --workbuddy-version 5.8.3
& '<Python3.12完整路径>' .\install.py check
```

只有显式执行 `install` 才注册用户级 `hooks.SessionStart`；安装中央大道本身不会更改 Host。默认安装到当前用户 `.workbuddy/hooks/onhand-startup-portable`，保留其他设置和钩子。可用 `--config-dir '<目标配置目录>'` 指定真实配置位置或测试目录；所有后续命令使用相同参数。安装期间关闭会同时写设置的程序；工具检测读写漂移，但没有跨程序事务锁。

重复相同安装不改设置；参数或文件不同则 STOP，不覆盖旧适配层。旧实验适配层不会被本工具识别或删除：若目标用户已有 OnHand 启动接线，先审阅并停用旧条目，避免双重触发。已有有效接线无需重复安装本组件。

## 检查、卸载与回滚

`check` 只检查配置与文件，不生成模拟会话，不连接产品。真正接入结果以新会话后安装目录 `logs/last-status.json` 为准；PASS 必须来自产品成功回执。失败会记 STOP/FAIL，适配器退出 0 让 WorkBuddy 会话继续；日志无法写入时不能据此认定成功。

```powershell
& '<Python3.12完整路径>' .\install.py uninstall
# 或在设置仍与安装后完全一致时，精确恢复安装前字节：
& '<Python3.12完整路径>' .\install.py rollback
```

卸载只移除本组件唯一描述标记的独立钩子组，保留其他钩子及后续设置。重复卸载安全。回滚校验设置与备份摘要，发现后续修改即拒绝；此时使用卸载保护新修改。卸载/回滚后保留适配层、日志和备份供审阅；确认无需审计后可手动删除本组件专属目录及 `backups/onhand-startup-install.json` 指定的备份。不要删除整个 hooks 或恢复整份旧配置覆盖后来修改。

## 接入、移动与长会话

仅处理 Host 亲自提供的 SessionStart/startup 与 session_id，调用现有 `ESTABLISH_SESSION`、`startup_hook=true`，不产生身份、不自动执行业务 Skill、不启动后台服务。运行时会写产品自身合法会话载体；“不修改 Core”不代表运行无需状态写权限。已审查 resident 映射与原适配层一致；本包不含其他 Skill，缺失必需产品时正式入口可能 STOP。

移动技能区或 Node 后，旧绝对路径不再有效。由用户明确选择新入口，先卸载钩子；审阅并移走专属旧适配目录后重新安装。不扫描磁盘、不自行寻找替代入口。

载体 TTL 是 1800 秒。有效期间正式调用可刷新载体；过期后旧关联必须停止，Host 可根据当前真实入口和同一真实会话身份显式调用正式 ESTABLISH_SESSION，重新 bootstrap，并使用新 attachment/resident 关联。不能重放旧业务请求或复用旧句柄。原启动适配层跳过 resume，不自动承担续期。既有隔离验证过期停止与显式重建；没有 WorkBuddy 持续 30 分钟真实会话自动续期 PASS。

本组件可读适配代码及安装工具适用随包 LICENSE；Core 仍以原字节码 wheel 发行。Node、Python、WorkBuddy 由用户另行取得并遵守各自许可。
