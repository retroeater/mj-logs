# 説明文（.mj-lead）の幅の実装記録

このファイルは完了済み作業の記録。現状とルールは docs/handover.md。

---

### 表・SVGの幅に揃える（#312、2026-09-14、マージコミット `fe99a60`）

**要点: このCSSはクラス名による opt-in。** 表やグラフを持つページを新しく
追加・移行したとき、下のセレクタに拾われるクラスが付いていないと、説明文
だけが既定の720pxに取り残される。エラーは出ず、見た目でしか気づけない。

該当が見込まれるもの:

- Google Charts の6ページ（`houou_results` / `ouka_results` / `wrc_results` /
  `houou_ranking` / `ouka_ranking` / `wrc_ranking`）は houou/・ouka/・jwrc/ へ移り、#582 で消した（2026-10-19）
- 新サイト（#296）と選手個別ページ（#219）。CSSを引き継ぐなら同じ罠がある

表やグラフを追加するときは、説明文の幅が表・SVGの幅と一致するかを
実測すること（ページ全体の横スクロール幅が増えていないかも見る）。

#### 仕組み

- 既定は `.mj-lead { max-width: 720px }`（#158）
- 表・SVGの幅の上限をCSS変数にし、表/SVGと説明文の両方から参照する。
  セレクタは `body:has(<表やグラフのクラス>) .mj-lead` の形。
  説明文がDOM上で表の前に移っても効く（#229 で再現確認済み）

| 対象 | セレクタ | 説明文の上限 |
|---|---|---|
| 表11ページ（幅100%） | `body:has(.mj-table)` | `none` |
| `jpml_pros` | `body:has(#pros_table)` | `--mj-pros-table-width`（934px） |
| リーグ2ページ | `body:has(.mj-league-chart-desktop)` | `--mj-league-chart-desktop-width`（1400px）。480px以下はモバイル用SVG（上限なし）に合わせ `none` |
| 牌効率 | `body:has(.mj-bar-chart-desktop)` | `--mj-bar-chart-desktop-width`（1200px）。480px以下は `--mj-bar-chart-mobile-width`（360px） |

- 帰り道: 一覧（`video_wayhome`）は `body:has(.mj-video-list)` で `none`（上のカードの並びと同じ左右の余白、2026-10-13）。
  各話（`wayhome/`）は説明文を出さない（ヒーローの紹介文と同じ文のため、2026-10-13）。詳細は `docs/notes/video-wayhome.md`
- 採らなかった案: 表と説明文を `width: fit-content` のラッパーで包む方式。
  `.mj-table-auto` の表が内容幅まで縮む（rh_results で 1440→284px）

#### 検討事項: 逆向き（既定を none にする）書き方

既定を `max-width: none` にし、`jpml_pros`・グラフ3ページ・`video_wayhome` /
`wayhome/` を例外にする書き方もある。ページを追加しても取り残されない反面、
表を持たないページが説明文を得たときに画面幅いっぱいまで伸びる。壊れ方が
大きいのは後者なので現行方式（opt-in）を採った。#111・#141 で対象ページが
一度に6つ増えるなら、そのタイミングで再検討の余地がある。
