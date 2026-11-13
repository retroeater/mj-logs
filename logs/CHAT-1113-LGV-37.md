# CHAT-1113-LGV-37

- 着手日時: 2026-11-13
- 対象issue: なし
- ブランチ: work/1113-lgv-daily
- 着手時HEAD: e2a89a973

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の log/ を毎朝の変換の後に作り直す。駅の一覧に無い駅を毎月の常設 issue で知らせる
Chat-Ref: CHAT-1113-LGV-37
マージ: 承認済み（チャットで、2026-11-13、平野さん）。ただし下の「費用の条件」を満たし、止まる条件に当たらないときに限る。満たさなければマージせずに判断待ちで止まる
貼る時機: CHAT-1113-LGV-36 の完了後（同じ docs/notes/log-page.md を変えるため）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1113-lgv-daily の作成と push、`git push origin work/1113-lgv-daily:cloudflare` を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1113-lgv-daily を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-lgv-daily origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-LGV-36 のログの `## 報告` が「完了」であることを確かめ、そうでなければ止まる。CHAT-1113-LGV-36 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1113-LGV-37` を足す。

## 目的
1. 毎朝の取り込み・変換の結果と、平野さんの【3】の直しが、次の朝にページへ出るようにする（今は log/ の再生成が毎週月曜の全ページの再生成・生成スクリプトの push・手動だけで、最大1週間出ない）
2. 【3】に手で入れた駅が駅の一覧に無いとき（例: 池袋西口・新宿東口・西麻布）に気づけるようにする

### 決定（2026-11-13、平野さん）
- log/ を毎朝作り直す。ただし GitHub Actions の費用を把握し、月 $5 以上になるなら週次のままにする

### 費用の条件（チャット側の見込みと、確かめ方）
- リポジトリは private。2026-11-13 の手動の再生成（run 43350503345、`target_page: log_pages`）は 76秒・ジョブ1本（ubuntu-latest）。1分単位の切り上げで1回2分、毎朝で月 約60分の見込み
- 手順で、(i) 作業ブランチでの実行の所要時間とジョブの数 (ii) GitHub の今の Linux の1分あたりの料金（公式の料金の文書で確かめ、URL と確かめた日を書く）(iii) 月の見込み（分・無料枠を使い切っている場合の金額）を出す。無料枠の残りはセッションから見えなければ「平野さんが Billing で確かめる」と書く
- 無料枠を使い切っている場合の金額が月 $5 以上なら、マージせずに止まる

### 前提（チャット側。平野さんの決定ではない）
- 作り: `run-daily.yml` に、xconvert の後で `regenerate-page.yml` を `target_page: log_pages` で呼ぶジョブを足す（saikyo の呼び方〈`regenerate-saikyo.yml`〉と同じ形。権限・concurrency・手動実行のときに本番へコミットしないことを合わせる。予約実行〈`inputs.scheduled`〉のときだけ生成物をコミットする）。xconvert が失敗・取り消しでも、【3】の直しを出すために再生成は動かす（`!cancelled()`）。違いが無ければコミットしない（今の `regenerate-page.yml` の動き）。別の形のほうが今の作りに合えば変えてよい（理由を書く）
- 毎朝の運行の Sheets API の読み取りの上限（#662）に当たらないよう、並走するジョブとの関係を確かめる（log_pages が読むブックと読み取りの回数を書く。当たる見込みなら順番を変える）
- 駅の一覧に無い駅の知らせ（チャット側の案。2026-11-11 に平野さんへ「再発の防ぎ方を提案する」と伝えたまま出していなかったもの）: 毎月の常設 issue「ログ: 確かめる投稿」（`convert_x_posts.py` の mode closed、`check_issue()`）に「駅が駅の一覧に無い投稿」の節を足す。対象はログに載る投稿のうち、表示する駅が一覧に無いもの（【…】付きの海外・【県】付きで一覧にあるものは除く。LGV-28 の (c) と同じ見分け）。行は投稿 ID・日付・投稿者・店名・駅・X の投稿のリンク。直し方（【3】の「駅」を一覧の名前にする）を書く。0件なら節を出さない
- テストを足す（一覧に無い駅が節に出る・【London】付きと一覧にある【県】付きは出ない・0件で節が無い）。docs/notes/log-page.md・docs/notes/github-actions.md を直し、log/ がいつ作り直されるかを log-page.md に書く。docs/notes/self-review.md のとおり差分をレビューさせる
- ワークフローを変えるので、docs/notes/github-actions.md「ワークフローを変更したとき」のとおり、作業ブランチの ref で `run-daily.yml` を手動実行して確かめる（手動は dry-run の扱いで、本番に書かない）

## 手順
1. 未マージの work/ ブランチで `run-daily.yml`・`regenerate-page.yml`・`convert_x_posts.py`・docs/notes/log-page.md を変えているものを確かめる。log/ を作り直すきっかけ（今の全部）を洗い出して書く
2. ジョブと節・テスト・docs を入れる。テスト・ワークフローの検査（zizmor を含む）を回し、差分のレビューを受ける
3. 作業ブランチの ref で手動実行し、所要時間・ジョブの数・結果を書く。費用の条件の (i)〜(iii) を書く。`run-monthly.yml` を作業ブランチの ref で手動実行（dry-run）し、issue に書く内容（新しい節）を書く
4. 止まる条件に当たらなければ cloudflare へマージする。マージの後に最初に動く毎朝の運行の日時を書く

## 止まる条件
- 費用の見込みが月 $5 以上
- Sheets API の読み取りの上限に当たる見込みで、順番を変えても避けられない
- テスト・ワークフローの検査・手動実行が通らない
- 新しい依存が要る
- 未マージの work/ ブランチが上のファイルの同じ箇所を変えている、または取り込みで衝突する
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。費用の見込みと、最初に動く日時を書く
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-LGV-37.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-LGV-37 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1113-lgv-daily
- ログ: https://github.com/retroeater/mj/blob/work/1113-lgv-daily/docs/logs/CHAT-1113-LGV-37.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1113-lgv-daily
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e2a89a97）: https://github.com/retroeater/mj-logs/tree/main/guide/e2a89a97

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e2a89a97/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
