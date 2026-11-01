# CHAT-1101-MCK-28

- 着手日時: 2026-11-01
- 対象issue: #304・#296
- ブランチ: work/1101-mck-inv
- 着手時HEAD: cc449e20a

## 指示

【Claude作成】Claude Code 向け指示：#304 の (5) issue の棚卸しの深い観点（ほかの issue の完了で不要になったもの・#296 の子で新サイト送りをやめるもの）の候補を挙げる（判断待ちで止まる） Chat-Ref: CHAT-1101-MCK-28 マージ: ドキュメントのみ（ログ・docs/decisions）なので完了報告のうえ cloudflare へ入れてよい。issue を閉じる・ラベルや本文を変えることはしない（候補を挙げて判断待ちで止まる） 貼る時機: いつでも（CHAT-1101-MCK-27 と前後してよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1101-mck-inv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1101-mck-inv を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1101-mck-inv origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#304 の (5)（issue の棚卸し）で、CHAT-1101-MCK-23 は機械的に見られる観点だけを見た（Open 214件・保留 51件・90日以上の放置 0件・保留の理由が解消したもの 0件）。中身の判断が要る2つの観点を今月行う。
決定（2026-11-01、平野さん）

* (5) の深い棚卸しは今月行う（「両方やる」。#357 の片付けと同じ問いで答えた）

前提（チャット側。平野さんの決定ではない）

* 観点1「ほかの issue の完了で不要になったもの」: Open の issue の本文・最近のコメントが前提にしている issue・ページ・ファイル・ワークフローが、閉じた・廃止・置き換え済み（例: jpml_pros → pro/〈#219〉、旧ページ → houou/・ouka/、Workers Paid への切り替え、ワークフローの統合〈#564〉）になっているもの
* 観点2「#296（新サイト）の子で、新サイト送りをやめたほうがよいもの」: #296 の子（本文のタスクリスト・「新サイトで」と書いた issue）のうち、今のサイトでもう実現した・今のサイトで直すほうが早い・新サイトの方針（docs/decisions/design-renewal.md など）と合わなくなったもの
* 候補ごとに、根拠（どの issue・コミット・docs の記述か）と、案（閉じる／統合先／新サイト送りをやめて今のサイトで扱う／残す）を書く。確信度（高・中・低）を付ける。候補でないものは件数だけ
* 閉じる・本文を変えるのは平野さんの判断の後（別の指示）

手順

1. Open の issue の一覧（番号・題・ラベル・最終更新）を取り、#296 の本文と子の一覧を読む。#304 に着手中のコメントを残す
2. 観点1・2で候補を挙げ、ログの `## 経過` に表で書く（番号・題・観点・根拠・案・確信度）。「判断が必要なこと」に、確信度の高い候補の案をまとめる
3. #304 の「2026-11 実施」のコメントに返信で「(5) の深い棚卸し: 候補 N件（観点1 N件・観点2 N件）。一覧は CHAT-1101-MCK-28 のログ」と書く（本文・状態・ラベルは変えない）。`## 報告` の状態を「判断待ち」にする

止まる条件

* #304 が Closed、または他セッションの着手中コメントがある
* Open の issue が 300件を超える（件数を書いて、手順1だけで止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1101-MCK-28.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1101-MCK-28 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0: 「指示」欄の末尾が指示文の最後の行と一致することを確かめた
- 同じ Chat-Ref のコミット: `git log --all --grep="MCK-28"` は0件。`XXX` の重複は対象外。0章ゲートは実装を含まないため対象外。雛形の行の欠けなし。指示文の issue の state: #304・#296 は Open
- CLAUDE.md: `git log HEAD..origin/cloudflare -- CLAUDE.md` は0件
- 作業ブランチ: work/1101-mck-inv はローカル・リモートとも無く、`git checkout -b work/1101-mck-inv origin/cloudflare` で作った
- #304: Open。11-01 の「2026-11 実施」の後に他セッションの着手中は無い。着手中のコメント: https://github.com/retroeater/mj/issues/304#issuecomment-6552330922

### 1. 一覧

- Open の issue: **215件**（2026-11-01、REST の `issues?state=open`。300件以下なので続ける）
- #296 の sub-issue: 37件（Open 34・Closed 3）。#296 の本文（「新サイト送りにするとき・やめるとき」を含む）を読んだ
- 進め方: サブエージェント4つ（読むだけ。ファイル・git・GitHub に書かない）に分けた。#296 の Open の子 34件（観点1・2）と、それ以外の 180件を 60件ずつ3つ（観点1）。指示は scratchpad の prompt.md（使い捨て）。返ってきた候補を私が読んで確信度・案を直す

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cc449e20）: https://github.com/retroeater/mj-logs/tree/main/guide/cc449e20

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cc449e20/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/cc449e20.md
