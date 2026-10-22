# 墨子 · 插件工程师 (zhaoyu-mozi-engineer)

> 🛠️ 兼爱非攻，明鬼天志。道在守规矩、证据说话、不添乱。
> —— 墨子，昭瑜麾下的插件工程师。**Zhaoyu 出品**

一款给 DeepSeek Harness (DSH) 用的 **Agent Skill**：让 AI 变成"墨子"——一位靠得住、不瞎编、不添乱的插件工程师。装插件、造插件、修插件，一条龙。

## 一句话

加载这个 skill 后，无论你是 **找插件、装插件、自己写插件、还是插件出 bug 要调试**，AI 都会自动切换到墨子模式——先独立排查、再汇报结论、不编造、不猜测、测试驱动。

## 安装

GitHub 仓库：[SUJIElearning/zhaoyu-mozi-engineer](https://github.com/SUJIElearning/zhaoyu-mozi-engineer)

克隆到 DSH 技能目录（一行命令）：

```bash
git clone https://github.com/SUJIElearning/zhaoyu-mozi-engineer.git "$HOME/.dsh/skills/zhaoyu-mozi-engineer"
```

DSH 会监视技能目录，克隆进去**立即生效，不用重启、不用改配置**。装好后在 DSH 对话里说 **"加载墨子"** 即可。

装完目录结构是这样（DSH 只认 `SKILL.md` 所在目录，其他是文档）：

```
~/.dsh/skills/zhaoyu-mozi-engineer/
├── SKILL.md                  # 入口（DSH 加载用）
├── README.md                 # 本文件
├── LICENSE                   # MIT 许可证
├── VERIFICATION.md           # 陌生用户验证报告
├── references/
│   └── guide.md              # 详细手册（含失败对照表、坑的解法）
└── evals/
    └── trigger_cases.json    # 触发边界测试用例
```

## 小白 3 步用起来

**第 1 步：安装** → 上面那条命令即可。

**第 2 步：加载墨子**
在 DSH 对话中说：
> "加载墨子"

或者：
> "加载昭瑜的工程师"

如果你希望它**常驻生效**，把 `SKILL.md` 的身份部分加到工作区的 `AGENTS.md` 里。

**第 3 步：直接说需求**
用你最自然的话说出来就行，比如：
- "帮我装一个能发微信通知的插件"
- "我想自己写一个记录工作时间的插件"
- "这个插件跑起来有 bug，帮我看看"
- "有没有什么好用的 DSH 插件推荐？"

AI 会自动：
1. 先确认你要什么（问 1-2 个问题）
2. 找现成的或自己造
3. 自己调试，只汇报结论
4. 拿不准时才回来找你确认

## 它解决了什么问题

| 痛点 | 怎么解决的 |
|------|-----------|
| AI 写插件一堆 bug，反复调试好累 | 内置了完整的调试方法论，AI 先自己定位修复 |
| AI 遇到不懂的 API 就瞎编 | 强制要求：不知道的先查（cordis_inspect_query），不猜 |
| AI 说"应该没问题"但实际不行 | 测试驱动原则：必须验证通过才说完成 |
| 找不到好插件，每次从零造 | 优先搜索生态，有现成的就不重复造轮子 |

## 本包内容

```
zhaoyu-mozi-engineer/
├── SKILL.md                  # 入口（DSH 加载用）
├── README.md                 # 本文件
├── LICENSE                   # MIT 许可证
├── VERIFICATION.md           # 陌生用户验证报告
├── references/
│   └── guide.md              # 详细手册（含失败对照表、坑的解法）
└── evals/
    └── trigger_cases.json    # 触发边界测试用例
```

## 技术要求

- DSH 环境（DeepSeek Harness）
- 找插件需要 GitHub 网络访问
- 自建插件需要 DSH 的 `cordis-plugin-development` 工具集

## 许可

MIT © 昭瑜 (Zhaoyu) · [SUJIElearning](https://github.com/SUJIElearning)

*Zhaoyu 出品 · 开源版*