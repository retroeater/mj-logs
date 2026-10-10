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
- #194・#340: どちらも Open。他セッションの着手中コメントは無い（#340 の 2026-09-16 の XC-06 の着手中は同じ日に結果のコメントで終わっている）。両方に着手中のコメントを残した

### 前提の確認

| 前提 | 実物 | 判定 |
|---|---|---|
| `data/youtube_meta.json` は 2026-09-21 取得・39話 | `fetched_at_utc` 2026-09-21T15:04Z、`video_count_fetched` 39、`missing_video_ids` 空 | 一致 |
| 一覧の OGP は `img/ogp/wayhome/index-20260921.jpg` | このファイル1枚だけ | 一致 |
| `regenerate-page.yml` は `fetch_youtube_channels.py` を動かし `fetch_youtube_meta.py` は動かさない | 「YouTubeチャンネル情報を取得」（週次・手動の all だけ、`continue-on-error`）だけ | 一致 |
| `fetch_youtube_meta.py` はシート E列から動画 ID を集めて `videos.list` | `wayhome.QUERY`（`SELECT A,D,E,H WHERE G = "Y"`）の E列 → 50件ずつ `videos.list` | 一致 |
| /live の取り込みは `UU`・`UUMO` を歩いて全動画を集める | `fetch_live_channel_raw.py` の `PLAYLIST_PREFIXES = ("UU", "UUMO")`、`walk_playlist()` | 一致 |
| title/ の決勝の動画は /live の【2】＋【3】から大会・期で照合 | `generate_title_pages.load_broadcasts()`（`live_layer3.fetch_matches()`、鍵は `broadcast_key()` の (タイトル戦, 期)） | 一致（ただし「決勝動画」の節の中身は下の手順2の注意） |
| 常設 issue は #426・#475 など、帰り道用は無い | ラベル「種類: 常設」は #506・#481・#475・#426・#357・#352（すべて Open）。帰り道用は無い | 一致 |
| `regenerate-page.yml` は辞書で失敗している | WHS-01 で見たとおり（81a53d18）。この指示では `regenerate.py` を流していない | — |

### 手順1: 新しい回の拾い方

読んだもの: 層1 `data/live_channel_raw.jsonl`（リポジトリにある。動画ごとに最後の行を採る。14,175本、最新の公開日時 2026-10-09T15:00Z）と、帰り道シートの全列（`SELECT *`、39行、全行 G=Y）。

- 題名に「帰り道」を含む動画: **36本**。シート E列の39話と突き合わせると、両方にある 35本／層1にだけある 1本／シートにだけある 4本
- 両方にある35本の題名は、すべて `《<大会・期> <名前>編》帰り道ついていってイイっすか` の形（`vol.N` 付きを含む）

層1にだけある（帰り道シートに無い）:

| 動画ID | 題名 | 公開日 | 見分け |
|---|---|---|---|
| `6WAPjcxT78A` | 《第３期JPMLWRCR勝又健志編》帰り道ついていってイイっすか | 2024-09-12 | 題名の形はほかの回と同じで、帰り道の回に見える（予告・切り抜きの印は無い。公開、限定 N、取得可）。シートに行が無い理由は分からない（行を消したか、足し忘れ） |

シートにだけある（層1の題名に「帰り道」が無い）:

| 動画ID | シート（名前・D列） | 層1の題名 |
|---|---|---|
| `9drvti0iySM` | 福島佑一 第49期王位戦 #2 | 《第49期王位 福島佑一 編》王位についていってイイっすか |
| `WDRBtJJME7I` | 三浦智博 第40期十段戦 | 十段位ついていってイイっすか《三浦智博特別編》 |
| `kf3RaFtz8o8` | 白鳥翔 第15期麻雀グランプリMAX #2 | 《第15期麻雀グランプリMAX 白鳥翔編vol.4》実家についていってイイっすか |
| `o28svvuVI0M` | タマシュ・エルドス ワールド・リーチ・プロ | 《初のワールド・リーチ・プロ・タマシュ・エルドス編》 |

