# CHAT-1011-STL-05

- 着手日時: 2026-10-11
- 対象issue: #475、#510
- ブランチ: work/1011-stl
- 着手時HEAD: a81419df

## 指示

【Claude作成】Claude Code 向け指示：「使われていない登録」の運用の決定と規則を文書・issue に書き残す（申送り。文書と issue のコメントのみ、コード・シートは変えない） Chat-Ref: CHAT-1011-STL-05 マージ: 承認済み（チャットで）（変更は docs/ と docs/decisions/ と issue だけ） 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1011-stl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-stl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-stl の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1009-STL-02・CHAT-1010-STL-04 のログの `## 報告` を読む。

目的
「使われていない登録」（STL-02 で本番入り、10/10〜11 の毎朝の実行で動作を確認済み）の運用で平野さんが決めたことと、名前の検査から導かれる規則を、文書と常設 issue に書き残す。チャットにしか無い決定を、ほかのチャットが読める所へ移す。
決定（2026-10-11、平野さん）

* 「使われていない登録」に出た行は、他団体の現役プロも含めてすべて消してよい（連盟以外のプロは2000人以上いて、念のため残す意味が薄いため）。消した人がまた放送に出たら「未登録の名前」に出るので、その時に足し直す
* 10/11 に初回の一覧の全件（「連盟プロ以外」26行・「別名」`訂正` 2行、ほかに 10/10 に増えた村越一郎 1行）を平野さんが手で消した。手動実行（apply なし・unused_report あり）で「連盟プロ以外」0行・「別名」0行を確認済み

前提（チャット側。平野さんの決定ではない）

* #475 の本文の案内の文（STL-03 で足した段落）は「他団体の現役プロなど、今後また出る人も入ります」と、残すことを示唆する書き方になっている。bot のコメントの文面（`scripts/lib/unused_names.py` の `section()`）も同じ文（要確認）。決定に合わせて「他団体の現役プロでも消してよい」と読める文に直したいが、コードの文面はこの指示では変えず、変える案だけを報告に書く
* 登録名が変わった人の扱い（STL-04・UNR-13 の齋藤敬輔 → 齋藤けーすけの例）: 旧名の行が「連盟プロ以外」に残っているとき、新名の行を別に足すと同じ人が2人になる。名前の検査（docs/notes/live-page-design.md「1-5」の 3・4）から、正しい手順は「旧名の行の名前を新名に書き換え、『別名』に `旧名 → 新名`（`登録名変更`）を足す」の2つを同時に行うこと、と読んでいる（要確認。検査3: 変換後が「プロ」か「連盟プロ以外」にある、検査4: 変換前が「連盟プロ以外」に無い）
* #510（手動で非表示にした行を gviz が返すか）は未解決。返さない場合、非表示の行でだけ使われている名前は「使われていない」に出る。この依存は #510 に書かれていない見込み（要確認）
* docs/notes/chat-side-operations.md は警告域の直前（CHAT-1005-UNR-13 で 66 バイト）なので、この指示では触らない

手順

1. 決定の記録: docs/decisions/live.md に上の決定を足す（日付・Chat-Ref 付き。既存の形式に合わせる）。docs/notes/live-page-design.md「1-5」の「使われていない登録」の項の今の内容を読み、「出た行はすべて消してよい（他団体の現役プロを含む。2026-10-11 の決定）」と、登録名が変わった人の手順（上の前提を実物の検査の定義で確かめてから、規則の1〜2行だけ）を足す。追記先の残り容量（ファイルのサイズ）を測って報告に書く。同じ趣旨の記述があれば置き換え・拡張してよく、矛盾していてどちらが正か判断が要るときだけ止まる
2. #475 の本文: 案内の段落の「消してよい候補で、他団体の現役プロなど今後また出る人も入ります。」を、「消してよい候補です。他団体の現役プロも含めてすべて消してよく、また放送に出たときは「未登録の名前」に出るので、その時に足し直します。」に直す。直した後の該当箇所をログに引用する。bot のコメントの文面（`unused_names.py`）を同じ趣旨に直す案（差分の形）を報告の「判断が必要なこと」に書く（この指示では変えない）
3. #510 へのコメント: #510 の本文とコメントを読み、「使われていない登録」の検査（`scripts/check_unused_names.py`）も、非表示の行が読めない場合は「使われていない」を多く出す側に働くことを、1件のコメントで書く（読む利用先の表は live-page-design.md「1-5」を指すだけにする）。すでに同じ趣旨が書かれていれば書かない

止まる条件

* 追記先と矛盾していて、どちらが正か判断が要る
* 登録名が変わった人の手順が、実物の名前の検査の定義（`scripts/lib/names.py`）と合わない（案を直さず、食い違いを報告に書いて止まる）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（docs のみなので Workers Builds は走らない見込み）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-STL-05.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-STL-05 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1011-stl
- ログ: https://github.com/retroeater/mj/blob/work/1011-stl/docs/logs/CHAT-1011-STL-05.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-stl
- 確認用URL: なし
- マージ: 未
- issue: #475、#510
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a1a50feb）: https://github.com/retroeater/mj-logs/tree/main/guide/a1a50feb

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1a50feb/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
