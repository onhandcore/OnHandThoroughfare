# OnHandThoroughfare · 中央大道

[English](README.md) | 简体中文

**OnHandThoroughfare（中央大道）是整个 OnHand 外置技能矩阵的核心连接基础：一个入口连接外置技能矩阵，让技能随身携带，并支持 Host 启动时自动接入。**

- **统一入口**：连接已经审查、正式挂载的外置 Skill 矩阵，无需逐个把实现复制进 Host。
- **外置管理与携带**：支持移动 SSD、切换 Host，以及第三方 Skill 的正式外置管理。
- **启动钩子接入**：在支持且正确配置的 Host 中，新会话可自动连接中央大道，无需每次输入手动连接提示词。

WorkBuddy 5.8.3 已有真实 SessionStart 自动接入 E2E PASS。完成一次可选的本地适配层安装与显式注册后，新会话可自动接入；这不代表零配置、全部 Host 兼容或业务 Skill 自动执行。

## 矩阵核心：一个入口，各自分工

整套 OnHand 外置技能矩阵以中央大道作为统一连接与机械转运核心。Host 可以通过这一入口使用已经正式挂载的逻辑辅助 Skill 与通用工作 Skill，各 Skill 保留独立职责，并按各自正式合同运行。

- **中央大道负责 WHERE + HOW**：按正式连接校验技能位置、机械传递调用，并按原合同把结果交回 Host；自身不做技能选择、规划或业务推理。
- **Host 保留决策权**：负责理解任务、选择技能（WHO）、判断授权与执行条件，并决定最终执行者及是否继续工作。组合中的 BestFit 可提供候选匹配证据，最终选择仍由 Host 作出。
- **技能可外置、移动和按需装卸**：Skill 主体、运行环境和各自状态保留在技能区，可放在本地目录或移动 SSD；新增、替换和拆卸由 Host 在用户授权下按正式生命周期完成。
- **方便切换 Host**：换设备、Host 或盘符后，提供当前真实入口并重新校验接入；同一会话复用有效连接，不沿用另一会话的凭据。各 Skill 的运行条件与许可仍须满足。

“核心连接基础”描述的是矩阵的统一连接方式，不代表所有 OnHand Skill 都必须依赖中央大道才能独立运行；能否独立运行由各 Skill 自身合同与运行条件决定。

权限不足、连接失效或合同校验失败时停止并报告，不能用搬动项目、扩大权限或绕过正式入口补救。

## 开放的逻辑辅助体系：不限于 OnHand 家族

中央大道的逻辑辅助体系不限于 OnHand 家族成员。理论上，所有采用 `SKILL.md` 作为正式接入入口的逻辑辅助 Skill，都可以成为中央大道的兼容接入对象；这是一项兼容范围说明，单有 `SKILL.md` 不等于满足实际接入条件或已经可以调用。

符合实际接入条件的逻辑辅助 Skill，可根据其能力、正式合同及用户授权，选择适用的接入方式：

| 接入方式 | 含义与边界 |
|---|---|
| `ON_DEMAND`（按需线） | 需要时，由 Host 按正式合同发起调用。 |
| `SESSION_RESIDENT`（会话常驻线） | 在当前 Host 会话内建立有效接入，供后续任务使用；不代表后台持续运行，也不代表自动执行业务。 |

接入方式须有正式合同依据并经过接线审查，不能仅凭目录名、技能描述或 Host 偏好任意指定。需要明确区分三个层次：

- **理论兼容范围**：采用 `SKILL.md` 正式入口、可能符合接入条件的逻辑辅助 Skill；尚不构成正式接入或运行验证结论。
- **已完成正式接入的技能**：已按正式合同完成必要审查、登记与接线，并在所需会话内建立有效接入的技能；目录存在或被 Host 发现不等于正式接入。
- **已经过真实运行验证的技能**：有对应技能、Host、版本、环境及调用场景的真实运行证据支持；验证结论仅覆盖证据所述范围，不能从正式接入推导为全部业务或跨 Host 验收通过。

本 README 不将理论兼容表述为所有第三方 Skill 已经实测 PASS，也不提供“任意 Skill 即插即用”的保证。下文启动钩子的验证结论只覆盖列明的 Host 接入场景，不替代各 Skill 自身的运行验证。

