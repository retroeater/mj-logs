# CHAT-1011-ABM-02

- 着手日時: 2026-10-11
- 対象issue: #425（関連: #501・#530、新規の OGP の issue）
- ブランチ: work/1011-abm
- 着手時HEAD: 44da572ec999a62c44a693ab956eb1563e40d251

## 指示

【Claude作成】Claude Code 向け指示：#425（最強戦の個別ページ）の grill の決定を記録し、issue と設計メモを直す
Chat-Ref: CHAT-1011-ABM-02
マージ: ドキュメントのみ（ログ・docs/decisions/・docs/notes/saikyo-page-design.md）なので完了報告のうえ cloudflare へ入れてよい。コード・ワークフロー・シート・生成物は変えない
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1011-abm を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-abm origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1011-ABM-01 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1011-ABM-02` を足す。

## 目的
CHAT-1011-ABM-01 の調査を受けて、チャットで /grill-me の形で決めたこと（#425 の11項目）を docs/decisions に記録し、関係する issue と設計メモの古い記述を直す。実装はまだしない（次は試作の指示）。

### 決定（2026-10-11、平野さん）
1. 同じ年度・同じ日に2つある対局（14組）の識別子: 日付（`YYYYMMDD`）のままにし、2つ目にだけ枝番 `-2` を付ける。今 `?match=<日付>` で表示される対局（`assets/saikyo.js` が選ぶ、年度ページで先に出る方）が日付だけの識別子を引き継ぐ。2つ目の対局の共有ボタンが1つ目を出す不具合は、#425 の中で直す（単独では直さない）
2. URL の形: 年度 `/saikyo/<年度>/`、対局 `/saikyo/<年度>/<対局識別子>.html`、卓 `/saikyo/<年度>/<卓のアンカー>.html`（卓のアンカーは今の `<対局識別子>-s<ステージ連番>[-<卓>]`）。年度ページも `saikyo/<年度>.html` から `saikyo/<年度>/` へ移し、#425 と同時に入れる。旧 `/saikyo/<年度>.html` は `/saikyo/<年度>/` へ 301
3. 今後、対局日が空の行が入ったら: その対局だけ個別ページを作らずに飛ばし、知らせる。年度ページには出す
4. ページの中身: 年度ページは各対局の勝ち上がり者（対局名から対局のページへリンク）。対局のページは今の `?match=` の表示と同じ（ステージ順に全卓・各卓4人）で、卓の見出しから卓のページへリンクし、ABEMA のリンクも添える。卓のページはその卓の4人と「ABEMA で見る」（URL があるときだけ）、同じ対局のほかの卓と対局のページへの導線。個別ページには共有ボタンを付けない。年度ページの対局ごとの共有ボタンは、対局のページへのリンクに置き換えて外す（#530 の最強戦の分。2026-10-10 に「残す」とした対局ごとの共有ボタンの扱いを置き換える）。固定バー・年度プルダウン・パンくずは年度ページと同じ部品
5. 勝ち上がり者: 各対局の最後のステージで結果（G 列）が「通過」の人。シートの値は変えない（「優勝」の値は足さない）。表示は、決勝の通過者に「優勝」、それ以外の最後のステージの通過者に「通過」のバッジ。未対局の対局は見出しと「未対局」を出す。年度ページの選手名の絞り込みは外す（全員の検索はトップに任せる）
6. トップの全期間の検索結果の行き先は卓のページにする。旧年度 URL に `#<卓のアンカー>`（または `#match-…`）を付けて来た人は、301 の後の新しい年度ページの JS が卓のページ（または対局のページ）へ `location.replace` で移す
7. サイトマップはトップ・年度・対局・卓の全部（見込み 879件）。canonical は個別ページも年度ページも自分自身。OGP 画像は年度の今の画像を使い回し、題名と説明はページごとに出し分ける。`llms.txt` の「## 麻雀最強戦」に URL の形を書く。作る指示と公開の指示を分け（docs/new-page-checklist.md）、年度ページの移動と `?match=` の転送は公開の指示で一度に行う
8. 個別ページの OGP 画像は、個別ページ化の完了後に改めて検討する。そのための別の issue を起票する
9. `?match=` の転送: Cloudflare の Single Redirects の1ルールで、`https://ryoei.pro/saikyo/*.html?match=*` を `https://ryoei.pro/saikyo/${1}/${2}.html` へ 301（クエリは引き継がない）。個別ページに無い値と、後ろに別のパラメータが付いた URL は 404 でよい。ルールの作成は公開の指示の時に平野さんが行う
10. 動画の URL の列: 「最強戦」シートの L（備考）の後ろに M「動画URL」を足す。卓の4行のうちどれか1行以上に入れればよい。4行で違う URL が入っていたら警告し、その卓にはリンクを付けない
11. #501: #425 は #501 を待たずに作り、M 列の URL をそのまま出す。#501 は個別ページの公開の後に進める。作る前の確かめ（利用規約・公式 API）はチャット側が調べ、番組一覧から最強戦の回を並べられるかは平野さんの PC で見る。入力は M 列の URL（重複を除く）、出力は平野さんの PC 上の CSV（リポジトリに入れない）、シートには書き込まない、定期実行にせず手で動かす。平野さんの PC には Python 3.14.3 が入っている

