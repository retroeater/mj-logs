# CHAT-1014-JPL-04

- 着手日時: 2026-10-14
- 対象issue: #523・#497（あわせて #488 の JPL-01 の片付け）
- ブランチ: work/1014-jpl
- 着手時HEAD: b0cfab082

## 指示

【Claude作成】Claude Code 向け指示：JPL-03 の続き。WRC 2大会の改称（「タイトル戦」タブだけ）の影響を先に確かめ、JPMLリーグ の行の確認と JPL-01 の片付けを行う（#523・#497） Chat-Ref: CHAT-1014-JPL-04 マージ: ドキュメントのみ（ログ・docs/decisions・docs/handover.md）なので完了報告のうえ cloudflare へ入れてよい。ただし、マージで動く自動再生成が今のシートで止まる・title/ などの生成物を変える見込みなら、マージせず判断待ちで止まる。コード・生成物は変えない 貼る時機: いつでも（平野さんの「タイトル戦」タブの変更は済んでいる）。JPL-03 と同じセッションでよい 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-jpl の使用と push、cloudflare へのマージ（上の「マージ:」の行の範囲）を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1014-jpl を続けて使う（JPL-03 の中断のログがある）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したら、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい（解いた後の該当箇所をログに引用する）。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。docs/logs/CHAT-1014-JPL-03.md の `## 報告` を読み、状態が「中断」でなければ何もせず止まる。JPL-03 の `## 報告` の状態の末尾に `/ 続き: CHAT-1014-JPL-04` を足す（`## 指示` 欄より後ろの見出しを相手にする）。#497・#523 が Open で、他セッションの着手中コメントが無いことを確かめ、両方に着手中コメントを残す。

目的
JPL-03 は「タイトル戦」タブに JPMLリーグ の行が無く止まった。平野さんが行を足し、あわせて WRC 2大会を改称した（「タイトル戦」タブだけ）。改称は #523（2026-10-09 に別課題とした件）にあたり、「タイトル」タブが旧名のままだと title/ の生成が止まるおそれがある（docs/notes/title-pages.md の「タイトル戦」タブの節、CHAT-0916-TT-14 の帝王戦の例）。生成が止まるかどうかと改称の影響を先に確かめ、JPL-03 の残り（JPMLリーグ の行の確認、JPL-01 の片付け）を行う。この指示ではシート・コード・生成物を直さない。
決定（2026-10-14、平野さん）

* 「タイトル戦」タブに JPMLリーグ の行を足した
* 「JPML WRCリーグ」を「WRCリーグ」に、「JPML WRC-Rリーグ」を「WRC-Rリーグ」に改称した。変えたのは「タイトル戦」タブの大会名だけ（「タイトル」タブ・「別名」タブなどは変えていない）
* 改称の影響を先に確かめる（チャット側の案 A を採った。改称を元に戻す案 B は採らない）。「タイトル」タブの旧名の行を直すか「別名」で寄せるかは、影響の洗い出しの後に決める

前提（チャット側。平野さんの決定ではない）

* JPL-01 の「判断が必要なこと」の扱い（チャット側の案。JPL-03 の前提と同じ）: (1) docs/handover.md 5章「次の会話の順番」の「#497／#488（連盟の予定表で JPMLリーグの「(仮)」が外れてから）」は、#488 を消し、#497 をこの指示の結果に合わせて直す（待ちなら待ちの理由に書き換える）(2) YouTube の題名の確かめは issue では追わず、チャット側がカレンダー「ryoei.pro」に 2027-03-08 の予定を作った（10/15 朝の【2】の確認の予定もある）
* チャット側が平野さんに出した JPMLリーグ の行の値の案: slug「jpml-league」、表示順 430、区分「連盟」、状態「継続」（実際の値は平野さんが決めた。要確認）
* 改称の影響が出そうな所の候補（#523 の本文にも候補があるはず。要確認。実物で足し引きする）: 「タイトル」タブの旧名の行（件数）、「別名」タブ、title/ の表示・slug（jwrc・jwrc-r は変わらない見込み）・OGP 画像 `img/ogp/title/<slug>-black.png`（大会名の文字）、`search.json`・`years.json`・sitemap、/live の大会名の照合（`broadcast_key()` など）、`yotei.EVENTS`（JPL-01 で足したテストの WRC の件名を含む）、houou/ など他ページの「JPML WRC」の表記
* 自動再生成（regenerate-page.yml など）は、cloudflare への push と毎朝の実行でシートを読み直す（要確認。docs/notes/github-actions.md）。改称で生成が止まるなら、次の毎朝の実行でも止まる

