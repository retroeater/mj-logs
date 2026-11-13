# CHAT-1113-RSC-17

- 着手日時: 2026-11-13
- 対象issue: #630・#408
- ブランチ: work/1113-rsc
- 着手時HEAD: 4223278

## 指示

【Claude作成】Claude Code 向け指示：旧公式の結果の名前を「別名」で現在名に直して当てる（名前の書き方が違う5卓）、research-candidates.yml の古い説明を直す、RSC-14 の残りの決定を記録する Chat-Ref: CHAT-1113-RSC-17 マージ: 承認済み（チャットで） 貼る時機: いつでも（RSC-16 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1113-rsc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1113-rsc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1113-RSC-16 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1113-RSC-17` を足す。

目的
CHAT-1113-RSC-16 の判断待ち 1〜3 への回答を入れる。旧公式の結果一覧（`source=official`）の名前を「別名」で現在名に直してから当てるようにし、名前の書き方が違う5卓を「別名」で解決できるようにする。あわせてワークフローの古い説明を直し、RSC-14 から残っていた決定を記録する。
決定（2026-11-13、平野さん。RSC-16 の判断待ちへの回答）

* 1: 名前の書き方が違う5卓（独歩・トレンディエンジェルたかし・井上美里×2・林下清志（ビッグダディ））は、人がシートに直接入れるのではなく「別名」で解決する
* 2: `research-candidates.yml` の冒頭のコメントと入力 `source` の説明の古い記述を直す
* 3: タイトル戦の対局日（C）の残り 104期は API の調べものをやめ、外の資料待ちに戻す。`--skip-found` の今の仕様（未照合の候補がある対象も飛ばす）はそのまま。RSC-14 の指示に無い追加2つ（入力 `skip_found`、C の巡回の順）は受け入れる
* この指示のマージを承認する。シートの「最強戦」は変えない

前提（チャット側。平野さんの決定ではない）

* 今の当て方: 卓の4人は「別名」で現在名に直したシートの名前、旧公式のページの名前はそのまま比べている（docs/notes/research-candidates.md の B の照合）。ページの名前も「別名」に通せば、「別名」に行がある名前は当たる
* 「別名」は ryoei.pro_選手 の「別名」タブ（変換前・変換後・区分。区分は登録名変更と訂正、docs/notes/houou-top.md）。書くのは平野さん（Claude Code は書かない）。足す行の案: 井上美里 → 波奈美里（登録名変更の見込み）、独歩 → 山田独歩、トレンディエンジェルたかし → たかし、林下清志（ビッグダディ） → ビッグダディ。後の3つは改名でも誤記でもない「外の資料の書き方」なので、今の区分に合うか、区分を足すべきかを確かめる（要確認）
* `scripts/check_unused_names.py` は、どのシートにも出ない「別名」の変換前を「使われていない」として出す作り（要確認）。ページの名前だけに出る変換前（独歩など）がそう扱われないように、`NAME_RESOLVERS` に旧公式の読み取りを足すなどの対応が要る見込み。2026-11-04 の決定（D の記事の名前は「別名」で直さず、`NAME_RESOLVERS` に足さない）は D の話で、この指示では D は変えない
* RSC-16 の未確認の項目（2019〜2021年度の本文に「n月n日」で始まる行があると見出しの区切りが変わりうる）は、この指示で触る関数に近ければ試験を足す（触らなければ扱わない）

手順

1. #630・#408 が Open で、他のセッションの着手中のコメントが無いことを確かめ、#630 に着手中のコメントを書く（本文・状態・ラベルは変えない）。「別名」タブの今の行（上の4つの変換前がすでにあるか）と区分の値・使い方、`check_unused_names.py` の作りを読み、前提と違えばログに書く
2. 旧公式の名前を「別名」で直してから当てるようにし、`check_unused_names.py` で必要な対応をし、試験を足す。`research-candidates.yml` の冒頭のコメントと `source` の説明を、今の読み方（2013〜2021年度の9ページ）に直す（docs/notes/github-actions.md の表の行も合わせる）。docs（research-candidates.md・決定〈上の 1〜3〉）を直す。自己レビュー（docs/notes/self-review.md）の後にマージする。#408 に、C の残りを外の資料待ちに戻す決定をコメントする
3. 平野さんが「別名」に足す行の一覧（変換前・変換後・区分）を「判断が必要なこと」に書く（区分は 1 で確かめた使い方に合わせ、合わなければ案とメリット・デメリット・推奨を書く）。足された後に回す `source=official` の手順（dry-run で5卓が当たる見込みを確かめ、書く）も書いて、判断待ちで止まる（シートを書かないため、この指示では本実行しない）

止まる条件

* #630・#408 が Closed、または他のセッションの着手中のコメントがある
* 「別名」の変換前に上の4つと同じ名前があり、別の変換後になっている（どちらが正か平野さんの判断が要る）
* 差分が次の範囲を超える: 調べもののスクリプト・`check_unused_names.py`・試験、`research-candidates.yml` のコメントと入力の説明、docs（notes・decisions・ログ）。ページ・生成物・ほかのワークフロー・`research-candidates.yml` の動き（ジョブ・手順）が変わったら止まる（マージしない）
* 未マージの work/ ブランチが同じ関数・同じ行を変えている、または取り込みで衝突する
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1113-RSC-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1113-RSC-17 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 42232786）: https://github.com/retroeater/mj-logs/tree/main/guide/42232786

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/42232786/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a5fcf1af.md
