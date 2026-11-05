# CHAT-1105-LGV-02

- 着手日時: 2026-11-05
- 対象issue: #640（参照）
- ブランチ: work/1105-lgv
- 着手時HEAD: a5ef018c5

## 指示

【Claude作成】Claude Code 向け指示：「ログ」の告知動画を直す（場面6でタグを外す手順を映す・場面7は結果で止める） Chat-Ref: CHAT-1105-LGV-02 マージ: 判断待ちで止まる（制作のスクリプトは作業ブランチに置き、動画の確定までマージしない。docs/notes/page-announcement.md「進め方」） 貼る時機: CHAT-1105-LGV-01 の後（同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lgv の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lgv を続けて使う（CHAT-1105-LGV-01 の制作のスクリプトがあるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき: 両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1105-LGV-01 のログの `## 報告` が「判断待ち」で、`scripts/promo_video/log/` が work/1105-lgv にあることを確かめ、そうでなければ何もせずに止まる。log/ の告知の期間は CHAT-1105-LGV-01 の着手（2026-11-05 14:20 JST）から続いている。

目的
CHAT-1105-LGV-01 の初版（34.5秒）の「判断が必要なこと」への平野さんの回答に合わせて、告知動画を撮り直して送る。
決定（2026-11-05、平野さん）

* 場面7は LGV-01 の報告の案 (b): 現在地の結果で止め、ページの下の説明文までは送らない。テロップ「飲食店1,700店超・2,400件超」はそのまま（画面の下の件数は、それまでに何度も映っているため）
* 場面6は、タグを外す手順を映す（ページが自動で外すのに任せない）
* 新宿の店名「レディース&ジェントルメン」の `&amp;` は直す（直し方は別の指示。この指示ではデータもページも変えない）
* ほかは CHAT-1105-LGV-01 の決定のまま（構成・投稿者2人・茗荷谷を映さない・はしご・新宿駅・曲調 b）

前提（チャット側。平野さんの決定ではない）

* 場面6の外し方の案: 投稿者「谷岡育夫」→ 店名「支那麺 はしご」→ 駅「銀座」の順に × を押し、それぞれに押す印（丸）を出してから押す。3つとも外すとトップ（カテゴリの行）が一瞬戻るが、そのまま映す。その後に照準のアイコンを押す
* 手順が増える分、場面6を延ばし、全体は約35〜37秒にしてよい（ほかの場面の秒は変えない。延ばした後の秒を表で書く）
* 「&」の直しはこの後に別の指示で行う。撮るコマに「&」の文が映るかを、茗荷谷と同じ形（画面に見えている文）で確かめ、映るなら止めずに場面と秒を報告する

手順

1. CHAT-1105-LGV-01 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1105-LGV-02` を足す
2. 上の決定・前提のとおり `scripts/promo_video/log/` を直して撮り直す。撮った後にコマで確かめる（場面6で3つのタグを順に外すのが映るか、場面7が現在地の結果で止まるか、茗荷谷・Google マップの画面が映らないか、白いままのコマが無いか、「&」が映るか）。長さ・各場面の秒の実際を表にして書く
3. mp4 を `SendUserFile` で送り、直したスクリプト・README.md を push する。判断待ちで止まる（動画の直し・確定は次の指示）

止まる条件

* 茗荷谷が映らない撮り方が見つからない
* 本番の「支那麺 はしご」・新宿の駅の点・現在地のアイコンが LGV-01 の値と食い違い、構成のとおりに撮れない（値を書いて止まる）
* 未マージの work/ ブランチが `scripts/promo_video/log/` の同じファイルを変えている、または取り込みで衝突する

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1105-LGV-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1105-LGV-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1105-lgv
- ログ: https://github.com/retroeater/mj/blob/work/1105-lgv/docs/logs/CHAT-1105-LGV-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lgv
- 確認用URL: なし
- マージ: 未
- issue: #640（参照）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4a8b9cb3）: https://github.com/retroeater/mj-logs/tree/main/guide/4a8b9cb3

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4a8b9cb3/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/275d8111.md
