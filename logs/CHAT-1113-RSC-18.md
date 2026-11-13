# CHAT-1113-RSC-18

- 着手日時: 2026-11-13
- 対象issue: #630
- ブランチ: work/1113-rsc
- 着手時HEAD: 46a658d54

## 指示

【Claude作成】Claude Code 向け指示：「別名」に2行を足した後、旧公式の結果から名前の書き方が違う5卓を候補タブに書き、2行を消す決まりを記録する
Chat-Ref: CHAT-1113-RSC-18
マージ: ドキュメントのみ（ログ・docs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: 平野さんが ryoei.pro_選手 の「別名」タブに下の2行を足した後（RSC-17 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-17 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-18` を足す。

目的
CHAT-1113-RSC-17 の判断待ち 1・2 への回答を入れ、名前の書き方が違う5卓（#630）を旧公式の結果から候補タブに書く。
決定（2026-11-13、平野さん。RSC-17 の判断待ちへの回答）

* 1: 「別名」に次の2行を区分「訂正」で足す（RSC-17 の案A）。平野さんの手作業で、この指示を貼る前に済ませる
   * トレンディエンジェルたかし → たかし（訂正）
   * 林下清志（ビッグダディ） → ビッグダディ（訂正）
* 2: 旧公式のためだけの「別名」（訂正）の行は、`source=official` の本実行で候補タブに書けたら消してよい。この決まりを docs/notes/research-candidates.md に1文書く。今回の2行は、この指示で書けた後に平野さんが消す
* この指示の後の `source=official` の本実行（R_候補 の「候補」タブに書く）を承認する。シートの「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* RSC-17 の dry-run の見込み: 2行を足すと「書く行」は5件（2018-06-30 天鳳ニューパワー ベスト16・2015-06-13 菊の陣 決勝・2014-09-21 全日本（女流） 決勝・2016-05-21 著名人代表決定戦 決勝・2014-05-17 著名人代表決定戦風神編 決勝）で、5件とも照合済みの見込み
* 「たかし」「ビッグダディ」が「プロ」か「連盟プロ以外」にあるかは未確認（無いと、その「別名」の行は警告を出して使われない、`lib/names.py`）
* 2行を消すのは平野さんの手作業。消したことの確かめ（#475 の毎朝の検査に出なくなる等）は、平野さんの消した後の報告で行う（この指示では待たない）

手順

1. #630 が Open で、他のセッションの着手中のコメントが無いことを確かめ、着手中のコメントを書く（本文・状態・ラベルは変えない）
2. cloudflare で `research-candidates.yml` を手動実行する（kind B・source official・dry_run 真）。「書く行」が5件・5件とも照合済みであることを確かめる。違えば（2行が読まれていない・名前の警告が出た等）、実行のログの該当の行と理由を書いて止まる。合えば dry_run を外して実行し、候補タブに5行を書く。5行の表（対象キー・候補・確度・備考）を書き、#630 に要約をコメントする
3. docs/notes/research-candidates.md の「B の旧公式の結果一覧」の節に、決定2の1文（旧公式のためだけの「別名」〈訂正〉の行は、本実行で候補タブに書けたら消してよい。消した後に回しても、候補タブに行がある卓は飛ばす）を書き、決定を docs/decisions に記録する。「判断が必要なこと」に、平野さんが「別名」から2行を消す依頼を書く

止まる条件

* 0章の前提（2行が足されている）が dry-run で確かめられない（「書く行」が3件のまま等）
* #630 が Closed、または他のセッションの着手中のコメントがある
* dry-run で5件以外の卓が「書く行」に出る（書かずに止まり、差を書く）
* 差分が次の範囲を超える: docs（notes・decisions・ログ）。コード・ワークフローが変わったら止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認:
  - 同じ Chat-Ref のコミット・ログは無い。`RSC` は続き（18）
  - RSC-17 は別のセッションが行い、cloudflare にマージ済み。状態は判断待ち
  - 作業ブランチ: origin/work/1113-rsc は origin/cloudflare の祖先（マージ済み）。`git merge --ff-only origin/cloudflare` で進めた（46a658d54）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 0.: 「指示」欄の末尾は指示文の最後の行と一致。CHAT-1113-RSC-17 の `## 報告` の状態を「判断待ち / 続き: CHAT-1113-RSC-18」にした

