# CHAT-1104-PRP-17

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-polish
- 着手時HEAD: 8c7dd9a31（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：pro/ の中だけで効く直し（ホーム画面の名前・X の既定のアイコン・JS のテスト・「ほか N件」の確かめ）と古い docs を未公開のまま本番に入れ、#641 の本文を今の形に書き直し、ワークフローの安全上の論点を起票する
Chat-Ref: CHAT-1104-PRP-17
マージ: 承認済み（チャットで。2026-11-04 に平野さんが、下の「止まる条件」〈noindex が全ページに残る・生成物の差分が pro/ の中だけ・導線と転送が変わらない・テストが通る〉でマージしてよいと単独の問いで答えた。1つでも当たればマージしない）
貼る時機: いつでも（新しいセッションを推奨）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-prp-polish を使う（公開の準備の work/1101-prp-pub とは分ける。work/1101-prp-pub はこの指示では変えない）。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-polish origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。取り込みで `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしは両方を残して解く。生成されたページだけの衝突は、取り込んだ後のスクリプトで生成し直して解く。それ以外の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
CHAT-1104-PRP-16 の洗い出し（そのログの `## 経過`「2. 洗い出しの表」。番号はその表）のうち、未公開のまま本番に入れられるもの（区分①）を入れる。あわせて #641 の本文を今の形に直し、pro/ と独立した安全上の論点を起票する。

### 決定（2026-11-04、平野さん）
- 区分①のうち B1・B2・B15・B16 を公開の前に入れる。B13（同じ年の獲得タイトルの並び、#408 が先）・B22（最終更新日・theme-color、#239・#271）は公開の後
- docs の古い記述（A8・A9）は直す（チャット側の見立てで、平野さんに聞かずに進めると伝えたもの）
- #641 の本文を、この指示で今の形に書き直す（済んだ項目にチェック・要らなくなった項目を消す・公開の日の手順を `docs/notes/pro-pages.md`「公開のマージ」に合わせる）
- B18（`regenerate-page.yml` の push の job が書き込みの資格情報を持ったまま `pip install` する）は issue を起票し、セキュリティ点検（SEC）の系列に任せる
- 「鳳凰」シートの第38期後期の3名の空白（A13）は、平野さんが 2026-11-02 に直した（申告）。公開のマージの生成で、3名が個別ページへ向くことを確かめる
- PRP-16 の Q1（2026-11-04）: live/ の選手のカード（A5）・成績ページから個別ページへの戻りのリンク（C10、#644）・帰り道の選手のリンク（C17、#195）は、3つとも公開のブランチに入れる（この指示ではしない。CHAT-1104-PRP-19 で行う）

### 前提（チャット側。平野さんの決定ではない）
- B1: `apple-mobile-web-app-title="連盟プロ"` は pro/ のトップにだけ付ける（旧 `jpml_pros.html` と同じ扱い。チャット側の案）
- B2: X の既定のアイコン（`abs.twimg.com/sticky/default_profile_images/…`）を「画像なし」と扱い、頭の1字の丸にする。PRP-01 の時点で12ページ（要確認。今の数と名前を書く）。#135 にコメントする
- B15: `assets/pro.js` の検査は既存の形（リポジトリにある JS の検査の置き方。新しい依存は足さない）で、並べ替え・予定を隠す処理（日本時間の日）・絞り込みの組み合わせ（複数選択・カテゴリの重ね掛け・「すべて」・`?all=1` の書き換え）・お気に入りを確かめる。既存の形で書けない部分は、確かめた表を `## 経過` に書く
- B16: 予定が6件以上ある選手の生成物を実データで1つ以上見て、「ほか N件」の行と live のシートの読み方を書く（直す必要が見つかれば直し、無ければ記録だけ）
- A8: `docs/notes/static-generation.md` の pro/ の行（今の絞り込み・`plans.json`・質問欄が無いこと）。未マージの work/ ブランチが同じ行を変えていれば、この文書だけ変えずに報告に書く（PRP-03 で LGN と衝突した例がある）
- A9: `docs/new-page-checklist.md`「URL パラメータの規約」の pro/ の行（`by`・`all` は今は使わない・複数の値は「,」区切り・`ouka` はシートのタブにもある）
- #641 の本文: PRP-16 の表の C1〜C5・C9 が古い項目。work/1101-prp-pub の `docs/notes/pro-pages.md`「公開のマージ（#641）」を読んで合わせる
- B18 の起票: 同じ主題の issue をクローズ済みを含めて探し、あれば起票せずコメントする。ラベルは「分野: セキュリティ」などがあれば付け、無ければ付けずに報告に書く。本文に PRP-16 の表の B18 の行を引用する
- 本番（ryoei.pro）へのリクエストは、この指示の全体で20件まで、1分に10件以下。まとめて送るときは UA に `mj-internal` を含める（CLAUDE.md）

## 手順
1. B1・B2・B15・B16 を入れ、テストを足す（足したテストは修正前のコードで失敗することを確かめる）。pro/ を Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す。A8・A9 を直す
2. 確かめ（下の止まる条件を全部ここで機械的に確かめ、結果を `## 経過` に書く）: テスト（`python3 -m unittest discover -s scripts/tests` と `node --test 'workers/*/test/*.test.mjs'`）。生成物の差分を種類ごとに数えた表。既定のアイコンだった選手のページを1つ、プレビュー（付かなければローカル）で 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ cloudflare へマージし（CLAUDE.md「ブランチ運用」）、本番の `pro/` と既定のアイコンだった選手のページが 200・noindex ありで新しい形になったことを確かめる（15分で古いままなら「未確認の項目」へ）。#641 の本文を書き直す（直した前と後の要点を `## 経過` に書く）。B18 を起票する。上の「決定」を docs/decisions/pros.md に足す

## 止まる条件（1つでも当たればマージしない）
- 生成した pro/ の全ページに `<meta name="robots" content="noindex">` が残っていない（1件でも無い）
- 生成物の差分が pro/ の外に出た（毎朝の生成などで動いた差分として分けられるものは除き、分けたものを書く）
- `navbar.js` の「連盟プロ」の行き先・`index.html` のサイドメニュー・`llms.txt`・`sitemap*.xml`・`_redirects`・`wrangler.jsonc`・`workers/` が origin/cloudflare から変わっている
- テストが1つでも落ちる、または `regenerate-page.yml` の `check`（`pro_pages`）で止まる理由が出た（一時的な接続の失敗は、失敗したジョブだけを1回再実行して success なら止まらない）
- 未マージの work/ ブランチ（work/1101-prp-pub を除く）が、変えるファイルの同じ行・同じ関数を変えている、または取り込みで衝突する（A8 の文書だけが当たるなら、その文書だけ外して進める）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-prp-polish
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-polish/docs/logs/CHAT-1104-PRP-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-polish
- 確認用URL: 作業中
- マージ: 作業中
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8c7dd9a3）: https://github.com/retroeater/mj-logs/tree/main/guide/8c7dd9a3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8c7dd9a3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
