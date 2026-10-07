# CHAT-1005-RVW-14

- 着手日時: 2026-10-07
- 対象issue: #377（クローズ）・新しい issue（起票）
- ブランチ: work/1007-rvw-close
- 着手時HEAD: de42f8de

## 指示

【Claude作成】Claude Code 向け指示：#377 を閉じ、辞書の新カテゴリ「Mリーグ」の追加と既存カテゴリの見直しを新しい issue に起票する Chat-Ref: CHAT-1005-RVW-14 マージ: ドキュメントのみ（ログ・docs/decisions・handover）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1005-RVW-13 は完了済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1007-rvw-close の作成と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1007-rvw-close を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1007-rvw-close origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
#377（辞書データのカテゴリ）は、CHAT-1005-RVW-13 で本番に入り、本文の要望を満たした。#377 を閉じ、残る作業を新しい issue に分ける。変更は issue と docs だけ。
決定（2026-10-07、平野さん）

* #377 を閉じる
* 別の課題として起票する: 辞書に新カテゴリ「Mリーグ」を追加する（選手名・チーム名）。あわせて既存のカテゴリを見直す

前提（チャット側。平野さんの決定ではない）

* 新しい issue の題の案: 「辞書: 新カテゴリ『Mリーグ』（選手名・チーム名）を追加し、既存のカテゴリを見直す」。ラベル・親 issue・本文の節立ては、CLAUDE.md と既存の issue の決まりに合わせる（要確認）。期日は無い（カレンダーの予定は作らない）
* 新しい issue の本文に書くこと（事実は実物で確かめて書く）:
   * 決定（上の2行目。平野さんの言葉のまま）
   * 今の作り: カテゴリは「連盟プロ」（「プロ」タブから生成）と「麻雀用語」（「辞書」タブ）。カテゴリを足すには `scripts/generate_resource_dictionary.py` の `CATEGORIES` に1行足し、「辞書」タブに行を足す。知らないカテゴリ名の行が先にタブへ入ると生成が止まり、前回の辞書が残る（CHAT-1005-RVW-13 の調べ。コードの1行を先に入れてから、タブに行を足す）
   * まだ決まっていないこと（チャット側が挙げた論点。平野さんの決定ではない、と明記する）: (a) Mリーグの選手名・チーム名のデータの出どころ（「辞書」タブに手で入れるか、別のタブ・シートから生成するか）と、入れ替わりへの追随 (b) チーム名の品詞（固有名詞か）と、選手名の品詞（人名） (c) 連盟プロでもある Mリーガーの重なり（同じ読み・語・品詞の行は、ダウンロード時に1行にまとめる作りになっている。カテゴリごとの件数の表示では両方に数えられる） (d) 「既存のカテゴリを見直す」の中身（平野さんに聞く。例: 「麻雀用語」のうちコメントが「連盟」の語〈大会名・支部名など〉を別のカテゴリに分けるか）
   * あわせて片付けられる小さな残り（CHAT-1005-RVW-11 の報告）: ページの `data-label`・`data-updated` 属性は保存名に使わなくなった（害は無い）。カテゴリの「◯語、YYYY-MM-DD更新」の表示を残すかは未決（保存名から「版」の日付を外した理由〈随時更新されるため日付の意味が薄い〉は、この表示にも当てはまる）
* #377 のクローズのコメント: できたことは CHAT-1005-RVW-13 のコメントにあるので、重ねて書かず、「本文の要望は満たした。残りは #<新しい番号> に分けた」と書く（末尾に Chat-Ref の行）
* カレンダー: チャット側が平野さんの予定表を「【R#377】」で探し、10/1〜10/14 の範囲に予定が無いことを確かめた（消す予定は無い）
* docs/handover.md 5章の issue の表・「次の会話の順番」に #377 が残っていれば外し、新しい issue を載せる決まり（表に載せる基準）に当たるなら1行足す（要確認: 今の文面と基準）。「最終更新」は書き換えない。サイズが警告域（26,624）の外であることを確かめる

手順

