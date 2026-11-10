# CHAT-1110-LGV-22

- 着手日時: 2026-11-10
- 対象issue: なし
- ブランチ: work/1110-lgv-chainissue
- 着手時HEAD: 760c8e2cf

## 指示

【Claude作成】Claude Code 向け指示：「ログ」のチェーン店で別の店舗の place_id が付いている見込みの25件を、平野さんが確かめる issue にまとめる（CHAT-1110-LGV-21 の続き。件数を直した） Chat-Ref: CHAT-1110-LGV-22 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1110-LGV-21 の続き。LGV-21 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1110-lgv-chainissue の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1110-lgv-chainissue を続けて使う（CHAT-1110-LGV-21 のログがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1110-LGV-21 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1110-LGV-22` を足す（LGV-21 は止まる条件「件数が合わない」で止まった）。

目的
CHAT-1110-LGV-20 の洗い出しの (A)（本文に駅が無く店舗を特定できていない投稿のうち、同じ店名が別の place_id にも付いている店）から、確かめ済みの椿屋珈琲を除いた20店・25件を、平野さんが1件ずつ確かめて【3】で直せる issue にまとめる。シートにもページにも書かない。
決定（2026-11-10、平野さん）

* 件数の直し: LGV-21 の指示の「26件」はチャット側の数え違い。LGV-20 の (A) の表から椿屋珈琲を除くと20店・25件で、これが対象（平野さんの決定〈issue にまとめて確かめる〉は変わらない）
* (A) の20店・25件は、issue に一覧にし、平野さんが X の投稿を見て確かめる。違う店舗なら正しい place_id を【3】の「place_id」に入れる。店舗が分からなければ【3】の「表示」に N を入れる（案a）
* 椿屋珈琲（1450394631586279428）は確かめ済みで直し不要（LGV-18・20）

前提（チャット側。平野さんの決定ではない）

* 対象: LGV-20 のログの経過の (A) の表のうち、椿屋珈琲の行を除いた20行の「特定できていない投稿」25件（投稿 ID は表のとおり）
* issue（新規）: 題の例「ログ: チェーン店で別の店舗が付いている見込みの投稿」。ラベルは #675 に合わせる（「状況: 待ち」「分野: 整理・保守」「対象: resource_logs」）。作る前に題で検索し、同じ主題の issue が無いことを確かめる
* 本文: 冒頭に確かめ方（X の投稿を開き、今の店舗の地図と見比べる。違えば同じ店名の別の店舗の地図から正しい店舗を選び、その place_id を【3】の「place_id」に入れる。どれでもない・分からなければ【3】の「表示」を N にする。終わったら行の頭にチェックを入れる）。続けて店ごとの節（店名・今の place_id・店の駅）に、投稿ごとのチェックボックスの行（投稿 ID・日付・投稿者・X の投稿のリンク `https://x.com/i/status/<投稿ID>`）。店の節に、今の店舗と同じ店名の別の店舗の地図のリンク（`https://www.google.com/maps/search/?api=1&query=<店名をURLエンコード>&query_place_id=<place_id>`）を、今の店舗を先頭にして並べる。日付・投稿者は本番の `log/search.json`（cloudflare の生成物）から読む。本文は書かない
* 地図のリンクは place_id を使った Google マップの URL で、Google の内容を保存・加工しない（docs/notes/log-page.md の決まり）。Places の API は呼ばない
* 平野さんが直し終えた後の確かめは、別の指示で行う（この issue に「直し終えたらチャットに伝える」と書く）

手順

1. LGV-20 のログの (A) の表から20店・25件を取り出し、`log/search.json` で日付・投稿者・店の駅を引く。25件のどれかが `log/search.json` に無ければ、その投稿 ID と理由の見込みを書く（issue には入れて「今は載っていない」と書く）
2. issue を作る。ログに issue の番号と、本文の店の数・投稿の数を書く
3. ログを cloudflare へマージする

止まる条件

* 同じ主題の issue が既にある（番号を書いて止まる）
* 25件の数が合わない（LGV-20 の表から取り出した数を書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、平野さんが issue の25件を確かめて【3】を直すことを書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-LGV-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-LGV-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1110-lgv-chainissue
- ログ: https://github.com/retroeater/mj/blob/work/1110-lgv-chainissue/docs/logs/CHAT-1110-LGV-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-lgv-chainissue
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 097c4d99）: https://github.com/retroeater/mj-logs/tree/main/guide/097c4d99

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/097c4d99/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/535e861a.md