- 4本とも層1にはある（取得可）。題名が「帰り道」を含まない特別編で、決定5の「拾えない例外は手で直す」に当たる。「ついていってイイっすか」で拾えば3本は拾えるが、`o28svvuVI0M` はどちらも含まない
- 予告・切り抜きは、題名に「帰り道」を含む動画の中には無かった
- **このまま決定5で実装すると、初回の実行で `6WAPjcxT78A` が「新しい回」として知らせに出る**（判断が必要なこと）

層の提案:

- **読むのは層1（リポジトリの `data/live_channel_raw.jsonl`）がよい。** 【1】は層1をそのまま書き写したタブで中身は同じ。【2】は帰り道の動画を扱わない
  （上の36本はすべて【2】で 放送対局候補 が空・タイトル戦が空）。jsonl はチェックアウトしたリポジトリにあるため、シートの読み取りも鍵も要らない
- 動画ごとに最後の行を採り、`状態` が取得不可の行は除く。`限定`=Y（メンバー限定）は今のところ0本で、扱いは決めておく（案: 拾う）
- タイミング: **週次の再生成の中**（決定1の取得と同じ実行）。層1は毎朝 04:00 JST の取り込みが `cloudflare` にコミットするので、月曜 05:37 JST の実行は当日朝までの動画を読める。
  知らせを早めたいなら毎朝の取り込みの後にも検知だけを足せるが、決定1の形（週次＋急ぐときは手動）で足りると見る
- 新しい回の1行の D列（タイトル）と大会・期は、層1の題名の `《…》` から作るしかない（【2】が解析していないため）。
  今の35本＋特別編で試すと、`《》` の中から名前と「編」「vol.N」を外した残りが今の D列と一致したのは **39本中24本**。
  残りは大会名の書き方の違い（`十段位`→`十段戦`、`鳳凰位`→`鳳凰戦`、`王位`→`王位戦`、`新人王`→`新人王戦`、`若獅子`→`若獅子戦`、`JPMLWRC`→`JPML WRCリーグ`、`世界麻雀2025`→`世界麻雀TOKYO2025`）と特別編。
  変換の表を持つか、title/ の「タイトル戦」タブの大会名に寄せるかは実装で決める（判断が必要なこと）

### 手順2: 決勝動画の照合

- 帰り道シートに大会名・期の別々の列は無い。**D列「タイトル」が `第16期麻雀グランプリMAX` のように期と大会名を続けて書き**、同じ期の2本目以降は ` #2` を付ける
- 照合: D列から ` #N` を外した文字列を、title/ の期の題（`Period.title`、「タイトル」タブの「タイトル」列。例 `第16期麻雀グランプリMAX`）と完全一致で引き、
  title/ と同じ `load_broadcasts()`（【2】＋【3】、決勝段階・`is_listed()`・限定の重なりを落とす）の結果を見た。読むだけのスクリプトで、title/ の関数をそのまま呼んだ
- **注意: H列（決勝動画URL）に入っているのは、title/ の期ページの節「決勝動画」（動画の単位が回戦・卓）ではなく、節「決勝ライブ」（日・ステージ）の動画だった。**
  比較は「決勝ライブ」とした。「決勝動画」の節は、ある期では 4〜8本あり、H列の値を含まない

結果（決勝ライブと H列）:

