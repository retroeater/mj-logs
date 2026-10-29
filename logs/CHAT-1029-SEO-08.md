# CHAT-1029-SEO-08

- 着手日時: 2026-10-29
- 対象issue: #5
- ブランチ: work/1029-seo8
- 着手時HEAD: d801c237b

## 指示

【Claude作成】Claude Code 向け指示：Mつくの前後のリンクの題を、チーム名の無い回は作る人の名前だけに戻し、条件を満たせば cloudflare へマージする
Chat-Ref: CHAT-1029-SEO-08
マージ: 承認済み（チャットで。2026-10-29、平野さん「すべて推奨、マージしてよい」。条件は下の「止まる条件」の差分の範囲。範囲の外の変化が1つでもあればマージせずに止まる）
貼る時機: いつでも（SEO-07 と同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1029-seo8〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1029-seo8 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1029-seo8 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1029-seo8 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1029-SEO-07 で、チーム名の無い回の題を「作る人（公開日）」にした。前後のリンクは日付の行（`mj-video-prevnext-date`）の下に題が出るので、同じ日付が2回見える。前後のリンクの題だけを作る人の名前に戻す。SEO-07 の判断待ちの2点に答え、SEO-07 のログを閉じられる形にする。

### 決定（2026-10-29、平野さん）
- SEO-07 の「判断が必要なこと」の1点目: 画像の alt（関連カード・一覧のカード）・og:image:alt・一覧の `ItemList` の名前は、公開日を足さない今の形（`alt_name()`）のままにする（alt は写真＝作る人の説明なので名前だけで足り、ItemList の項目はそれぞれ別の URL を持つため）
- 2点目: 前後のリンクの題だけ、チーム名の無い回は作る人の名前に戻す（日付の行で区別できるため）。`<title>`・og:title・パンくずは公開日付きのまま
- 下の「止まる条件」の差分の範囲に収まれば、プレビューを見ずに cloudflare へマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- SEO-07 のログ（`## 経過`「手順1 実装」）によると、前後のリンクは `scripts/generate_mtsuku_episodes.py` の `build_prevnext_link()` が `episode_name()` を使っている（要確認）。これを `alt_name()` に替える見込み（関数の名前が役割に合わなくなるなら、名前の付け替えは今の呼び出しの範囲で行ってよい）
- 差分は、SEO-07 で変わった前後の回9ページの `mj-video-prevnext-title` の文字が SEO-07 の前に戻る分だけの見込み（SEO-07 のログ「手順2 生成」の9ページ: GNCqIFbBV_A・HgJy7ik0Mno・WsBTyiXi7hE・XABpqoyq9xE・bNPj2Xh_MKg・lG9c_Am70ek・poVkDif7aes・v0EI5aVIYMs・vtE90-iTtRo）と、sitemap の `<lastmod>`
- テスト（`scripts/tests/test_mtsuku.py` の `TitleWithoutTeamTest`）の、前後のリンクに公開日が入るという検査を、入らないという検査に直す
- docs/notes/video-mtsuku.md「帰り道との違い」の題の決まりの行（SEO-07 で足した句）に、前後のリンクは作る人だけであることを足す（追記先の今の内容を読む）
- 生成は `regenerate-page.yml` で行う（コンテナにはシートの鍵が無い）。先に `dry_run`・`compare_ref`（その時点の origin/cloudflare）で差を見る

## 手順
1. 上の前提のとおり、前後のリンクの題を直し、テストと docs/notes/video-mtsuku.md を直す。`python3 -m unittest discover -s scripts/tests` が通ることを確かめる
2. `regenerate-page.yml` を work/1029-seo8 で `dry_run`・`compare_ref` 付きで実行し（target は SEO-07 と同じ `mtsuku mtsuku_episodes`）、差の行を全件分類してログに書く。下の範囲に収まれば、書く実行で生成してコミットする
3. 上の「決定」を docs/decisions/README.md の書き方に従って docs/decisions/mtsuku.md に足し、SEO-07 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1029-SEO-08` を足す。ログを仕上げて `git push origin work/1029-seo8:cloudflare` で入れる（push 直前の確かめは CLAUDE.md「ブランチ運用」）。本番（`?v=<未使用の値>` 付き）で、前後の回1ページ（例 `mtsuku/HgJy7ik0Mno.html`、要確認）の前後のリンクの題に日付が無いことと、`mtsuku/2FOsXsIqTAA.html` の `<title>` が公開日付きのままであることを確かめてログに書く。作業ブランチの片付けは docs/notes/branch-operations.md「ブランチを削除するとき」を読んでから、そのとおりに行う

## 止まる条件
- 前後のリンクの題を組む箇所が前提と違い、`<title>`・og:title・パンくずを変えずに前後のリンクだけを直せない（直さずに止まる）
- 手順2の差に、次の範囲の外の変化が1つでもある（全件を書いて、マージせずに止まる）: (a) 対象の5回（nLeGJz-nt-E・CtDQ5NjWSN0・2FOsXsIqTAA・f7__Vg7EUNE・vSduV7-kLls）を指す `mj-video-prevnext-title` の文字から「（YYYY年M月D日）」が消える変化 (b) 生成の後に動く sitemap の `<lastmod>`（変わったページの分だけ）。ほかのシートの変化による差が混ざって切り分けられないときも止まる
- テストが失敗した（今回の変更による失敗なら止まる。無関係な失敗なら原因を報告に書いて進めてよい）
- 未マージの work/ ブランチが、Mつくの生成の同じ関数を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` から確かめる）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）
- マージ後の check-run の失敗（今回の変更による失敗なら止まる。無関係なら原因を報告に書いて本番の確かめへ進めてよい）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1029-SEO-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1029-SEO-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-29）

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: なし。CLAUDE.md は起動時から変わっていない。work/1029-seo8 はローカル・リモートとも無く `git checkout -b work/1029-seo8 origin/cloudflare`（着手時 HEAD d801c237b）
- 未マージの work/ ブランチで Mつくの生成（`scripts/lib/mtsuku.py`・`generate_mtsuku.py`・`generate_mtsuku_episodes.py`）を変えているもの: 無し

