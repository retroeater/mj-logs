# CHAT-0930-CAL-04

- 着手日時: 2026-09-30（JST）
- 対象issue: #448（sub-issue を起票する予定）
- ブランチ: work/0930-cal
- 着手時HEAD: 7a3378b7

## 指示

【Claude作成】Claude Code 向け指示：放送対局の公開カレンダー「mj_放送対局」を、サイトの「リソース > カレンダー」に統合する（プレビューまで） Chat-Ref: CHAT-0930-CAL-04 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/<識別子> を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/<識別子> origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
放送対局の公開カレンダー（#448）へのサイトからの導線が無い（CAL-01 の報告「判断が必要なこと」7）。サイトのナビの「リソース > カレンダー」にあるページへ、既存のカレンダーと同じ形で統合する。
決定（2026-09-30、平野さん）

* サイトからの導線は「リソース > カレンダー」に統合する。

前提（チャット側。平野さんの決定ではない。実物と食い違えば止まる）

* カレンダー ID は `c_aed30ad3c5ab614ab5304a4c64b1c13162f76b9966d80db2a51e61e9792d994d@group.calendar.google.com`（一般公開、予定の詳細を表示）。カレンダー名は「mj_放送対局」。
* 「リソース > カレンダー」のページには、誕生日・道場部ゲストなど既存の公開カレンダーが載っているはず（未確認）。
* CHAT-0930-CAL-03（件名の変更の grill-me）は同時に動く別セッション。CAL-03 が #448 に書くコメントや起票する issue はこの作業と重ならないので、それを理由に止まらない。

手順

1. 確かめと issue: ナビの「リソース > カレンダー」が指すページ（ファイル名）と、そこに載っているカレンダー（名前・ID・載せ方〈埋め込み・購読リンク・説明文〉・並び順）を書く。そのページが生成物なら生成スクリプトとデータの置き場所も書く。同じ目的の issue と、同じファイルを触る未マージのブランチ（`git branch -r --no-merged origin/cloudflare`）を確かめる。無ければこの作業の issue を起票して #448 の sub-issue にし、着手中コメントを書く。
2. 実装: 既存のカレンダーと同じ載せ方・同じ部品で「mj_放送対局」を足す。表示名・説明文・並び順は、既存の書きぶりに合わせた案を1つ作る（説明文には、完全版のライブ配信の予定であること、枠が出る前の時刻は暫定であることを入れる）。CSP（#9）の予定や外部ドメインを増やさない方針（handover「外部ドメインへの依存を増やさない」）に照らして、新しい外部ドメインが増えないことを確かめる。docs/notes/yotei-sheet.md に導線の場所を1行足す。
3. プレビュー: push してプレビューを出す。最終報告の「確認用:」に、そのページのプレビュー URL を書く（ログには書かない）。

止まる条件

* 「リソース > カレンダー」のページが無い、または既存のカレンダーの載せ方が1つにそろっておらず、どれに合わせるか判断が要る。
* 同じ目的の issue か、同じファイルを触る未マージのブランチがある。
* 載せるために新しい外部ドメインが要る。
* 手順3まで終えたら、判断待ちで止まる（平野さんのプレビュー確認を待つ）。cloudflare へはマージしない。

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、表示名・説明文・並び順の案を書く。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-CAL-04.md を書き、最後の行に Chat-Ref: CHAT-0930-CAL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 識別子: `git log --all --grep=CHAT-0930-CAL-04` は0件。`work/0930-cal` はリモートにあり cloudflare にマージ済み（7a3378b7 = origin/cloudflare）なので、そのまま使う。
- 手順0: 指示欄の末尾は指示文の最後の行と一致。
- 前提の「CAL-03 は同時に動く別セッション」は実物と違う: CAL-03 はこのセッションで行い、完了している（#479 起票、7a3378b7 でマージ）。この作業の妨げにはならない。

### 手順1: 確かめ

- ナビ（`navbar.js` のリソースのドロップダウン）の「カレンダー」は `/resource_calendar.html`（新しいタブ）。**HTML のページは無く**、`_redirects` の静的な1行で Google カレンダーの埋め込み URL（`https://calendar.google.com/calendar/embed?...`）へ 301 している。生成物ではない（`generate_live_pages.py` が書き換えるのは `# live: generated`〜`# live: end` の間だけ）
- 載せ方は1つにそろっている: 1本の埋め込み URL に `src=`（カレンダーID の base64）と `color=` を1組ずつ並べる。表示名は各カレンダーの Google 側の名前がそのまま出る。**サイト側に説明文を置く場所は無い**。公開の iCal で見ると、どのカレンダーにも説明（X-WR-CALDESC）は無い
- 共通のパラメータ: `height=600`・`wkst=2`・`ctz=Asia/Tokyo`・`bgcolor=#ffffff`・`title=ryoei.pro`・`showTitle=0`・`showTz=0`
- 今の6本（`src` の並び順）:

| 順 | 名前（公開 iCal の X-WR-CALNAME） | カレンダーID | 色 |
|---|---|---|---|
| 1 | 日本の祝日 | `ja.japanese#holiday@group.v.calendar.google.com` | `#616161` |
| 2 | 【一般公開】予定表 | `c_4c3960f0…77c5@group.calendar.google.com` | `#3F51B5` |
| 3 | mj_Mリーグ | `c_6bahtu8k2mngepnicu2j12a10g@group.calendar.google.com` | `#118745` |
| 4 | mj_竹書房 | `ryoei.net_d2u80e2cd20crs4kbnav9sa1tk@group.calendar.google.com` | `#FA9E05` |
| 5 | mj_道場部ゲスト | `c_vr4jbqlqs6tpd5i5cpt2ujv2lk@group.calendar.google.com` | `#FF0066` |
| 6 | mj_誕生日 | `c_1db2adc7…0fd7@group.calendar.google.com` | `#e4c441` |

