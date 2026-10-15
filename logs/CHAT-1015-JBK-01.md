# CHAT-1015-JBK-01

- 着手日時: 2026-10-15
- 対象issue: 未定（起票後に記入）
- ブランチ: work/JBK
- 着手時HEAD: 95d46b4d9c3c30d6c7673caf1e51e44e05557884

## 指示

【Claude作成】Claude Code 向け指示：「女流勉強会」の一覧・各話を帰り道と同じ形で作る（未公開でプレビューまで）
Chat-Ref: CHAT-1015-JBK-01
マージ: 判断待ちで止まる（平野さんが動く画面〈プレビュー〉で仕様を確かめてから決める）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/JBK の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/JBK を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/JBK origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。
   **平野さんは約4時間不在。着手前・作業中に質問せず、帰り道・Mつくとの差異、判断に迷う動画、不明な点は「仮置き」で決めて進め、仮置きしたことをすべて報告の「判断が必要なこと」に一覧で書く。止まるのは下の「止まる条件」に当たるときだけ。**

## 目的
日本プロ麻雀連盟の YouTube チャンネルの「女流勉強会」の動画を、帰り道（`/wayhome/`）・Mつく（`/mtsuku/`）と同じ仕様・デザインの一覧と各話のページにし、未公開の形でプレビューに出す。平野さんは戻ったらプレビューで仕様を確かめる。

### 決定（2026-10-15、平野さん）
- 動画「女流勉強会」のページを作る。「帰り道」とほぼ同じ構成で、講師と生徒を並べるイメージ
- 対象は、日本プロ麻雀連盟チャンネルの、タイトルに「女流勉強会」が含まれる全動画。判断に迷うものは仮置きし、あとで平野さんに確かめる
- 「Mつく」のときのように、動く状態（プレビュー）まで作る。不明な点は仮置きして、あとでまとめて確かめる

### 前提（チャット側。平野さんの決定ではない。実物に合わせて変えてよい）
- 仕様の正: 帰り道は `docs/decisions/wayhome.md`・`docs/notes/video-wayhome.md`、Mつくは `docs/decisions/mtsuku.md`・`docs/notes/video-mtsuku.md`。「同じ形」はそこにある一覧・各話の形（濃色固定、最新話のヒーロー、横スクロールのカード、固定の検索バー、件数の表示なし・0件の知らせ、再生時間、「▶ 再生」、「一覧に戻る」、前後のエピソード、「〇〇の他のエピソード」、各話の canonical・`VideoObject`・`BreadcrumbList`、共有ボタンなし、各話の `.mj-lead` なし）を指す。帰り道に固有のもの（「決勝戦を見る」、一覧の OGP 合成画像）は作らない
- 動画の元データの案: /live の層1 `data/live_channel_raw.jsonl`（連盟チャンネルの公開とメンバー限定の動画。動画ごとの最新の行に題名・概要欄・公開日時・再生時間・サムネイル・状態がある、要確認）から、題名に「女流勉強会」を含む動画を拾う。YouTube の API・鍵は使わない
- 講師・生徒の案: 題名・概要欄から各回の講師と生徒（人数は回ごとに違ってよい）を読み取り、リポジトリの仮のファイル（例 `data/joryu_benkyokai_provisional.tsv`。列はシートのタブにそのまま貼れる形: 動画ID・公開日・講師・生徒1〜N など）に書いて、生成はそれを読む。読み取れない・自信の無い回は、その回と理由を一覧にする。シートのタブ（Mつくのように平野さんが持つ）にするか、題名からの自動の読み取りを正にして補正だけをタブにするかは、平野さんが戻ってから決める（選択肢・長所短所・Code の推す案を報告に書く）
- 仮置きの案（チャット側の案。実物を見て変えてよい）:
  - URL は `/benkyokai/`（一覧 `benkyokai/index.html`、各話 `benkyokai/<動画ID>.html`）。シリーズ名・見出しは「女流勉強会」
  - 各話の主人公（ヒーローの名前と X の写真、「〇〇の他のエピソード」の束ね方）は講師。生徒は Mつくの「指名選手」と同じ位置・形の写真カードの節「生徒」に並べる（講師も同じ節の先頭か別の節「講師」に出すかは、見比べて良い方を仮置き）
  - 一覧の検索は、公開日・題名の回の名前・講師・生徒で拾う。検索欄の文言は「選手名で検索」程度
  - 紹介文（各話のヒーロー・meta description・og:description・`VideoObject.description`）は自動で組む。例「日本プロ麻雀連盟の「女流勉強会」。<講師>プロが講師を務めた回です。」
  - 一覧の og:image は最新話の YouTube サムネイル（合成画像は作らない。Mつくと同じ）
  - メンバー限定・配信予定・配信中・ショート・切り抜き、題名に「女流勉強会」を含むが別の番組の回などは、入れるか外すかを仮置きし、回ごとに一覧にする（既定の案: 公開済みの通常の動画とライブのアーカイブは入れ、メンバー限定・配信予定は外す）
  - 新しい回の扱い（層1の毎日の取り込みから自動で載せる／帰り道のように常設 issue で知らせてシートに足す／何もしない）は作らない。案と見込みを報告に書く