通用工作 Skill 仍可按现役 `OnHandThoroughfare/GeneralSkills/<SkillName>/` 路径规则进行外置管理，须由用户明确选择并完成正式管理生命周期。逻辑辅助 Skill 的实际存放位置遵守各自正式合同与现役接线事实，本说明不为其新增统一目录层级，也不要求将其全部迁入 `GeneralSkills/`。中央大道不扫描目录自动登记。

## 启动时接入，首次使用也能接入

启动钩子属于 Host 集成：由 Host 在会话启动时调用正式接入流程，中央大道校验入口、根身份和当前会话，建立或复用有效连接。它不是中央大道自启动的后台服务，也不自动执行未经授权的 Skill。

| Host / 环境 | 已核实范围 | 使用条件与限制 |
|---|---|---|
| Windows Codex 集成 | 既有记录支持已配置启动钩子的接入能力 | 不保证全部 Codex 版本兼容；接入与调用仍受实际权限限制 |
| WorkBuddy 5.8.3 / Windows | 适配器 v1.0.1 真实新会话 SessionStart E2E PASS；Host 直接触发 Node → Python → ESTABLISH_SESSION，原钩子继续运行 | 需要一次可选本地安装与显式注册；适配器仅有一个真实新会话样本，另有同会话受控重复探针；30 分钟 TTL 自动续期未验证 |
| 其他 Host、版本、系统或设备 | 本轮未验证启动钩子 | 按目标 Host 独立验证；无钩子时，在首次实际使用时完成正式接入 |

### WorkBuddy：一次安装，新会话自动连接

本发行包附带独立的可选组件 `optional/WorkBuddy/`。先安装中央大道，再由用户选择真实 `SKILL.md` 入口与 Node.js 路径，显式执行组件的 `install.py install`；中央大道的普通安装不会悄悄修改 Host 设置。详见[组件安装说明](optional/WorkBuddy/README.zh-CN.md)。需要 Windows、WorkBuddy 5.8.3、自行取得的 Node.js 18 或以上，以及产品 CPython 3.12.x 环境；其他版本未经验证。

安装工具提供检查、重复安装、保留其他钩子的卸载，以及有摘要保护的回滚。只在新会话 `startup` 建立连接，不自动执行 BestFit、Drudge 或第三方业务 Skill，不新增后台进程。

**长会话说明**：会话载体 TTL 为 30 分钟。有效期间正式调用可刷新载体；过期后 Host 可使用当前真实入口及真实会话身份，通过已有正式入口显式重新接入，再使用新的关联。发行包已隔离验证过期拒绝和显式重建；启动适配层跳过 `resume`，没有验证 WorkBuddy 长会话自动续期，不承诺连接永久有效。

启动接入需要外部技能区在线、入口有效、Host 支持本地工具并具备必要权限。拔盘后连接不可用；重新连接由 Host 根据明确提供的路径验证，不后台扫描、不自动重连。自动接入不等于全部 Skill 已通过跨 Host 验收。

## 关于 Host 主动调用外置 Skill

成功连接中央大道，并不代表 Host 一定会主动调用其中的 Skill。

不同 AI Agent Host 具有各自的技能发现、选择与调用机制。部分 Host 在实际任务中可能更倾向于使用自身原生 Skill 或内置能力，而未主动选择已经接入中央大道的外置 Skill。

因此，某个 Skill 没有被主动调用，不一定意味着该 Skill 未正确安装、连接失败或功能异常。

用户可以明确要求 Host 查询或调用相应的外置 Skill。实际能否调用成功，仍取决于 Host 的支持能力、当前接入状态及技能自身的使用条件。

OnHandThoroughfare 负责统一连接与机械转运，不干预 Host 的技能选择权重，也不保证外置 Skill 获得优先调用。

## 用一句提示词连接

已完成安装后，将实际路径提供给 Host。`X:` 仅为示例盘符：

```text
请连接 OnHandThoroughfare，入口位于：
X:\ONHAND_SKILL_AREA\OnHandThoroughfare\SKILL.md
```

已正确配置且验证启动钩子的环境，无需每次重复此提示词。无钩子的 Host 在本会话首次实际使用时完成相同接入，之后复用；新会话建立自己的连接。提示词不能代替安装、权限或第三方登记。

## 目录与安装

现役路径合同从实际 `OnHandThoroughfare/SKILL.md` 定位产品；其产品目录的父目录是当前技能区。下面是部署结构示意，非下载包完整清单：

