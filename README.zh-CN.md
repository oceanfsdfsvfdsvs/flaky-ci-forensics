# Flaky CI Forensics

[English](README.md) | [简体中文](README.zh-CN.md)

不要再盲目 rerun CI，先弄清楚它为什么失败。

本项目是 [High Value Agent Skills](https://github.com/oceanfsdfsvfdsvs/codex-high-value-skills) 集合的一部分。

`flaky-ci-forensics` 帮助工程团队把间歇性 CI/测试失败转换成可执行的 triage 决策。它结合失败分类体系、本地解析脚本、报告模板和 guardrails，比直接把日志丢给模型说“帮我 debug flaky test”更稳定。

## 解决什么问题

团队经常浪费 CI minutes 和工程师注意力去重跑最终会通过的失败；同时，真实产品回归也可能被误判成 flake。这个 skill 强制 agent 保留证据、分类失败模式、估算成本，并给出边界清晰的下一步动作。

## 为什么不只用普通 Prompt

- 本地解析 JUnit XML、CI logs 和可选 history CSV。
- 区分 timeout、selector sync、外部服务失败、状态泄漏、runner 问题和真实回归。
- 在有输入时估算每天浪费的 CI minutes。
- 避免 agent 用 “probably flaky” 掩盖真实产品回归。

## 快速开始

```bash
python3 scripts/flaky_ci_forensics.py \
  --junit scripts/fixtures/junit.xml \
  --log scripts/fixtures/ci.log \
  --history scripts/fixtures/history.csv \
  --avg-job-minutes 14 \
  --runs-per-day 60
```

## 输入

- CI 或本地测试生成的 JUnit XML。
- 包含失败片段、retry 信息、runner metadata 或浏览器日志的 CI log。
- 可选 history CSV，包含 test-level run/failure/rerun-pass 统计。
- 可选成本参数，例如平均 job 分钟数和每天运行次数。

## 输出

脚本输出 Markdown 报告，包括：

- CI decision。
- 失败聚类。
- 成本和频率估算。
- 可能根因。
- 最小修复计划。
- 需要增加的 instrumentation 和 guardrails。

## 运行时状态

| Runtime | 状态 |
|---|---|
| Codex/OpenAI 风格 | 支持 `SKILL.md` 和 `agents/openai.yaml`。 |
| Claude Code | 支持 `.claude/skills/flaky-ci-forensics/SKILL.md` mirror，或复制整个目录。 |
| OpenClaw | 提供安装说明；具体 CLI 可见性取决于本地 OpenClaw 配置。 |
| Hermes | 提供说明，但 native handler 未声明为已验证。 |
