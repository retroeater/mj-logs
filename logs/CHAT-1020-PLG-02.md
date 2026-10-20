# CHAT-1020-PLG-02

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1020-plg
- 着手時HEAD: 779477b8e

## 指示

【Claude作成】Claude Code 向け指示：claude-md-management の監査（claude-md-improver）を CLAUDE.md に当て、報告だけをログに残す Chat-Ref: CHAT-1020-PLG-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも（CHAT-1020-PLG-01 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
公式プラグインの試行（CHAT-1020-PLG-01）の 2 つ目。claude-md-management の skill `claude-md-management:claude-md-improver` を mj の CLAUDE.md に当て、品質の評価と直す候補をログに残す。CLAUDE.md・docs は変えない（監査の報告だけ）。変更はログ（docs/logs/）と docs/decisions/ のみ。
決定（2026-10-20、平野さん）

* 公式プラグインは claude-code-setup → claude-md-management の監査 の順で試す

前提（チャット側。平野さんの決定ではない）

* `claude-md-improver` は報告の後に書き換えまで進む作り（CHAT-1020-PLG-01 の報告 (c)）。書き換えはさせず、止まる条件で差分を捨てる
* CLAUDE.md には容量の上限（28KB、警告域 26KB）と、規則を足すときの「知見 → 選んだ層 → 理由」の表（#492）がある（要確認。CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。監査の提案は、これに合うかで評価する
* 今の CLAUDE.md は約 24,883 bytes（CHAT-1020-PLG-01 の報告 (b) の値。要確認）

手順

1. CLAUDE.md「CLAUDE.md / handover.md の更新ルール」と docs/notes/skills.md「コンテキストの負荷」を読み、CLAUDE.md の今の容量（bytes）を測ってログの `## 経過` に書く。
2. Skill ツールで `claude-md-management:claude-md-improver` を呼ぶ。引数は「mj の CLAUDE.md（リポジトリ直下。ほかの CLAUDE.md があればそれも）を監査し、品質の報告だけを出す。ファイルは変えない。」。skill の出力（品質の評価・指摘・直す候補）は要約せず全件をログの `## 経過` に写す。呼んだ後に `git status --short` を取り、ログに書く。
3. ログの `## 報告` の「判断が必要なこと」に、指摘・候補の 1 件ごとに次を書く: (a) mj の規則（更新ルール・上限・「テスト・hook で確かめた規則はガイド文から消す」）に照らして、採るべきか・採れないか・既に別の文書（docs/notes/・docs/decisions/）にあるか (b) 採るなら、どの文書のどの節を変えるか（この指示では変えない）。最後に、プラグインの監査が mj に役立つか（独自の文書構成を理解できていたか）の所見を 3 行以内で書く。

止まる条件

* 手順 2 で `git status --short` に差分（CLAUDE.md・ほかのファイル）が出たとき: `git checkout -- <ファイル>` で捨て、何を変えようとしたか（差分の要約）をログに書いて、手順 3 へ進む（止まらない。ただし新しいファイルが作られていたら削除し、名前を書く）
* skill がセッションの一覧に無い、または呼べないとき: その旨を書いて止まる
* （未マージの work/ ブランチとの重なり）この指示は docs/logs/ と docs/decisions/ しか変えないので、重なりでは止まらない

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1020-PLG-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1020-PLG-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 対応中
- ブランチ: work/1020-plg
- ログ: https://github.com/retroeater/mj/blob/work/1020-plg/docs/logs/CHAT-1020-PLG-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1020-plg
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: 未記入
- 未確認の項目: 未記入
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a59dc74）: https://github.com/retroeater/mj-logs/tree/main/guide/4a59dc74

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a59dc74/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
