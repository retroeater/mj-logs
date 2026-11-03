# CHAT-1104-LGN-23

- 着手日時: 2026-11-04
- 対象issue: #640（公開・段2）・#255
- ブランチ: work/1104-lgn-release（origin/cloudflare から作った）
- 着手時HEAD: e27f2c52a

## 指示

【Claude作成】Claude Code 向け指示：log/ を公開する準備（段2、#640。新着のカードの高さ・Google マップの URL の直しと、noindex を外す・メニュー・転送・旧ページの生成をやめる。プレビューまで）
Chat-Ref: CHAT-1104-LGN-23
マージ: 判断待ちで止まる（navbar を変えるので、平野さんの確認の後にマージの指示を別に出す）
貼る時機: CHAT-1104-LGN-21 が cloudflare へマージされ、平野さんが本番の未公開の URL（https://ryoei.pro/log/）を実機で確かめた後（0章で LGN-21 のログの `## 報告` のマージが「済」であることを確かめ、済でなければ何もせず止まる）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgn-release の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-lgn-release を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lgn-release origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうし、`docs/notes/static-generation.md`「ページの一覧」の、ほかのページの記述と log/ の記述が同じ位置に足された衝突は、両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成物だけの衝突は、取り込んだ後のスクリプトで生成し直す。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて「貼る時機」の前提を確かめる。

## 目的
CHAT-1104-LGN-22 は送る前に差し替えたため欠番（平野さんが本番の未公開の log/ を確かめて出した直しを足した）。
未公開で本番に入れた log/（#639、LGN-21 で段1が済んだ）を公開する準備をする。docs/new-page-checklist.md「段2 公開する」と #640 の本文のとおり、1つの作業ブランチで変更を作り、プレビューで止まる。cloudflare へのマージ（公開）は、平野さんが確かめた後の別の指示で行う。

### 決定（2026-11-04、平野さん）
- 本番の未公開の log/ を確かめた（2026-11-04）。公開の前に次の2点を直す
  - 「新着」のカードで、駅名が無い店は空の行を入れ、日付が常に3行目に来てカードの高さがそろうようにする
  - 店名を押して開く Google マップが、iPhone の Google マップのアプリで「一致する検索結果はありません」（検索欄に `place_id:ChIJ…` と出る）になる。正しく店が開くリンクにする
- 公開は、LGN-21 のマージの後、平野さんが本番の未公開の URL を実機で確かめたら行う
- メニューは今の位置・名前のまま（「リソース」の下の「ログ」）。リンク先を log/ に替える
- `resource_logs.html` は log/ へ 301 で転送し、`resource_logs.html` の生成もやめる（生成スクリプト・再生成の対象・毎朝の作り直しの対象を log/ に切り替え、旧ページのファイルを消す）。転送した時に #255 を閉じる（2026-10-28 の決定）
- 前に共有された `?tag=` の語は、`tanilog` だけを読み替える（`?tag=tanilog` は投稿者「谷岡育夫」の絞り込みにする）。ほかの語は検索語の初期値のまま

