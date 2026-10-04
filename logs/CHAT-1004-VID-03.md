# CHAT-1004-VID-03

- 着手日時: 2026-10-04
- 対象issue: なし
- ブランチ: work/1003-vid-01
- 着手時HEAD: 4d4442a2（origin/work/1003-vid-01 2ad55f97 に origin/cloudflare 2040e6c4 を merge）

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」（title/）の告知動画の初版を作り、平野さんに送る（十段戦 第43期＋検索「岡本和也」。マージせず判断待ちで止まる）
Chat-Ref: CHAT-1004-VID-03
マージ: 判断待ちで止まる（平野さんが動画を見て決める。制作のスクリプトを直しの往復に使うため、作業ブランチに残したまま止まる）
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。未マージの work/1003-vid-01 を続けて使う（CHAT-1003-VID-01 の下調べの続きのため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1003-vid-01 への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1003-VID-01 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。CHAT-1004-VID-02 のコミット（`git log --all --grep="CHAT-1004-VID-02"`）か docs/logs/ のログがあれば、何もせず止まる。

## 目的
X に投稿する「タイトル戦」（https://ryoei.pro/title/）リニューアルの告知動画の初版を作り、平野さんに mp4 を渡す。平野さんが見て、直すか投稿するかを決める。環境の作り方（プロキシの CA の登録、HyperFrames の実行のしかた、連番の撮り方）は CHAT-1003-VID-01 のログの「経過」に従う。CHAT-1004-VID-02 は送る前に差し替えたため欠番（検索する選手の変更）。

### 決定（平野さん）
- 2026-10-03: 告知は X に投稿する15〜30秒程度の短い動画。構成は「数字のモーショングラフィックス → 実画面の操作デモ → URL」の合わせ技。選手の写真（X の画像）が映るのはそのまま映してよい
- 2026-10-04: 操作デモで映す期は「十段戦 第43期」、検索する選手は「岡本和也」（当初の「白鳥翔」から変更）

### 前提（チャット側。平野さんの決定ではない）
チャット側が示した一式。平野さんは「大会・選手を変える」とだけ答えており、ほかの項目は明示の決定ではない。実物に合わせて変えてよく、変えたら報告に書く。
- 形式: 縦長 1080×1920・30fps・約20秒（15〜30秒に収める）・無音・mp4（H.264、yuv420p）
- 構成と文言の案（黒地・白字。title/ の OGP と同じ調子）
  - 数字（約4秒）: 「タイトル戦のページを新しくしました」→「20大会・363期の決勝」→「決勝の映像 619本」（数字はカウントアップ）
  - 操作デモ（約13秒）: 入口 → プルダウンで十段戦 → 第43期 → 期ページ（決勝の選手 → 決勝ライブ〈決勝動画があればそこまで〉）→ 検索欄に「岡本和也」→ 出場した期の一覧。場面ごとに短い字幕（例: 「大会を選ぶ」「期ごとの決勝の結果」「決勝の放送をすぐ見られる」「選手名で検索（決勝進出 581人）」）
  - URL（約3秒）: 「ryoei.pro/title/」
- 字幕と URL は画面の下端から10%以上離す（X の再生の操作部が下に重なるため）
- 操作デモの素材はスクリーンショットの連番（1170×1992。CHAT-1003-VID-01 で確認した撮り方）
- 十段戦 第43期は、CHAT-1003-VID-01 のログでは「決勝動画が無い期」に含まれ、十段戦は写真の無い選手がいる期が多い。平野さんはこれを聞いたうえで指定している（決勝ライブだけ・代替アバターありでも進めてよい）
- 「岡本和也」の決勝進出の回数と写真の有無は、チャット側では確かめていない
- 記録の年（1973年〜）は動画に出さない（年が仮の値の期があるため）
- 音は入れない。/brag は入れない（HyperFrames を直接使う）
- 平野さんはマージ（docs/logs・docs/decisions・docs/notes/title-pages.md の数値直し。動画は入れない）を 2026-10-04 に承認済み。ただし直しの往復に備え、今回はマージせず、動画が確定した後の指示でまとめてマージする

### やらないこと
- 動画・連番の画像・取得したフォントや gsap などの素材をコミットしない
- `.claude/` 配下、title/ の生成物・生成スクリプト・CSS・JS・シートを変えない
- X など外部へ投稿・送信しない（平野さんへのファイルの受け渡しを除く）
- cloudflare へマージしない