```text
ONHAND_SKILL_AREA/
├─ ONHAND_ROOT.json                  # 技能区身份，首次安装独立初始化
├─ OnHandThoroughfare/
│  ├─ SKILL.md                       # 正式入口与公开合同
│  ├─ UNIVERSAL_THIN_HANDOFF.md
│  ├─ GENERALSKILLS_PORTABILITY_PUBLIC_CONTRACT.md
│  ├─ LICENSE
│  ├─ scripts/invoke.py              # 必要公开 launcher
│  ├─ .venv/                        # 自行建立的运行环境
│  ├─ AVAILABLE_SKILLS.json          # 可用技能投影
│  ├─ SESSION_RESIDENT_LINES.json    # 会话常驻接入投影
│  ├─ GeneralSkills/<SkillName>/     # 经用户选择并完成正式登记的第三方 Skill
│  ├─ state/                        # 中央大道自身状态
│  └─ workspace/                    # 任务状态、暂存与证据
└─ <其他 OnHand 产品>/               # 若另行安装，保持各自顶层目录
```

`GeneralSkills/` 是现役第三方外置路径，历史 `通用skill` 不是当前安装指引。Host 发现链接只指向正式产品，不是第二份产品或状态权威。`workspace/` 不要求移动你的项目仓库。

本发行包要求 **CPython 3.12.x**，Core 使用字节码；不同 Python minor 版本不兼容。配套 wheel 的版本为 `1.0.0+rc20261024`，内部历史版本为 `1.0.0`。Core 字节码与运行合同保持已验证字节；本次仅更新 wheel 内许可、描述性 METADATA 及 RECORD 完整性表。双语发行修订为 `FIRST_PUBLIC_BILINGUAL_20261024`，以文件清单及 SHA256 区分。保留的 wheel 版本 `rc20261024` 属于构建来源标识，发行身份以本修订及校验值为准。

1. 按外层发行包的 `RELEASE_MANIFEST.json` 与 `SHA256SUMS.txt` 校验文件大小和 SHA256。Agent Skill ZIP 解压后，其自带 `RELEASE_MANIFEST.json` 覆盖产品文件。清单不包含自身；各校验文件包含对应清单。
2. 将 Agent Skill ZIP 解压到选定技能区，得到 `OnHandThoroughfare/SKILL.md`。
3. 用自行取得的 CPython 3.12 创建产品 `.venv`，安装配套 wheel：

```powershell
py -3.12 -m venv '<技能区>\OnHandThoroughfare\.venv'
& '<技能区>\OnHandThoroughfare\.venv\Scripts\python.exe' -m pip install --no-index --no-deps '<发行包目录>\onhandthoroughfare-1.0.0+rc20261024-cp312-none-any.whl'
```

4. 由 Host 按路径合同为新技能区初始化唯一根身份，再接入真实入口；不复制他人的 `ONHAND_ROOT.json`、登记、Binding 或会话状态。

首次安装的空投影不代表已拥有其他 Skill。已有技能区不得用空模板覆盖正式状态；升级需另行安排。本包不含 Python、pip、其他 OnHand 产品和第三方 Skill。

WorkBuddy 启动接入已在 Windows / WorkBuddy 5.8.3 / Node v22.22.2 / CPython 3.12.14 验证通过。适配器 v1.0.1 只有一个真实新会话样本，同会话受控重复探针不计为第二个新会话。未验证干净机器、其他版本、跨设备 SSD 或 30 分钟 TTL 自动续期。

## 许可与转发

Copyright (c) 2026 OnHand Core

OnHand 家族统一采用闭源发行原则。本发行包的第一方程序、字节码、必要 launcher、合同与文档统一适用随包 [LICENSE](LICENSE)。允许自由转发官方完整、未经修改的成品；不授权修改、重新包装后公开发布，不主动公开第一方源码。

Core 保持字节码发行；除必要公开 `scripts/invoke.py` 外，可选 Host 适配层和安装工具以可读代码提供，便于本地审阅与配置。字节码不是加密，也不保证无法分析或反编译。第三方独立许可及真实存在的既有合法授权继续受到尊重。


LICENSE 中的中文为适用许可正文，英文仅为便利性翻译；如有不一致，以中文为准，英文翻译不额外授予权利。