- 前例: #407（「誕生日」を埋め込みに足した、closed）。末尾に6本目として足し、色は平野さんが比較ページで選んだ（既存と紛れにくい色）。既定で表示（埋め込みで初期チェックを外すパラメータは見つからなかった）
- 「mj_放送対局」の公開 iCal は読める（X-WR-CALNAME「mj_放送対局」、CAL-01 で 142件）＝一般公開になっている
- 同じ目的の issue: 無い（Open・Closed 479件で「resource_calendar」「「カレンダー」」「埋め込み」などを照合。#407 は誕生日で完了、#429 は書籍〈別のカレンダー、未着手〉、#390〜#392 は転記の自動化）
- 同じファイル（`_redirects`・`docs/notes/yotei-sheet.md`）を触る未マージのブランチ: 無い（`work/0929-sh-14`・`work/0929-sh-15`）
- issue: #480「「カレンダー」の埋め込みに「放送対局」カレンダーを加える」を起票し #448 の sub-issue にした。着手中コメント: https://github.com/retroeater/mj/issues/480#issuecomment-5896020136

### 手順2: 実装（68a75164）

- `_redirects` の `/resource_calendar.html` の転送先に、`src=`（「mj_放送対局」のカレンダーID の base64、`=` の詰め物なし。既存と同じ書き方）を6本目の `src` の後ろに、`color=%238E24AA` を6本目の `color` の後ろに足した。ほかのパラメータ・301・行の並びは変えていない
- 復号して確かめた: `src` 7本・`color` 7本が「祝日 #616161 / 【一般公開】予定表 #3F51B5 / mj_Mリーグ #118745 / mj_竹書房 #FA9E05 / mj_道場部ゲスト #FF0066 / mj_誕生日 #e4c441 / **mj_放送対局 #8E24AA**」の順。転送先のホストは `calendar.google.com` のまま
- 外部ドメイン: 増えない（転送先は既存の `calendar.google.com`。ページ側の HTML は無く、`_headers` に CSP やカレンダーの記述も無い）
- 表示名・説明文: 埋め込みに出るのは Google 側のカレンダー名（今は「mj_放送対局」）だけで、サイト側に説明文を置く場所は無い。既存6本にも説明は無い。案は報告の「判断が必要なこと」に書いた（コードでは変えていない）
- `docs/notes/yotei-sheet.md`「公開カレンダーへの同期」に導線の場所を1行足した
- `python3 scripts/check_asset_limits.py`: `_redirects` 静的 35（上限 2,000）ほかすべて OK。`python3 -m unittest discover -s scripts/tests`: 407件 OK
- 注: Q の色は #407 と同じく平野さんが決めるもの。今回はプレビューで見るための案として、Google カレンダーの標準色で既存6本と紛れにくいグレープ `#8E24AA` を入れた

## 報告

- 状態: 判断待ち（平野さんのプレビュー確認を待つ）
- ブランチ: work/0930-cal
- ログ: https://github.com/retroeater/mj/blob/work/0930-cal/docs/logs/CHAT-0930-CAL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-cal
- 確認用URL: プレビューあり（URL は最終報告）。見るページは `/resource_calendar.html`（プレビューの Worker でも `_redirects` の 301 で Google カレンダーの埋め込みへ飛ぶ）。check-run「Workers Builds: mj」は 68a75164 で success
- マージ: 未（cloudflare へは入れない。`_redirects` を変えるので、平野さんの確認の後に別の指示で）
- issue: #480（起票、#448 の sub-issue、着手中コメント）
- 判断が必要なこと:
  - プレビューで「カレンダー」を開き、7本目に「mj_放送対局」が出て予定が見えるか（色・既定で表示）を見て、マージしてよいか
  - 表示名の案: **「mj_放送対局」のまま**（既存の「mj_Mリーグ」「mj_竹書房」「mj_道場部ゲスト」「mj_誕生日」と同じ「mj_」＋中身の書きぶり。埋め込みに出るのは Google 側の名前なので、変えるなら平野さんが Google カレンダーの設定で変える）
  - 説明文の案: 埋め込みには説明文を出す場所が無く、既存6本にも説明は無い。置くなら Google カレンダー側の「説明」（購読したときなどに見える）に「日本プロ麻雀連盟 YouTube チャンネルのライブ配信（完全版）の予定です。配信枠が公開される前の予定の開始時刻は暫定で、枠の公開後に更新します。」。置くかどうかと文面を決めてほしい（サイトのコードは変えない）
  - 並び順・色の案: **末尾（7本目）・グレープ `#8E24AA`**（#407 の前例どおり末尾。色は Google の標準色で既存6本〈グラファイト・ブルーベリー・緑・橙・ピンク・レモン〉と紛れにくいもの）。【一般公開】予定表の隣（3本目）にする案もある。色は #407 のように比較して選び直してもよい
- 未確認の項目:
  - ブラウザでの見え方（埋め込みに7本目が出るか、色・既定の表示）。セッションから確かめたのは、プレビューの `/resource_calendar.html` が 301 で `calendar.google.com` の埋め込みへ飛び、転送先の `src` が7本で最後が「mj_放送対局」、最後の `color` が `#8E24AA` であることまで
- エラー:
  - プレビューの URL を取り出す途中で、Bash の実行が auto モードの分類器の「no verdict (error)」で7回続けて通らなかった（一時的な失敗と表示された）。少し置いて再実行したら通った

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 68837a87）: https://github.com/retroeater/mj-logs/tree/main/guide/68837a87

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/68837a87/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbb55c8c.md