手順

1. 確かめる（読むだけ）: 「タイトル戦」タブを生成と同じ経路で読み、JPMLリーグ・WRCリーグ・WRC-Rリーグ の行の値（大会名・slug・表示順・区分・状態）をそのままログに書く。表示順の重なりも書く。「タイトル」タブの大会名ごとの行数で、旧名（JPML WRCリーグ・JPML WRC-Rリーグ）と新名の件数を書く
2. 生成を手元で走らせる（`python3 scripts/regenerate.py title_pages` など。書き込み・push はしない）。止まるならその出力をそのままログに引用する。止まらないなら、origin/cloudflare の生成物との差分を種類に分けて書く（JPMLリーグ が「記録の無い大会」に出るか、WRC 2大会の表示名・ページの増減・URL の変化）。次の毎朝の実行・cloudflare への push で同じことが起きる見込みと、その時刻（ワークフローの起動の表から）を書く。改称の影響を、上の候補を実物で確かめて表にする（場所・今の値・影響〈生成が止まる／表示が変わる／照合が外れる／影響なし〉・直し方の案）。「タイトル」タブを直す案と「別名」で寄せる案、その他の案を、メリット・デメリット・平野さんの手作業の要否つきで「判断が必要なこと」に書き、推奨と理由を書く。#523 に結果を要約してコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）
3. JPMLリーグ の行（#497）と JPL-01 を片付ける: #497 にこの指示の結果をコメントする（本文・状態・ラベルは変えない。末尾に Chat-Ref の行）。#497 をこの時点でクローズしてよいか、記録が入るまで待つかの案（メリット・デメリット）と、記録が入った時点で要る作業（OGP 画像など）を「判断が必要なこと」に書く。docs/decisions/title.md に上の「決定」を足す。前提 (1) のとおり docs/handover.md を直し（今の内容を読んでから）、docs/decisions/broadcast-calendar.md の JPL-01 の節に前提 (2) の扱いを1行足す。docs/logs/CHAT-1014-JPL-01.md の `## 報告` の状態を「完了」にし、末尾に `/ 続き: CHAT-1014-JPL-04` を足す（「判断が必要なこと」の2点に、この指示で扱った旨を書く）。「マージ:」の行のとおり cloudflare へ入れる（入れる前に、マージで動く自動処理と、それが今のシートで止まらない・生成物を変えないことを確かめる）

止まる条件

* 0章で JPL-03 の状態が「中断」でない、#497・#523 が Closed、または他セッションの着手中コメントがある
* 「タイトル戦」タブに JPMLリーグ の行が無い・2行以上ある、または WRC 2大会の名前が決定と違う（値を書いて止まる）
* 手順2で生成が止まる、または生成物が変わる見込みで、cloudflare への push が自動再生成を動かす（手順1〜3の記録はログに書き、マージせず判断待ちで止まる。シート・コード・生成物は直さない）
* docs/ 以外を変える必要が出た
* 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare`）が docs/handover.md 5章・docs/decisions/title.md・broadcast-calendar.md の同じ行・節を変えている、または取り込みで衝突する（同じファイルの別の箇所なら止まらずログに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-JPL-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-JPL-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1014-jpl
- ログ: https://github.com/retroeater/mj/blob/work/1014-jpl/docs/logs/CHAT-1014-JPL-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-jpl
- 確認用URL:
- マージ:
- issue: #523・#497
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 6f8f8ab2）: https://github.com/retroeater/mj-logs/tree/main/guide/6f8f8ab2

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/6f8f8ab2/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
