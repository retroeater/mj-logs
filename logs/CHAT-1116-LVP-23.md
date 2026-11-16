# CHAT-1116-LVP-23

- 着手日時: 2026-11-16
- 対象issue: #362・#495・#475・#396・#397
- ブランチ: work/1116-lvp-post
- 着手時HEAD: f533d3bfd

## 指示

【Claude作成】Claude Code 向け指示：live/ の公開の後に回した残り（#495・#475 の抜き出し・#396・#397 の本文・LV-04 の差4・文書の古い記述）を片付け、条件つきで cloudflare へマージする Chat-Ref: CHAT-1116-LVP-23 マージ: 承認済み（チャットで）。生成物の差分が下の「止まる条件」に当たれば、マージせずに止まる 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1116-lvp-post の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1116-lvp-post を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1116-lvp-post origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
/live の公開（#362）の後に回した小さな残りを、告知動画の前に片付ける。
決定（2026-11-04〜16、平野さん）

* 公開の後に回したものを、告知の投稿を待たずに今片付ける（2026-11-16）
* #495: 【3】の行数が急に減ったら、/live の生成を止める（2026-10-16 に「公開の直後に別の指示で入れる」と決めたもの）
* #475 の「ベスト16A卓:黒沢咲」: シートの「別名」に行を足すのではなく、概要欄からの抜き出しの規則を直す（卓などの前置き「ベスト16A卓:」が名前に付いたまま抜き出される形を直す）。「大阪予選1位」は対応しない（2026-11-04）。bot の文面の案内先「#446 の項目8」を #490 に直す
* #396・#397: 本文の「X を埋める」は古いので、「かな・所属」だけに直して残す（2026-11-04）
* LV-04 の 798 と 794 の差4: 調べずに閉じる（3層化で前提が変わったため。2026-11-04）
* docs/notes/live-page-design.md の古い記述（2-1 付近の共有ボタンの記述など、CHAT-1104-LVP-15 の報告）を直す
* `scripts/generate_pro_pages.py` の docstring「live/ のトップと同じ日付の新しい順」（CHAT-1104-LVP-16 の報告で合わなくなった）を直す

前提（チャット側。平野さんの決定ではない）

* #495 の中身（どれだけ減ったら止めるか）は #495 の本文とコメントにある（要確認。閾値が決まっていなければ、案を書いて実装は止める）
* #475 の抜き出しは `scripts/lib/live_extract.py`（層2の規則）と見ている（要確認）。直した後に「黒沢咲」が名簿にも「連盟プロ以外」にも無ければ、未登録の名前として残る。そのときは「連盟プロ以外」に足すかを平野さんに聞く（このときは止まらず、報告に書く）
* LV-04 の差4 が何の issue・どの記述かは、docs/decisions/live.md の LV-04 と #362 のコメントで確かめる（要確認）
* C8（title/ の期ページから live/ の期への静的なリンク）は、SEO 系列が検討中の案B（title/ の期ページから同じ期の live/ の対局へのリンク）と同じ主題なので、この指示では起票しない。SEO 系列の結果を待つ
* 告知動画の制作はまだ始めていない（docs/notes/page-announcement.md の告知の期間の決まりには当たらない）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。#495・#475・#396・#397 の本文とコメントを読み、上の前提と食い違う点を書く。#495 と #475 の直しを実装し、テストを足す（#495 は減った件数・割合の境、#475 は前置きの付いた名前の形）
2. #396・#397 の本文を直す。LV-04 の差4 の項目を閉じる（issue ならコメントを書いてクローズ、記述なら直す。閉じる理由は上の決定）。docs の古い記述と docstring を直す。全ページを再生成し、テストを回し、生成物の差分を書く（#475 の直しで抜き出しの変わる行を全件）
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後、Workers Builds の check-run の結果を確かめる。上の「決定」を docs/decisions/live.md に足す

止まる条件

* #495 の閾値が本文・コメントで決まっていない（案を書いて、#495 だけ実装せずに残りを進める）
* #475 の直しで、「黒沢咲」の行のほかに抜き出しが変わる行がある（全件を書いて、マージせずに止まる）
* 生成物の差分が、#475 の直しで説明できる live/・pro/ の変化のほかに出る（マージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/lib/live_extract.py`・`scripts/generate_live_pages.py`・`scripts/generate_pro_pages.py`・docs/notes/live-page-design.md の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* #362・#495・#475 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、#475 の直しの後の「黒沢咲」の扱い（未登録として残るか）と、#495 の閾値（決まっていなかった場合）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1116-LVP-23.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1116-LVP-23 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1116-LVP-23` のコミットは0件。`LVP` は同じセッションの続き。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-16 11:47 JST
- 0. 「指示」欄の末尾は指示文の最後の行と一致。work/1116-lvp-post はリモート・ローカルとも無いので origin/cloudflare から作った
- #362・#495・#475 に他セッションの着手中のコメントは無い（#362 の着手中は同じセッションの LVP-19〜22 のもの、#475 の最後の着手中は CHAT-1115-ACF-45 で完了済み）。#362 に着手中のコメントを書いた
- 未マージの work/ ブランチ: `work/1101-prp-pub` が `scripts/generate_pro_pages.py`・`scripts/generate_live_pages.py` を変えているが、今回直した `page_cards()`（258行）の箇所は変えていない（止まらない）。`live_extract.py`・live-page-design.md を変えているブランチは無い
- **前提と食い違う点**:
  - **#495**: 止める割合・意図して減らしたときの通し方・手動実行の入力が本文・コメントで決まっていない（本文は「例: 20%以上の減少」まで。コメントは0件）。止まる条件のとおり実装せず、案（A: 【2】の候補との突き合わせで50行を超えて欠けたら止める〈推し〉／B: 前回の行数から20%以上減ったら止める）を #495 にコメントした
  - **#475**: 「ベスト16A卓:黒沢咲」の抜き出しの直しは、CHAT-1115-ACF-45 が `scripts/lib/live_extract.py` の `TABLE_LABEL_RE` で済ませ、cloudflare に入っている（b565cbb21、2026-11-15）。次の取り込みで「未登録の名前 2名 → 0名」（黒沢咲・香野蘭とも登録済みの名前として読まれる）。bot の文面の案内先も同じ指示で直してあるが、行き先は #490 ではなく「`scripts/lib/live_extract.py`。docs/notes/live-channel-write.md「7. 毎日の取り込み」」。前提（未実装）と実物が食い違うため、#475 には手を入れていない（今回の直しで抜き出しが変わる行は0件）
  - **docs/notes/live-page-design.md の共有ボタンの記述**: CHAT-1104-LVP-16（30612b576）で「置かない」に直してあった。同じ 2-1 付近〜2-8 に残っていた古い記述（トップの並び・ヒーロー）を直した
  - **LV-04 の差4**: issue ではなく、#362 本文の「公開の後に回したもの」の1行（と docs/decisions/live.md の 2026-11-04 の行）。決定の記録の行は変えず、#362 に閉じた旨をコメントした。#362 本文は手順1の「本文は変えない」に従い変えていない
