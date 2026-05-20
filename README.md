# Sheet Bug Fixer

一个用于 Codex 的流程型 skill，适合处理「Google Sheets / 表格里的 bug 列表」并同步修复状态。

## 作用

这个 skill 会指导 Codex 按表格状态来修 bug：

- 读取 bug 表格，识别 bug 描述、截图预览、状态、备注等列
- 只处理状态为 `新增` 的 bug
- 准备修复前，把状态改为 `修改中`
- 修复完成并验证后，把状态改为 `待验证`
- 如果描述模糊、缺少复现条件、无法确认预期，或不是明确 bug，则标记为 `不解决`，并在备注里写清原因或问题
- 修改代码后运行合适的构建、测试或检查命令
- 最后把修复结果和验证情况汇总给使用者

它不会自动包含你的表格权限、代码仓库权限或 Google 账号权限；这些仍然需要使用者自己拥有。

## 适用场景

适合这样的任务：

```text
请根据这个 Google Sheet 里的新增 bug 帮我修复代码，并同步更新状态。
```

表格通常包含：

- bug 现象或描述
- 截图预览或截图链接，可为空
- bug 状态
- 备注
- 其他辅助列，例如负责人、日期、优先级

skill 会优先通过表头识别列，而不是完全依赖固定列号。

## 安装

在 Codex 中可以用仓库地址安装：

```text
安装这个 skill：https://github.com/xiaocao222111/sheet-bug-fixer
```

安装完成后，可以通过下面的方式显式调用：

```text
Use $sheet-bug-fixer to process this bug spreadsheet: <你的表格链接>
```

也可以直接描述任务，Codex 在匹配到表格驱动修 bug 的场景时会使用它。

## 使用示例

```text
Use $sheet-bug-fixer to process this Google Sheet:
https://docs.google.com/spreadsheets/d/xxxx/edit

第一列是 bug 描述，第二列是截图，第五列是状态。
只处理新增状态的 bug。开始修之前改成修改中，修完改成待验证。
如果无法复现或描述不清楚，改成不解决，并在备注里写清楚原因。
```

Codex 会按流程：

1. 读取表格和状态列
2. 找出 `新增` 的行
3. 先更新待处理行状态
4. 修改本地代码
5. 运行验证命令
6. 回写状态和备注
7. 总结修复内容、未处理原因和验证结果

## 权限说明

如果这个 GitHub 仓库是 public，任何人都可以安装这个 skill。

如果仓库是 private，只有有仓库访问权限的人可以安装。

使用这个 skill 修复真实项目时，还需要：

- 能访问目标 Google Sheet
- 能访问并修改目标代码仓库
- 已配置可用的 Google Drive / Google Sheets connector
- 本地具备项目构建和测试所需环境

## 仓库内容

```text
sheet-bug-fixer/
  SKILL.md
  agents/
    openai.yaml
```

`SKILL.md` 是 Codex 实际读取的流程说明。  
`agents/openai.yaml` 是 Codex UI 展示名称、简介和默认提示。
