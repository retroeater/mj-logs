# CHAT-1105-LGF-08

- 着手日時: 2026-11-05
- 対象issue: なし
- ブランチ: work/1104-lgf-alias
- 着手時HEAD: a090449be

## 指示

【Claude作成】Claude Code 向け指示：log/ の店の駅を店ごとに数の多い駅にそろえ、駅名を一覧の表記に寄せる／国土数値情報の出典を末尾の (i) にまとめる
Chat-Ref: CHAT-1105-LGF-08
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1105-LGF-07 の判断待ちの続き。同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lgf-alias の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1104-lgf-alias を続けて使う（LGF-05・07 と一緒にプレビューを見てからマージするため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、docs/decisions/*.md・docs/logs/ の末尾の追記どうしは両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて、CHAT-1105-LGF-07 と CHAT-1104-LGF-06 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1105-LGF-08` を足す。

## 目的
CHAT-1104-LGF-06 で、同じ place_id で投稿ごとに駅が違う店が 33店あった。店名（LGF-05）と同じく、生成で店ごとの駅をそろえる（案 B）。離れた駅の6店と大至は、この後に平野さんが【3】で確かめて直す（案 A。この指示には含めない）。あわせて、トップの右上の出典の (i) を、紹介文の末尾の (i) にまとめる。

### 決定（2026-11-05、平野さん）
- 駅の揺れは A＋B で直す: 生成で店ごとに投稿の数の多い駅にそろえ（B）、離れた駅の6店（LGF-06 の表の 11・13・17・20・23・33番）と大至（3番）だけを【3】で確かめて直す（A）
- 「四ッ谷」（小さい ッ）は生成で寄せる。比べるときだけでなく、表示も駅の一覧の正しい表記（四ツ谷）に寄せる
- 紹介文の末尾の「利用条件 (i)」の見た目は LGF-07 のプレビューのとおりでよい
- 国土交通省（国土数値情報）の出典の注釈も、下の (i) の中にまとめる

### 前提（チャット側。平野さんの決定ではない）
- 今の店の駅は、駅を持つ最も新しい1件の投稿から、駅・駅の候補・駅の座標・都道府県・突き合わせ用の駅をまとめて取る（docs/notes/log-page.md、`group_shops()` の `STATION_FIELDS`。LGF-06 のログ「統一の仕方の候補」）
- 新しい決め方: 投稿ごとの駅を突き合わせ用の駅（一覧の名前に寄せた値）で数え、投稿の数が最も多い駅 → 同数なら最も新しい投稿の駅。選んだ駅を持つ投稿のうち最も新しい1件から、今までどおりまとめて取る（駅の候補・座標・都道府県を別々の投稿から混ぜない）。駅が空の投稿は数えない
- 駅名の寄せ: `lib/stations.py` の `station_key()`（市ケ谷 → 市ヶ谷 の寄せ）に、小さい ッ を ツ にしたときに一覧の駅になるなら一覧の名前にする寄せを足す（四ッ谷 → 四ツ谷）。表示する駅名（結果・カード・候補・タグ）は一覧の表記にする。一覧に無い駅（海外・【…】付きなど）の表示は今のまま。ほかにも、表記の違いで一覧に当たらない駅がいくつあるかを数えて書く（直すのは四ッ谷の寄せだけ）
- 出典: 今はトップ（`#log_home`）の右上の「(i)」（`SOURCE_HTML`）に国土数値情報の出典の1文とリンクがある（log-page.md「出典」）。これを紹介文の末尾の (i)（`TERMS_HTML`）の中に移し、右上の (i) は消す。開いたときの並びの案: 利用条件の2文とリンク → 出典の1文とリンク。(i) のラベルは「利用条件・出典 (i)」とする（チャット側の案。出典を探す人がラベルから辿れるように。プレビューで「利用条件 (i)」に戻してよい）。出典の文・URL は変えない。国土数値情報の出典の表記は CC BY 4.0 の条件（log-stations.md「出典の表記」）で、開けば読める形で足りる見込み（要確認。log-stations.md に表示の位置の条件が書かれていれば従い、合わなければ止まる）
- 月1回の閉業の確かめ（`convert_x_posts.py` の `closed_targets()`）は、LGF-07 で足した `HIDDEN_PLACES` の店にも Place Details を呼ぶ（LGF-07 のレビューの指摘）。載せない店は確かめる意味が無いので、`closed_targets()` でも外す（費用を減らすため。呼び出しを減らすだけで、増やさない）

## 手順
1. 駅の決め方と駅名の寄せを上のとおりに直し、テスト（同数・空の投稿・四ッ谷・一覧に無い駅）を足す。`closed_targets()` で `HIDDEN_PLACES` を外し、テストを足す。docs/notes/log-page.md・docs/notes/log-stations.md の該当の項を直す
2. 出典を末尾の (i) に移し、右上の (i) を消す。log-page.md の「出典」「利用条件」の項とテストを直す。docs/notes/self-review.md のとおり1・2の差分をレビューさせる
3. `regenerate-page.yml` を作業ブランチの ref・`target_page: log_pages` で手動実行して生成物を作り直し、LGF-07 の生成（98328180e）の search.json と比べて、店の駅が変わった店を全件、表にする（鍵・店名・前の駅・新しい駅・投稿ごとの駅の数）。LGF-06 の表の 33店と突き合わせ、33店の外で駅が変わった店があれば理由を書く

## 止まる条件
- 店の数・投稿の数が LGF-07 の生成（1,739店・2,427件）と違う（毎朝の取り込みで増えた分は除く。増えた店は鍵で一覧にし、それ以外の差があれば止まる）
- 店の駅が変わった店が、LGF-06 の表の 33店の外に 5店を超えてある（四ッ谷の寄せ・毎朝の取り込みで説明できるものは除く。説明を書く）
- 出典・利用条件の文と URL が変わる（置き方と見た目だけを変える）
- log-stations.md の出典の表記の条件が、たたんだ (i) の中に置く形と合わない
- 生成が止まる・失敗する（一時的な接続の失敗は、失敗したジョブだけを1回やり直して success なら止まらない）
- テストが通らない（自分の変更で落ちると分かっているテストは直してよい）
- 未マージの work/ ブランチが、同じ関数（`group_shops()`・`station_key()`・`closed_targets()`・`SOURCE_HTML`・`TERMS_HTML` の置き場）を変えている、または取り込みで衝突する（origin/work/1104-lgn-policy の `SOURCE_HTML` の変更は、/policy/ を作らない決定でマージされないので、重なっても止まらずログに書く）
- 終わったら、マージせずに判断待ちで止まる（LGF-05・07 と合わせて平野さんがプレビューを見る）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューで見る所（末尾の (i) を開いた形、駅が変わった店のうち投稿の多い店の `?shop=` のパス、`?station=四ツ谷`）を「判断が必要なこと」に書く（プレビューの URL そのものはログに書かない）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGF-08.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGF-08 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj fa43459d）: https://github.com/retroeater/mj-logs/tree/main/guide/fa43459d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/fa43459d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1a06d89d.md