### 前提（チャット側。平野さんの決定ではない）
- 今の店の URL は `https://www.google.com/maps/place/?q=place_id:<place_id>` の形で、アプリはこれを検索語として扱う。Google の Maps URLs の形 `https://www.google.com/maps/search/?api=1&query=<店名>&query_place_id=<place_id>` に替える案（店名は URL エンコード。Google の Maps URLs の文書で今の形を確かめる。要確認）。place_id の無い行と、旧「ログ」から移した `maps.app.goo.gl`・`goo.gl` の共有リンクは今のまま。生成のとき（`scripts/` の log/ の生成と、place_id から URL を組み立てる共通の関数）で直す。変換（`convert_x_posts.py`）が【2】の「店URL」に今の形を書いているなら、それも同じ形に直す（既存の【2】の値は書き換えず、生成のときに place_id から組み立て直す形でよい。どちらにしたかをログに書く）
- 店名とピンのアイコンは、今は1つのリンクの中にある（リンク先は同じ1つ。チャット側が本番を Chrome で読んで確かめた。アイコンは Google マップへ開くことの印）。この形は変えない
- 新しい共通ナビ（帯＋メニューのシート、TOP 系列の work/1104-top）は、TOP 系列のマージで log/ にも自動で当たる（`navbar.js` の白地の対象に `/log/` がある。CHAT-1104-TOP-13 のログ）。この指示ではナビの形は変えない。work/1104-top が先に cloudflare に入っていれば取り込む
- #640 の本文の公開の条件のうち「【2】の座標の列を埋めた（現在地から探すを公開時に出すなら）」は、2026-10-29 の決定（Google の座標を保存しない。現在地は駅の座標で探す）で要らなくなった。#640 に、その旨をコメントする
- 転送の形: `_redirects` に `/resource_logs.html` → `/log/` の 301 を足す。クエリ（`?tag=`・`?name=`）が転送先に引き継がれるかを、プレビューで確かめる（要確認。引き継がれなければ止まって報告する）。`?name=<名前>` は log/ の投稿者の絞り込み（`?name=`）にそのまま当たる見込み
- `tanilog` の読み替えは log/ の JS に置く暫定の処理になる。外す条件を決めて、`簡略化:` のコメントと docs/notes/log-page.md に書く（例: docs/notes/redirects.md の四半期の棚卸しで `resource_logs.html?tag=tanilog` の流入が0件になったら外す。案。平野さんは技術的負債に外す条件を付けることを好む）。転送を足したら docs/notes/redirects.md の一覧に行を足す（`?tag=tanilog` の読み替えも書く）
- 生成をやめる範囲: `scripts/generate_resource_logs.py` と、それにだけ使っていた `resource_logs.js`・テスト・`regenerate.py` の対象・ワークフロー（run-daily の xconvert の後の作り直しの対象など）を `git grep -n 'resource_logs'`（リポジトリ全体。除外は docs/decisions・docs/logs・handover-archive の過去の行だけ）で洗い出し、log/ に切り替えるか消す。消すと log/ の生成が困るもの（共通の読み込みなど）は残す。洗い出した行と、切り替え・消す・残すの別をログの表に書く
- 段2のチェックリスト（#640 の本文）の「変更」の項目を全部行う: noindex を外す（「公開していない」と書いたコメント・docstring・生成物の説明も直す）、navbar、サイトマップ（lastmod は手で書かない。push 前に `xml.etree.ElementTree` で parse する）、`llms.txt`、title・description・h1・og:title・og:image（canonical は既定で出さない）、`docs/notes/static-generation.md`「ページの一覧」・ページの設計メモ・`docs/handover.md` の「未公開」の記述
- 公開前の Search Console の数字を残すか: 残さない案（houou_race と同じ。平野さんが違えば言う）

## 手順
1. 着手前の確かめ: #640 に着手中のコメントを残す。`regenerate-page.yml` を作業ブランチの ref・入力 `check`・`target_page: log` で手動実行し、生成が止まらないことを確かめる。`resource_logs` の参照を洗い出してログの表にする
2. 上の2点の直しと、公開の変更を1つの作業ブランチで作る（noindex・navbar・サイトマップ・`llms.txt`・301 と `tanilog` の読み替え・旧ページの生成をやめる・docs）。検査（`scripts/tests/` と `node --test`）を直す。`regenerate-page.yml` を作業ブランチの ref で入力無しで手動実行し、生成物をコミットさせる
3. docs/notes/self-review.md のとおり差分をレビューさせる。プレビューで、navbar の「ログ」から log/ が開くこと、`/resource_logs.html`・`/resource_logs.html?tag=tanilog`・`/resource_logs.html?name=谷岡育夫` の転送先、log/ に noindex が無いこと、新着のカードの日付の位置、店名のリンクの URL の形を確かめ、見る URL と結果を報告に書いて止まる

## 止まる条件
- 「貼る時機」の前提が満たされない
- 301 の転送でクエリが引き継がれない（引き継ぐ形を足さずに、確かめた結果を書いて止まる）
- 旧ページの生成をやめると、ほかのページの生成・ワークフローが止まる（どこが止まるかを書いて止まる）
- 作るのに、新しい依存・CSP の変更・シートへの書き込み・外部 API の呼び出しが要る
- 未マージの work/ ブランチが、この作業で変える共通の部品（`navbar.js`・`_redirects`・`sitemap*.xml`・`llms.txt`・`scripts/regenerate.py`・`.github/workflows/`）の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- `.github/workflows/` を変えるときは docs/notes/github-actions.md「ワークフローを変更したとき」に従い、作業ブランチで手動実行して確かめる（できなければ止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。洗い出しの表と、プレビューでの確かめの結果を書く
- マージは冒頭の「マージ:」の行のとおり（平野さんが確かめるまで cloudflare へ入れない。#255 を閉じるのもマージの後）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LGN-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LGN-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-lgn-release
- ログ: https://github.com/retroeater/mj/blob/work/1104-lgn-release/docs/logs/CHAT-1104-LGN-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lgn-release
- 確認用URL: なし
- マージ: 未
- issue: #640・#255
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj c00e4591）: https://github.com/retroeater/mj-logs/tree/main/guide/c00e4591

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/c00e4591/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
