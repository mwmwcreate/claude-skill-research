# research — Claude Code Skill

外部リサーチが必要になったとき、Claude Code が**現在のプロジェクトを理解したうえで
Research Request を組み立て、外部の Researcher へ調査を委譲する**ためのSkill。

「ChatGPTに質問するSkill」ではない。
どの判断が、どの外部事実が分からないせいで止まっているのかを特定し、
調査を依頼し、返ってきた結果を評価して元の作業に戻すまでを扱う。

## 設計

Researcher（誰が調べるか）と、受け渡し方法（どう渡すか）を分離している。

| 層 | ファイル | 役割 |
|---|---|---|
| 判断ロジック | `SKILL.md` | Researcher非依存。委譲判断 / Research Level / 結果評価 |
| 依頼フォーマット | `templates/research-request.md` | Researcher非依存の依頼書テンプレート |
| 受け渡し | `researchers/chatgpt.md` | ChatGPT + 手動コピペ固有の作法 |

現在の構成は **Researcher = ChatGPT / 受け渡し = 手動コピー&ペースト（Human-in-the-loop）**。
OpenAI API・MCP・ローカルLLM へ移行する場合は `researchers/` にアダプタを足し、
`SKILL.md` の構成表を書き換える。上2層は触らない。

## Research Level

| Level | 用途 |
|---|---|
| Quick | 単一の事実・仕様・事例の確認 |
| Standard | 複数ソースの比較と分析 |
| Deep | 論文調査・競合分析・UXリサーチ・市場調査 |

## インストール

```bash
git clone https://github.com/mwmwcreate/claude-skill-research.git ~/.claude/skills/research
```

`~/.claude/skills/` に置けば全プロジェクトで使える。
特定プロジェクトだけで使うなら `<project>/.claude/skills/research` へ。

## 使い方

```
/research 〈調べたいこと〉
```

Claude Code が現在のプロジェクトを読んだうえで Research Request を生成し、
コピー可能な形で提示して止まる。ChatGPT に貼り、返ってきた Research Result を
そのまま Claude Code に貼り戻すと、評価してから元の作業を再開する。

外部事実を推測で答えるより確認した方が適切な場面では、Claude 側から自発的に提案する。

## 方針

- 一次情報を優先し、情報の日付を確認する
- 仮説を無条件に肯定させない。反証・別解釈を必ず探させる
- 見つからなかったことは推測で埋めさせない
- Research Request は外部サービスに渡る。秘密情報は載せない
- Research そのものを目的化しない