- 文書（32d2332cf）: docs/notes/live-page-design.md の「2-2」「2-8」のトップの並びを「更新」の日付（ページ内の動画の最新の日付）の順に、「2-5」のヒーローを「個別ページにだけ」（2-10 のとおり）にし、期のページの日付の範囲を「説明文（meta description）」にした
- docstring（884575e3a）: `scripts/generate_pro_pages.py` の `page_cards()` を「ページの日付〈最も早い動画の日付〉の新しい順、page_date_order()。live/ のトップの「更新」の順とは違う」にした（出力は変わらない）
- #396・#397: 題名と本文を「かな・所属団体」だけに直した（目的の行、現状の X の件数を外し、冒頭に「範囲の更新（2026-11-04、平野さんの決定）」の1行を足した。#396 の後続の行も「かな・所属」に）
- テスト 2,200件 OK。全ページの再生成（dry_run、compare_ref origin/cloudflare、run 43794570376）: **差分なし**
- 自己レビュー（新しい文脈のサブエージェント、1回。対象: live-page-design.md と generate_pro_pages.py の差分、観点: 書き換えた文が今のコード・生成物と合うか、消した記述に残すべきものが無いか）:

| 指摘 | 重大度 | 扱い |
|---|---|---|
| `live_cards()` の docstring「live/ のトップの並び(新しい順)」が、直した `page_cards()` と食い違う | 要修正 | 直した（d8b3a5bfb） |
| 2-5 の期のカードの「更新」の例が古い形（「2023-12-05 更新」）、「最新の公開日」はライブなら配信日 | 任意 | 直した（3d2ca32b8） |
| 2-6 の「この日付を…並び…のすべてに使う」がトップの並び（更新の順）と食い違う | 任意 | 直した（3d2ca32b8）。2-5 の LP-11 の判断の列挙（期のヒーローの範囲など）は判断の記録として残した（2-10 の注記が上書きする） |
| 「並びは 2026-11-04 の決定」の出典の書き方 | 任意 | 直さない（並びはその日の LVP-16 で入ったコミット 1bedc53a8 で、日付は合う） |

  証跡: build_top の並びと live/index.html の「更新」772件が降順であること、トップ・大会・期にヒーローが無く個別に1件あること、page_cards が page_date_order であることをレビューが確かめた。直しは docstring と文書だけで、生成物は変わらない（再生成の比べの後の変更は docstring のみ）。テスト OK

## 報告

- 状態: 判断待ち
- ブランチ: work/1116-lvp-post
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1116-LVP-23.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1116-lvp-post
- 確認用URL: なし（生成物の変更なし。文書と docstring のみ）
- マージ: 済（MERGE_SHA）
- issue: #362・#495・#475・#396・#397
- 判断が必要なこと:
  - #495 の止める境（決まっていなかったので実装していない）: 案A（推し。/live の生成で、【3】に無い【2】の候補が50行〈約1%〉を超えたら止める。保存するファイルも通す入力も要らない）か、案B（【3】の行数が前回の生成から20%以上減ったら止める。前回の行数を data/ に残し、手動実行の入力で通す）。#495 のコメントに詳細
  - #475 の「黒沢咲」: 抜き出しの直しは CHAT-1115-ACF-45（b565cbb21）で済んでおり、黒沢咲は登録済みの名前として読まれている（未登録の名前は 0名。「連盟プロ以外」に足す必要は無い）。bot の文面の案内先は同じ指示で「#446 の項目8」から「`scripts/lib/live_extract.py`。docs/notes/live-channel-write.md「7. 毎日の取り込み」」に直されている。決定の「#490 に直す」に合わせて書き換えるか（今回は前提と食い違うため変えていない）
  - #362 本文の「公開の後に回したもの」の行（LV-04 の差4・#396・#397・#475）: 本文は変えていない（コメントで閉じた旨を書いた）。本文の行を消す・済にするか
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 67eac94e）: https://github.com/retroeater/mj-logs/tree/main/guide/67eac94e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/67eac94e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/2cedda30.md
