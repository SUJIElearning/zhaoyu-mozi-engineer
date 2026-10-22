# 验证报告（VERIFICATION.md）

> 陌生用户视角实测报告 · zhaoyu-mozi-engineer · 发布前必读

## 一、包完整性验证（隔离目录）

按 zhaoyu-meta-skill 的[发布前验证手册](../zhaoyu-meta-skill/references/verification-guide.md)执行 L1 级验证：**完全隔离目录，把自己当陌生人**，只照 README 操作，不凭记忆补步骤。

| # | 场景 | 命令/操作 | 实际结果 | 状态 |
|---|------|----------|----------|------|
| 1 | 从发布物复制文件 | 复制包到全新隔离目录 | 7 个文件齐全（SKILL.md / README.md / LICENSE / VERIFICATION.md / .gitignore / references/guide.md / evals/trigger_cases.json） | ✅ |
| 2 | frontmatter 有效性 | `node` 解析 SKILL.md frontmatter | `name=zhaoyu-mozi-engineer` 有效 | ✅ |
| 3 | evals JSON 有效性 | `JSON.parse(trigger_cases.json)` | JSON 有效，7 条用例（5 触发 + 2 不触发，含"加载墨子"） | ✅ |
| 4 | DSH 能加载 | 调用 skill 工具加载 `zhaoyu-mozi-engineer` | 成功返回完整 `<skill_content>`，墨子人格 + 四阶段工作流完整 | ✅ |
| 5 | 公开仓库可匿名克隆 | `git clone --depth 1 https://github.com/SUJIElearning/zhaoyu-mozi-engineer.git` | 克隆成功，7 个文件全部到位 | ✅ |

## 二、陌生用户「一句话下载」实测（真实 headless 会话）

**这是最关键的验证**：不是靠推测，而是搭一个真正的陌生用户环境跑真实会话。

**环境搭建**：
- 全新 `DSH_HOME`（`%TEMP%\mozi-fresh-home`）：**skills 目录为空**（没有任何墨子的痕迹）、无全局 AGENTS.md、独立 profile
- 用 `dsh headless "<用户的一句话>"` 启动真实 agent 会话（DSH CLI 的一次性执行模式）

### 实验 1：只给 skill 名字

> 用户输入：`帮我下载 zhaoyu-mozi-engineer 这个 skill`

**实际结果：❌ 失败**。AI 的真实行为：
1. 不知道仓库在哪 → 尝试 `web_search`（失败：搜索端点余额不足）
2. 尝试 `web_fetch` GitHub API（失败：fetch failed）
3. **开始猜地址**：`github.com/zhaoyu-mozi-engineer`、`zhaoyu/mozi-engineer`、甚至猜成"商汤的 Mozi 项目"
4. 跑到本地文件系统乱搜 skill 目录

**结论**：只说名字，AI 无法定位仓库，会**编造/瞎猜地址**。这种"一句话"不可靠。

### 实验 2：给完整仓库地址

> 用户输入：`帮我下载这个 skill 装进 DSH 技能目录：https://github.com/SUJIElearning/zhaoyu-mozi-engineer`

**实际结果：⚠️ 逻辑全部正确，卡在环境网络**。AI 的真实行为：
1. ✅ 正确理解任务（下载 GitHub 仓库 → 装进 DSH 技能目录）
2. ✅ 正确读取 `DSH_HOME` 环境变量，定位到 `$DSH_HOME/skills` 目录
3. ✅ 正确执行 `git clone`
4. ❌ clone 失败：该 headless 环境的 shell 网络被沙箱封锁（连 baidu.com 都不通）
5. AI 还尝试了 ghproxy / gitclone / fastgit 等镜像与代理探测（说明它会为失败找替代路径）

**结论**：给了 URL，AI 的流程是正确且完整的；能否成功取决于两个环境变量：
- **shell 网络是否可用**（沙箱可能封锁外网，需要提权或用户环境允许）
- **写技能目录的权限**（`$DSH_HOME/skills` 可能在会话工作区之外，默认 workspace-write 策略下会被拒绝，需要批准）

## 三、最终结论（诚实版）

| 用户说法 | 结果 |
|---------|------|
| "帮我下载 zhaoyu-mozi-engineer 这个 skill"（只给名字） | ❌ 不可靠：AI 找不到仓库，会瞎猜地址 |
| "帮我装这个 skill：`https://github.com/SUJIElearning/zhaoyu-mozi-engineer`"（给 URL） | ⚠️ 流程正确；需 shell 能联网、可能需要批准写权限 |
| 自己终端执行 `git clone ... "$HOME/.dsh/skills/zhaoyu-mozi-engineer"` | ✅ 确定可行（已实测，公开仓库匿名克隆成功） |

**推荐的"一句话"话术**（最接近一句话成功）：

> 帮我把这个 skill 装进 DSH 技能目录：https://github.com/SUJIElearning/zhaoyu-mozi-engineer

**为什么带 URL 很关键**：实测证明 AI 不会自己知道 `SUJIElearning/zhaoyu-mozi-engineer` 这个地址；只给名字它就会猜。带上 URL，它就能正确执行安装流程。

**为什么可能需要批准**：DSH 的文件沙箱默认只允许写会话工作区，而技能目录在 `$DSH_HOME/skills`（工作区之外），且联网也可能受沙箱限制。这部分取决于用户自己的 DSH 设置，不是 skill 能控制的。

*验证时间：2026-10-23 · 由昭瑜的发布流程执行 · 全部结论基于真实会话日志，非推测*
