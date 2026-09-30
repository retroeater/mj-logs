# CHAT-0930-HKG-04

- 着手日時: 2026-09-30
- 対象issue: なし
- ブランチ: work/0930-hkg-04
- 着手時HEAD: a7ffc243

## 指示

【Claude作成】Claude Code 向け指示文（HKG-04）
Chat-Ref: CHAT-0930-HKG-04 作業ブランチ: work/0930-hkg-04（origin/cloudflare 起点） マージ: 承認済み（チャットで）。ただし「止まる条件」に当たったら判断待ちで止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。
目的
Code の処理中に確認ダイアログで承認を求める運用をやめます。承認はチャットで事前に行い、その結果を指示文の「マージ:」の行に書く運用に移します（平野さん決定、2026-09-30）。

* hook（.claude/hooks/mj-git-guard.py）の ask は廃止し、確認なしで通します。deny（拒否）はそのまま残します。
* cloudflare への成果物のマージは、指示文に「マージ: 承認済み（チャットで）」とあるときだけ行います。無ければ、今までどおり判断待ちで止まります（既定は止まる側）。docs/logs のみ・ドキュメントのみなど、今セッションに任されている反映は変えません。
* チャット側は、指示文を作る前にマージの可否を平野さんに確かめ、「マージ:」の行に書きます。プレビューを見て決める変更は「判断待ちで止まる」と書きます。

手順

1. 現状を調べる
   * 読むもの: mj-git-guard.py、.claude/settings*.json（hooks と permissions）、CLAUDE.md「ブランチ運用」「作業ログ」、docs/notes/skills.md、docs/instruction-template.md、docs/notes/chat-side-operations.md。
   * hook が ask を返す場合と deny を返す場合を、すべて一覧にしてログに書きます。
   * settings の permissions に、確認を求める規則（ask 等）があれば、それも一覧にします。
2. hook の変更
   * ask を返している箇所を、確認なしで通す（allow、または何も返さない）ように変えます。
   * 不要になった判定（logs_only_push など）は、残す理由が無ければ消して構いません。
   * deny の判定は変えません。
   * 手順1で見つかった ask が cloudflare・claude/* への push 以外にもあれば、変えずに止まってください（止まる条件）。settings の permissions に確認の規則があった場合も同様です。
3. 試験
   * hook に JSON を与えて、次の結果をログに表で書きます。
      * cloudflare へのコード込みの push → 確認なし
      * claude/* への push → 確認なし
      * docs/logs のみの push → 確認なし
      * deny の全ケース → 今までどおり deny
4. 文書の変更（writing-for-agents skill に従い、最小限の言葉で）
   * CLAUDE.md「ブランチ運用」: 成果物の cloudflare へのマージは、指示文に「マージ: 承認済み（チャットで）」があるときだけ行う。無ければ判断待ちで止まる。承認は Code の処理中には求めない。「作業ログ」の「hook は確認なしで通す」は実態に合わせます。
   * docs/instruction-template.md: 冒頭の Chat-Ref・作業ブランチの行の後に「マージ: 承認済み（チャットで）／判断待ちで止まる」の行を足します。
   * docs/notes/chat-side-operations.md: チャット側は指示文を作る前にマージの可否を平野さんに確かめ、「マージ:」の行に書く。
   * docs/notes/skills.md: hook の説明を実態に合わせます。
   * 容量判定（CLAUDE.md「更新ルール」）を実行してログに書きます。
5. マージ
   * 承認済みなので、止まる条件に当たらなければ cloudflare へマージします。
   * このマージの push は旧 hook で判定されるので、確認ダイアログが1回出ます（平野さんが許可します）。
   * その後の、結果を書くログの追いの push は新 hook で判定され、確認は出ないはずです。push の直前に hook の判定をログに書いてから push してください。
   * Actions と Workers Builds の結果もログに書きます。

止まる条件

* 手順2の、cloudflare・claude/* 以外の ask、または settings の確認の規則が見つかった。一覧と提案をログに書き、work ブランチにだけ push して判断待ちにします。
* 試験で期待と違う結果が出た。
* 容量判定が警告になった。

完了条件
hook と文書の変更が cloudflare に入り、このログが追いの push で cloudflare に入っていること。
内容を理解したら、着手前に作業ブランチ名と識別子の確認結果を一言返してから始めてください。

## 経過

- 識別子: CHAT-0930-HKG-04 のコミットは無い。HKG は同じセッションの HKG-01〜03 だけで使用

## 報告

- 状態: 対応中
- ブランチ: work/0930-hkg-04
- ログ: https://github.com/retroeater/mj/blob/work/0930-hkg-04/docs/logs/CHAT-0930-HKG-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-hkg-04
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 71e77c16）: https://github.com/retroeater/mj-logs/tree/main/guide/71e77c16

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/71e77c16/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/71e77c16/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/71e77c16/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/71e77c16/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/71e77c16/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/23c98011.md
