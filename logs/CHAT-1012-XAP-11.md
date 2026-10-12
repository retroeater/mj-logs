# CHAT-1012-XAP-11

- 着手日時: 2026-10-12
- 対象issue: #514
- ブランチ: work/1012-xap
- 着手時HEAD: 5b280b21

## 指示

【Claude作成】Claude Code 向け指示：10/12 04:10 の SNS ブックの毎日の更新（update-sns-book.yml）の失敗の原因を調べる（#514） Chat-Ref: CHAT-1012-XAP-11 マージ: ドキュメントのみ（docs/logs/・docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい。調査だけで、コード・ワークフロー・シートは変えない。直し方の判断が残れば状態は判断待ち 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1012-xap を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1012-xap origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1012-xap の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 XAP は同じチャットの CHAT-1010-XAP-02〜07・CHAT-1011-XAP-09・10 で使ったもので、重複ではない。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SNS ブックの毎日の更新が 2026-10-12 04:10 JST の回で失敗した。原因を調べ、直し方の案を出す（直すのは次の指示）。
決定（平野さん）

* なし（調査の指示）

前提（チャット側。平野さんの決定ではない）

* mj-logs の actions/status.md（2026-10-12 11:07 JST に書き出し）で読んだこと: update-sns-book.yml の run #13（38166926033、workflow_dispatch〈Worker から〉、cloudflare、04:10 開始、5分10秒）が failure。前日の #12（38078775949、04:10、50秒）は success。同じ朝の check-image-links.yml #30（04:30、Worker から）と #31（06:37、schedule）は success
* update-sns-book.yml は CHAT-1011-XAP-09 で、【3】のページが使う値が変わった日にジョブ `regenerate` から regenerate-page.yml（all）を `workflow_call` で呼ぶようになった。5分10秒かかっていることから、失敗したのはジョブ `update` ではなく `regenerate` の側の見込み（要確認）
* 2026-10-11 に平野さんが SNS ブック【2】を直した: 吉田彩乃の X を空に（備考に元の ID）、村田昴の X を「-」に。ほかのチャット（RDN・帰り道〈WHS〉など）も 10/11〜12 に cloudflare を変えている。原因がほかのチャットの変更なら、そのチャットの名前と Chat-Ref を書く
* 別のチャットからの共有（2026-10-12）: 「プロ」の N 列（見出し「YouTube画像」）は廃止し、消す作業は「SNS 移行の完了」でまとめて行う（平野さんの決定）。`update_sns_book.py` などが「プロ」の「YouTube画像」を読んでいるかは未確認。今回の調査のついでに、`update_sns_book.py`・`lib/pro_sheet.py`・`lib/sns.py` が「プロ」のどの見出しを読むかを表にする（SNS 移行の完了の前提にする）
* 使う skill は無い

手順

1. run 38166926033 のジョブごとの結論とステップごとの結論を取り、失敗したステップのログの要点（エラーの行と、その前後の数行。トークンや鍵は書かない）をログに書く。ジョブ `update` が success なら、その出力（X API を呼んだ件数・【3】の状態ごとの件数・`pages_changed`）も書く
2. 原因を突き止める: 失敗が regenerate-page.yml の中なら、その日のほかの regenerate-page.yml の実行（push・手動など）の結果と比べ、同じ原因かを書く。ほかのチャットの変更が原因なら、そのコミットと Chat-Ref を書く。今も同じ失敗が起きるか（再実行で直るか）は、ワークフローを動かさずにコードとシートの状態から判断する（どうしても要るときだけ、update-sns-book.yml を dry-run で1回だけ手動実行してよい。X API を呼ばない・シートに書かない形）
3. `update_sns_book.py`・`lib/pro_sheet.py`・`lib/sns.py` が「プロ」「連盟プロ以外」のどの見出しを読むか（特に「YouTube画像」）を表にする。直し方の案を、どのファイルのどこを直すか・誰の作業か（XAP か、ほかのチャットか）とあわせて書く。docs/decisions/saikyo.md に、調べたことの要旨を1行で足す（決定ではない旨）。#514 に要旨をコメントする

止まる条件

* docs/logs/・docs/decisions/ 以外のファイル、シート、ワークフローを変える必要が出た（変えずに書く）
* 手動実行の完了を待つのは15分まで（超えたらその時点の状態を書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1〜3の結果（失敗したジョブ・ステップ・エラーの要点、原因、ほかのチャットとの関係、読む見出しの表、直し方の案）がログにある
* 直し方で平野さんかほかのチャットが決めることは、報告の「判断が必要なこと」に書く（状態は CLAUDE.md「作業ログ」節のとおり）
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1012-XAP-11.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1012-XAP-11 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- `git log --all --grep="CHAT-1012-XAP-11"`: 0件。`origin/work/1012-xap`・ローカルの `work/1012-xap`: 無し → `git checkout -b work/1012-xap origin/cloudflare`（5b280b21）
- 0. 指示欄の末尾の行は「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」で、指示文の最後の行と一致
- 冒頭の雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- #514 に着手コメント（https://github.com/retroeater/mj/issues/514#issuecomment-6116542780 ）

