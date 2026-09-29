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

## 報告

- 状態: 作業中
- ブランチ: work/SKN
- ログ: https://github.com/retroeater/mj/blob/work/SKN/docs/logs/CHAT-0930-SKL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/SKN
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b39d1043）: https://github.com/retroeater/mj-logs/tree/main/guide/b39d1043

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b39d1043/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b39d1043/docs/handover.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b39d1043/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b39d1043/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/74aa7996.md