1. 確かめる: #377 の状態（Open であること）と最近のコメント、辞書・Mリーグを扱う Open の issue がほかに無いか（あれば起票せず、その issue 番号と要点を報告に書いて止まる）。`scripts/generate_resource_dictionary.py` の `CATEGORIES`・`KNOWN_POS` の今の中身
2. 書く: 新しい issue を起票する。#377 にクローズのコメントを書いて閉じる。決定を docs/decisions/features.md に足す。handover.md を上の前提のとおり直す
3. マージする（CLAUDE.md「ブランチ運用」。ドキュメントのみ）。結果をログに書く

止まる条件

* #377 が既に閉じている（起票だけ行い、報告に書く。止まらなくてよい）
* 辞書・Mリーグを扱う Open の issue が既にある
* handover.md が警告域に入る
* docs と issue 以外を変える必要が出た
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。報告に新しい issue の番号と題を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1005-RVW-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1005-RVW-14 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 2026-10-07 着手。CHAT-1005-RVW-14 のコミットなし。work/1007-rvw-close はローカル・リモートとも無く、origin/cloudflare（de42f8de）から作成
- 0. 指示欄の末尾は指示文の最後の行と一致。雛形の行は揃っている
- 1. #377 は Open（ラベル: 分野: UI/UX・分野: データ・対象: resource_dictionary。「状況:」ラベルなし。最新のコメントは RVW-13 の結果）
- 辞書・Mリーグを扱う Open の issue: `search_issues` と Open の issue の題（「辞書」「Mリーグ」「Mリーガー」「dictionary」）、ラベル「対象: resource_dictionary」で探した。辞書を扱うのは #377 だけ。#391（「カレンダー」の Mリーグの試合日程の転記の自動化）は Mリーグだが辞書とは別の課題。#388（映画の一覧）にもラベル「対象: resource_dictionary」が付いているが辞書とは無関係（ラベルは直していない）。止まる条件に当たらない
- `scripts/generate_resource_dictionary.py`: `CATEGORIES` は `("pros", "連盟プロ", None)`・`("mahjong", "麻雀用語", "麻雀用語")`、`KNOWN_POS` は「名詞」「固有名詞」「人名」。「麻雀用語」のコメント「連盟」は 89 件（`dic/mahjong.json`）
- 2. #515「辞書: 新カテゴリ『Mリーグ』（選手名・チーム名）を追加し、既存のカテゴリを見直す」を起票した（ラベル: 分野: データ・対象: resource_dictionary。#377 と同じ系統。親 issue・期日なし）。本文は「決定」「今の作り」「まだ決まっていないこと〈チャット側の論点、平野さんの決定ではないと明記〉」「あわせて片付けられる小さな残り」、末尾に Chat-Ref
- #377 にクローズのコメント（本文の要望は満たした。残りは #515 に分けた。末尾に Chat-Ref）を書き、completed で閉じた
- docs/decisions/features.md に「2026-10-07（CHAT-1005-RVW-14）」を足した
- docs/handover.md: 5章「次の会話の順番」には #377 は残っていなかった（RVW-13 で外した）。表の「現行サイトで小さく作れるもの」の行の「#389 → #377 → #277 → #388」を「#277 → #388（#389 は整備待ち）」にし、#377 は済み・続きは #515 と書いた。#515 は決めることが残る課題で「着手可能な主なもの」の基準に当たらないため、表の行は足さず同じ行に参照を置いた。「最終更新」は変えていない。サイズ 23,187（警告域の外）

## 報告

- 状態: 完了
- ブランチ: work/1007-rvw-close
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1005-RVW-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1007-rvw-close
- 確認用URL: なし（ドキュメントのみ）
- マージ: 済（ドキュメントのみ。docs/ 配下のため check-run は出ない見込み）
- issue: #515（起票）「辞書: 新カテゴリ『Mリーグ』（選手名・チーム名）を追加し、既存のカテゴリを見直す」、#377（クローズのコメントを書いて閉じた）
- 判断が必要なこと:
  - #515 の論点 (a)〜(d)（データの出どころと入れ替わりへの追随、品詞、連盟プロとの重なり、「既存のカテゴリを見直す」の中身）は平野さんに聞く必要がある
  - #388（映画の一覧）にラベル「対象: resource_dictionary」が付いている。辞書とは関係ないため外すか（今回は変えていない）
  - 指示文の雛形の行に欠けは無い
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj e3c5b6f5）: https://github.com/retroeater/mj-logs/tree/main/guide/e3c5b6f5

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/e3c5b6f5/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/1257323c.md
