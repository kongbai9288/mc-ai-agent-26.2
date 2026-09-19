# mc-ai-agent-26.2（临时资源仓）

**目标**：让 AI 真的能玩 Minecraft Java **26.2**，而不是只会发两条命令就卡住。

思路：**AI 只发"意图"，本地自动驾驶层（Baritone / Printer / fakeplayer）负责 tick 级执行。**

---

## 一、已核实的版本矩阵（26.2 全部有货）

| 组件 | 版本 | 状态 |
|---|---|---|
| Minecraft | **26.2**「Chaos Cubed」（2026-06-16，协议 776） | ✅ |
| Java | **25**（class 主版本 69），不可降级 | ✅ |
| Fabric Loader | **0.19.5** | ✅ 你的 `-AI-` 仓里已有 jar |
| Fabric API | **0.160.0+26.2** | ✅ 同上 |
| Fabric Loom | 1.17+（插件 id `net.fabricmc.fabric-loom`） | ✅ |
| Gradle | **9.7.0** | ✅ 别用 9.1.0 / 8.14.3 |
| **Baritone**（男中音） | `dysnasia/baritone-26.2`（Mojmap，Fabric/Forge/NeoForge） | ✅ 客户端 |
| **Litematica + Printer** | `26.2-3.2.2`（2026-06-17） | ✅ 客户端，需 MaLiLib |
| **Carpet**（fakeplayer 全功能） | `26.2`（2026-06-17） | ✅ 服务端 |
| **MCPFabric** | 0.2.0+，50+ tools，1.21.1–26.2，MIT | ✅ 客户端 + 服务端 |
| **Baritone MCP Bridge** | v0.3.0（26.2，扩展 8766 端点） | ✅ 最贴合本项目 |
| **Minecraft MCP Server Mod** | 26.2，localhost:8080 | ✅ |

⚠️ **26.1 起 MC 发布未混淆版本**，这是十年来最大断裂：所有 1.21.11 及更早的 mod 至少要重新编译；Yarn 已被 Fabric 停止官方支持，必须用 **Mojang 官方命名**。

---

## 二、你已有的资源

| 仓库 | 内容 | 用途 |
|---|---|---|
| `kongbai9288/-AI-` | **AI_DEV_GUIDE.md**（26.2 实测校准）+ JDK25 / Fabric Loader / Fabric API / mod 模板 | 这就是你说的"开发相关资源"，工具链齐全 |
| `kongbai9288/deepchatbykongbai9288` | 26.2 Fabric 服端 mod，`!ai <问题>` → GLM-4.7-flash | 现成的 LLM 入口，可复用 |
| `kongbai9288/-26.2` | 固定种子找最低沼泽小屋工具 | 与本项目无关 |

⚠️ `-AI-/toolchain/yarn-1.21.11+build.6.jar` 是 **1.21.11** 的映射，**对 26.2 无效**，别用。

---

## 三、目录

```
README.md              本文件：版本矩阵 + 资源清单 + 待确认问题
docs/架构方案.md        完整三层架构、NBT 摘要策略、战斗方案、落地路线
template-26.2/         已按 26.2 校准的构建文件（可直接 cp 成新项目）
  ├─ build.gradle      noIntermediateMappings + 无 mappings + jar 产物
  ├─ gradle.properties JDK25 / 26.2 / Loader 0.19.5 / API 0.160.0+26.2 / Gradle 9.7.0
  ├─ settings.gradle
  └─ fabric.mod.json
```

旧模板 `templates/fabric-mod/` 是 **1.21.4 + Yarn**，在 26.2 下编不过——`template-26.2/` 是它校准后的替换版。

---

## 四、待你拍板的 4 个问题

1. **部署形态**：单机（integrated server）还是专用服务器？
   → 单机：Baritone / Printer / Carpet 能一起用；专用服务器：客户端 mod 只有装了的那台机器能用，fakeplayer 走服务端。
2. **桥接层**：直接用 Baritone MCP Bridge + MCPFabric 组合，还是自研一个 bridge mod？
   → 自研可控性最高（能自由塞 NBT 摘要、自定义战斗 Process），但工作量最大。
3. **AI 入口**：继续用你现有的 GLM-4.7-flash（deepchat），还是接 Claude Code / 其他 MCP 客户端？
4. **第一目标**：生存自动化（挖矿/建造/ farm）优先，还是战斗/探索优先？
   → 建议生存优先，P0 最容易出效果。

---

## 五、安全提醒

- Baritone / Printer 在多数公共服务器属**违规**，请只在自己的世界或自己的服务器使用。
- 桥接层务必保留命令白名单，禁用 `/op`、`/stop`、删世界类命令。