| 名前 | D列 | 結び付き | 照合で出る決勝ライブ | H列 | 比較 |
|---|---|---|---|---|---|
| 紺野真太郎 | 第16期麻雀グランプリMAX | 結び付いた | `Rda5SV25t7o`, `3N2s-WC3nrQ` | `3N2s-WC3nrQ` | 複数（2本。H列は最後＝最終日と一致） |
| 武田雛歩 | 第11期桜蕾戦 | 結び付いた | `7C61aX9jOKU` | `7C61aX9jOKU` | 一致 |
| 山田祐輝 | 第11期若獅子戦 | 結び付いた | `S9oz5rBcXMw` | `S9oz5rBcXMw` | 一致 |
| 橘来奈 | 第18期JPML WRCリーグ | 結び付いた | `d_IhTMD0aOY` | `d_IhTMD0aOY` | 一致 |
| 白鳥翔 | 第15期麻雀グランプリMAX #2 | 結び付いた | `7uLobGgAUKA`, `8eLDX6j60Vk` | `8eLDX6j60Vk` | 複数（2本。H列は最後＝最終日と一致） |
| 石川正明 | 第50期王位戦 | 結び付いた | `iSy2KAbv6sM` | `iSy2KAbv6sM` | 一致 |
| 三浦智博 | 第4期帝王戦 | 結び付かない | — | `JnlEecy4uRg` | 結び付かない（title/ に同じ題の期が無い） |
| 浜野太陽 | 第42期十段戦 | 結び付いた | `VaPLytOwS3w`, `j_lD5bIdTJ4`, `obpel11rd1c` | `obpel11rd1c` | 複数（3本。H列は最後＝最終日と一致） |
| 白鳥翔 | 第15期麻雀グランプリMAX #1 | 結び付いた | `7uLobGgAUKA`, `8eLDX6j60Vk` | `8eLDX6j60Vk` | 複数（2本。H列は最後＝最終日と一致） |
| 清水香織 | 第20期女流桜花 | 結び付いた | `jCqjAsPUbaA`, `9ZocGGZ8ADM`, `xJWgkZo66Qw` | `xJWgkZo66Qw` | 複数（3本。H列は最後＝最終日と一致） |
| 渡辺史哉 | 第10期若獅子戦 | 結び付いた | `pfGReFbsEF0` | `pfGReFbsEF0` | 一致 |
| 猿渡輝也 | 第6期JPML WRC-Rリーグ | 結び付いた | `R_-PypkSHXY` | `R_-PypkSHXY` | 一致 |
| 朝比奈ゆり | 第17期JPML WRCリーグ | 結び付いた | `HtqrYcX8BAU` | `HtqrYcX8BAU` | 一致 |
| 三浦智博 | 麻雀日本シリーズ2025 | 結び付いた | `aHDtB07qMR0` | `aHDtB07qMR0` | 一致 |
| 内川幸太郎 | 世界麻雀TOKYO2025 | 結び付かない | — | `9sP_ePg3xgg` | 結び付かない（title/ に同じ題の期が無い） |
| 和田直樹 | 第38期新人王戦 | 結び付いた | `tAHj-naBotY` | `tAHj-naBotY` | 一致 |
| 白鳥翔 | 第41期鳳凰戦 #2 | 結び付いた | `PjYuInY0Pa4`, `-KvhQ7qLxuI`, `1ezLbYMFpbM`, `gJ9iB5lbZnc` | `gJ9iB5lbZnc` | 複数（4本。H列は最後＝最終日と一致） |
| 覚野陽生 | 第5期JPML WRC-Rリーグ | 結び付いた | `_a21xRHoUZY` | `_a21xRHoUZY` | 一致 |
| 高宮まり | 女流プロ麻雀日本シリーズ2025 | 結び付いた | `z_5hFV9IHDA` | `z_5hFV9IHDA` | 一致 |
| HIRO柴田 | 第5期鸞和戦 | 結び付いた | `yNq8GNQZdRI` | `yNq8GNQZdRI` | 一致 |
| 吉田幸雄 | 第33期麻雀マスターズ | 結び付いた | `TrjuXyJoP_U` | `TrjuXyJoP_U` | 一致 |
| タマシュ・エルドス | ワールド・リーチ・プロ | 結び付かない | — | 空 | 結び付かない（title/ に同じ題の期が無い） |
| 白鳥翔 | 第41期鳳凰戦 #1 | 結び付いた | `PjYuInY0Pa4`, `-KvhQ7qLxuI`, `1ezLbYMFpbM`, `gJ9iB5lbZnc` | `gJ9iB5lbZnc` | 複数（4本。H列は最後＝最終日と一致） |
| 福島佑一 | 第49期王位戦 #2 | 結び付いた | `yIZdG8lPNIY` | `yIZdG8lPNIY` | 一致 |
| 朝比奈ゆり | 第9期桜蕾戦 | 結び付いた | `XJOSNfKHIBY` | `XJOSNfKHIBY` | 一致 |
| 柿本幸宏 | 第9期若獅子戦 | 結び付いた | `TihH2tMhhW4` | `TihH2tMhhW4` | 一致 |
| 朝比奈ゆり | 第16期JPML WRCリーグ | 結び付いた | `1-_AIOdPHus` | `1-_AIOdPHus` | 一致 |
| 小車祥 | 第4期JPML WRC-Rリーグ | 結び付いた | `6MSXNKH_kBk` | `n1u6r6DxYx0` | 違う |
| 清水香織 | 第19期女流桜花 | 結び付いた | `NgdnhoZIvLM`, `rW4Bb-JCBQk`, `j21ZV3qugDE` | `j21ZV3qugDE` | 複数（3本。H列は最後＝最終日と一致） |
| 三浦智博 | 第41期十段戦 | 結び付いた | `ct4qBXQvxeY`, `h98ISVHHaYk`, `amTs0IqIutE` | 空 | H列が空（照合では決勝ライブ3本） |
| 福島佑一 | 第49期王位戦 #1 | 結び付いた | `yIZdG8lPNIY` | `yIZdG8lPNIY` | 一致 |
| 田中祐 | 第8期若獅子戦 | 結び付いた | `3FUEeMNaIWI` | `3FUEeMNaIWI` | 一致 |
| 鴨舞 | 第8期桜蕾戦 | 結び付いた | `NzYiOhTQ-yg` | `NzYiOhTQ-yg` | 一致 |
| 横田幸太朗 | 第37期新人王戦 | 結び付いた | `SUAFFKhFpYs` | `i0aPZ9IOqbc` | 違う |
| 三浦智博 | 第40期十段戦 | 結び付いた | `y5EE5a-Eq7g`, `t-u3XURt8MY`, `x5eNiaZK9r0` | 空 | H列が空（照合では決勝ライブ3本） |
| 古橋崇志 | 第15期JPML WRCリーグ | 結び付いた | `qDXzHp4FHlE` | `qDXzHp4FHlE` | 一致 |
| 紺野真太郎 | 第3期帝王戦 | 結び付かない | — | `bVeavoP15PU` | 結び付かない（title/ に同じ題の期が無い） |
| 本田朋広 | 第32期麻雀マスターズ | 結び付いた | `GUdz4zcSbTs` | `GUdz4zcSbTs` | 一致 |
| 岡崎圭吾 | 第4期鸞和戦 | 結び付いた | `Jq8DwPkKV5w` | `Jq8DwPkKV5w` | 一致 |

