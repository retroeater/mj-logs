# CHAT-1104-TOP-13

- 着手日時: 2026-11-04
- 対象issue: #101
- ブランチ: work/1104-top
- 着手時HEAD: 4bae3e3b4

## 指示

【Claude作成】Claude Code 向け指示：AI 検索の入力欄の幅を本文の面にそろえ、TOP-11〜13 を cloudflare へマージする（#101） Chat-Ref: CHAT-1104-TOP-13 マージ: 承認済み（チャットで）。条件: (1) origin/cloudflare を取り込んだ後にテスト（`python3 -m unittest discover -s scripts/tests`）が通る (2) cloudflare との差分が TOP-11・TOP-12 の変更（TOP-12 のログ `## 経過` 2 の表のファイル）とこの指示の幅の直し（CSS・テスト・docs）とログ・決定だけで、ほかの新しい変更を含まない (3) 取り込んだ後の `regenerate-page.yml` の作業ブランチでの手動実行（dry_run・all・compare_ref＝その時点の origin/cloudflare）で、中身の違う生成物が TOP-12 の6件（houou/・ouka/・jwrc/・wrcr/・saikyo/・top/ の index.html）だけ。1つでも満たさなければマージせずに止まる 貼る時機: いつでも（TOP-12 と同じセッションの続きで可） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-top への push と cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-top を続けて使う（TOP-11・12 の成果に幅の直しを足してマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行・`navbar.js` の白地の対象パスの if の連なりに行を足すもの〈TTL 系列が title/ を足す見込み〉）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。生成されたページの衝突は取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1104-TOP-12 のログの `## 報告` の状態が「判断待ち」であることを確かめ、その状態の末尾に `/ 続き: CHAT-1104-TOP-13` を足す（TOP-11 は `/ 続き: CHAT-1104-TOP-12` が付いている）。

目的
PC 幅で、帯の下に開く AI 検索の入力欄（今は最大 560px）が下の本文の面（400px）より広く見えるので、本文の面と同じ幅にそろえる。そのうえで、TOP-11・12（新しい共通ナビ・AI 検索のアイコン・ページの中の質問欄の廃止）とあわせて cloudflare へマージする。
決定（2026-11-04、平野さん。TOP 系列のチャットで）

* TOP-12 のプレビューは平野さんが確認済み
* 帯の下に開く AI 検索の入力欄の幅を、本文の面の幅にそろえる（TOP-12 の仮置き「欄の幅は 560px まで」を置き換え）。≡ のシートの幅（中央に最大 560px）は変えない
* pro/ のトップのページの中の質問欄は、PRP 系列が pro/ の公開の作業の中で外す（伝言済み）。TOP は pro/ の生成を変えない。質問欄の部品（`ASK_TEMPLATE`・`houou_ask.*` など）は pro/ が外した後に TOP が片付ける
* TOP-11・12・13 を cloudflare へマージしてよい（マージの可否はほかの問いと分けて聞いた。条件は冒頭の「マージ:」の行）
* TOP-12 の仮置きのうち幅以外（アイコンの形と読み上げ「AI に質問」・開いている間のアイコンの地・番組のページで開くと文書の先頭へ移ること・定数に無い項目はその名前のまとまりで末尾・番組の濃色のページの色）は決定とする

前提（チャット側。平野さんの決定ではない）

* 本文の面の幅は houou/・pro/ と同じ 400px（docs/notes/design.md。要確認）。欄はその幅で中央にそろえ、360px でもはみ出さない
* マージで本番に入るもの: 白地の新しい形のページ（houou/・ouka/・jwrc/・wrcr/・saikyo/・pro/・r/・top/・番組のページ〈wayhome/・exam/・mtsuku/・study/・live/ の個別ページ〉）の共通ナビが新しい帯（右上に AI 検索のアイコンと ≡）に替わる。5大会のトップと新トップのページの中の質問欄が消える。古い表のページ（黒地のナビ）・title/・今のトップ `/`（index.html）は変わらない
* マージで自動の再生成（`regenerate-page.yml`）が走ると、シート側のその日の変化による他ページの差分が混ざることがある（この指示の差分と分けて報告する）
* 本番での確かめに送る問いは1日の上限に数える。送るのは2問まで（UA に `mj-internal`）