### 1. run 38166926033 のジョブ・ステップ

head は `4e18a785`（cloudflare）。

| ジョブ | 結論 | 時刻（UTC） |
|---|---|---|
| `update` | success | 19:11:02〜19:11:38 |
| `regenerate / regenerate`（regenerate-page.yml を `workflow_call`） | failure | 19:11:39〜19:16:07 |

`regenerate` のステップ: チェックアウト・Python・YouTubeチャンネル情報を取得・帰り道のYouTube情報を取得・帰り道の一覧のOGP画像を作る・対象ページを再生成（19:12:02〜19:16:00、約4分）・サイトマップのlastmod・well-formedness・title/ の転送の行・変更をコミット・push はすべて success（楽天は skipped）。
**失敗したのは「帰り道の前回の知らせを読む」（actions/github-script）だけ**。後の帰り道の3手順と「飛ばしたページを報告」は skipped。

失敗したステップのログの要点:

```
変更なし                       ← 直前の「変更をコミット・push」の出力
##[group]Run actions/github-script@v7
...
RequestError [HttpError]: Resource not accessible by integration
##[error]Unhandled error: HttpError: Resource not accessible by integration
  status: 403,
    url: 'https://api.github.com/repos/retroeater/mj/issues?state=open&per_page=100',
      'x-accepted-github-permissions': 'issues=read',
```

ジョブ `update` の出力（「実行」）:

```
mode: update / dry_run: false
- 【1】元データ を書き直します(プロ 1099・連盟プロ以外 739)
- 【2】ID: 1864 行(一覧に無い 26)、書き直す
- X API を呼んだ件数: 0(上限 30)
- X状態: 解決 1052、未入力 799、既定のアイコン 12、アカウントなし(確認済み) 1
- note状態: 未入力 1660、確認済み(取得しない) 204
- YouTube状態: 未入力 1782、解決 82
- 【3】画像取得 を書き直します
- ページが使う値(ID・画像URL)が変わった人: 2
```

`pages_changed` は true（`regenerate` が動いたことから。ログには `Set output 'pages_changed'` の行だけで値は出ない）。

### 2. 原因

- update-sns-book.yml のジョブ `regenerate` は `permissions: contents: write` だけを持つ。`workflow_call` で呼ばれたワークフローの GITHUB_TOKEN はこの権限になる（issues は none）
- 2026-10-11 に帰り道のチャット（WHS）が regenerate-page.yml に帰り道の取り込みの手順を足した（c460f644、Chat-Ref: CHAT-1011-WHS-13）。「帰り道の前回の知らせを読む」は issue の一覧を読む（issues: read が要る）。条件は `github.event_name != 'push' && target_page が all` で、`workflow_call` では `github.event_name` が呼んだ側（workflow_dispatch）になるため、SNS ブックからの呼び出しでも動いた
- → 403 で失敗。**原因はほかのチャット（帰り道〈WHS〉）の変更 c460f644（CHAT-1011-WHS-13）と、XAP-09 の `workflow_call` 呼び出しの組み合わせ**。SNS ブックのスクリプト・シートの値（10/11 の吉田彩乃・村田昴の直し）は関係しない
- ページの生成・push は失敗の前に終わっている。「変更なし」だったのは、ページが ID を【2】から読むため、10/11 の【2】の直しが 10/11 02:26 UTC の再生成（ce02c249）で既に入っていたため（ce02c249 で吉田彩乃の X のリンクが消えている）。今回の【3】の変化ではページに差分が出なかった。サイトへの実害はない

その日のほかの regenerate-page.yml の実行との比較:

| run | 起動 | head | 結論 | 失敗の手順 | 同じ原因か |
|---|---|---|---|---|---|
| #276（38186096774） | schedule、10/11 23:55 UTC | 4e18a785 | failure | 「帰り道の取り込みの失敗を報告」（新しい回2件で `exit 1`、#545 にコメント） | 違う（直接の実行なので権限はある。WHS-13 の「新しい回で失敗にする」仕様による） |
| #277（38193457871） | workflow_dispatch、work/1012-whs、01:50 UTC | c3362de8 | success | — | — |
| #278・#279 | push（cloudflare） | 32ed04cb・bba86b6d | success | — | — |

