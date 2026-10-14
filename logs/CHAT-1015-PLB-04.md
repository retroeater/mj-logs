# CHAT-1015-PLB-04

- 着手日時: 2026-10-15
- 対象issue: #567
- ブランチ: work/1015-plb
- 着手時HEAD: 3cf17d8

## 指示

【Claude作成】Claude Code 向け指示：（PLB-03 の続き）元の3タブの改名と「桜花」「JWRC」の列の削除の後で、生成とページが変わらないことを確かめる Chat-Ref: CHAT-1015-PLB-04 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも（CHAT-1014-PLB-03 と同じセッションでよい） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1015-plb を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1015-plb origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。 CHAT-1014-PLB-03 のログの `## 報告` を読み、状態が「判断待ち」でなければ何もせず止まる。判断待ちなら、その状態の末尾に `/ 続き: CHAT-1015-PLB-04` を足す（このログの push と同じ時に）。

目的
issue #567 の、平野さんの手作業の後の確かめ。PLB-03 の「未確認の項目」（列を消した後・元のタブを改名した後の本番の表示と、次の再生成）を片付ける。
決定（2026-10-15、平野さん）

* 元の3タブ（正本の「プロ」、ryoei.pro_その他マスタの「連盟プロ以外」「別名」）の名前に「(旧)」を付けた（平野さん、完了）
* 「桜花」「JWRC」の列を消した（平野さん、完了）。ただし「桜花」の G 列「結果」も、JWRC と同じ理由（予選の通過・敗退を載せる可能性）で残した。消したのは「桜花」の C・Q〜U・W 列と、「JWRC」の C〜E・N〜U・W 列
* 1週間後を目途に「(旧)」の3タブを消す（カレンダー「ryoei.pro」に 2026-10-21 の予定あり。平野さんの手作業）

前提（チャット側。平野さんの決定ではない）

* 列を消した後の「桜花」「JWRC」の見出しは、どちらも「名前・期・リーグ・リーグキー〈桜花のみ〉・順位・結果・合計・第1節〜（桜花は第8節、JWRC は第5節）・表示・備考」の並びになっている見込み（要確認。実物を読んで書く）
* PLB-03 で確かめたのは JWRC の G 列を残した形。「桜花」の G 列を残した形はまだ確かめていない
* 改名後の元の3タブは、どの生成も読まない見込み（PLB-02 で読み手をすべて切り替えた）

手順

1. 「桜花」「JWRC」の今の見出しを生成と同じ経路（`lib/sheets.py`）で読み、ログに書く。上の決定と合わない（消したはずの列が残っている、残すはずの列が無い）なら止まる。正本のブックと ryoei.pro_その他マスタのタブ一覧（htmlview）に「(旧)プロ」「(旧)連盟プロ以外」「(旧)別名」があり、元の名前のタブが無いことを確かめる。#567 に着手中のコメントを残す（本文・状態・ラベルは変えない）。
2. `python3 -m unittest discover -s scripts/tests` を通し、origin/cloudflare のコードで `python3 scripts/regenerate.py all` を実行して、cloudflare にある生成物との差を見る。差があれば種類ごとに分け、シートの値の変化（新しい対局結果など）で説明できるものと、列の削除・改名で説明できるものを分けて書く。後者は無い見込み。生成物はコミットしない。
3. JS のページ（ouka_results・wrc_results・ouka_ranking・wrc_ranking）が、今のシートで列の削除の前と同じ表を出すことを、本番で確かめる（届かなければ PLB-02 と同じ node の方法で、今の見出しから引き直した問い合わせが同じ列を指すことを確かめ、本番の見え方は「未確認の項目」へ）。結果を #567 にコメントし、「1週間後に (旧) の3タブを消す」作業が残ることを書く（#567 はまだ閉じない）。

止まる条件

* 手順1で、見出し・タブ名が上の決定と合わない
* 手順2で、列の削除・改名で説明できる差が出た、またはどのページかの生成が止まった（止まったページと理由を書く）
* 手順3で、JS のページの表が列の削除の前と違う
* 読むたびに行数が変わる

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、残る平野さんの手作業（2026-10-21 頃に「(旧)」の3タブを消す）を書く
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1015-PLB-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1015-PLB-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1015-plb
- ログ: https://github.com/retroeater/mj/blob/work/1015-plb/docs/logs/CHAT-1015-PLB-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1015-plb
- 確認用URL: なし
- マージ: 未
- issue: #567
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3cf17d8b）: https://github.com/retroeater/mj-logs/tree/main/guide/3cf17d8b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3cf17d8b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/3cf17d8b.md
