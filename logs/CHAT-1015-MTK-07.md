# CHAT-1015-MTK-07

- 着手日時: 2026-10-15
- 対象issue: #569
- ブランチ: work/MTK
- 着手時HEAD: 1c5f7b1

## 指示

【Claude作成】Claude Code 向け指示：Mつく（/mtsuku/）の生成とプレビュー（「連盟プロ以外」への登録の後の続き） Chat-Ref: CHAT-1015-MTK-07 マージ: 判断待ちで止まる（平野さんがプレビューで見てから決める） 貼る時機: 平野さんが「Mつく」タブの59行目（動画ID CtDQ5NjWSN0）の「名前3」を「松ヶ瀬隆弥」に直した後（CHAT-1015-MTK-06 の続き。同じセッションの続きでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/MTK の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/MTK を続けて使う（CHAT-1015-MTK-04 の作業の続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。取り込みで生成物でない文書・コードが衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行。例: `scripts/regenerate.py` の `FROZEN_FROM_ALL` への `video_mtsuku` の追加〈CHAT-1015-MTK-05〉と、work/MTK の `/mtsuku/` の2ページの追加）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1015-MTK-06 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1015-MTK-07` を足す。 この指示でも、不明な点は「仮置き」で決めて進め、報告の「判断が必要なこと」に一覧で書く（止まるのは下の「止まる条件」に当たるときだけ）。

目的
CHAT-1015-MTK-04・MTK-06 で止まった原因（「プロ」「連盟プロ以外」に登録の無い4名、59行目の「松ヶ瀨隆弥」）を平野さんが直したので、/mtsuku/ の一覧と各話を生成し、プレビューを出す。
決定（2026-10-15、平野さん）

* MTK-04 の報告の3名（七瀬美希・設楽遙斗・佐藤聖誠）を「連盟プロ以外」に足した（平野さん）
* 「Mつく」タブの「松ヶ瀨隆弥」を「松ヶ瀬隆弥」に直した（「別名」には足さない）（平野さん。11行目・29行目に続き、MTK-06 の報告の59行目の「名前3」も直した）
* MTK-04 で決めたこと（生成を止める範囲・「指名選手」・「N巡目」・見出しは作る人の名前と X の写真だけ、など）はそのまま

前提（チャット側。平野さんの決定ではない）

* MTK-04 の仮置き1〜7（公開日の形・総再生時間・`<title>` など）は、プレビューを見た後に平野さんが決める。この指示では変えない
* 足した3名は、SNS ブックの【1】には次の毎日の更新（04:10 JST）まで載らない（要確認）。生成がこの3名を「X の ID が無い人」（止めない。写真なしで代替アバター）として扱うなら、そのまま進めてよい。「登録が無い人」として止めるなら、下の止まる条件のとおり
* 「Mつく」タブの動画IDの列の見出しは「URL」のままかもしれない（MTK-04 の報告。生成はどちらでも読む）

手順

1. `python3 scripts/regenerate.py --check mtsuku mtsuku_episodes`（ページ名は work/MTK の実物に合わせる）で、止まる理由が無くなったことを確かめ、警告（X の ID が無い人など）の一覧をログに書く
2. 一覧と各話を生成してコミットし、帰り道・最強戦の生成物の差分が0であることを確かめる。push してプレビューを出す
3. 375px・PC 幅・1920×1080 で、一覧・最新話・最古の回・チーム名の無い回（`vSduV7-kLls`）・連盟以外の作る人の回・指名選手に今回足した3名のいる回・松ヶ瀬隆弥の回を headless Chrome で見て、所見をログに書く（写真・「N巡目」の札・代替アバターの見え方を含む）。#569 にコメントする

止まる条件

* 手順1でまだ止まる理由がある（その一覧を書いて止まる。足した3名が SNS ブックに載っていないことが理由なら、そう書く）
* 帰り道・最強戦の生成物に差分が出る
* 「Mつく」タブの行数が 61 と違い、読み直すたびに変わる。読んだ再生時間に1分未満・1時間以上の回がある
* 未マージの work/ ブランチとの衝突は、上の「作業ブランチ」の行のとおり
* cloudflare への push はしない（マージは判断待ち）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に MTK-04 の仮置き1〜7（そのまま写す）と、この指示で仮置きしたことを書く
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧・最新話・チーム名の無い回・連盟以外の作る人の回・足した3名のいる回）を書く
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-MTK-07.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-MTK-07 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットなし。work/MTK はローカル・リモートとも 1c5f7b1
- 雛形の行はすべてあり（貼られた文では改行が詰まっていた）。0.「指示」欄の末尾は指示文の最後の行と一致。MTK-06 のログの状態に「 / 続き: CHAT-1015-MTK-07」を足した
- `git merge origin/cloudflare`（ad09434、衝突なし）。テスト 全体 OK

### 手順1 --check

`python3 scripts/regenerate.py --check mtsuku mtsuku_episodes`: 2ページとも「通りました（61回）」。止まる理由・警告なし（「Mつく」タブ 61行）。
生成は X の ID が無い人を止めないので警告に出さない。`check_saikyo_unregistered.collect_mtsuku()` で数えた結果: 作る人と指名選手132名のうち、登録が無い 0名・重複などで使われない 0名・**X の ID が無い 13名**:
設楽遙斗（最高位戦）・三原孝博（連合）・高橋良介（連盟）・平賀聡彦（最高位戦）・七瀬美希（協会）・荒正義（連盟）・坂本大志（最高位戦）・灘麻太郎（連盟）・ケネス徳田（連盟）・金子正輝（最高位戦）・佐藤聖誠（最高位戦）・森山茂和（連盟）・西島一彦（連盟）。
足した3名（七瀬美希・設楽遙斗・佐藤聖誠）は「連盟プロ以外」から引けるが、SNS ブックにまだ X の ID が無いため写真なし（指名選手のカードは代わりの絵）。止まる理由ではない

### 手順2 生成（e50d804）

- `python3 scripts/regenerate.py mtsuku mtsuku_episodes`: `mtsuku/index.html` と各話61枚（62ファイル）を作り直した。差分は `mtsuku/` だけ
- `regenerate.py video_wayhome wayhome_episodes title_pages saikyo_pages`: `wayhome/`・`title/`・`saikyo/`・サイトマップの差分0

### 手順3 確かめ

headless Chromium（ローカルの `http.server`）で 375×812・1366×900・1920×1080。見たページ: 一覧・最新話 ZeFtFG2ov_Q・最古の回 nLeGJz-nt-E・チーム名の無い回 vSduV7-kLls・連盟以外の作る人の回 6vXHmiBj--c（竹内元太、最高位戦）・足した3名のいる回 VokQ75dRDe4（仲林圭、4巡目 七瀬美希）・dOi0lO1Q_oI（金子正輝、3巡目 佐藤聖誠）・6vXHmiBj--c（2巡目 設楽遙斗）・松ヶ瀬隆弥の回 ElS68JMR6hU。

- 全ページ・全幅で JS のエラー・横スクロール・読めない画像なし
- 見出し（h1）は作る人の名前だけ（和泉由希子・園田賢・滝沢和典・竹内元太・仲林圭・金子正輝・松ヶ瀬隆弥）。連盟以外の作る人（竹内元太・松ヶ瀬隆弥）も写真が出る。X の ID が無い作る人（金子正輝）は名前だけ
- 日付の行は「公開日・「チーム名」・再生時間」（チーム名の無い滝沢和典の回は「公開日・再生時間」）
- 指名選手: 「1巡目」〜「4巡目」の札が写真の左上に出る（3人の回〈園田賢〉は3巡目まで）。PC は4人横1列、375px は2×2。代わりの絵（`img/avatar.svg`）は X の ID が無い人だけ: 坂本大志・設楽遙斗・七瀬美希・平賀聡彦・佐藤聖誠（どの回でも名前は絵の下端に出る）
- 松ヶ瀬隆弥の回: 見出し「松ヶ瀬隆弥」と写真、「松ヶ瀬隆弥の他のエピソード（1件）」。59行目を直した多井隆晴の回（CtDQ5NjWSN0）の3巡目は「松ヶ瀬隆弥」で写真あり
- 一覧: ヒーローは最新話の和泉由希子、総再生時間「約12時間19分」、`?name=` は MTK-01 と同じく効く

### プレビュー

- e50d804 の「Workers Builds: mj」success・「公開対象を検査する」success。プレビューで `/mtsuku` → 301、`/mtsuku/`・ZeFtFG2ov_Q・vSduV7-kLls・6vXHmiBj--c・dOi0lO1Q_oI が 200 で noindex あり、各話に「N巡目」の札。dOi0lO1Q_oI は手元の生成物と同じバイト。`/video_mtsuku.html` は 200（旧のまま）

## 報告

- 状態: 判断待ち（平野さんがプレビューで見て、マージと仮置きを決める）
- ブランチ: work/MTK
- ログ: https://github.com/retroeater/mj/blob/work/MTK/docs/logs/CHAT-1015-MTK-07.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/MTK
- 確認用URL: プレビューあり（URL は最終報告。一覧・最新話・チーム名の無い回・連盟以外の作る人の回・足した3名のいる回）。この版で見える印: 各話の「指名選手」の写真の左上に「1巡目」〜「4巡目」
- マージ: 未（判断待ち）
- issue: #569（生成とプレビューをコメント）
- 判断が必要なこと:
  - プレビューで見て、マージするかを決める
  - MTK-04 の仮置き1〜7（そのまま写す）:
    - 仮置き1 公開日: シートの日付の 00:00 JST（`uploadDate` は「2025-07-22T00:00:00+09:00」）。ほかの案: 日付だけの形
    - 仮置き2 総再生時間: 全回の再生時間がそろったときだけ出す（今は61回そろっていて出る）。ほかの案: そろった回だけで出す
    - 仮置き3 `<title>`・og:title・前後のリンク・`VideoObject.name` は「「チーム名」 作る人」のまま（同じ作る人の回が11名に2回ずつある）。見出し（h1）だけ作る人
    - 仮置き4 指名選手の節の位置は前後のエピソードの下・「〇〇の他のエピソード」の上。カードは X ID がある人だけ X へのリンク（最強戦と同じ）、名前は現在名、団体は alt にだけ出す
    - 仮置き5 「N巡目」の札の色・大きさは title/ の決勝ライブの札と同じ（黒 75%・白字・0.875rem）
    - 仮置き6 常設 issue「最強戦の出場者の登録漏れ」の「Mつく」の節に、X ID が無い人も出す。10名が残る間 issue は閉じない（閉じたいなら、X ID が無い人を節から外すか別の issue にする）
    - 仮置き7 名前の辞書の読み込み（`load_name_book()`）は `generate_saikyo_pages.py`・`generate_live_pages.py` と同じ処理の3つ目の写しを `lib/mtsuku.py` に置いた（最強戦のファイルを変える未マージのブランチがあるため共通化しなかった）
  - X の ID が無い人は今は13名（MTK-04 の10名に、足した3名〈設楽遙斗・七瀬美希・佐藤聖誠〉が加わった）。SNS ブックの毎日の更新・【2】の ID の入力で写真が出るようになる
  - この指示で仮置きしたこと: なし
- 未確認の項目:
  - 足した3名が SNS ブックに載った後（毎日の更新、04:10 JST）の写真（X の ID が入れば、次の再生成で出る）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj ddd0fcee）: https://github.com/retroeater/mj-logs/tree/main/guide/ddd0fcee

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/ddd0fcee/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/66464371.md
