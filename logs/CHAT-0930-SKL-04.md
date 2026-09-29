# CHAT-0930-SKL-04

- 着手日時: 2026-09-30
- 対象issue: なし
- ブランチ: work/SKN
- 着手時HEAD: e932470d

## 指示

【Claude チャット作成】指示文 SKL-04: branch-operations.md の hook との不整合と、instruction-template.md の公開
Chat-Ref: CHAT-0930-SKL-04 作業ブランチ: work/SKN（origin/cloudflare 起点）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。
このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
目的
SKL-03 で見つかった2つの不整合を直す。文書と、ガイド文書を mj-logs に写す仕組みだけの変更。

1. docs/notes/branch-operations.md はローカルブランチの削除に `git branch -D` を書いているが、hook（.claude/hooks/mj-git-guard.py）が `-D` を deny するため実行できない（SKL-03 で `-d` で代替）。
2. チャット側は docs/instruction-template.md を読めない。ログ末尾のガイドリンク一覧に無く、mj-logs の guide/<SHA>/ の tree ページは自動アクセスが拒否される。docs/notes/chat-side-operations.md は指示文を作る前にこの雛形を読むよう求めているので、写す対象とリンク一覧に加える。

手順

1. branch-operations.md の削除手順を `git branch -d` に直す（マージ済みが前提であることを一言添える。未マージのブランチを消す必要が出た場合の扱いは、hook の deny を外さず「平野さんが GitHub の画面で消す」とする）。同じ文書に `-D` 前提の記述が他にあれば同様に直す。CLAUDE.md・cloud-sessions.md に `-D` の記述があるかも検索し、あれば報告（CLAUDE.md は今回触らず報告のみ）。
2. ガイド文書を mj-logs に写す仕組み（Actions のワークフロー）と、ログ末尾のガイドリンク一覧を書く規則（CLAUDE.md か docs 内のどこにあるか調べる）の両方に docs/instruction-template.md を加える。写す対象の定義が一か所（一覧ファイル等）なら、そこだけ変える。CLAUDE.md の規則を変える必要がある場合は、変更案と変更後のサイズを示して止まる。
3. 変更後、Actions が instruction-template.md を guide/<SHA>/ に写すことを、マージ後の実行結果（mj-logs 側のコミットに該当ファイルが含まれること）で確かめ、ログに blob URL を書く。マージ前に確かめる手段があればそれでもよい。

マージ
完了条件を満たしたら `git push origin work/SKN:cloudflare` でマージしてよい（平野さん承認済み。hook の ask が出る）。マージ後の自動再生成でサイトの表示は変わらない想定（docs とワークフローのみ）。手順3の確認はマージ後に行い、結果をログに追記してから報告する。
止まる条件

* CLAUDE.md の変更が必要なとき（案を示して止まる）
* 写す仕組みの変更が Actions の使用量（#298）を目に見えて増やすとき（例: 起動条件が変わる）

完了条件

* branch-operations.md に `-D` の手順が残っていない
* ログ末尾のガイドリンク一覧に docs/instruction-template.md の行があり、mj-logs の guide/<SHA>/docs/instruction-template.md が実際に読める（blob URL を報告に書く）
* assets-check 警告なし、マージ済み
* 最終報告に、変えたファイルの一覧、CLAUDE.md・cloud-sessions.md の `-D` 記述の有無、instruction-template.md の blob URL を書く

最後に、上の完了条件を満たしているか自分で確かめてから報告してください。 不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 指示の末尾照合: 貼られた指示文の最後の行は「…この行が指示文の最後の行です。」で、上の `## 指示` の末尾と一致
- Chat-Ref 確認: `git log --all --grep="CHAT-0930-SKL-04"` 0 件。`SKN` も全ブランチのコミットに 0 件、リモートに work/SKN 無し
- ブランチ: `git checkout -b work/SKN origin/cloudflare`（e932470d）

### 手順1: `-D` の記述

