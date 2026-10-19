# WRC-R リーグの新ページ `wrcr/`（jwrc/ との違い）

WRC-R リーグの成績とランキングを、WRC リーグの `jwrc/`（`docs/notes/jwrc-top.md`）と同じ作りで `wrcr/` に作る（#366。2026-10-19 の決定で、jwrc/ とは別のページ）。
**ここには jwrc/ と違うことだけを書く。** 書いていないこと（画面・部品・数え方・確かめ方・最高到達・通過の3部門・規定・表示の上限・順位変動の通過ライン）は jwrc-top.md のとおり。大会の差の一覧は `docs/notes/league-pages-compare.md`。
2026-10-19 に未公開の形で作った（work/WRR。全ページ noindex、navbar・サイトマップ・`llms.txt`・既存のページからのリンクは無い）。公開は #611（navbar は「WRC-R」を「WRC」の隣の独立の1段の項目にする。2026-10-19 の決定）。旧ページは無い（WRC-R の成績表はこれまで無かった）ので、301 の転送は無い。

## 構成と生成

- URL: `wrcr/`（トップ・質問欄）・`wrcr/players/`・`wrcr/ranking/<スラッグ>/`（9部門。`wrcr/ranking/` は `total/` へ 301）・`wrcr/race/`
- 生成は `scripts/generate_wrcr_pages.py`（`regenerate.py` の `wrcr_pages`、出力 `wrcr/`）。`generate_houou_pages.py` を大会の設定 `WRCR` で動かすだけ（`JWRC` と同じくリーグの無い大会）
- 集計元は正本の「WRCR」シート（「R_タイトル戦_成績」。列は「JWRC」と同じ12列）。「表示」が Y の行の数が 400〜2,000 の外なら止める（`WRCR.rows`。2026-10-19 は 572行）
- 規定は `lib/ranking.py` の `CONTESTS["WRCR"]`（WRC リーグと同じ値、2026-10-19 の決定）
- 質問欄の検索データは `workers/ask/data-wrcr.json`（`scripts/generate_ask_data_wrcr.py`、`regenerate.py` の `ask_data_wrcr`）

## データの違い

- 期は第1〜7期（2026-10-19）。第8期（進行中）は完了してから「WRCR」シートに入れる（2026-10-18 の決定）。行が入ったときの進行中の期の行のリンクは公式の成績表（`WRCR.result_urls` の第8期、無い期は大会のページ `WRCR.result_index`）。**期が変わったら `result_urls` に次の期の成績表の URL を足す**
- 途中敗退の行の「合計」は、出た節の得点の合計（公式の表では「敗退」の文字。2026-10-18 の決定）
- 最高到達と現タイトルホルダーの決勝は「タイトル」タブの「JPML WRC-Rリーグ」（#523 の後の「WRC-Rリーグ」も受ける）。予選に出ていない決勝進出者（1〜4位）は jwrc/ と同じく成績の行の無い選手として載せる（例: 第5期の優勝者 覚野陽生）。2026-10-19 の現タイトルホルダーは第7期の優勝者
- 名寄せ（「別名」）で今の名前になる選手がいる（例: 樫野凪 → かしのなぎ）。「連盟プロ以外」の選手（例: 第1・2期の椿椋 → つばきりょう）は在籍者でないので個人成績・ランキングに出ない（houou/ と同じ）

## 画面の違い

- 文言は大会名を「WRC-Rリーグ」にしたもの（title は「<ページ> | WRC-Rリーグ | 日本プロ麻雀連盟 | ryoei.pro」）。トップの3項目の説明は jwrc/ と同じ。質問欄の例は「例：現タイトルホルダーは？」（2026-10-19）
- 大会の印は `is-wrcr`・`data-contest="wrcr"`。最近見た選手は localStorage の `mj:recent-players:wrcr`
- OGP は `img/ogp/wrcr/index-white.png`（白地・黒字の「WRC-Rリーグ」＋「ryoei.pro」、docs/notes/ogp.md の規則）
- 未公開の間は共通ナビの形（白地の `.mj-nav-new`）を当てない（jwrc/ も公開で当てた。`navbar.js` は公開の issue で変える）

## 質問欄

- Worker `mj-ask` と1日の上限を3大会と共有する。wrcr/ の質問欄は問いに `contest: "wrcr"` を添え、Worker は `data-wrcr.json` と `ask.mjs` の `WRCR_PLAN_SYSTEM`（部門は `JWRC_DIVISIONS`）で答える。記録の型は「wrcr:league」の形
- 決勝は title/search.json の「WRC-Rリーグ」（`/title/jwrc-r/`）、放送対局は /live の「WRC-Rリーグ」（`/live/jwrc-r/`）。WRC リーグ（R の付かない方）と世界麻雀は答えない（`none`）
- テストは `workers/ask/test/wrcr.test.mjs`（jwrc/ のデータに WRC-R が混ざらないことも見る）

## 確かめ方

jwrc-top.md の「確かめ方」に加えて:

- 通過の3部門と通算・期平均の上位10人は「WRCR」シートから別に数えた値と一致する（2026-10-19。違いは同じ値の並びだけ）