手順

1. 幅を直し（CSS・テスト・`docs/notes/top-page.md`・`docs/notes/design.md` の該当の行・`docs/decisions/top.md` に上の「決定」）、360px・400px・PC 幅で欄と本文の面の左右の端がそろうことを画面で確かめる。origin/cloudflare を取り込み（上の衝突の扱い）、マージの条件 (1)〜(3) を確かめる
2. 条件を満たせば `git push origin work/1104-top:cloudflare` でマージする（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD`）
3. 本番で確かめる（15分で反映されなければ「未確認の項目」に回して完了にする）: `https://ryoei.pro/houou/?v=<未使用の値>` に新しい帯とアイコンが出てページの中の質問欄が無いこと、アイコンを押して開いた欄で「現鳳凰位は？」を1問送り答えが出ること（`context: "houou"`）、`https://ryoei.pro/wayhome/?v=<…>` に新しい帯が出ること、`https://ryoei.pro/jpml_pros.html?v=<…>`・`https://ryoei.pro/title/?v=<…>`・`https://ryoei.pro/?v=<…>` が変わっていないこと。#101 にマージと本番の確かめをコメントし、着手中を解除する

止まる条件

* TOP-12 が判断待ちでない、または work/1104-top がすでにマージ済み
* マージの条件のどれかを満たさない（満たさなかった条件と中身を書く）
* 上の扱いで解けない衝突が出た
* 古い表のページ・title/・`https://ryoei.pro/` の生成物や本番の内容が変わった（見つけたら直さずに報告する）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（ドキュメントのみの変更は、行が無くても完了報告のうえマージしてよい）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-TOP-13.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-TOP-13 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前の確認

- 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は4行とも有り。同じ Chat-Ref のコミットは0件
- CHAT-1104-TOP-12 の `## 報告` の状態は「判断待ち」、origin/work/1104-top（4bae3e3b4）は cloudflare に未マージ。TOP-12 の状態に ` / 続き: CHAT-1104-TOP-13` を足した（35b7b35cc）

### 1. 幅の直し（9f32ae0c6・43467242e）

- 本文の面の幅は、白地のページの外枠 `.mj-race` の `max-width: 400px`（左右の内側 16px、面は 368px）。帯の下の欄 `.mj-nav-ask-panel` を `max-width: 560px` → `400px`（内側 16px は同じ）にした。≡ のシート（`.mj-houou-sheet` の 560px）は変えていない
- 画面（ローカル＋Playwright、入力欄 `.mj-houou-ask-form` と本文の面 `nav.mj-houou-menu` の左右の端の x 座標）:

| ページ | 360px | 400px | 1280px |
|---|---|---|---|
| houou/・top/・saikyo/（直す前） | 16–344 / 16–344 | 16–384 / 16–384 | 376–904 / 456–824（欄が広い） |
| houou/・top/・saikyo/（直した後） | 16–344 / 16–344 | 16–384 / 16–384 | 456–824 / 456–824 |

  番組の濃色のページ（wayhome/ など）は本文が 400px の面ではない（全幅の一覧）ので、欄は同じ 400px で中央（1280px で 449–817）
- 検査: `test_top_page.py` に「欄の max-width が `.mj-race` と同じ・シートは 560px」を足した（直す前の 560px では落ちる）
- docs: `docs/notes/design.md`（共通ナビ〈新しい形〉の行）・`docs/notes/top-page.md`・`docs/decisions/top.md`（2026-11-04 の TOP-13 の決定）

### 2. 取り込みとマージの条件

- `origin/cloudflare`（cf56b6db4、41コミット）を merge で取り込んだ（ac2ec04c2）。衝突なし
- (1) テスト: `python3 -m unittest discover -s scripts/tests` OK（取り込んだ後）
- (2) cloudflare との差分（`git diff --name-only origin/cloudflare...HEAD`）: TOP-12 のログ「7.」の表のファイルから `saikyo/players/index.html` が抜けた（取り込んだ cloudflare 側の再生成で同じ内容になった）だけで、ほかの新しいファイルは無い。docs/logs は TOP-11・12・13 の3件。sitemap の差分は lastmod の5行だけ

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dbac236b）: https://github.com/retroeater/mj-logs/tree/main/guide/dbac236b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/dbac236b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
