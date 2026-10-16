# CHAT-1016-SKR-05

- 着手日時: 2026-10-15
- 対象issue: #549
- ブランチ: work/1015-skr-vid
- 着手時HEAD: 76e8c0e96

## 指示

【Claude作成】Claude Code 向け指示：最強戦のリニューアルの告知動画の初版を、公開の作業ブランチのプレビューから作って送る（マージしない）
Chat-Ref: CHAT-1016-SKR-05
マージ: 判断待ちで止まる（平野さんが動画を見て確定するまで。制作のスクリプトは作業ブランチに置く）
貼る時機: 平野さんがクラウド環境の Network access の Allowed domains に `image.p-c2-x.abema-tv.com` を足した後。新しいセッションに貼る（設定の読み込みに左右されるため。0章で「このセッションが新しく始めたものか」をログに書く）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1015-skr-vid の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1015-skr-vid を続けて使う（CHAT-1015-SKR-04 が作ってログだけを push し、止まったため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。公開の作業ブランチ `work/1015-skr-pub` には push しない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。このセッションが新しく始めたものかをログに書く。CHAT-1015-SKR-04 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1016-SKR-05` を足す。

## 目的
CHAT-1015-SKR-04 は ABEMA の画像サーバへの接続がプロキシに拒否されて止まった（同じ指示のやり直し）。平野さんが環境の Allowed domains に `image.p-c2-x.abema-tv.com` を足した（平野さんの申告。手順1 で取得できるかを確かめる）。
最強戦（saikyo/）のリニューアル（#549、未マージの `work/1015-skr-pub`）の X での告知動画の初版を作り、mp4 を `SendUserFile` で平野さんに送る。型は docs/notes/page-announcement.md（進め方・告知動画の型）。動画が確定したら、別の指示で `work/1015-skr-pub` と制作のスクリプトをマージする（＝公開）。

### 決定（2026-10-15、平野さん）
- 撮るのは公開前の作業ブランチ `work/1015-skr-pub` の Workers Builds のプレビュー（別名 URL。URL はログに書かず、check-run「Workers Builds: mj」の出力から取る）。本番は撮らない
- 見本の選手は桑田憲汰。選手の写真・名前・成績はそのまま映してよい。曲は今までと同じ曲調 b（`scripts/promo_video/title/music.py`）
- 構成・テロップ・投稿文はチャット側の案のまま（下の表）

| 場面 | 秒 | 画面 | テロップ |
|---|---|---|---|
| 1 タイトル | 0〜2.5 | 白地に2行「最強戦」「全16年度の全対局を1卓ずつ」 | なし |
| 2 検索 | 2.5〜5.5 | トップの検索欄に選手名を入れる（桑田 → 桑田憲汰） | 選手名で検索 |
| 3 一覧 | 5.5〜8.5 | その選手が出た対局の面が並ぶ | 出場した対局がひと目で |
| 4 卓 | 8.5〜12.5 | 1つ押して卓のページへ（ABEMA のサムネイル・出場者） | 卓ごとのページ |
| 5 再生 | 12.5〜15 | 「▶ 再生」に丸を出す（押した先は映さない） | ABEMA の対局動画へ |
| 6 年度 | 15〜19.5 | 年度ページで「麻雀最強戦2025 ▾」から年度を切り替える | 2011〜2026年の全年度 |
| 7 締め | 19.5〜23 | 白地に `img/ogp.png` の「ryoei.pro」が1秒で現れ、その後は動かない | なし |

投稿文（平野さんが投稿する。参考としてログに写すだけ）:
```
「最強戦」をリニューアルしました。
https://ryoei.pro/saikyo/

・選手名から、出場した全対局を検索
・卓ごとのページから ABEMA の対局動画へ
・2011年〜2026年の全年度に対応
```

### 前提（チャット側。平野さんの決定ではない）
- 「全16年度」「2011〜2026年」は、`work/1015-skr-pub` の生成物の年度ページの数と範囲で確かめる（要確認。違えばテロップと場面1の文言を実物に合わせ、ログに書く）
- 場面4 の卓は、桑田憲汰が出た卓のうち ABEMA の回があり、ヒーローにサムネイルが出るもの（新しい年度を優先）を選ぶ。場面6 の切り替え先の年度は、写真の多い過去の年度を1つ選ぶ（選んだ理由をログに書く）
- 撮り方は型のとおり（iPhone 13 相当 390×664・3倍の高解像度のスクリーンショットの連番、写真は読み終わってから撮る、白いままのコマを残さない、最初のコマからタイトルがはっきり出る、テロップの位置）。既存の `scripts/promo_video/`（houou_race・dictionary・title）の作りを流用し、新しい制作のスクリプトは `scripts/promo_video/saikyo/` に置く（作り直しの手順は同じフォルダの `README.md`）
- CHAT-1015-SKR-04 で確かめた事実（ログの経過）: プレビューは開けて印も出た。X の写真は取得できた。年度は 2011〜2026 の16年度。場面4 の候補は 2025 決勝（`2025/20251214-s1`）・2025 ファイナル 2nd Stage ベスト8 B卓（`2025/20251214-2-s1-b`）で、どちらも ABEMA の回がある

## 手順
1. 確かめる: `work/1015-skr-pub` の HEAD とその check-run「Workers Builds: mj」が success で、プレビューの別名に `?v=<SHA>` を付けて開けること（CHAT-1015-SKR-02 のログの「確認用URL」の印が出ていること）。ABEMA の画像と X の写真（`pbs.twimg.com`）を1枚ずつ取得できるか。年度の数と範囲
2. 作る: 上の構成で初版の mp4（1080×1920・30fps・H.264・yuv420p・AAC・-16 LUFS 前後、約23秒）を作り、`SendUserFile` で送る。最初のコマ・各場面の代表のコマを PNG でログの経過に挙げる（画像そのものはリポジトリに入れない。動画もリポジトリに入れない）
3. 制作のスクリプトと README を作業ブランチにコミットして push し、判断待ちで止まる（docs/notes/page-announcement.md の実例の表への追記は、確定後のマージの指示で行う）

## 止まる条件
- `work/1015-skr-pub` のプレビューが開けない、または CHAT-1015-SKR-02 の印が出ない
- ABEMA の画像の取得がプロキシに拒否される（ヒーローのサムネイルが写らない動画は作らない。どのホストが拒否されたかを書いて止まる。チャット側が平野さんに環境のネットワーク設定の変更を頼む）。X の写真も同じ
- 構成どおりに撮れない場面がある（例: 桑田憲汰の卓に ABEMA の回が無い、検索の結果の面が想定と違う）。その場面の代わりの案を書いて止まる
- 未マージの work/ ブランチが `scripts/promo_video/` の同じファイルを変えている（別のファイルなら止まらずログに書く）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（状態は「判断待ち」。判断が必要なこと: 動画を確定してよいか・直す所）
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1016-SKR-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1016-SKR-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj f003b3c7）: https://github.com/retroeater/mj-logs/tree/main/guide/f003b3c7

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/f003b3c7/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/82bde681.md