- 写真: Mつくと同じ規則（名前を「別名」で現在名に直して「プロ」「連盟プロ以外」から引き、登録が無い人・X ID があって写真が読めない人で生成を止める。X ID が無い人は止めず名前だけ・代わりの絵）で作る。**ただしこのプレビューに限り、止まる人がいれば、その人を名前だけ（カードは代わりの絵）で出す仮の扱いで生成し、該当者の名前と理由の一覧を報告に書く**（平野さんが不在でシートを直せないため。マージの前にこの仮の扱いを外す）
- 帰り道・Mつくと共通にできる部品（`lib/wayhome.py`・`lib/mtsuku.py`・`lib/name_book.py`・`style.css`・JS）は複製せず共通化してよい。ただし帰り道・Mつく・title/ の生成物は1バイトも変えない
- `.github/workflows/` のファイルはこの指示では変えない（Actions の整理〈WFL 系列、#564〉が並行して変えているため）。`assets-check.yml` の許可リストなど、要る変更は報告に書いて進める（作業ブランチの check がそれだけで失敗するのは想定どおり。最終報告に「失敗通知が届くが対応不要」と書く）

## 手順
1. 調べる: 同じ主題の issue（「女流勉強会」「benkyokai」で、クローズ済みも）と未マージの work/ ブランチを確かめる。層1から題名に「女流勉強会」を含む動画を拾い、全件（動画ID・公開日・題名・状態・再生時間）と、読み取った講師・生徒、入れる・外す・迷うの仕分けをログの `## 経過` に表で書く（仕分けの理由も）。作る issue を起票し（題は「女流勉強会の一覧・各話を帰り道と同じ形で作る」程度）、着手中のコメントを書く
2. 作る: 一覧と各話を、上の決定・前提で未公開（docs/new-page-checklist.md の段1: noindex・どこからも未リンク・サイトマップ未参照・`llms.txt` に載せない）で生成する（`regenerate.py` のページ名・`OUTPUT_OVERRIDES`・`_redirects`・`.assetsignore` は new-page-checklist.md の「作る段の確かめ」で決める）。テスト（`scripts/tests/test_mtsuku.py` に倣う）を足し、`regenerate.py video_wayhome wayhome_episodes mtsuku mtsuku_episodes title_pages` の生成物の差分が0であることを確かめる
3. 確かめと文書: 375px・PC 幅・1920×1080 で、一覧・最新話・最古の回・生徒の多い回・写真の無い人がいる回を headless Chrome で見て所見をログに書く。プレビュー（確認用 URL）を出す。公開の issue を起票し（本文に段2のチェックリスト、navbar の「動画」への追加、「作る issue を待つ」）、作る issue に番号をコメントする。`docs/notes/` に実装記録（新しい文書なら handover.md の6章の表に1行）、`docs/notes/static-generation.md`「ページの一覧」に「noindex・メニュー未掲載、公開は #NNN」、平野さんの決定を `docs/decisions/`（分野は README に従う）に書く

## 止まる条件
- 層1から「女流勉強会」を含む動画が1本も見つからない、または読み直すたびに本数が変わる（本数を書いて止まる）
- 帰り道・Mつく・title/ の生成物に差分が出て、共通化の直しで消せない
- 同じ主題の open issue がある、または他のセッションの着手中のコメントがある
- 未マージの work/ ブランチが、変えるファイルの同じ行・同じ関数（`lib/wayhome.py`・`lib/mtsuku.py`・`lib/name_book.py`・`style.css` の `.mj-video-*`・`.mj-mtsuku-round`・`regenerate.py` の `OUTPUT_OVERRIDES`・`_redirects`）を変えている、または取り込みで衝突する（`git branch -r --no-merged origin/cloudflare` の一覧から確かめる。同じファイルの別の箇所の変更では止まらず、ログに書く）
- cloudflare への push はしない（マージは判断待ち）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、仮置きしたことの一覧（何を・どう仮置きしたか・ほかの案）、迷った動画の一覧、写真の仮の扱いをした人の一覧、データの持ち方（シートのタブか自動の読み取りか）と新しい回の扱いの選択肢（長所・短所・推す案）、要るワークフローの変更を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）。最終報告に確認用の URL（一覧と、最新話・最古の回の各話）を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-JBK-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-JBK-01 を書く

平野さんは不在のため着手前の質問はせず、不明な点は仮置きで進めて報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep` と `docs/logs/` の履歴（unshallow 後）に `JBK` なし
- 作業ブランチ: ローカル・リモートとも `work/JBK` なし → `git checkout -b work/JBK origin/cloudflare`

## 報告

- 状態: 作業中
- ブランチ: work/JBK
- ログ: https://github.com/retroeater/mj/blob/work/JBK/docs/logs/CHAT-1015-JBK-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/JBK
- 確認用URL: 未
- マージ: 未
- issue: 未
- 判断が必要なこと: 未
- 未確認の項目: 未
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 95d46b4d）: https://github.com/retroeater/mj-logs/tree/main/guide/95d46b4d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/95d46b4d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95d46b4d.md