## 手順
1. **素材を撮る。** 先に、その時点の本番の十段戦 第43期の期ページを確かめ、決勝ライブ・決勝動画の本数と、写真カードのうち代替アバターの枠数をログに書く。検索する「岡本和也」の決勝進出の回数と、写真の有無（代替アバターか）もログに書く（写真が無くても進めてよい）。動画に出す数字（大会数・期数・決勝の映像の本数・決勝進出の実人数）を CHAT-1003-VID-01 と同じ数え方で数え直し、違えば新しい値を使って差を書く。コンテナに日本語の字形のフォント（Noto Sans CJK JP など）を入れ、ページの字が日本語の字形で出ることを静止画で確かめてから、前提の操作デモの流れを連番で撮る。写真・サムネイルの読み込みを待ってから撮り、白いままの画像が映るフレームを残さない。検索で1人だけ出たときは期の一覧が最初から開いているので、押して閉じない。
2. **描画する。** HyperFrames で前提の構成を作り、`hyperframes check` のあと `render` で mp4 にする（gsap とフォントはローカルに置き、利用統計・更新確認・skill の取得は切る）。書き出した mp4 を ffprobe で確かめ（解像度・fps・長さ・コーデック・音声トラックの有無・サイズ）、2秒おきの静止画を自分で見て、字の欠け・折り返し・はみ出し・白いフレーム・灰色の余白が無いこと、字幕が読める大きさと長さ（1つ2秒以上）で出ていることを確かめる。だめな所は直して描画し直す（描画のやり直しは3回まで。残った問題は報告に書く）。
3. **渡して、残す。** mp4 を `SendUserFile` で平野さんに送る（失敗したら別の手段を試さず、失敗の内容と受け渡しの候補を報告する）。制作に使ったスクリプト（撮影の台本、HyperFrames の構成の HTML・設定、環境を作る手順のスクリプト）を作業ブランチにコミットして push する。置き場所は `.assetsignore` で配信から外れる既存のディレクトリの下にし、最上位に新しい項目を作らない（置いた場所と、新しいセッションで作り直す手順をログに書く）。docs/notes/title-pages.md の古い数値（期数・決勝の映像の期数と本数・検索のページ数など）は、現在の内容を読んだうえで、手順1で数えた値と日付に置き換える（同じ趣旨の記述は置き換え・拡張でよい。矛盾があり、どちらが正か判断が要るときだけ止まる）。

## 止まる条件
- 0章の確認が満たされない
- 十段戦 第43期の期ページが無い、決勝ライブも決勝動画も1本も無い、または検索で「岡本和也」が出ない
- 連番の撮影か描画ができない（別の手段を2つまで試してだめなとき）
- 15〜30秒に収まらない
- 「やらないこと」のどれかが必要になった

## 完了条件
- mp4 を平野さんに送り、ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告には、動画の仕様（ffprobe の値）、場面ごとの秒数と字幕の文言、使った数字と数え直しの差、十段戦 第43期の実態（映像の本数・代替アバターの枠数）、岡本和也の決勝進出の回数と写真の有無、前提から変えた点、静止画で確かめた結果、スクリプトの置き場所、受け渡しの結果を含める
- マージは冒頭の「マージ:」の行のとおり（状態は「判断待ち」）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1004-VID-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1004-VID-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `git log --all --grep` と `docs/logs/` の履歴に CHAT-1004-VID-02・VID-03 は無し（VID は本セッションが VID-01 で使った識別子）
- ブランチ: ローカルの work/1003-vid-01 は origin/work/1003-vid-01（2ad55f97）と一致。origin/cloudflare が祖先でなかったため `git merge origin/cloudflare`（取り込んだのは data/live_channel_raw.jsonl の4行のみ）
- 0: 指示欄の末尾は指示文の最後の行と一致。CHAT-1003-VID-01 の `## 報告` の状態は「判断待ち」

## 報告

- 状態: 作業中
- ブランチ: work/1003-vid-01
- ログ: https://github.com/retroeater/mj/blob/work/1003-vid-01/docs/logs/CHAT-1004-VID-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-vid-01
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 815188dc）: https://github.com/retroeater/mj-logs/tree/main/guide/815188dc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/89a52942.md