### 手順1 実装

- 前後のリンクの題を組む箇所は前提どおり `scripts/generate_mtsuku_episodes.py` の `build_prevnext_link()`（`mtsuku.episode_name()` を使っていた）。`<title>`・og:title（`episode_page_title()`・`episode_og_title()`）・パンくず（`build_episode()`）は `episode_name()` のまま別の箇所なので、前後のリンクだけを直せる
- `build_prevnext_link()` の題を `mtsuku.alt_name()` に替えた。関数の名前は変えず、docstring を直した（`episode_name()` から「前後のリンク」を外し、`alt_name()` に「前後のリンク〈日付の行で見分けられる〉」を足した）
- テスト `TitleWithoutTeamTest`: 前後のリンクの題に公開日が入る検査を、入らない（「乙」だけ）検査に直し、テストの名前も合わせた。修正前のコードでは通らない（#310）。全体 OK
- docs/notes/video-mtsuku.md「帰り道との違い」の題の行の括弧を「前後のリンク〈日付の行で見分けられる〉・画像の alt・一覧の `ItemList` は公開日を足さない」に直した

### 手順2 生成

- run 40884476196（work/1029-seo8、`dry_run`・`compare_ref` d801c237b、target `mtsuku mtsuku_episodes`）: success、「差分あり 9 件」。前後の回9ページ（GNCqIFbBV_A・HgJy7ik0Mno・WsBTyiXi7hE・XABpqoyq9xE・bNPj2Xh_MKg・lG9c_Am70ek・poVkDif7aes・v0EI5aVIYMs・vtE90-iTtRo）が各2行。SEO-07 の9ページと同じ
- run 40884757135（書く）: success、08819ddbb「chore: regenerate mtsuku/index.html mtsuku/ via GitHub Actions」
- 差の全件の分類（`git diff -U0` の −/＋ の組ごとに、−の行から「（YYYY年M月D日）」を1つ取ると＋の行に一致し、`mj-video-prevnext-title` を含み、対象の5回のどれかを `href` で指すことを機械的に確かめた）: 前後のリンク 9。**範囲の外の変化は0**。sitemap-mtsuku.xml は変わらない（9ページの lastmod は SEO-07 で 2026-10-29 になっていて、同じ日のため）

### 差分のレビュー（docs/notes/self-review.md）

新しい文脈のサブエージェント（general-purpose、sonnet、71,241 トークン）に、目的・決定・条件と `git diff origin/cloudflare...HEAD -- scripts docs/notes mtsuku` の見方、規約を渡した。要修正は無し、任意1件。

| 指摘 | 扱い |
|---|---|
| docs/notes/video-mtsuku.md の題の行の前半が「`<title>`・og:title・前後のリンク・パンくずは「「チーム名」 作る人」」のままで、前後のリンクが後半の括弧の例外になっている | 直した（前半を title・og:title・パンくずにし、「前後のリンク・画像の alt・一覧の `ItemList` は「「チーム名」 作る人」で、チーム名の無い回も公開日を足さない」を別の文にした） |

docs だけの直しなので2回目は行っていない。

### 手順3 決定・マージ

- docs/decisions/mtsuku.md に「2026-10-29（CHAT-1029-SEO-08、#5）」を足した。SEO-07 のログの状態の末尾に「 / 続き: CHAT-1029-SEO-08」を足した

## 報告

- 状態: 作業中
- ブランチ: work/1029-seo8
- ログ: https://github.com/retroeater/mj/blob/work/1029-seo8/docs/logs/CHAT-1029-SEO-08.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1029-seo8
- 確認用URL: なし
- マージ: 未
- issue: #5
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0635eab）: https://github.com/retroeater/mj-logs/tree/main/guide/d0635eab

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0635eab/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/4aa1e3f2.md
