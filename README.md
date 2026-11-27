# Enhancer

把想让 AI 做的事写在 Codex 的 `$enhancer` 或 Claude Code 的 `/enhancer` 后面，就能得到一段可以直接复制到目标 AI 的提示词。它也可以修改已有提示词。比如你只想改最后一句，它会保留前面的原文和排版。Codex 版位于 [codex/enhancer](codex/enhancer/)，Claude Code 版位于 [claude/enhancer](claude/enhancer/)。

## 使用示例

在 Codex 中输入以下内容。在 Claude Code 中把 `$enhancer` 换成 `/enhancer`。

```text
$enhancer 帮我写一条英文提示词，让 AI 运行 report.py 分析 {{csv_path}}，只比较 revenue 和 cost，只输出含这两个字段的 JSON。
```

你会得到一条类似这样的英文指令。

```text
Run report.py on {{csv_path}} and compare only revenue and cost. Return a JSON object with exactly two fields, "revenue" and "cost".
```

`{{csv_path}}` 会保留到成品里，供你使用时填写。运行脚本和输出 JSON 都是这条英文指令交给目标 AI 的任务。

如果目标 AI 看不到当前聊天，Enhancer 会把“按刚才的标准”换成前文中明确提到的标准。遇到必须取舍的要求，例如既要合法 JSON 又要在 JSON 后附一段说明，它会先问你想保留哪一种格式。

修改已有提示词时，把修改要求和原稿写在同一条消息里。Enhancer 只改你指定的部分，其余文字和排版逐字保留。

```text
/enhancer 只改最后一句，改成用 Markdown 表格输出，列为地区、同比降幅、原因。原稿如下：

你是一名资深数据分析师。请阅读 {{report_path}} 中的季度报告……

输出要简洁一些。
```

## 安装

### Codex

将下面这行发给 Codex 的技能安装器。安装完成后，在新聊天中输入 `$enhancer` 即可调用。

```text
$skill-installer 请从 https://github.com/billcjy1990/enhancer/tree/main/codex/enhancer 安装 Enhancer。
```

### Claude Code

把 `claude/enhancer` 文件夹复制到 Claude Code 的个人技能目录 `~/.claude/skills/`。在 Windows PowerShell 中运行：

```powershell
git clone https://github.com/billcjy1990/enhancer.git
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse enhancer\claude\enhancer "$HOME\.claude\skills\enhancer"
```

在 macOS 或 Linux 中运行：

```bash
git clone https://github.com/billcjy1990/enhancer.git
mkdir -p ~/.claude/skills
cp -R enhancer/claude/enhancer ~/.claude/skills/enhancer
```

只想在某个项目里使用时，改为复制到该项目的 `.claude/skills/enhancer`。安装后开一个新会话，输入 `/enhancer` 加上你的需求即可调用。你说“帮我写个 prompt”之类的话时，Claude 也可能自动调用它。

## 模型要求

### Codex

改写使用 `gpt-6.2-sol`，推理强度为 `medium`。在已确认使用这组配置的聊天里，Enhancer 直接改写。在其他聊天里，它会调用使用这组配置的子代理，因此运行环境需要支持子代理模型选择，并且账号需要有该模型的使用权限。指定配置不可用时，Enhancer 会告诉你原因，由你决定是否改用其他模型。

### Claude Code

改写使用 `claude-haiku-5-5`，推理强度为 `medium`，由 [SKILL.md](claude/enhancer/SKILL.md) 开头的 `model`、`effort` 字段指定，与当前会话用的模型和推理强度无关。技能在独立上下文（`context: fork`）中运行，看不到当前对话，所以原稿和必要的前文要写进同一条消息。账号需要有 Haiku 5.5 的使用权限。在自动模式（auto mode）下，Claude Code 可能跳过技能指定的模型，改用会话模型。
