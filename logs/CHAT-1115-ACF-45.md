# CHAT-1115-ACF-45

- 着手日時: 2026-11-15
- 対象issue: #475
- ブランチ: work/1115-acf
- 着手時HEAD: 1607687f84

## 指示

【Claude作成】Claude Code 向け指示：/live の概要欄で「ベスト8A卓:」のような段階名付きの卓の見出しを外す（#475 の「ベスト16A卓:黒沢咲」「ベスト8A卓:香野蘭」の解消） Chat-Ref: CHAT-1115-ACF-45 マージ: 承認済み（チャットで。条件: 手順3のテストと、作業ブランチでの update-live-channel の手動実行〈書かない道筋〉が success で、未登録の名前から「ベスト16A卓:黒沢咲」「ベスト8A卓:香野蘭」が消え、【2】の候補の行数・対局者の変わる行が手順1の見込みの範囲に収まるとき。外れたらマージせず報告して止まる） 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1115-acf の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1115-acf を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1115-acf origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」） 使う skill: /live の層2の規則は docs/notes/live-channel-write.md・docs/notes/live-page-design.md を着手時に読む。docs の記述の直しは writing-for-agents

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の概要欄の対局者の切り出しで、「ベスト8A卓:」「ベスト16A卓:」のように卓の前に段階名が付いた見出しを外し、各卓の先頭の対局者が未登録の名前（#475）に誤って出ないようにする。
決定（2026-11-15、平野さん）

* 「別名」に「ベスト8A卓:香野蘭」などを足す対処はしない。層2の規則（`scripts/lib/live_extract.py`）を直す
* この直しは ACF の系列で行う（/live の系列へは回さない）

前提（チャット側。平野さんの決定ではない）

* 11/15 の run-daily（43629699250）の `live / update` で、#475 に「新しく出た名前: ベスト8A卓:香野蘭(1行)」が付いた。香野蘭さんは「プロ」シートにある（平野さんの申告）。同じ回の動画 `hZLGBjOKYkU`（コスモビューティーグランプリ2026 ベスト16）では、対局者「ベスト16A卓:黒沢咲、伊達朱里紗、菅原千瑛、香野蘭」のうち未登録は先頭の「ベスト16A卓:黒沢咲」だけで、4人目の香野蘭は照合できていた。「ベスト16A卓:黒沢咲」は 10/28 から #475 に残っている（チャット側でログ・#475 のコメントを見て確認）
* 原因の見立て: `TABLE_LABEL_RE = re.compile(r"^◎?([A-H])卓(?:予選|決勝)?(?:[:：]|\s)")`（live_extract.py 112行目付近）は行頭が卓の形のときだけ見出しとして外すので、「ベスト8A卓:」は外れず先頭の名前に残る。卓の選択（その動画の卓の行に絞る処理、350行目付近）も同じ理由で外れている可能性がある。実物で確かめる（要確認）
* #475 への知らせの文面（「概要欄の読み違いの抜き出しの直しは #446 の項目8」）は、#446 が閉じていて項目8も実況・解説の話なので合っていない。案内先を今の正しいもの（docs の節、または「層2の規則は live_extract.py」）に直す
* 同じ動画のタイトル戦が「麻雀グランプリMAX」と読まれている（題は「コスモビューティーグランプリ2026」）。「グランプリ」の語での読み違いの疑い。今回は直さず、実物で確かめて結果をログに書き、読み違いなら報告の「判断が必要なこと」に、直し方の案（規則か【3】か）と影響する行数を添えて挙げる（要確認）

手順

1. 上の前提を実物で確かめ、食い違えば止まる。層1（生データ）の全概要欄で、行頭が「<段階名>[A-H]卓」（段階名は ベスト16・ベスト8・準決勝 など、`[A-H]卓` の前に卓の見出しでない文字が付く形）の行を数え、直しで対局者・卓が変わる見込みの行（動画ID・変更前・変更後）を一覧にしてログに書く。これを手順3の見込みとする。未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）で live_extract.py・live_candidate.py の同じ箇所を変えているもの・取り込みで衝突するものを確かめる。#475 に着手中のコメントを書く（本文・状態・ラベルは変えない）
2. `TABLE_LABEL_RE`（と卓の選択で同じ形を使う箇所）を、段階名付きの見出しも外す・選べるように直す。段階名の語彙をどこまで認めるかは、手順1の実例と、名前を誤って削らないこと（見出しは `卓` と区切り〈`:`・`：`・空白〉の両方を要する形のままにする等）を見て決め、理由をログに書く。テスト（scripts/tests の live_extract の既存テストに、「ベスト8A卓:」「ベスト16A卓:」と、今まで通る形の両方の例）を足す。#475 の知らせの文面の案内先を直す。docs の記述（live-channel-write.md・live-page-design.md など、`git grep -n -e TABLE_LABEL_RE -e '#446 の項目8'` で洗う。docs/decisions・docs/logs・handover-archive の過去の行は除く）を直す
3. 確かめ: テスト一式と、作業ブランチで update-live-channel を手動実行（書かない道筋）し、未登録の名前から2件が消え、変わる行が手順1の見込みと一致することを確かめる（差分の表をログに書く）。無関係な失敗（mj-logs の actions/morning.md の1節に出ているもの、Sheets API の 429 で失敗したジョブだけの再実行で success になるもの）は原因を書いて進める。通ればマージし、#475 に「原因と直し・消える見込みの2件」をコメントする（#475 は常設なので閉じない）。報告の「未確認の項目」に、マージ後の初回の run-daily（翌朝 04:00 JST）で #475 に「消えた名前: 2名」が付くことを書く

止まる条件

* 前提が実物と食い違う（未登録の原因が卓の見出しでない等）
* #475 に他セッションの着手中コメントがある、または同じ論点（概要欄の卓の見出し）の open issue がほかにある
* （未マージの work/ ブランチとの重なり）未マージの work/ ブランチが live_extract.py・live_candidate.py の同じ関数・同じ正規表現を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* 手順3で変わる行が手順1の見込みの外に出る（対局者・卓・タイトル戦が見込みに無い行で変わる）
* 今回の変更による失敗を2回直しても success にならない
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1115-ACF-45.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1115-ACF-45 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし。ACF は最初の指示でないため識別子の重複確認は対象外。work/1115-acf はローカル・リモートとも無く、origin/cloudflare（1607687f84）から作成
- 手順0: 指示欄の末尾は指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致
- 雛形の行: Chat-Ref・マージ・貼る時機・共通手順の4行とも有り（欠けなし）

## 報告

- 状態: 作業中
- ブランチ: work/1115-acf
- ログ: https://github.com/retroeater/mj/blob/work/1115-acf/docs/logs/CHAT-1115-ACF-45.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1115-acf
- 確認用URL: なし
- マージ: 未
- issue: #475
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 1607687f）: https://github.com/retroeater/mj-logs/tree/main/guide/1607687f

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/1607687f/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1607687f.md
