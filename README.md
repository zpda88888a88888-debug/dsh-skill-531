# 531 — 五句话读完，三句话想透，一句话记住

一个 [DeepSeek Harness](https://github.com/deepseek-ai/deepseek-harness)（DSH）skill：把任意一篇文章压缩成**固定三段结构**——5 句话摘要、3 个核心洞察、1 句话总结。

> A DSH skill that compresses any article into a fixed three-part shape: a 5-sentence summary, 3 core insights whose semantics switch by text type, and a 1-sentence takeaway.

**关键词 / Keywords**：摘要 · 精读 · 结构化汇总 · 读书笔记 · 提炼要点 · summarization · structured summary · article digest · reading notes · key insights · DSH skill · Claude skill compatible

---

## 为什么不是"随便总结一下"

普通总结最大的失败模式是**注水**：句子越长越安全、洞察越抽象越不容易被反驳。531 用三条硬约束堵住这个口子：

| 约束 | 具体规则 |
|---|---|
| **配额固定** | 5 / 3 / 1 三个槽位，不多不少 |
| **长度硬上限** | 每句 ≤ 60 字，超了砍，不许为凑满而注水 |
| **可回指** | 每句必须能指回原文具体段落；指不回去 = 脑补，删 |
| **禁止评价性抽象词** | "张力""深刻""引人深思"这类词整类禁用 |
| **供不起就空缺** | 3 条凑不出时写空缺行，并说明为什么凑不出 |

最后一条是关键：**允许空缺**比"必须三条"更能压住幻觉。

---

## 安装

skill 是纯文件，没有依赖，装 = 放到 DSH 会扫描的目录。

### 方式一：用户级（所有项目可用，推荐）

```bash
git clone https://github.com/zpda88888a88888-debug/dsh-skill-531.git ~/.dsh/skills/531
```

### 方式二：项目级（跟着仓库走，团队共享）

```bash
mkdir -p .dsh/skills
git clone https://github.com/zpda88888a88888-debug/dsh-skill-531.git .dsh/skills/531
```

项目级目录优先级高于用户级；把 `.dsh/skills/` 提交进 git，克隆仓库的人就自动拥有这个 skill。

### 方式三：手动复制

```bash
mkdir -p ~/.dsh/skills/531
curl -fsSL https://raw.githubusercontent.com/zpda88888a88888-debug/dsh-skill-531/main/SKILL.md \
  -o ~/.dsh/skills/531/SKILL.md
```

### 方式四：离线 zip（内网 / 没有 git）

拿到 `531-skill.zip` 后：

```bash
unzip 531-skill.zip -d ~/.dsh/skills/
```

zip 内已带 `531/` 前缀，解压即可，不用手动建目录。

### 验证装好了

重启会话后，在新会话里输入 `/531`。看到 skill 内容被注入即成功。

如果没生效，检查两点：

1. 路径必须是 `<某个 skill 根>/531/SKILL.md`，**目录名可以随意，但文件名必须是 `SKILL.md`**（也支持扁平文件 `<根>/531.md`，但不支持嵌套 `**/SKILL.md`）。
2. frontmatter 的 `name` 必须是**带引号的字符串**：`name: "531"`。写成 `name: 531` 会被 YAML 解析成数字，loader 直接丢弃该文件且不报错。

---

## 使用

三种触发方式，效果相同：

```
/531                      ← 显式调用，把一个 skill 用在你刚贴的文章上
用 531 读这篇文章          ← 自然语言，模型自己加载
总结成 531                 ← 同上
```

直接贴文章 + `/531` 最省事。

---

## 它怎么决定"3"是什么

531 的核心设计：**5 和 1 的语义固定，3 的语义随文本类型切换**。判定走两轴，不看文体名。

|  | 管你怎么做 | 不管你怎么做 |
|---|---|---|
| **能验对错** | **Q1 规范执行型**<br>手册 / 标准 / SOP / 合规政策<br>→ `3 个使用约束` | **Q2 事实论证型**<br>行业报告 / 技术博客 / 论文<br>→ `3 个核心洞察` |
| **说不清对错** | **Q3 说服动员型**<br>宣言 / 营销文案 / 政策倡议<br>→ `3 个立场前提` | **Q4 叙事阐释型**<br>小说 / 诗歌 / 散文 / 影评<br>→ `3 条叙事线索` |

所以同一套 531，读合规手册时逼你写"什么条件下这条会误用"，读小说时逼你指出"承重意象出现在哪几处"——**"洞察"的定义跟着文本走，而不是套一个万能模板**。

---

## 输出长什么样

```markdown
## 5 句话摘要
1. …
2. …
3. …
4. …
5. …

## 3 个核心洞察        ← 小标题按象限替换
1. …
2. …
3. …

## 1 句话总结
…
```

---

## 目录结构

```
.
├── SKILL.md      # skill 本体（frontmatter + 指令）
├── README.md     # 本文件
├── LICENSE       # MIT 许可证全文
└── .gitignore
```

`SKILL.md` 的 frontmatter：

```yaml
---
name: "531"
description: "Use when 用户说 /531、用 531 读、总结成 531…"
metadata:
  tags: [summarization, reading, structured-output, "531", 摘要, 精读, 读书笔记]
---
```

---

## 兼容性

- **DeepSeek Harness**：skill 只认 `<root>/<name>/SKILL.md` 或 `<root>/<name>.md`，本仓库按此布局。
- **Claude / 其他支持 `SKILL.md` 的 agent**：frontmatter 的 `name` + `description` 是通用约定，正文可直接复用；`metadata.tags` 会被忽略但不影响加载。

---

## License

MIT
