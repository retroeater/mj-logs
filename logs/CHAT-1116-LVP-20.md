# CHAT-1116-LVP-20

- 着手日時: 2026-11-16
- 対象issue: #362
- ブランチ: work/1105-lvp-auto
- 着手時HEAD: d1881f90b

## 指示

【Claude作成】Claude Code 向け指示：/live の掲載の自動（work/1105-lvp-auto）を「タイトル戦」シートの「自動掲載」の列で読む形に直し、規則を1つ直して、ページが増減しなければ cloudflare へマージする Chat-Ref: CHAT-1116-LVP-20 マージ: 承認済み（チャットで）。ただし、試しでページが増える・減るときは、マージせずに判断待ちで止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-lvp-auto の push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1105-lvp-auto（CHAT-1115-LVP-19 の実装がある）を続けて使う。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」） 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1115-LVP-19 のログの `## 報告` の状態の末尾に `/ 続き: CHAT-1116-LVP-20` を足す。

目的
CHAT-1115-LVP-19 で作った /live の掲載の自動を、平野さんの答えに合わせて直し、本番へ入れる。
決定（2026-11-16、平野さん）

* 「タイトル戦」シートに「自動掲載」の列を足した（平野さんの手作業）。LVP-19 の報告の18大会に加えて、小島武夫杯帝王戦と JPMLリーグも Y にした。コードは仮の一覧（`AUTO_TITLES`）を消し、この列を読む（暫定を残さない）
* 帝王戦を Y にしたので、【3】の掲載が空欄の帝王戦の行（LVP-16 の時点で10行）にも自動が当たる（2026-11-04 の決定「今の空欄すべてに当てる」のとおり。2026-10-17 の「帝王戦は公開の後に回す〈#590〉」をこの形で進める）
* 規則を1つ直す: 公開の生配信（層1の「限定」N）で、題名に【無料放送】が無く、同じ大会・期・日にメンバー限定版も無いものは、N ではなく保留にする（全編無料なのに【無料放送】が無い配信〈第43期十段位決定戦の二日目・最終日のような例〉を、知らせなしに N にしないため）。同じ日にメンバー限定版がある公開版は今のまま N
* 配信予定（配信が始まっていない）の行を保留の知らせから外したこと（LVP-19 でずらした点）は、そのままでよい
* リーチ麻雀世界選手権・昇龍戦も、決定どおり自動にする
* 試しでページが増える・減るときは、マージせずに止まる（平野さんが見てから決める）。増減が無ければマージしてよい

前提（チャット側。平野さんの決定ではない）

* 列の位置は LVP-19 の報告の案（「表示順」の右、D列）。コードは見出し「自動掲載」の名前で読む。平野さんが入れた位置と値は、実物で確かめる
* JPMLリーグは live/ のページが無い大会（LVP-19 の報告）。Y にしたことで【2】の自動の値が付く行、作られるページ・警告が出るかは要確認
* 帝王戦の空欄の行は、`live/teiou/` の既存のページに足されるか、新しいページになる見込み（要確認）。帝王戦はステージの記号・まとめ単位の扱いが #590 で未決のところがある
* 告知動画（live/ の公開の告知）の制作はまだ始めていない（docs/notes/page-announcement.md の告知の期間の決まりには当たらない）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。origin/cloudflare を取り込む（衝突の扱いは CLAUDE.md「ブランチ運用」）。「タイトル戦」シートの「自動掲載」の列（位置・Y の大会の一覧）を読んで書く。`AUTO_TITLES` を消してこの列を読む形に直し、上の規則の直しを入れ、テストを直す・足す（列が無い・見出しが違うときの扱いも1本）
2. 試す（シートと issue には書かない。LVP-19 の手順2と同じ形）: (a) 【3】に掲載がある全行の一致率、(b) 今【3】が空欄の行に当てたときの大会ごとの Y・N・保留の件数と全行の表（帝王戦・JPMLリーグを含む）、(c) その結果で増える・減るページの URL の全件と、生成の警告、(d) 保留の知らせの文面。全ページを再生成し、テストを回す
3. (c) でページの増減が0なら、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージし、cloudflare で `update-live-channel.yml` を apply なしで1回手動実行して、知らせの段の dry-run の出力を書く。増減があればマージせずに止まり、`## 報告` の「判断が必要なこと」に (b)(c) の表を書く。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 「タイトル戦」シートに「自動掲載」の列が無い、または Y の大会が上の決定（LVP-19 の18大会＋帝王戦・JPMLリーグ）と違う（違いを書いて止まる）
* (a) の一致率が 97.5% を下回る（不一致の上位の理由を書いて止まる）
* (b) で、「自動掲載」が空の大会（日本シリーズ2つ・紅龍戦など）の行に値が付く
* (c) でページが増える・減る（マージせずに止まる）
* 【2】【3】の読み込みが失敗する、または読み直すたびに行数が変わる（件数を書いて止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_layer3.py`・`scripts/lib/live_candidate.py`・`scripts/write_live_channel_candidate.py`・`.github/workflows/update-live-channel.yml` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の手動実行が今回の変更で失敗する（原因を書いて止まる）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「未確認の項目」に、保留が実際に出たときの issue の作成・コメント・閉じる動きと、コメントの通知メールが届くか（平野さんの GitHub の通知の設定）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1116-LVP-20` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致。作業ブランチ work/1105-lvp-auto はリモートにある。origin/cloudflare は HEAD の祖先でない（取り込む）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-16 10:20 JST（毎朝の取り込みの直後ではない）

## 報告

- 状態: 作業中
- ブランチ: work/1105-lvp-auto
- ログ: https://github.com/retroeater/mj/blob/work/1105-lvp-auto/docs/logs/CHAT-1116-LVP-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1105-lvp-auto
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 298a2545）: https://github.com/retroeater/mj-logs/tree/main/guide/298a2545

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/298a2545/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c5c45c17.md
