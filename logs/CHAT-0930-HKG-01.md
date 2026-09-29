# CHAT-0930-HKG-01

- 着手日時: 2026-09-29
- 対象issue: なし
- ブランチ: work/0930-hkg-01
- 着手時HEAD: 263bee89
- 識別子: HKG は全ブランチのコミット・`docs/logs/` の履歴・リモートのブランチ名に無く、重複なし（替えていない）

## 指示

【Claude作成】Claude Code 向け指示文（HKG-01）
Chat-Ref: CHAT-0930-HKG-01 作業ブランチ: work/0930-hkg-01（origin/cloudflare 起点）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。 識別子 HKG は、チャット側が使用済み一覧を確かめずに付けたものです。重複していれば、別の3文字に替えてログの冒頭に書いてください。
目的
.claude/hooks/mj-git-guard.py は、cloudflare への push を一律で ask にしています。そのため、確認指示のログ（docs/logs のみ）を cloudflare へ入れるたびに承認を求められ、回数が多すぎる状態です。CLAUDE.md「ブランチ運用」では、ログのみの反映はもともとセッションに任されています。この運用に hook を合わせます。確認なしで通す範囲は docs/logs/ 配下だけにします（平野さん決定）。ガイド文書を含む docs/ のほかの場所は、引き続き ask にします。
手順

1. mj-git-guard.py、.claude/settings*.json の hooks 設定、docs/notes/skills.md、CLAUDE.md「ブランチ運用」「作業ログ」を読みます。そのうえで、今の判定（cloudflare・claude/* を ask にする箇所と、その理由文）と、ログを cloudflare へ入れる手順の現状（1回の指示で何回 cloudflare へ push する書き方になっているか）をログに書いてください。
2. hook に次の判定を足します。push 先が cloudflare のとき、次の3つをすべて満たす場合だけ allow にし、それ以外は今の ask のままにします。
   * push 元（コマンドに書かれた `<src>:cloudflare` の src。省略時は HEAD）が origin/cloudflare から fast-forward で入る。
   * origin/cloudflare..src の差分ファイルがすべて docs/logs/ 配下にある（追加・変更に限る。削除・リネームを含むなら ask）。
   * force 系オプション（--force・-f・--force-with-lease・+refspec）が無い。
   * git コマンドの失敗・解釈できないコマンド・複数 refspec などで判定できない場合は ask にしてください（fail closed）。hook の中では fetch しないでください。origin/cloudflare が古いと差分に他の変更が混ざりますが、その場合は ask 側に倒れるので問題ありません。
   * claude/* への push など、ほかの判定は変えないでください。
   * 直すときは writing-for-agents skill に従い、理由文は短く保ってください。
3. テストをします（手元で hook に JSON を与える形でよい）。少なくとも次の各ケースについて、期待どおりの結果になることをログに表で書いてください。
   * docs/logs のみで ff → allow
   * docs/handover.md を含む → ask
   * コードを含む → ask
   * 非 ff → ask
   * --force → 今までどおり
   * claude/* → ask
   * 画像のコマンド `git fetch -q origin && git merge-base --is-ancestor origin/cloudflare HEAD && git push origin work/…:cloudflare 2>&1 | tail -1` の形 → docs/logs のみなら allow
4. CLAUDE.md（と docs/notes/skills.md の hook の説明）を最小限だけ直します。「docs/logs のみの cloudflare への push は hook が確認なしで通す。それ以外は ask」と書き、1回の指示の中で cloudflare への push は最後の1回にまとめ、途中のログ先行 push は work ブランチにだけ行うことを明確にします。手順1で今の記述がすでにそうなっていれば、文言は足さずにその旨をログに書いてください。CLAUDE.md の容量上限（CLAUDE.md「更新ルール」）の判定を実行し、結果をログに書いてください。

止まる条件

* hook の構造上、push 元と範囲を安全に特定できない。
* 変更後に CLAUDE.md の判定が警告になる。
* 手順2の条件のほかに、緩めたほうがよさそうな箇所が見つかった（提案だけログに書き、実装はしない）。

完了条件
hook の変更はコードの変更なので、マージしないでください。work/0930-hkg-01 に push し、次の3つをログに貼って「判断待ち」で止まってください。平野さんのマージ判断を待ちます。

* 差分（hook・CLAUDE.md・skills.md の全体）
* テスト結果の表
* 容量判定の結果

内容を理解したら、着手前に作業ブランチ名と識別子の確認結果を一言返してから始めてください。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/0930-hkg-01
- ログ: https://github.com/retroeater/mj/blob/work/0930-hkg-01/docs/logs/CHAT-0930-HKG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-hkg-01
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dc536d83）: https://github.com/retroeater/mj-logs/tree/main/guide/dc536d83

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/6442bb63.md
