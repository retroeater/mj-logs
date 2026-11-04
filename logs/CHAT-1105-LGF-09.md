# CHAT-1105-LGF-09

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: 3639bbf16（c50456e46 に origin/cloudflare を取り込んだマージ）

## 指示

【Claude作成】Claude Code 向け指示：work/1104-lgf-alias（LGF-05・07・08）を cloudflare へマージする
Chat-Ref: CHAT-1105-LGF-09
マージ: 承認済み（チャットで）。下の「止まる条件」のマージの条件をすべて満たしたときだけ
貼る時機: いつでも（CHAT-1105-LGF-08 の判断待ちの続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgf-alias を続けて使う（LGF-05・07・08 の変更をマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の文書の衝突は解かずに止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-08 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1105-LGF-09` を足す。

## 目的
LGF-05（店名の統一・#665 の保留）・LGF-07（載せない店・利用条件のたたみ）・LGF-08（駅の統一・四ツ谷の寄せ・出典を末尾の (i) へ・閉業の確かめから載せない店を外す）を、プレビューの確認のうえ cloudflare へ入れる。新しい変更は足さない。

### 決定（2026-11-05、平野さん）
- LGF-05・07・08 のプレビューを見て、まとめてマージしてよい

### 前提（チャット側。平野さんの決定ではない）
- マージで変わるもの（LGF-05・07・08 のログ `## 経過`）: log/ の店名を店ごとに投稿の数の多い表記にし、投稿の行に別名を出さない（別名は検索欄の語に当たる）。新宿地下ラーメン（1店・9件）を載せない。店の駅を投稿の数の多い駅にし、駅名を一覧の表記にする（四ッ谷 → 四ツ谷、ケ → ヶ、笹塚の字）。紹介文の末尾の「利用条件・出典 (i)」に利用条件と国土数値情報の出典をまとめ、トップの右上の (i) を消す。`convert_x_posts.py` の月1回の閉業の確かめで載せない店を外す。コードは `scripts/generate_log_pages.py`・`scripts/lib/stations.py`・`scripts/convert_x_posts.py`・`assets/log.js`・`style.css`、生成物は `log/index.html`・`log/search.json`・`sitemap-pages.xml`（lastmod）
- `style.css`・`lib/stations.py` はほかのページと共通。`station_key()` は変換（all・stations）でも使う（LGF-08 のレビューで意図どおりと確かめ済み）。CLAUDE.md「判断・作業の原則」の共有の部品を変えたときの再生成が要るかは、Code が規則に従って決める
- 毎朝の取り込み（04:00 JST の run-daily）の後に cloudflare の log/ の生成物が新しくなっていることがある。生成物が衝突したら、生成物は手で解かず、コードと文書を取り込んだ後に `regenerate-page.yml`（作業ブランチの ref・`target_page: log_pages`）で作り直す

## 手順
1. origin/cloudflare を取り込み（必要なら）、生成物が衝突したら上の前提のとおり作り直す。テスト（`python3 -m unittest discover -s scripts/tests` と `node --test scripts/tests/js/*.test.js`）を回す
2. 取り込みの後の search.json を origin/cloudflare の search.json と比べ、店・投稿の数の差が「新宿地下ラーメンの1店・9件を外した分」と「毎朝の取り込みで足された分」だけで説明できることを確かめる（足された店・投稿は鍵・投稿 ID で一覧にする）。トップ・`?station=四ツ谷`・`?shop=ChIJzyJ4NBiNGGARlmBtxAiDwsk` をプレビュー（Playwright、UA に mj-internal、幅 390）で開き、LGF-08 のプレビューと同じ見え方であることを確かめる
3. マージの条件をすべて満たしたら cloudflare へ fast-forward でマージする。本番の `https://ryoei.pro/log/`（`?v=<マージの SHA>`）で「利用条件・出典」の (i) と、`?shop=ChIJkdDwfBKNGGAR2oXHz9NjCc8` が「見つかりませんでした」になることを確かめる。15分たっても本番が古いままなら判断待ちにせず「未確認の項目」に回して完了にする。LGF-05・07・08 のログの状態は CLAUDE.md「作業ログ」節に従って直す

## 止まる条件
- マージの条件（1つでも外れたらマージしない）:
  - (1) テストが通る
  - (2) origin/cloudflare との差分が、上の前提に挙げたコード・生成物・docs（docs/notes/log-page.md・docs/notes/log-stations.md・docs/decisions・docs/logs）・テストだけ
  - (3) 手順2の数の差が、新宿地下ラーメンの1店・9件と毎朝の取り込みの分だけで説明できる
  - (4) 手順2のプレビューの見え方が LGF-08 と同じ
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- マージ後の check-run の失敗は、今回の変更による失敗と無関係な失敗を分けて書く。無関係なら原因を報告に書いて残りの手順を進めてよい
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-09.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-09 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 99eb9dc9）: https://github.com/retroeater/mj-logs/tree/main/guide/99eb9dc9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/99eb9dc9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/04d7a070.md