まとめ（39行）: 一致 23／複数（2〜4本で、H列は最後＝最終日と一致）8／H列が空 2／違う 2／結び付かない 4

- **複数（8行）**: 決勝が複数日の期。H列はどれも最後の1本（`day_branch_key()` の並びで最終日）と一致した。決定8の「複数あるときは空」に従うと、今の H列の8行に当たる新しい回は空になる（判断が必要なこと）
- **違う（2行）**: 小車祥 第4期JPML WRC-Rリーグ・横田幸太朗 第37期新人王戦。H列は無料の冒頭版（`n1u6r6DxYx0`・`i0aPZ9IOqbc`、【3】の冒頭=Y、掲載が空）で、
  title/ は `is_listed()` で冒頭版を落とし、メンバー限定の全編（`6MSXNKH_kBk`・`SUAFFKhFpYs`）を載せている
- **H列が空（2行）**: 三浦智博 第40期・第41期十段戦。照合では決勝ライブが各3本（複数）。決定9の知らせの対象
- **結び付かない（4行）**: D列の大会名が title/ の大会名と違う
  - `第3期帝王戦`・`第4期帝王戦` → title/ は `第3期小島武夫杯帝王戦`・`第4期小島武夫杯帝王戦`（【3】には決勝の行がある: `bVeavoP15PU`・`JnlEecy4uRg`。どちらも H列と同じ動画）
  - `世界麻雀TOKYO2025` → title/ は `リーチ麻雀世界選手権` の `第4回`（【3】の `9sP_ePg3xgg` が第4回の決勝で、H列と同じ）
  - `ワールド・リーチ・プロ` → title/ に大会が無い（H列も空）。決定9の知らせに毎回出る
  - 大会名の別名（`帝王戦`→`小島武夫杯帝王戦`、`世界麻雀TOKYO2025`→`第4回リーチ麻雀世界選手権`）を持てば、3行は一致になる

title/ の照合の関数を使い回せるか:

