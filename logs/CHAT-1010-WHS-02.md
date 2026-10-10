# CHAT-1010-WHS-02

- 着手日時: 2026-10-10
- 対象issue: #194、#340
- ブランチ: work/1010-whs
- 着手時HEAD: 647a8db8

## 指示

【Claude作成】Claude Code 向け指示：「帰り道」の新しい回の自動取り込み（#194）と一覧 OGP の自動生成（#340）の実装の前に、実物で3点を確かめてログに書く（何も直さない）。grill の決定を docs/decisions に記録する Chat-Ref: CHAT-1010-WHS-02 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい〈調査だけの指示〉 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1010-whs を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1010-whs origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#194・#340 の実装（次の指示）の前に、決定どおりに作れるかを実物で確かめる。あわせて、チャットでの grill の決定を docs/decisions と #194・#340 に残す。
決定（2026-10-10、平野さん。チャットでの grill）
取り込み（#194）

1. YouTube からの情報の取得（`fetch_youtube_meta.py`）は、週次の再生成（`regenerate-page.yml` の月曜 05:37 JST）に組み込む。急ぐときは手動で実行する
2. `data/youtube_meta.json` に閲覧数（viewCount）と取得日時（`fetched_at_utc`）を保存しない（#192 の「閲覧数は保存のみ残す」を取り消す）。JSON は中身が変わったときだけコミットする
3. シートにあって JSON に無い回は、その回だけ外してほかを生成し、ワークフローは失敗の扱いにする（#192 の「生成を止める」を置き換える）
4. 前に公開していた回が YouTube で見られなくなった（非公開・削除で情報が取れない）ときは、知らせるだけにする。ページを消すかは、平野さんがシートの行を消して決める

新しい回の知らせ 5. /live が取り込む連盟チャンネルの動画のうち、題名に「帰り道」を含み、帰り道シートに無いものを新しい回とみなす。表記の揺れなど、それで拾えない例外は手で直す 6. 知らせ先は、失敗の扱い（GitHub の失敗通知メール）と、帰り道用の常設 issue（新しく作る）へのコメントの両方。詳しい中身は常設 issue に書く 7. 知らせには、帰り道シートにそのまま貼れる1行を入れる。シートへの追記は自動では行わず、平野さんが貼る（#194 の CHAT-0913-SP-01 の「自動追記は行わない」のまま） 8. その1行には、決勝動画 URL（H列）の候補を入れる。探し方は title/ の期ページの「決勝動画」と同じ照合（大会・期、/live の【2】自動変換＋【3】手動補正）。見つからない・複数あるときは空にして、そう書く 9. 帰り道シートで H列（決勝動画URL）が空の回があれば知らせる
OGP（#340） 10. 一覧の OGP 画像は、週次のジョブで取り込みと一緒に作り直す。画像とページは同じコミットに入れる。同じ公開日の回が2本あるときは、名前に連番を付けて（例 `index-20261010-2.jpg`）作り直す（今の「同じ名前で中身が変わるとエラーで止まる」を置き換える）
範囲 11. #533（1ページの失敗で全体の再生成が止まる）は別に進める 12. 指示は2本に分ける。この指示（確かめ）の後に、実装とマージの指示を出す
前提（チャット側。平野さんの決定ではない）

