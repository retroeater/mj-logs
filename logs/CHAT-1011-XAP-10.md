# CHAT-1011-XAP-10

- 着手日時: 2026-10-11
- 対象issue: #514
- ブランチ: work/1011-xap-note
- 着手時HEAD: e4de7328

## 指示

【Claude作成】Claude Code 向け指示：XAP-02〜09 の振り返りの申送り（chat-side-operations.md・instruction-template.md に確かめ方の一文を足す。#514） Chat-Ref: CHAT-1011-XAP-10 マージ: ドキュメントのみ（docs/notes/・docs/instruction-template.md・docs/logs/・docs/decisions/）なので、完了報告のうえ cloudflare へ入れてよい。それ以外のファイルは変えない 貼る時機: いつでも 作業ブランチ: クラウドセッションで実行する。work/1011-xap-note を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1011-xap-note origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1011-xap-note の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 XAP は同じチャットの CHAT-1010-XAP-02〜07・CHAT-1011-XAP-09 で使ったもので、重複ではない（XAP-01・08 は欠番）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
XAP-02〜09（X API への切り替え・SNS ブック）の振り返りで出た、機械的に確かめられる手順だけを文書に残す。心得は書かない。
決定（2026-10-11、平野さん）

* 振り返りの知見のうち、手順に落とせるものを申送りとして文書に足す（「申送り」の規則どおり）

前提（チャット側。平野さんの決定ではない）

* 足したいのは次の4点。どれも今ある規則に「確かめ方の一文」を足す形で、新しい節は作らない
   1. docs/notes/chat-side-operations.md「外部サービスの設定」: 外部サービスの API を使う実装の指示では、本番の手順の前に「そのアカウント・プランで対象の API を1件だけ呼んで通る」ことを手動実行で確かめる手順を最初に置く（事例: CHAT-1010-XAP-02 が、アプリが古い区分のプロジェクト「Standard Basic」に付いていたため HTTP 403 client-not-enrolled で止まった）
   2. docs/notes/chat-side-operations.md「書く前に実物で確かめる」の「複数のチャットが同じ仕組みを変えているとき」の項: 確かめ方として「指示で変える関数名・ファイル名で mj-logs の logs/ を grep し、状態が判断待ち・中断のログがあればそのチャットの最新のログを読む」を足す（事例: CHAT-1010-XAP-07 が、帰り道の未マージの work/1010-whs と `lib/wayhome.py` の同じ関数で重なって止まった）
   3. docs/instruction-template.md「見込みと比べて止まる条件は、許すずれを数で書く」の項: 「見込みの数は、直前のログの表にある数から計算し、どの表のどの数かを書く」を足す（事例: CHAT-1010-XAP-05 が初回の X API の呼び出しを「0〜数件」と見込んだが、XAP-04 の表〈X ID 210・画像 161〉から 49 件と読めた。実際は上限の 30 件）
   4. docs/instruction-template.md「報告の項目」の近く: 「該当者の数を報告させるときは、名前の一覧も書かせる」を足す（事例: CHAT-1011-XAP-09 の報告が「アカウントなし 2人（吉田彩乃など）」で、2人目を平野さんが探した）
* 「手作業の証跡を受け取ってから指示を出す」は、プロジェクトのメモリーにすでにあり、文書には足さない（XAP-05 の止まりはチャット側がそれを守らなかったもの）
* chat-side-operations.md は 2026-10-11 の版（a1a50feb）で 26,105 バイトと、冒頭の上限（警告 26KB／失敗 28KB）の警告の域にある。足す分は、同じ節の事例の文を docs/notes/handover-archive-2026.md「docs/notes/chat-side-operations.md から」へ移して相殺する（規則と理由の一句だけを残す）
* 使う skill は無い

手順

1. 同じ論点の open issue を検索し（クローズ済みも含めて確認。検索語: 「申送り」「client-not-enrolled」「未マージ」「見込み」）、同じ趣旨の規則や issue がすでにあれば止まって報告する。追記先4か所の今の文を読み、同じ趣旨の記述があれば置き換え・拡張にする（どう処理したかを報告に書く）
2. 上の4点を足す。chat-side-operations.md は足す前と後のバイト数を測り、増えた分以上を同じ節の事例の文を archive へ移して減らす（足した後が足す前以下になるように）。instruction-template.md も足す前後のバイト数を書く。`assets-check.yml` の確かめ方（文書の容量の検査）があれば手元で同じ検査を通す
3. docs/decisions/saikyo.md に、この申送り（4点の要旨と事例の Chat-Ref）を1節で足す。#514 に申送りの要旨をコメントする（閉じない）。cloudflare へマージする

止まる条件

* 同じ論点の issue や、同じ趣旨の規則がすでにあって、足すべきかの判断が要る
* 追記先が上の前提と矛盾していて、どちらが正か平野さんかチャット側の判断が要る
* chat-side-operations.md が足す前より大きくなる（相殺できない）
* docs/notes/・docs/instruction-template.md・docs/logs/・docs/decisions/ 以外のファイルを変える必要が出た（変えずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 4か所の追記の文面（足した後の該当の行の引用）、足す前後のバイト数、archive へ移した文、#514 へのコメントの URL がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1011-XAP-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1011-XAP-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: `git log --all --grep="CHAT-1011-XAP-10"` は0件。XAP は同じチャットの識別子（指示文のとおり）
- 作業ブランチ: origin/work/1011-xap-note は無く、ローカルにも無い。origin/cloudflare（e4de7328）から作った

## 報告

- 状態: 作業中
- ブランチ: work/1011-xap-note
- ログ: https://github.com/retroeater/mj/blob/work/1011-xap-note/docs/logs/CHAT-1011-XAP-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1011-xap-note
- 確認用URL: なし
- マージ: 未
- issue: #514
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b1051c14）: https://github.com/retroeater/mj-logs/tree/main/guide/b1051c14

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b1051c14/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/a81419df.md
