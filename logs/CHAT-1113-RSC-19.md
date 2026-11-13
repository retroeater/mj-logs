# CHAT-1113-RSC-19

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 6d4e48852

## 指示

【Claude作成】Claude Code 向け指示：（RSC-18 の再開）「別名」の区分を直した後、旧公式の結果から名前の書き方が違う5卓を候補タブに書き、2行を消す決まりを記録する
Chat-Ref: CHAT-1113-RSC-19
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: 平野さんが「別名」タブの「トレンディエンジェルたかし → たかし」の行の区分を「訂正」に直した後（RSC-18 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-18 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-19` を足す。

目的
CHAT-1113-RSC-18（中断）の再開。目的・決定は RSC-18 の指示文のとおり。
決定（2026-11-13、平野さん。RSC-18 の判断待ちへの回答）

* 1: 平野さんが「トレンディエンジェルたかし → たかし」の行の区分を「訂正」に直した（この指示を貼る前）
* 2: 「別名」の2行は、この指示で候補タブに書けた後に平野さんが消す（RSC-18 のとおり）
* 3: RSC-17 の着手中のコメントは終わった指示のものとして扱ってよい（RSC-18 の進め方のまま）
* RSC-18 の決定（2行は区分「訂正」、旧公式のためだけの「別名」の行は書けたら消してよい、本実行の承認）はそのまま

前提（チャット側。平野さんの決定ではない）

* RSC-18 の dry-run（run 43364265658）では「書く行」が4件で、著名人代表決定戦 決勝（2016-05-21）が区分の空の行のため当たらなかった。区分を直せば5件になる見込み
* RSC-18 の手順3（docs/notes/research-candidates.md の1文・docs/decisions の記録）が済んでいるかは、RSC-18 のログで確かめる（要確認。済んでいなければこの指示で行う）

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）
2. RSC-18 の手順2をやり直す: dry-run で「書く行」が5件・5件とも照合済み・名前の警告（「たかし」「ビッグダディ」が名前の辞書に無い等）が無いことを確かめ、合えば dry_run を外して実行し、候補タブに5行を書く。5行の表（対象キー・候補・確度・備考）を書き、#630 に要約をコメントする
3. RSC-18 の手順3が済んでいなければ行う。「判断が必要なこと」に、平野さんが「別名」から2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消す依頼を書く

止まる条件

* dry-run の「書く行」が5件でない、照合済みでない、または名前の警告が出る（書かずに止まり、実行のログの該当の行と理由を書く）
* #630 が Closed、または他のセッションの着手中のコメントがある
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-19.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-19 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミット・ログなし。`RSC` は続き（19）。作業ブランチ: origin/work/1113-rsc は origin/cloudflare の祖先（マージ済み）なので `git merge --ff-only origin/cloudflare` で進めた（6d4e48852）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1113-RSC-18 の `## 報告` の状態を「中断 / 続き: CHAT-1113-RSC-19」にした
- RSC-18 の手順3: docs/decisions の記録は済み。docs/notes の1文は、書けた後の決まりなので書いていない（RSC-18 のログ）

### 1. #630

- Open。最後の着手中のコメントはこのセッションの RSC-18 のもの（RSC-17 のものは決定3のとおり扱う）。着手中をコメントした（issuecomment-6844701978）

### 2. dry-run（cloudflare、run 43369883294）

- 結果は RSC-18 の dry-run（run 43364265658）と同じ。「書く行」は4件（天鳳ニューパワー・菊の陣・2014 風神編・2014 全日本〈女流〉、4件とも照合済み）。2016-05-21 著名人代表決定戦 決勝は、まだ当たらない。ログの該当の行:

```
警告: 「別名」の行を使いません(区分が想定外です「」): トレンディエンジェルたかし → たかし
```

- 理由: スクリプトは「別名」を、実行のたびにシートから直に読む（`lib/name_book.py` → `lib/sheets.py` の `fetch_records`。Google Visualization の問い合わせで、見出し「変換前」「変換後」「区分」の列を見出しの名前で読む）。その読み取りで、この行の「区分」がまだ空になっている。考えられる理由（ここからは確かめられない）は次のとおり:
  - 直した値が保存されていない、または別の行・別の列に入った
  - 見出し「区分」の列とは違う列に入った
  - 問い合わせの答えが古いまま返った
  - 列の型の推定で値が空として返った（Google Visualization は、列の大半と型の違うセルを空で返すことがある）
- 止まる条件（「書く行」が5件でない・名前の警告が出る）に当たるので、本実行はしていない（候補タブに書いていない）。#630 の要約のコメントも書いていない
- 「ビッグダディ」には、名前の辞書に無いという警告は出ていない（風神編の卓が当たった）。「たかし」は、行が区分で弾かれるので、まだ確かめられない
- 決定を `docs/decisions/automation.md` に記録した。差分は、このログ・RSC-18 のログの状態の行・`docs/decisions/automation.md` だけ

### マージ

- ドキュメントのみ。`git push origin work/1113-rsc:cloudflare` で入れた。push 直前に再 fetch して `merge-base --is-ancestor` を確かめ、範囲のコミットの件名・本文に閉じるキーワードの直後の `#番号` が無いことも確かめた。この行を含むコミットが範囲の終わり

## 報告

- 状態: 中断
- ブランチ: work/1113-rsc（cloudflare にマージ済み。ドキュメントのみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1113-RSC-19.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済み（ドキュメントのみ。範囲は「マージ」の行）
- issue: #630（着手中のコメント。Open のまま）
- 判断が必要なこと:
  1. 平野さんの確かめ: ryoei.pro_選手 の「別名」タブで、「トレンディエンジェルたかし」の行を見てほしい。見出しが「区分」の列に「訂正」が入っているか。前後に空白や別の字が無いか。保存されているか。スクリプトの読み取り（run 43369883294）では、まだ空になっている。ほかの「訂正」の行（例: 「林下清志（ビッグダディ）」）と同じ形になっていれば、少し時間をおいて dry-run をやり直す（この指示の手順2から）
  2. 「別名」の2行（トレンディエンジェルたかし・林下清志（ビッグダディ））を消すのは、候補タブに書けた後。今は消さない
- 未確認の項目: 「たかし」が「プロ」か「連盟プロ以外」にあるか。区分が読まれた後の dry-run で確かめる
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 39e55941）: https://github.com/retroeater/mj-logs/tree/main/guide/39e55941

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/39e55941/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