* チャット側が 2026-10-10 に読んだ事実（要確認）: `data/youtube_meta.json` は 2026-09-21 取得・39話。一覧の OGP 画像は `img/ogp/wayhome/index-20260921.jpg`。`regenerate-page.yml` は `fetch_youtube_channels.py`（#3）を動かすが、`fetch_youtube_meta.py` は動かさない。`fetch_youtube_meta.py` はシートの E列（視聴URL）から動画 ID を集めて `videos.list` を呼ぶ
* /live の取り込み（`scripts/fetch_live_channel_raw.py`・`update-live-channel.yml`、仕組みは `docs/notes/live-channel-write.md`）は、チャンネルのアップロードの再生リスト（`UU`・`UUMO`）を歩いて全動画を集めている（要確認）。帰り道の新しい回の検知は、この取り込みの結果（【1】か【2】）を読むのがよいとチャット側は考えている。どの層を読むのがよいかは実物を見て提案してよい
* title/ の期ページの「決勝動画」は、`scripts/generate_title_pages.py` が /live の【2】＋【3】から大会・期で照合して出している（要確認。`docs/notes/title-pages.md`「期ページの放送」）
* 常設 issue（ラベル「種類: 常設」）は、#426（道場部ゲストの取り込み）・#475（/live の未登録の名前）などがある。帰り道用は無い（要確認）
* 2026-10-10 の時点で、`regenerate-page.yml` は「辞書」シートの見出しの変化で `resource_dictionary` が止まり、失敗している（辞書のチャットで対応中、CHAT-1010-WHS-01 の報告）。この指示は読むだけなので関係しないが、`regenerate.py all` を流すと同じところで止まる。流す必要があれば帰り道の2ページ（`video_wayhome`・`wayhome_episodes`）だけにする

手順

1. 新しい回の拾い方を確かめる: /live の取り込みの結果のうち、題名に「帰り道」を含む動画を数え、帰り道シートの E列の39話と突き合わせる。表にする（両方にある／/live 側にだけある〈動画 ID・題名・公開日〉／シート側にだけある〈動画 ID・題名〉）。/live 側にだけあるものが、帰り道の回か、関係の無い動画（予告・切り抜きなど）かを題名で見分けて書く。どの層（【1】・【2】）を、どのタイミング（毎朝の取り込みの後か、週次の再生成の中か）で読むのがよいかを提案する
2. 決勝動画の照合を確かめる: 帰り道シートの39行の大会名・期（シートのどの列か、書き方）から、title/ の大会・期に結び付けられるかを1行ずつ試し、表にする（結び付いた／結び付かない〈理由〉）。結び付いた行は、title/ と同じ照合で出る決勝動画と、今の H列の値を比べる（一致／違う／照合で見つからない／複数ある）。title/ の照合の関数を使い回せるか（借りる場合に変える所）も書く
3. 記録する（コードは変えない）: 常設 issue の形（#426 の本文とコメントの書き方を読み、帰り道用にどう書くか）を案としてログに書く。`regenerate-page.yml` のどこに取得・検知・OGP の生成を入れるか、要る Secret（`YOUTUBE_API_KEY` 以外にシートの読み書きの権限が要るか）、#192 の決定（閲覧数の保存・生成を止める）を書いた文書・テストの場所を洗い出して、案としてログに書く。上の「決定」を `docs/decisions/` の合う分野に足す（README のとおり）。#194 と #340 に、決定の要点と、このログを SHA を固定した permalink で示すコメントを書く

止まる条件

* #194・#340 に他セッションの着手中コメントがある
* 上の「前提」の事実と実物が大きく食い違い、決定どおりに作れない（例: /live の取り込みに帰り道の動画が含まれない、title/ の決勝動画が /live を使っていない）。食い違いを書いて止まる（調べ終えた所までログに書く）
* ログと docs/decisions 以外（コード・ワークフロー・シート・生成物）を変える必要が出た（変えずに止まる）
* 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（実装の判断が要る点は「判断が必要なこと」に書く）
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1010-WHS-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1010-WHS-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1010-WHS-02"` は0件。セッションの2つ目の指示なので識別子の確認は不要
- 作業ブランチ: ローカルの `work/1010-whs`（cc4915c9）は `origin/cloudflare` の祖先 → `git merge --ff-only origin/cloudflare`（647a8db8）。リモートもマージ済み
- 指示欄の末尾は指示文の最後の行と一致。雛形の4行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

## 報告

- 状態: 作業中
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/work/1010-whs/docs/logs/CHAT-1010-WHS-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 未
- issue: #194、#340
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 647a8db8）: https://github.com/retroeater/mj-logs/tree/main/guide/647a8db8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/647a8db8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
