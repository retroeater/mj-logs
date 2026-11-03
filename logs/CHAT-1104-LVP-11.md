# CHAT-1104-LVP-11

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 27194dcfb

## 指示

【Claude作成】Claude Code 向け指示：live/ の公開の変更に、プレビューの直し（件数の行を下部の文章へ・期ページの「決勝結果」のリンクを外す）と文書の食い違いの直しを足し、プレビューで止める Chat-Ref: CHAT-1104-LVP-11 マージ: 判断待ちで止まる（告知動画を撮った後、次の指示でマージ＝公開する） 貼る時機: CHAT-1104-LVP-10 の判断待ちの後（同じセッションの続きでも新しいセッションでもよい。毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lvp を続けて使う（CHAT-1104-LVP-10 の公開の変更があるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。origin/work/1104-lvp に、CHAT-1104-LVP-10 の報告（27194dcfb）より後のコミットが毎日の再生成（`chore: regenerate ...`）のほかにあれば、ログに書いて止まる。

目的
平野さんがプレビューを見て決めた直しを、公開の変更（work/1104-lvp）に足す。告知動画は直した後のプレビューで撮るので、プレビューで止める。
決定（2026-11-04、平野さん）

* トップ・タイトル戦・期のページの上部にある件数の行（トップ「767対局・2986本」、タイトル戦「13期・90対局・444本」、期「9対局・49本・2026年5月23日〜2026年9月26日配信」）を上部から消し、その数をページの下部の文章に含める
* 期のページの「決勝結果（タイトル戦）」のリンクを外す
* CHAT-1104-LVP-10 の報告の「元からの食い違い」を、この作業ブランチで直す: docs/notes/static-generation.md の「HTMLは21ページ」、`regenerate-sitemap.yml`・docs/notes/sitemap-lastmod.md・docs/notes/static-generation.md の列挙・`regenerate-page.yml` のコメントに mtsuku・exam・live のサイトマップが無いこと

前提（チャット側。平野さんの決定ではない）

* 下部の文章は今、トップ「日本プロ麻雀連盟チャンネルの放送対局動画2986本を、タイトル戦・選手・実況・解説等で検索できます。」、期「第43期十段戦の放送対局動画49本を、ステージ・卓ごとにまとめています。」の形（2026-11-04 の work/1104-lvp の生成物）。文面の案（実物に合わせて変えてよい）: トップ「…放送対局動画2986本（767対局）を、…」、期「第43期十段戦の放送対局動画49本（9対局、2026年5月23日〜2026年9月26日配信）を、…」、タイトル戦は同じ形で期の数・対局数・本数を入れる
* description・og:description に件数が入っている場合は、下部の文章と同じ数を保つ（要確認）
* 「決勝結果（タイトル戦）」のリンクを外すと、live/ の期から title/ の期へのリンクが無くなる。title/ の期から live/ の期へのリンクを戻すかは、公開の後の別の issue（C8、平野さんの決定 2026-10-17）で扱う。live/ と title/ のつなぎ方はその issue でまとめて決める（チャット側の案）
* 【3】のプレーオフの6行は、平野さんがまだ入れていない（2026-11-04）。この指示の確かめには含めない
* `.github/workflows/` の変更（`regenerate-sitemap.yml`・`regenerate-page.yml`）を含むので、CLAUDE.md「ブランチ運用」のとおり docs/notes/github-actions.md「ワークフローを変更したとき」を読み、作業ブランチで手動実行して確かめる

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。CHAT-1104-LVP-10 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1104-LVP-11` を足す。docs/notes/github-actions.md「ワークフローを変更したとき」を読む
2. 上の決定の3つを直す。トップ・タイトル戦・期のページを、端のデータ（期の数が多いタイトル戦、1対局だけの期、配信期間が1日の期）で 375px と 1280px の明・暗で撮る。全ページを再生成し、live/ と直した文書・ワークフローのほかの生成物の差分が0であることを確かめる。テストを回す。変えたワークフローを作業ブランチで手動実行して確かめる（実行できなければ報告して止まる）
3. 作業ブランチに push してプレビューで止める（確認用の URL は最終報告にだけ書く）。`## 報告` の「判断が必要なこと」に、下部の文章の実際の文面（3種類）と、プレビューで見てほしいページを書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 0章の確かめに当たる
* live/ と直した文書・ワークフローのほかの生成物に差分が出る
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 変えたワークフローの手動実行が、今回の変更で失敗する（無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`regenerate-sitemap.yml`・`regenerate-page.yml`・docs/notes/sitemap-lastmod.md・docs/notes/static-generation.md の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。対象のうち重なるものだけ外して残りを進めてよい。外したものと理由を報告に書く）
* #362 に他セッションの着手中コメントがある

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-11` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。origin/work/1104-lvp に 27194dcfb より後のコミットは無い
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-04 03:00 JST（毎朝の取り込みの直後ではない）

## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bec7039a）: https://github.com/retroeater/mj-logs/tree/main/guide/bec7039a

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bec7039a/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/53edca83.md
