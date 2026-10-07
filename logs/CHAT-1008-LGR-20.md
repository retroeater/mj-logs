# CHAT-1008-LGR-20

- 着手日時: 2026-10-08
- 対象issue: なし（同じ論点の issue を検索してから決める）
- ブランチ: work/1008-lgr
- 着手時HEAD: fc6bc248

## 指示

【Claude作成】Claude Code 向け指示：新しいページの告知（X の投稿文と告知動画）の型を、「鳳凰戦 順位変動」の実例から文書に書き残す（変更は docs だけ） Chat-Ref: CHAT-1008-LGR-20 マージ: ドキュメントのみ（docs/ 配下）なので、止まる条件に当たらなければ完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1008-lgr を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1008-lgr origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1008-lgr の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 変更の範囲: docs/（docs/notes・docs/decisions・docs/logs・docs/new-page-checklist.md・docs/handover.md を含む）。ページ・スクリプト・ワークフローは変えない

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「鳳凰戦 順位変動」の告知を、平野さんが X に投稿した。平野さんはこの形を今後の告知の型にすると決めたので、次に告知を作るチャットと Claude Code が同じ形で始められるよう、文書に書き残す。
決定（2026-10-08、平野さん）

* 「鳳凰戦 順位変動」の告知の投稿（https://x.com/retroeater/status/2107772937482694688 ）は、気になるところなし
* 今後の告知は、概ねこのテンプレートで行く（動画の構成を含めて）

前提（チャット側。平野さんの決定ではない）

* 投稿の実物（平野さんがチャットに送った画面の写しで、チャット側が読んだもの。2026-10-07 19:00 の投稿）:


```
鳳凰戦「順位変動」を公開しました。
ryoei.pro/houou_race.html

・リーグ戦の昇降級争いを節単位で再現
・昇降級ボーダーの激しい攻防を振り返る
・第23期鳳凰戦以降の全リーグに対応

```

その下に告知動画（24秒・縦長）が付く。動画を付けた投稿ではリンクのカード（OGP）は出ず、URL は本文の中の文字のリンクになる（画面の写しでも、カードは出ていない）

* 投稿文の型（上の実物から、チャット側が形にしたもの）: 1行目「<メニューの名前>「<ページの名前>」を公開しました。」／2行目にページの URL／1行空ける／「・」で始まる3行（何が見られるか、見どころ、対応している範囲）。動画を付ける。文の案はチャット側が出し、平野さんが直して投稿する（今回も、平野さんはチャット側の案から文言を変えている）
* 動画の型（CHAT-1007-LGR-17 の構成。docs/notes/houou-race.md「告知動画」と CHAT-1007-LGR-17 のログで確かめて書く）:
   * 形式: 縦長 1080×1920・30fps・15〜30秒（今回は24秒）・音楽つき（曲調 b、タイトル戦の告知と同じ曲）・白地に黒字
   * 構成: (1) タイトル（ページの名前と、ひとことの説明） (2) 実画面で、選ぶ部品を順に示す (3) 主な操作を押す（押した位置に印） (4) ページの中心の動きを実際の速さで見せる (5) 残りを早送り (6) 結果の画面で止め、対応している範囲をテロップで出す (7) 白地に「ryoei.pro」（`img/ogp.png`）が現れて締める（ほかの文字は足さない）
   * テロップは1つ2秒以上、画面の下の端から10%以上離し、見せたい所を隠さない。補足の説明が要る所だけに出す
   * 素材は本番のページを高解像度の連番で撮る。見本にする表は、そのページの既定の表示
* 進め方の型（今回の流れ）: チャット側が構成の表とテロップの案を出す → 平野さんが OK か文言を直す → Claude Code が初版を作って `SendUserFile` で送る（マージしない） → 平野さんが見て決める → 確定したら制作のスクリプトと文書をマージする（動画はリポジトリに入れない） → 平野さんが投稿する
* 動画ごとに平野さんに確かめること（型には含めず、毎回確かめる項目として書く）: 選手の写真・名前など人が映ること（タイトル戦と順位変動では、どちらも「そのまま映してよい」だった）、見本にするページや表、曲を変えるか
* チャット側の気づき（平野さんは判断していない。型の規則にはせず、「次に作る時に考える点」として1行で残す）: X のサムネイルは動画の最初のコマになる。今回はタイトルが現れる途中のコマだったので、サムネイルの「鳳凰戦 順位変動」の字が薄く出た。最初のコマからタイトルをはっきり出すと、再生前でも何の動画か分かりやすい
* 書く場所の案: 告知の型は1つの文書にまとめる（案: docs/notes/ の下に新しい資料。名前は既存の資料の付け方に合わせる。docs/notes/ は mj-logs に写るので、次に告知を始めるチャットが読める）。docs/notes/title-pages.md「告知動画」と docs/notes/houou-race.md「告知動画」には、その資料への参照を1行ずつ足す（同じ内容を2か所に書かない）。docs/new-page-checklist.md の公開の後の所に「告知するときは、この資料の型で」の1行を足す。docs/handover.md の資料の一覧に1行足す（容量の上限があるので、先に大きさを測る）
* タイトル戦の告知動画（黒地、数字のモーショングラフィックスから始まる構成）は、今回の型とは形が違う。型は今回の順位変動のものとし、タイトル戦の分は実例の1つとして触れるだけにする
* 決定は docs/decisions/page-release.md に新しい節として足す（合う分野が別にあれば、そちらでよい）
* mj-logs は public。投稿の URL と投稿文は公開されているものなので、文書とログに書いてよい
* 使う skill: 文書の文面は writing-for-agents を使う

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認）、あれば止まって報告する。論点は「告知（X の投稿・告知動画）の型・手順」
2. 型の資料を書く。docs/notes/houou-race.md「告知動画」・docs/notes/title-pages.md「告知動画」・CHAT-1007-LGR-17 のログの今の内容を読み、前提の案（投稿文の型、動画の型、進め方、毎回確かめること、次に考える点、実例）を、実物と食い違わない形で1つの資料にまとめる
3. 参照と記録を足してマージする。追記先（docs/new-page-checklist.md・docs/handover.md・2つの「告知動画」の節・docs/decisions/）の今の内容を読んでから1行ずつ足し、止まる条件に当たらなければ cloudflare へマージする

止まる条件

* 同じ論点の open issue がある
* 前提の案が、文書やログの実物（動画の仕様・構成・進め方）と食い違っていて、どちらが正か判断が要る（細かな違いは実物に合わせて書き、どこを合わせたかを報告に書く）
* docs/handover.md が容量の警告の線を超える（超える前の案と大きさを書いて止まる）
* 変更が「変更の範囲」の外に及ぶ
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる。ほかのセッションの push と重なって拒否されたときは、取り込み直して差分を確かめ、もう一度だけ行ってよい）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。`## 経過` に、作った資料の場所と見出しの一覧、参照を足した所、前提から実物に合わせて変えた点を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1008-LGR-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1008-LGR-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref: `git log --all --grep="CHAT-1008-LGR-20"` は0件（同じチャットの LGR の続き）
- ブランチ: work/1008-lgr はローカルにもリモートにも無い。`git checkout -b work/1008-lgr origin/cloudflare`（fc6bc248）
- 手順0: 指示欄の末尾の行は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つとも揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1008-lgr
- ログ: https://github.com/retroeater/mj/blob/work/1008-lgr/docs/logs/CHAT-1008-LGR-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1008-lgr
- 確認用URL: なし（docs のみ）
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b8cef8bc）: https://github.com/retroeater/mj-logs/tree/main/guide/b8cef8bc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b8cef8bc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fc6bc248.md