- `generate_title_pages.load_broadcasts()` はそのまま呼べる（今回そうした）。要るのは「タイトル」「タイトル戦」「別名」タブの読み込み（`fetch_tab()`・`load_taikai()`・`load_periods()`）と
  `live_layer3.fetch_layer2()`・`fetch_matches(layer2, (OPENING_HEADER,))`。どれも公開シートの読み取り（gviz）で鍵は要らない
- 借りるときに変える所の案:
  - 「決勝ライブ」（`Broadcast.is_live`）だけを候補にする（決定8の文言は「決勝動画」。どちらを使うかは判断が必要なこと）
  - `load_periods()` には写真の解決が要らないので、`final_counts()` と同じく名前の変換だけの `people` を渡す（`final_counts()` の前半を関数に切り出すと重複しない）
  - 期の題の引き方: 帰り道の D列（または `《》` から作った文字列）→ title/ の `Period.title` の完全一致に、大会名の別名の表を足す
  - 生成スクリプト（`generate_title_pages.py`）を import すると、帰り道の検知が title/ のモジュールに依存する。関数を `scripts/lib/` に移すか、import で借りるかは実装で決める

### 手順3: 記録（コードは変えていない）

#### 常設 issue の案（#426 を読んで）

#426 の形: 本文の冒頭に「通知先。閉じずに使い続ける（ワークフローはこの issue をタイトルで探すので、タイトルは変えない）」、
次に何がいつ動き、いつ書くか（変化が無い日は書かない）、「通知を見てすること」の表、仕組みの文書への参照、Chat-Ref。
ワークフローは `actions/github-script` で Open の issue をタイトルで探し、無ければ作る（`sync-dojo-calendar.yml`「結果をissueに知らせる」）。コメントの末尾に区切り線と実行ログへのリンク・関連 issue。

帰り道用の案:

- タイトル: `帰り道の新しい回`（ラベル: 種類: 常設・分野: 自動化・対象: video_wayhome）
- 本文:
  - この issue は週次の再生成（`regenerate-page.yml`、月曜 05:37 JST）の帰り道の取り込みの知らせ先。閉じずに使い続ける。タイトルで探すので変えない
  - 知らせる場面と、すること（表）:
    | 知らせ | すること |
    |---|---|
    | 新しい回（層1の題名に「帰り道」、シートに無い） | 知らせの1行を帰り道シートの末尾に貼る（H列の候補が空なら理由を見て埋める）。貼った後は次の週次か手動の再生成で出る |
    | シートにあって YouTube の情報が無い回（決定3） | その回は外して生成した。シートの URL の誤り・非公開を確かめる |
    | 前に公開していた回が見られない（決定4） | ページを消すなら、シートの行を消す（または G列を外す） |
    | H列が空の回（決定9） | 決勝ライブの URL を H列に書く |
    | 取り込みの失敗 | 実行ログを見る。次の週次か手動実行で追いつく |
  - 仕組みの文書: `docs/notes/video-wayhome.md`
- コメント（変化があった回だけ。中身が前回と同じなら書かない。#475 の隠した注記 `<!-- … -->` で前回の一覧を持つ方式が使える）:
  ```
  帰り道の取り込み（2026-10-12 の週次）

  ### 新しい回 1件
  シートに貼る1行（A〜I列、タブ区切り）:
  勝又健志	（空）	（空）	第3期JPML WRC-Rリーグ	https://www.youtube.com/watch?v=6WAPjcxT78A	（空）	Y	（決勝の候補）	（空）
  - H列（決勝動画URL）: 見つからない／複数（N本）のため空にした、など

  ### H列が空の回 2件
  - 三浦智博 第40期十段戦（WDRBtJJME7I）
  - …

  ---
  _[実行ログ](…)。関連: #194_
  ```
  （列は今のシートの A 名前・B X ID・C 公開日・D タイトル・E URL・F 画像URL・G 表示・H 決勝動画URL・I 備考。B・C・F は読まない列なので空でよい〈WH-57〉。G を Y にして貼るかは判断が必要なこと）

#### `regenerate-page.yml` への組み込みの案

