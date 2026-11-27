# Enhancer for Codex

把想让 AI 做的事写在 Codex 的 `$enhancer` 后面，就能得到一段可以直接复制到目标 AI 的提示词。它也可以修改已有提示词。比如你只想改最后一句，它会保留前面的原文和排版。技能文件位于 [codex/enhancer](codex/enhancer/)。

## 使用示例

在 Codex 中输入以下内容。

```text
$enhancer 帮我写一条英文提示词，让 AI 运行 report.py 分析 {{csv_path}}，只比较 revenue 和 cost，只输出含这两个字段的 JSON。
```

你会得到一条类似这样的英文指令。

```text
Run report.py on {{csv_path}} and compare only revenue and cost. Return a JSON object with exactly two fields, "revenue" and "cost".
```

`{{csv_path}}` 会保留到成品里，供你使用时填写。运行脚本和输出 JSON 都是这条英文指令交给目标 AI 的任务。

如果目标 AI 看不到当前聊天，Enhancer 会把“按刚才的标准”换成前文中明确提到的标准。遇到必须取舍的要求，例如既要合法 JSON 又要在 JSON 后附一段说明，它会先问你想保留哪一种格式。

## 安装

将下面这行发给 Codex 的技能安装器。安装完成后，在新聊天中输入 `$enhancer` 即可调用。

```text
$skill-installer 请从 https://github.com/billcjy1990/enhancer/tree/main/codex/enhancer 安装 Enhancer。
```

## 模型要求

改写使用 `gpt-6.2-sol`，推理强度为 `medium`。在已确认使用这组配置的聊天里，Enhancer 直接改写。在其他聊天里，它会调用使用这组配置的子代理，因此运行环境需要支持子代理模型选择，并且账号需要有该模型的使用权限。指定配置不可用时，Enhancer 会告诉你原因，由你决定是否改用其他模型。