### 1. #630

- 状態は Open
- 最後のコメントは、別のセッションの着手中のコメント（CHAT-1113-RSC-17、2026-11-13T03:55:55Z）だった。ただし RSC-17 はマージ済み・判断待ちで終わっており、この指示はその判断待ちへの回答。動いている作業ではないと判断して進めた
- 着手中をコメントした（issuecomment-6843714354）。この判断もコメントに書いた

### 2. dry-run（cloudflare、run 43364265658）

- 候補タブ: 428行・見出し 一致
- 「書く行」は4件で、5件ではなかった。4件とも照合済み:

| 対象キー | 候補 | 確度 |
|---|---|---|
| 2018-06-30 男子プレミアトーナメント 天鳳ニューパワー ベスト16 | 1位 朝倉康心・2位 山田独歩・3位 ゆうせー・4位 木原浩一 | 照合済み |
| 2015-06-13 女流プロ代表決定戦菊の陣 決勝 | 1位 和泉由希子・2位 松岡千晶・3位 愛内よしえ・4位 波奈美里 | 照合済み |
| 2014-05-17 著名人代表決定戦風神編 決勝 | 1位 萩原聖人・2位 加藤哲郎・3位 ビッグダディ・4位 勝間和代 | 照合済み |
| 2014-09-21 全日本プロ代表決定戦（女流） 決勝 | 1位 波奈美里・2位 美波智子・3位 菅原千瑛・4位 稲毛千佳子 | 照合済み |

- 欠けた1件は 2016-05-21 著名人代表決定戦 決勝。ログの該当の行:

```
警告: 「別名」の行を使いません(区分が想定外です「」): トレンディエンジェルたかし → たかし
```

- 理由: 「別名」の「トレンディエンジェルたかし → たかし」の行は、区分が空で読まれている（「訂正」になっていない）。もう1行の「林下清志（ビッグダディ） → ビッグダディ」は使われ、風神編の卓が当たった
- 止まる条件（違えば、ログの該当の行と理由を書いて止まる）に当たるので、本実行はしていない（候補タブに書いていない）。手順3の docs/notes の1文も、書けた後の決まりなので書いていない。決定は `docs/decisions/automation.md` に記録した（完了時の規則）
- 差分は、このログ・RSC-17 のログの状態の行・`docs/decisions/automation.md` だけ

### マージ

- ドキュメントのみ。`git push origin work/1113-rsc:cloudflare` で入れた。push 直前に再 fetch して `merge-base --is-ancestor` を確かめ、範囲のコミットの件名・本文に閉じるキーワードの直後の `#番号` が無いことも確かめた。この行を含むコミットが範囲の終わり

## 報告

- 状態: 中断
- ブランチ: work/1113-rsc（cloudflare にマージ済み。ドキュメントのみ）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1113-RSC-18.md
- 比較URL: なし（マージ済み）
- 確認用URL: なし
- マージ: 済み（ドキュメントのみ。範囲は「マージ」の行）
- issue: #630（着手中のコメント。Open のまま。要約のコメントは書いていない）
- 判断が必要なこと:
  1. 平野さんの手作業: 「別名」タブの「トレンディエンジェルたかし → たかし」の行の区分を「訂正」にしてほしい。今は空で読まれ、行が使われていない。直した後、この指示の手順2から（dry-run で5件 → 本実行）やり直す。書く行は5件になる見込み（今の4件と著名人代表決定戦 決勝）
  2. 「別名」の2行を消すのは、書けた後（上のやり直しの後）。今は消さない
  3. RSC-17 のコメント「#630 の他のセッションの着手中」の扱い: 終わった指示のものとして進めた。よければ、このままでよい
- 未確認の項目: 「たかし」が「プロ」か「連盟プロ以外」にあるか（区分を直した後の dry-run で、名前の警告が出ないかを見る）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 589c080b）: https://github.com/retroeater/mj-logs/tree/main/guide/589c080b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/589c080b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