- 置き場所: 今の「YouTubeチャンネル情報を取得」の直後（「対象ページを再生成」の前）に、同じ条件
  `github.event_name != 'push' && (inputs.target_page == '' || inputs.target_page == 'all')` で次を足す（push と、毎朝の取り込みからの `live_pages title_pages` の呼び出しでは動かない）
  1. 「帰り道の YouTube 情報を取得」: `fetch_youtube_meta.py`（`YOUTUBE_API_KEY`）。`continue-on-error` で既存の JSON のまま生成を続け、最後のステップで失敗にする（`fetch_youtube_channels` と同じ形）。
     決定2で `viewCount`・`fetched_at_utc` を書かない。決定4のため、取れなかった動画は前回の JSON の値を残して `missing_video_ids` に載せ、知らせる
  2. 「帰り道の新しい回を調べる」: 層1・シート・title/ の照合を読み、知らせの本文（Markdown）と有無を書き出す（新しいスクリプト）
  3. 「帰り道の一覧の OGP を作る」: `pip install Pillow` のうえ `build_wayhome_ogp.py`（決定10の連番の名前で）。Pillow は今このワークフローに入っていない
- 「対象ページを再生成」: 決定3のため `lib/wayhome.load_episodes()` は JSON に無い回を外して続け、外した回を出力に残す（ステップの最後で失敗にするための目印）
- 「変更をコミット・push」: `git add` の対象に `data/youtube_meta.json` と `img/ogp/wayhome`（削除も拾う `-A`）を足す。決定2で JSON の差分は中身が変わったときだけになり、`youtube_channels.json` と同じ扱いでよい。
  画像と `video_wayhome.html` は同じコミットに入る（決定10）
- 知らせ: 最後に `actions/github-script` で常設 issue へコメントし、新しい回・JSON に無い回・取得の失敗があればジョブを失敗にする（決定6の「失敗の扱い」）。
  H列が空の回（決定9）だけのときも失敗にするかは判断が必要なこと（毎週出続けるため、案: issue だけ・変化があったときだけ）
- 注意:
  - **今は #533 のとおり、1ページの失敗で `regenerate.py all` が止まり、コミットのステップも動かない**（2026-10-10 は辞書で止まっている）。帰り道の取り込み・OGP もその間はコミットされない
  - 週次は GitHub の `schedule`（Worker からの起動ではない）なので、実際の起動は遅れることがある
- Secret・権限:
  - Secret は `YOUTUBE_API_KEY` だけ（登録済み）。帰り道シート・title/ のタブ・/live の【2】【3】の読み取りは公開シートの gviz で鍵は要らない。層1はリポジトリの jsonl。**シートへの書き込みは無い（決定7）ので、書き込みの鍵（`LIVE_SHEETS_SA_KEY` など）は要らない**
  - issue へのコメントに `issues: write` が要る。`regenerate-page.yml` には `permissions` が無く、既定のトークンの権限で動いている（リポジトリの既定の権限はセッションからは読めなかった。`actions/permissions/workflow` はプロキシで拒否）。
    `permissions` を足すなら `contents: write`・`issues: write`。**`update-live-channel.yml` の `regenerate` ジョブ（`workflow_call`）は呼ぶ側の `contents: write` だけを持つため、呼ばれる側が `issues: write` を求めると起動で失敗しうる。**
    呼ぶ側の `regenerate` ジョブにも `issues: write` を足すか、知らせを別のジョブ・ワークフローに分ける（実装で確かめる。`.github/workflows/` を変えるので docs/notes/branch-operations.md「ワークフローを変更したとき」に従う）

#### #192 の決定（閲覧数の保存・生成を止める）を書いた場所