**既に直っている**: 帰り道のチャットが同じ失敗を診断し、c3362de8（2026-10-12 01:50 UTC、Chat-Ref: CHAT-1012-WHS-15）で「帰り道のYouTube情報を取得」「帰り道の一覧のOGP画像を作る」の条件に `contains(github.workflow_ref, '/regenerate-page.yml@')` を足した（`workflow_call` では `workflow_ref` が呼んだ側のファイルになる）。「前回の知らせを読む」以降は `steps.fetch_wayhome.outcome != 'skipped'` などで続けて skipped になる。c3362de8 は origin/cloudflare に入っている（`git merge-base --is-ancestor` が真）。WHS-15 のログは「呼び出し側に issues: write を足す案は採らない」（平野さんの決定）と書いている。

今も同じ失敗が起きるか（コードからの判断。ワークフローは動かしていない）: 起きない。今の regenerate-page.yml で issue の API を呼ぶのは帰り道の3手順（読む・調べる・書く）だけで、どれも `fetch_wayhome` が skipped なら動かない。「YouTubeチャンネル情報の取得失敗を報告」「飛ばしたページを報告」などは `echo`・`exit 1` だけで API を呼ばない。`fetch_channels`（YouTube API キー、`secrets: inherit` で渡る）は呼ばれた側でも動くが issues の権限は要らない。実物での確認は、次に【3】の値が変わって再生成を呼ぶ 04:10 の回（WHS-15 の「未確認の項目」と同じ）。dry-run の手動実行は行っていない（dry-run では書かないので `pages_changed` が立たず、再生成を呼ばないため確かめにならない）。

### 3. 「プロ」「連盟プロ以外」から読む見出し

| ファイル | 読むシート | 見出し | 備考 |
|---|---|---|---|
| `scripts/update_sns_book.py` `load_sources()` | プロ（`pro_sheet.fetch_pros`） | 登録名・ソートキー・XID・X画像・noteID・note画像・YouTubeID（＋在籍の判定の「表示」） | **check・init・update のすべてで呼ぶ**（update は 394 行で `_values` として捨てるが、`column_indexes` が見出しの存在を求めるので、列が無いと ValueError） |
| 同 | 連盟プロ以外（`fetch_records`） | 名前・名前かな・X ID・X画像URL | 同上 |
| `scripts/lib/pro_sheet.py` | プロ | 呼ぶ側が渡した見出しだけ＋表示・ソートキー | 「YouTube画像」は定数 `YOUTUBE_IMAGE` を持つだけ |
| `scripts/lib/sns.py` `fetch_pros()` | プロ | 登録名と、渡された見出しのうち SNS の6つ（XID・X画像・noteID・note画像・YouTubeID・YouTube画像）**以外** | SNS の6つは「プロ」から読まず SNS ブック（【2】【3】【4】）から入れる |
| 同 `others()` | 連盟プロ以外（呼ぶ側が読んだ行） | 名前・所属団体・所属補足 | X ID・X画像URL は SNS ブックから |

**「プロ」の「YouTube画像」（N列）は、3ファイルとも読まない**（`sns.fetch_pros` は渡されても SNS ブックから入れる。`YOUTUBE_IMAGE` を渡す呼び出しも scripts/ に無い。tests/test_jpml_pros.py が jpml_pros が読まないことを確かめている）。消しても動きは変わらない。
一方、**「SNS 移行の完了」で XID・X画像・noteID・note画像・YouTubeID の列を消すと、`update_sns_book.py` が check・update を含めて止まる**（`load_sources()` が見出しを求めるため）。連盟プロ以外の X ID・X画像URL も同じ。

### 直し方の案

- 今回の失敗: **直しは不要（済み）**。c3362de8（帰り道〈WHS〉、CHAT-1012-WHS-15）で直っている。XAP 側で update-sns-book.yml に `issues` の権限を足すのは、WHS-15 の決定（足さない）と重なるので行わない
- 「SNS 移行の完了」の前提（XAP の作業）: 「プロ」の SNS の5列・「連盟プロ以外」の X の2列を消す前に、`scripts/update_sns_book.py` の `load_sources()` で、update（【1】の名前・かなの写しだけが要る）が SNS の列を読まないようにする（`fetch_pros` に渡す見出しを登録名・ソートキーだけにし、SNS の値は init のときだけ読む、など）。「YouTube画像」だけなら今のままで消せる

### docs/decisions・#514

- docs/decisions/saikyo.md に要旨を1行足した（決定ではない旨を付けた）
- #514 に要旨をコメントした

## 報告

- 状態: 完了
- ブランチ: work/1012-xap
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1012-XAP-11.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1012-xap
- 確認用URL: なし
- マージ: 済（fast-forward、7c73600b）
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 915bafa2）: https://github.com/retroeater/mj-logs/tree/main/guide/915bafa2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/915bafa2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/5b280b21.md
