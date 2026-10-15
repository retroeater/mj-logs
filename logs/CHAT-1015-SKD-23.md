# CHAT-1015-SKD-23

- 着手日時: 2026-10-15
- 対象issue: #425
- ブランチ: work/1015-skd-notes（ログのみ）
- 着手時HEAD: e06076750

## 指示

【Claude作成】Claude Code 向け指示：「ABEMA の新しい回（卓ごとの動画）が出たかの検知を自動化する」を課題として起票する — issue 操作のみ Chat-Ref: CHAT-1015-SKD-23 マージ: なし（コミットを伴わない。issue の操作だけ） 貼る時機: いつでも（CHAT-1015-SKD-22 と同じセッションでよい。SKD-22 の途中なら、その完了後に貼る） 共通手順: CLAUDE.md「Chat-Ref」「作業ログ」節のとおり（識別子確認 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。コミットを伴わないので、issue の本文の末尾に `Chat-Ref: CHAT-1015-SKD-23` を書く。ログは SKD-22 の作業ブランチ work/1015-skd-notes に push してよい（平野さんが許可している。ログだけのコミット） 作業ブランチ: クラウドセッションで実行する。ログの置き場は work/1015-skd-notes（SKD-22 でマージ済みなら origin/cloudflare から作り直してよい。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
ABEMA の新しい回を ABEMA ブックの【1】元データへ入れる流れは「手動」と決まった。新しい回が出たかを毎朝検知することは今の仕組みではできないので、「検知を自動化する」を課題として issue に残す（今は着手しない）。
決定（2026-10-15、平野さん）

1. ABEMA の新しい回（卓ごとの動画）は、平野さんが PC のスクリプトで取った CSV を【1】元データに手で取り込む（ABM のチャットの「案B」。`docs/decisions/saikyo.md` の「流れは未決」を置き換える）
2. 新しい回が出たかの検知は、今は自動化しない。「検知を自動化する」を課題として起票する

前提（チャット側。平野さんの決定ではない）

* 起票の前に、同じ主題の issue をクローズ済みも含めて確かめる（`gh issue list --state all --search` が使えなければ REST で題名を読む。検索語: 「ABEMA」「検知」「新しい回」「abema_new」）。#501（閉じた。PC のスクリプト）・#425・#549 は関係する issue で、同じ主題ではない見込み。同じ主題の issue が開いていれば、起票せず止まって報告する
* issue の題名の案: 「ABEMA の新しい回（卓ごとの動画）が出たかの検知を自動化する」。ラベル: 「分野: 自動化」「対象: saikyo」（今ある名前を確かめて付ける。無ければ付けずにログに書く）。「状況:」ラベルは付けない（着手しない課題）
* 本文に書くこと（規則と事実だけ。文面は整えてよい）:
   * 背景: ABEMA は日本国外からの取得に地域のエラーを返す（#501 の本文、2026-10-05 の確かめ）。GitHub Actions・チャット側・クラウドの Claude Code はいずれも国外から動くため、回そのものを読めない。【1】の取り込みは平野さんの PC のスクリプトに頼っている（docs/notes/abema-book.md）
   * 決定（2026-10-15）: 新しい回は手動で取り込む。検知は今は自動化しない
   * 候補（決まっていない）: (a) 毎朝の `update_abema_book.py` が「対局日が過ぎたのに回が当たっていない卓」を数えて知らせる（回が出たはずの合図。ABEMA を読まない）。(b) 平野さんの PC で `abema_new.py` を予約実行し、前回との差分を知らせる（PC の起動が要る。Claude の予約実行で PC を使う経路が ABEMA に届くかは未確認）
   * 関係: #425（親。sub-issue にはしない）・#549・#501（閉じた）。`docs/notes/abema-book.md`
   * 末尾: `Chat-Ref: CHAT-1015-SKD-23`
* #425 に、決定1・2 と新しい issue の番号を1つのコメントで書く（本文・状態・ラベルは変えない。末尾に `Chat-Ref: CHAT-1015-SKD-23`）
* `docs/decisions/saikyo.md` の「流れは未決」の置き換えは、この指示では行わない（コミットを伴わない指示のため）。新しいチャット（最強戦の棚卸し）で記録させる。そのため、このログの `## 報告` の「未確認の項目」に「docs/decisions/saikyo.md への記録は未」と書く

手順

1. 同じ主題の issue を確かめる
2. issue を起票し、#425 にコメントする
3. ログを仕上げて push し、完了で止まる

止まる条件

* 同じ主題の issue が開いている
* #425 に他のセッションの着手中コメントがある

完了条件

* 確かめた issue の一覧、新しい issue の番号と URL、#425 のコメントの URL がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-SKD-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-SKD-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-skd-notes
- ログ: https://github.com/retroeater/mj/blob/work/1015-skd-notes/docs/logs/CHAT-1015-SKD-23.md
- 比較URL: なし
- 確認用URL: なし
- マージ: なし
- issue: #425
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj b50b41fd）: https://github.com/retroeater/mj-logs/tree/main/guide/b50b41fd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/b50b41fd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2fcae3af.md