### 前提（チャット側。平野さんの決定ではない）
- 事実は CHAT-1011-ABM-01 のログの `## 経過`・`## 報告` のとおり（219対局・643卓、同日の対局 14組、対局日が空の対局 0件、シートは A〜L）
- 平野さんは M 列をまだ足していない（この指示の時点。要確認ではなく、この指示では読まない）

## 手順
1. 決定を記録する: docs/decisions/README.md と docs/decisions/saikyo.md・features.md の今の内容を読み、上の「決定」を saikyo.md に足す。前の決定と食い違うもの（例: 2026-10-10 の対局ごとの共有ボタンを残す扱い、features.md の #425 の行）には README の書き方で置き換えの注記を付ける
2. issue を直す（どれも先に今の本文・コメントを読む）:
   - #425: 本文に「決まったこと（2026-10-11）」の節を足し、上の決定を要約し、docs/decisions/saikyo.md の節を指す。本文の「URL の形」（c-1〜c-3）と「生成とサイトマップ」は決定で置き換わった旨を書く（古い検討は消さずに残してよい）。コメントでこのログを指す
   - #501: 決定11をコメントする
   - #530: 最強戦の対局ごとの共有ボタンは #425 で対局のページへのリンクに置き換えて外す、とコメントする（#530 は閉じない）
   - 新しい issue「最強戦の個別ページの OGP 画像を検討する」を起票する（同じ主題の issue をクローズ済みも含めて先に検索する。#343 は年度ページの分で閉じている）。本文に「#425 の個別ページ化の完了を待つ」「今は年度の画像を使い回している」「作るなら毎朝の再生成の対象パスに `img/ogp/` が無い（CHAT-1011-ABM-01）」を書き、ラベル「状況: 待ち」と対象・分野のラベルを付ける
3. docs/notes/saikyo-page-design.md を直す（今の内容を読んでから）: 「1. データ源」の表に L「備考」と、M「動画URL」（#425 で足す予定。入れ方は決定10）を足し、A 列の備考を今の事実（対局日が空の対局は 0件、2026-10-11）に直す。「アンカー」に、同じ日に2つの対局があると `?match=` の対局識別子が重複すること（14組）と、#425 で枝番にすることを書く。#425 の個別ページの設計そのものは、試作の後で書くので、ここでは書かない

## 止まる条件
- #425・#501・#530 に他セッションの着手中コメントがある、またはどれかがクローズ済み
- OGP の issue と同じ主題の open の issue がある（起票せずに報告する）
- 決定が docs/decisions の既存の決定と矛盾し、どちらが正か判断が要る（置き換えの注記で済むものは止まらない）
- ログ・docs/decisions/・docs/notes/saikyo-page-design.md 以外を変える必要が出た（変えずに止まる）
- 取り込みで生成物でない文書が衝突した。ただし両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-ABM-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-ABM-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態:
- ブランチ:
- ログ:
- 比較URL:
- 確認用URL:
- マージ:
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 44da572e）: https://github.com/retroeater/mj-logs/tree/main/guide/44da572e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/44da572e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