- branch-operations.md: `-D` の記述は「ブランチを削除するとき」の1か所（`先頭が origin/cloudflare の祖先と確認したうえで -D で削除する`）だけ。
  `-D` を `-d` に直し、「ローカルの削除はマージ済みと確認したブランチに限り `git branch -d`。`-d` は現在の HEAD に対する判定なので、HEAD が
  `origin/cloudflare` を含む別のブランチに移ってから消す。それでも拒否される・未マージを消す必要が出たときは、hook を外さず平野さんが GitHub の画面で消す」とした。
  `-D` を deny する hook は docs/notes/skills.md への参照で示した。ほかの `-D` 前提の記述は無い
- CLAUDE.md: `-D` の記述なし（`git branch -D`・`-D` で検索）。変更していない
- cloud-sessions.md: `-D` の記述なし（「ブランチの削除」はリモートの削除がプロキシに拒否される話のみ）

### 手順2: instruction-template.md

- 調べた結果、**写す対象にはすでに入っていた**: `scripts/sync_guides.py` の `ALLOWED_PATTERNS` に `docs/instruction-template.md` があり、
  `sync-logs.yml` の起動条件（paths）にもある。mj-logs の `guide/<SHA>/docs/instruction-template.md` は既に写されていた（SKL-03 のマージ後の実行でも 30 ファイルに含まれる）。
  足りなかったのはログ末尾のリンク一覧だけ
- リンク一覧を決めるのは `scripts/sync_guides.py` の `LINKED_DOCS`（1か所）。`'docs/instruction-template.md'` を CLAUDE.md・handover.md の次に足した。
  CLAUDE.md にリンク一覧の規則は無く、CLAUDE.md の変更は要らない。ワークフロー（起動条件・ジョブ）は変えていないので、Actions の使用量は変わらない
- 一覧を説明している docs/notes/cloud-sessions.md「作業ログ」の1文を、instruction-template.md を含む形に直した
- テスト: `test_footer_links_instruction_template` を追加。`python3 -m unittest discover -s scripts/tests` → 386 tests OK
- assets-check（7b088c47、check-run 109513042200）: success。注釈は Node.js 20 非推奨の warning と ubuntu-latest 移行の notice のみで、ワークフロー自身の警告なし

### マージと手順3の確認

- マージ: 再 fetch のうえ `git merge-base --is-ancestor origin/cloudflare HEAD` が真であることを確かめて `git push origin work/SKN:cloudflare`（e932470d..7b088c47）
- `sync-logs.yml`（run 36600466253）が success。mj-logs の `guide/HISTORY` の最後の行が 7b088c47 になった
- `guide/7b088c47/docs/instruction-template.md` は raw で HTTP 200、先頭は「# 指示文テンプレート（チャット側 → Claude Code）」。
  `guide/7b088c47/docs/notes/branch-operations.md` も 200 で、`git branch -d` の記述を含む
- blob URL: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/docs/instruction-template.md

## 報告

- 状態: 完了
- ブランチ: work/SKN
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-SKL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/SKN
- 確認用URL: なし（docs・スクリプト・テストのみ。サイトの表示は変えていない）
- マージ: 済（7b088c47。ログの最後の push も cloudflare へ）
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目:
  - このログ自身の末尾に instruction-template.md のリンク行が付くか（この push の後の sync-logs で付く。下の経過を参照）
  - マージの push で hook の ask が出たか（セッション側には表示が返らない）
- エラー: なし

変えたファイル: docs/notes/branch-operations.md・docs/notes/cloud-sessions.md・scripts/sync_guides.py・scripts/tests/test_sync_guides.py・docs/logs/CHAT-0930-SKL-04.md。
CLAUDE.md・cloud-sessions.md の `-D` 記述: なし。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 7b088c47）: https://github.com/retroeater/mj-logs/tree/main/guide/7b088c47

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/7b088c47/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74aa7996.md
