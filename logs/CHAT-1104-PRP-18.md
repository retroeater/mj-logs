# CHAT-1104-PRP-18

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 04c5d480c

## 指示

【Claude作成】Claude Code 向け指示：公開のブランチ work/1101-prp-pub に cloudflare（live/ の公開・TTL の続き・PRP-17 を含む）を取り込み直し、`sitemap-live.xml` の3か所と `llms.txt` の連盟プロの行を直す。マージはしない
Chat-Ref: CHAT-1104-PRP-18
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。告知動画の後に、別の指示でマージする）
貼る時機: CHAT-1104-PRP-17 が cloudflare にマージされた後（0章で確かめる。マージされていなければ何もせず止まる）。新しいセッションを推奨（取り込みの衝突が多いため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なので merge で取り込む（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。衝突の解き方は下の「決定」と手順1のとおり。そこに書いていない衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-17 のログの `## 報告` でマージが済み（cloudflare に入った）ことを確かめる（済んでいなければ何もせず止まる）。CHAT-1104-PRP-14 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-18` を足して push する。

## 目的
公開の日を「公開のマージを1本出すだけ」にするため、公開のブランチを cloudflare に追いつかせ、live/ の公開で残った3か所と古い文を直す。番号は CHAT-1104-PRP-16 のログの「2. 洗い出しの表」の番号。

### 決定（平野さん）
- 2026-11-04: live/ の公開のブランチとの衝突（`regenerate-page.yml`・`update_sitemap_lastmod.py`・`docs/handover.md` などで `sitemap-pro.xml` と `sitemap-live.xml` を同じ行に足したもの）は、両方を残して解いてよい
- 2026-11-04: TTL を先にマージし、pro/ の公開のブランチの側で重なりを解く（期ページの決勝メンバーの行き先は `member_link()` の1か所。PRP-14 で一度解いた）
- 2026-11-04（PRP-11・13・17）: 未公開のまま本番に入れたものは cloudflare の形を正とする。pro/ のトップの質問欄と K8 は戻さない。トップの説明文は PRP-13 の平野さんの文
- 2026-11-04: 告知動画はまだ保留する。非公開のまま進められる作業をすべて済ませて、公開直前の状態にしたい

### 前提（チャット側。平野さんの決定ではない）
- A1〜A3: `sitemap-live.xml` を `.github/workflows/regenerate-sitemap.yml` の XML の確かめの一覧・`llms.txt` のサイトマップの行・`docs/notes/sitemap-lastmod.md` の対象の一覧に足す（LVP の伝言。live/ は公開済み。このブランチは同じ行に `sitemap-pro.xml` を足しているので、ここで両方をそろえる）
- A12: このブランチの `llms.txt` の連盟プロの行の文（「…言葉で質問できる欄もある」など）を、今の pro/（質問欄なし・桜花・★・PRP-13 の説明文）に合わせて直す
- A4・A11: 取り込みの衝突は PRP-16 のログの表の A4 の行にある見込み（`regenerate-page.yml`・`update_sitemap_lastmod.py`・`sitemap-pages.xml`・`docs/handover.md`・`generate_title_pages.py`・`docs/notes/title-pages.md`〈TTL の続き〉・`docs/notes/ogp.md`〈log/ の OGP〉・`docs/decisions/pros.md`・title/ と順位変動の生成物）。TTL の続きで期ページの行の部品が変わっていれば、`member_link()` の pro/ の判定を新しい形に入れ直す。`docs/notes/ogp.md` は両方の行を残す。pro/ 関係のファイルは `docs/notes/pro-pages.md`「未公開のまま入れたものと、公開のマージで入れるもの」に従う
- A8 の `docs/notes/static-generation.md` の pro/ の行は、PRP-17 が cloudflare に入れた文にこのブランチもそろえる（公開の日に衝突しないように）
- 生成されたページ（pro/・title/・順位変動・`sitemap*.xml` の lastmod）の衝突は、解いた後のスクリプトで生成し直す
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. `origin/cloudflare` を取り込み、上の解き方で衝突を解く。解いたファイルごとに、残した側と合わせ方を `## 経過` に表にし、解いた後の該当箇所を引用する
2. A1〜A3・A12 を直す。`regenerate-page.yml` で pro/・title/・順位変動（4大会）を作り直す（A11: `check` の `pro_pages`・`title_pages` が止まらないこと）。pro/ の個別ページの「すべて（N件）」の件数が live/ の検索の件数と合うかを、3人以上（白鳥翔ほか）で確かめる
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）がすべて通る。生成物の差分を cloudflare と比べ、種類ごとに数えた表（見込み: pro/ は公開の段の差分、title/ の期ページは決勝メンバーの行き先、順位変動はアイコンの行き先、`sitemap-pro.xml` が新しいファイル、`regenerate-sitemap.yml`・`llms.txt`・docs の直し。ほかが出たら止まる）。プレビュー（付かなければローカル）で `pro/`・`pro/sho.shiratori.23/`・title/ の期ページ1つ・`houou/race/` を見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」のうち docs/decisions に無いものを docs/decisions/pros.md に足し、`docs/notes/pro-pages.md`「公開のマージ」を今の残りに合わせる

## 止まる条件
- 0章の確かめで、PRP-17 が cloudflare に入っていない
- 上に解き方を書いていない衝突が出た（解かずに、衝突したファイルと両側の変更の要点を書いて止まる）
- 生成物に、見込みで説明できない差分が出た
- テストが落ちる（取り込みと解き方で直せる範囲は直してよい。直せなければ止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-18.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-18 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-18.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4c48f228）: https://github.com/retroeater/mj-logs/tree/main/guide/4c48f228

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4c48f228/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