| 場所 | 中身 |
|---|---|
| `scripts/fetch_youtube_meta.py` 冒頭の docstring（8・12行）、`FIELDS` の `viewCount`（68行）、`statistics.viewCount` の保存（132行）、`fetched_at_utc`（177〜179行） | 閲覧数の保存・取得日時。決定2で外す |
| `scripts/lib/wayhome.py` `load_episodes()`（122〜147行） | JSON に無い回で `ValueError`（生成を止める）。決定3で置き換える |
| `scripts/build_wayhome_ogp.py` docstring（12〜16行）・`main()`（95〜96行） | 同じ名前で中身が変わると止まる。決定10で置き換える |
| `scripts/lib/wayhome.py` `list_og_image_name()`（308〜312行） | 名前 `index-<最新話の公開日>.jpg`。決定10の連番をここで決める |
| `docs/notes/video-wayhome.md`「新しい回を追加する手順」（7〜20行。13・18行）、「#192 第2段」（127〜128行）、「#192 第3段」（162〜163行） | 手作業の手順・生成が止まる・閲覧数は保存のみ |
| `docs/notes/static-generation.md` 473行（`build_wayhome_ogp.py` の説明） | 一覧の OGP の作り方 |
| `docs/notes/ogp.md` 9・20行 | 名前は `list_og_image_name()` だけで決める（変わらない） |
| #194 の本文（「生成が止まる仕様」「閲覧数は保存のみ」）・コメント（CHAT-0914-YT-06） | 決定2・3で置き換え |
| テスト | `scripts/tests/` に `load_episodes`・`youtube_meta`・`list_og_image_name`・`build_wayhome_ogp` を扱うものは無い（実装でテストを足す） |

- 決定10の連番の例: 今の39本には同じ公開日（JST）の回が 2024-11-19 に3本、2025-10-17・2026-06-05 に各2本ある。
  案: 最新の公開日の回が N 本（N≥2）なら `index-<日付>-N.jpg`。名前が本数で決まるので、builder とページが同じ関数で同じ名前を出せる
- 決定を `docs/decisions/wayhome.md` に足し（2b7dc0f3）、README の一覧の行を直した。#194・#340 に決定の要点とログ（710db182 に固定）をコメントした
- 調べるのに使ったスクリプトは scratchpad（読むだけ。title/ の `load_broadcasts()` などをそのまま呼んだ）。リポジトリには入れていない。`regenerate.py` は流していない
- 止まる条件: 前提と実物の大きな食い違いは無い（/live の取り込みに帰り道の動画は含まれ、title/ の決勝は /live の【2】＋【3】を使う）。ログ・docs/decisions 以外は変えていない
- マージ: `origin/cloudflare`（0c17266d）を取り込み（衝突なし）、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1010-whs:cloudflare`（0c17266d..afa8f455）。ドキュメントのみで Workers Builds は走らない

## 報告

- 状態: 判断待ち
- ブランチ: work/1010-whs
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1010-WHS-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1010-whs
- 確認用URL: なし
- マージ: 済（afa8f455。ドキュメントのみ）
- issue: #194・#340（決定の要点とログをコメント）
- 判断が必要なこと:
  - `6WAPjcxT78A`（《第３期JPMLWRCR勝又健志編》帰り道ついていってイイっすか、2024-09-12）がシートに無い。シートに足すか、検知から外すか（このままだと実装後の初回に「新しい回」として出る）
  - H列の候補は title/ の節「決勝動画」（回戦・卓）ではなく「決勝ライブ」（日・ステージ）から採るか（今の H列は39行とも決勝ライブ側）
  - 決勝が複数日のとき: 決定8どおり空にするか、最後の1本（最終日）を候補にするか（今の H列の8行はすべて最終日と一致）
  - 無料の冒頭版とメンバー限定の全編があるとき、どちらを候補にするか（今の H列の2行は冒頭版。title/ は全編）
  - 大会名の違いの扱い: D列の `帝王戦`・`世界麻雀TOKYO2025` と title/ の `小島武夫杯帝王戦`・`リーチ麻雀世界選手権（第4回）`、YouTube の題名の `十段位`・`鳳凰位`・`JPMLWRC` など。別名の表を持つか（新しい回の D列は YouTube の題名から作るしかなく、今の形の素直な変換で今の D列と一致したのは39本中24本）
  - H列が空の回の知らせ（決定9）: 毎週出すか、変化があったときだけか。ジョブを失敗にするか（`ワールド・リーチ・プロ` は title/ に大会が無く、H列も空のまま出続ける）
  - 貼る1行の G列（表示）を `Y` で出すか空で出すか
- 未確認の項目:
  - `regenerate-page.yml` が既定のトークンで `issues: write` を持つか（リポジトリの既定の権限をセッションから読めない）。`update-live-channel.yml` からの `workflow_call` で呼ばれる側が `issues: write` を求めたときに起動できるか（実装で確かめる）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 963efe0d）: https://github.com/retroeater/mj-logs/tree/main/guide/963efe0d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/963efe0d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/647a8db8.md
