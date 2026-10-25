# CHAT-1024-VOD-03

- 着手日時: 2026-10-24
- 対象issue: #629, #173
- ブランチ: work/1024-vod
- 着手時HEAD: 070c0aa98

## 指示

【Claude作成】Claude Code 向け指示：動画の各話ページ（mtsuku・exam・wayhome・study）から VideoObject を外す（#629 の案 b）
Chat-Ref: CHAT-1024-VOD-03
マージ: 判断待ちで止まる
貼る時機: CHAT-1024-VOD-02 の完了（work/1024-vod が cloudflare にマージ済み）の後（同じセッションでよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1024-vod の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1024-vod を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1024-vod origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1024-VOD-02 の変更が origin/cloudflare に入っていなければ何もせず止まる。

## 目的
動画の各話のページはプレーヤーを埋め込んでいない（サムネイルと YouTube へのリンク）のに VideoObject を持っていて、Search Console の「動画再生ページに動画がありません」の元になりうる。各話からも VideoObject を外し、動画の構造化データを4つの番組のページから無くす（#629）。

### 決定（2026-10-24、平野さん）
- #629 は案 b: 各話のページ（mtsuku・exam・wayhome・study）からも VideoObject を外す（プレーヤーは埋め込まない）

### 前提（チャット側。平野さんの決定ではない）
- 各話の数は mtsuku 61・exam 12・wayhome 42・study 40 で、すべて VideoObject 1・og:video あり・`<iframe>` 0（CHAT-1024-VOD-01 のログの経過）。VideoObject を組むのは `scripts/lib/{mtsuku,exam,wayhome,study}.py` の `build_video_object()`、呼ぶのは各話の生成スクリプト（`generate_*_episodes.py` など。要確認）
- 外すのは VideoObject だけ。BreadcrumbList・og:type（video.other）・og:video・twitter 系は残す（og の規則は docs/decisions/seo-bing.md「埋め込みできる動画のページ（YouTube）は video.other ＋ og:video」のまま）
- 外した後に呼ばれなくなる関数（`build_video_object()`・`json_ld_html()` の VideoObject 用の部分など）は消してよい（残さない）。ただし4つの番組以外から呼ばれているものは残す
- docs/notes/video-{mtsuku,exam,wayhome,study}.md の VideoObject の記述（`VideoObject.name`・`VideoObject.description`・`isAccessibleForFree` など）を今の形に直す（紹介文・題名の規則そのものは meta・og に残るので変えない）
- #173（open、帰り道シートの列を個別ページと VideoObject に反映）は VideoObject の部分が無くなる。#173 にその旨をコメントする（状態は変えない）
- 4つの番組以外で VideoObject を出しているページがあるかは未確認（要確認）。あれば一覧にしてログと #629 に書くだけで、この指示では変えない

## 手順
1. #629・#173 と同じ論点の open issue を確かめ（クローズ済みも含めて）、#629 に他セッションの着手中コメントがあれば止まる。`git grep -n 'VideoObject'`（リポジトリ全体。docs/decisions・docs/logs・handover-archive の過去の行は除く）の結果を、4つの番組とそれ以外に分けてログに表で書く
2. 4つの番組の各話から VideoObject を外し、使われなくなった関数を消す。テストを直す（各話に VideoObject が無いこと・BreadcrumbList と og:video が残ることを確かめるテストを番組ごとに足す）。regenerate-page.yml で生成し直し、各話が VideoObject 0・BreadcrumbList 1・og:video 1 になり、差分が VideoObject の行だけであることを確かめる（変更前後の件数を番組ごとの表で書き、変わったファイルの一覧は `git diff --stat` で全件添える）。一覧4ページは変わらないことも確かめる。docs/notes と docs/decisions（mtsuku.md に決定、exam.md・wayhome.md・study.md に参照1行）を直す
3. issue: #629 に変更の要約・比較 URL・手順1の「それ以外」の一覧をコメントする（閉じるのはマージ後の本番確認のとき）。#173 に上の前提のとおりコメントする

## 止まる条件
- CHAT-1024-VOD-02 の変更が origin/cloudflare に入っていない
- #629 に他セッションの着手中コメントがある
- 各話の数が前提（mtsuku 61・exam 12・wayhome 42・study 40）から ±3 を超えて違う（新しい回の追加はこの範囲で許す）
- 各話の差分に VideoObject の行以外の変更がある
- 未マージの work/ ブランチが各話の生成スクリプトの JSON-LD を組む関数・`build_video_object()` を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く。重なる番組だけ外して進め、全部が重なるときだけ止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（判断が必要なこと: マージしてよいか）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1024-VOD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1024-VOD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0. 指示欄の末尾は指示文の最後の行（「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」）と一致。CHAT-1024-VOD-02 の変更（fb6258cb0）は origin/cloudflare の祖先
- 同じ Chat-Ref のコミット: 0件。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 4つともあり
- issue の state: #629 open（コメントは CHAT-1024-VOD-02 の決定の1件だけ。他セッションの着手中なし）、#173 open。#629 に着手中のコメントを残した
- 作業ブランチ: ローカル・リモートの work/1024-vod（fb6258cb0）は origin/cloudflare の祖先（マージ済み）。ローカルにあるので docs/notes/cloud-sessions.md「作業ブランチの用意」のとおり `git merge --ff-only origin/cloudflare` で 070c0aa98 へ進めた
- 同じ論点の issue（クローズ済みを含めて検索）: #629・#173・#620・#268（closed）のほかに無し
- 未マージの work/ ブランチで、各話の生成スクリプト・`scripts/lib/{mtsuku,exam,study,wayhome}.py` を変えているのは work/1017-lvp だけ（`lib/study.py` の `build_card_thumb_html()` の末尾〈メンバー限定の札〉と `test_study.py` の札の assert）。`build_video_object()`・JSON-LD を組む関数は変えていない → 止まらない
  - ただし `merge-tree` で試すと、この変更の後は work/1017-lvp が `scripts/lib/study.py`（消した `build_video_object()` が札の行の直後）と `scripts/tests/test_study.py`（`test_members_only` の `isAccessibleForFree` の行）でも衝突する。work/1017-lvp は今の cloudflare とも別の4ファイル（docs/decisions/live.md・live/index.html・live/pro-queen/24.html・live/pro-queen/index.html）で既に衝突している（2026-10-17 から判断待ち）。取り込むときは、study.py は札の行を残して `build_video_object()` を消す側、test_study.py は札の assert を残して `isAccessibleForFree` の行を消す側で解ける

### VideoObject の出どころ（`git grep VideoObject`、docs/decisions・docs/logs・handover-archive を除く、070c0aa98）

| 区分 | ファイル | 一致 |
|---|---|---|
| 4つの番組の各話（HTML） | mtsuku/*.html 61・exam/*.html 12・wayhome/*.html 42・study/*.html 40 | 各1 |
| 4つの番組のコード | `scripts/lib/{mtsuku,exam,study,wayhome}.py`（`build_video_object()`）、`scripts/generate_*_episodes.py` 4本（呼び出し）、`generate_{mtsuku,exam,study,video_wayhome}.py`（#620 の docstring） | — |
| 4つの番組のテスト | `test_mtsuku.py`・`test_exam.py`・`test_study.py`・`test_wayhome_links.py`・`test_wayhome_prevnext.py` | — |
| 4つの番組の docs | docs/notes/video-{mtsuku,exam,study,wayhome}.md | — |
| それ以外 | docs/new-site-design.md（新サイトの設計の記述、7行。ページではない） | 7 |

4つの番組以外で VideoObject を出しているページ: なし（一致する HTML は4つの番組の各話 155 件だけ。JS・workers にも無い）。各話の数は前提どおり（mtsuku 61・exam 12・wayhome 42・study 40）

### 変更

- 各話の `build_json_ld()`（`generate_{mtsuku,exam,study,wayhome}_episodes.py`）を BreadcrumbList だけにした。パンくずの2つ目の名前は今までの `VideoObject.name` と同じ式（exam は `episode.title`、mtsuku・study は `episode_name()`、wayhome は `f"{row.title} {row.interviewee}".strip()`）。wayhome は引数 `thumb_url` を外した。各話のヒーロー・og は自分で `resolve_hero_thumb()` を呼ぶので、サムネイルが無いときに止まる動きは変わらない
- `build_video_object()` を `scripts/lib/{mtsuku,exam,study,wayhome}.py` から消した（呼ぶのは4つの番組の各話だけだった）。`mtsuku.json_ld_html()` は一覧の ItemList・各話の BreadcrumbList・`test_script_json.py` で使うので残した。`wayhome.embed_url()` は og:video と generate_live_pages.py で使うので残した
- VideoObject に触れていた docstring・コメントを今の形に直した（自分が変えた範囲の関数・モジュールの説明だけ）
- テスト: 各話に VideoObject が無く、BreadcrumbList 1・og:video 1 があることを番組ごとに1本ずつ足した（test_mtsuku・test_exam・test_study・test_wayhome_prevnext）。VideoObject を前提にしていた assert（mtsuku の uploadDate・duration、study の VideoObject・uploadDate・isAccessibleForFree、wayhome の description の3か所目）を外すか否定に直した。修正前のコード（HEAD の worktree に新しいテストを写した）では新しい4本と wayhome_links の1本が FAIL、修正後は 1566件 OK
- docs/notes/video-{mtsuku,exam,study,wayhome}.md: `VideoObject.name`・`VideoObject.description`・`isAccessibleForFree`・`uploadDate` の記述を直し、「構造化データ（JSON-LD）: 一覧は ItemList、各話は BreadcrumbList だけ…」の1行を足した（日付つきの経緯の節は書き換えていない）
- docs/decisions: mtsuku.md に決定、exam.md・wayhome.md・study.md に参照1行

### 生成と確かめ

- regenerate-page.yml（dry_run・compare_ref `origin/cloudflare`、一覧4つと各話4つ）: run 40145130087。新旧で中身の違うファイルは各話 155 件の各1行だけ（一覧の index.html は入っていない）。違いがあるとジョブを失敗にする作りなので結論は failure（想定どおり）
- 同じワークフローを書く道筋で手動実行（各話4つ）: run 40145481805 が success、d591dccd4「chore: regenerate mtsuku/ exam/ study/ wayhome/ via GitHub Actions」を push した（158 ファイル。HTML 155 件はどれも `<script type="application/ld+json">{"@context": "https://schema.org", "@type": "VideoObject", …` の1行の削除だけで、追加の行は0。残り3つは sitemap-exam・mtsuku・wayhome の lastmod〈ワークフローが導出、各話が 2026-10-25 に〉）

| 番組 | 各話 | 変更前 VideoObject | 変更後 VideoObject | BreadcrumbList（前→後） | og:video（前→後） | `<iframe>` |
|---|---|---|---|---|---|---|
| mtsuku | 61 | 61 | 0 | 61 → 61 | 61 → 61 | 0 |
| exam | 12 | 12 | 0 | 12 → 12 | 12 → 12 | 0 |
| wayhome | 42 | 42 | 0 | 42 → 42 | 42 → 42 | 0 |
| study | 40 | 40 | 0 | 40 → 40 | 40 → 40 | 0 |

- 一覧4ページ（mtsuku・exam・wayhome・study の index.html）: origin/cloudflare との差分なし（ItemList 1 のまま）
- プレビュー（Workers Builds、d591dccd4、success）: 各話4件（mtsuku/ZeFtFG2ov_Q・exam/-PoeE0Bm2sE・wayhome/Yjp_PKHvldw・study/1QVc_wj9T-c）と一覧4ページがリポジトリの版とバイト一致。各話は BreadcrumbList 1 だけ、一覧は ItemList 1 だけ

### 変わったファイル（`git diff --stat origin/cloudflare HEAD`、docs/logs を除く、全件）

```
 docs/decisions/exam.md                 |   4 ++++
 docs/decisions/mtsuku.md               |   5 +++++
 docs/decisions/study.md                |   4 ++++
 docs/decisions/wayhome.md              |   4 ++++
 docs/notes/video-exam.md               |   3 ++-
 docs/notes/video-mtsuku.md             |   7 ++++---
 docs/notes/video-study.md              |   5 +++--
 docs/notes/video-wayhome.md            |   5 +++--
 exam/-PoeE0Bm2sE.html                  |   1 -
 exam/98GKK3KKYNk.html                  |   1 -
 exam/Esz7Mloebaw.html                  |   1 -
 exam/JTOTffVFqTg.html                  |   1 -
 exam/Lp8m7sAWT_k.html                  |   1 -
 exam/RfZxTnSGnBw.html                  |   1 -
 exam/eKMCpGQKTYk.html                  |   1 -
 exam/gyYpx9xfrLA.html                  |   1 -
 exam/ix9xGkF5pJs.html                  |   1 -
 exam/jal-jnnwJb4.html                  |   1 -
 exam/kzKMbbNvAcQ.html                  |   1 -
 exam/lGPjKHDAlzw.html                  |   1 -
 mtsuku/2FOsXsIqTAA.html                |   1 -
 mtsuku/3AAAF5661G8.html                |   1 -
 mtsuku/6NNrwkwSY4o.html                |   1 -
 mtsuku/6vXHmiBj--c.html                |   1 -
 mtsuku/85vyHIMV_bk.html                |   1 -
 mtsuku/A8h2RgjdS9Q.html                |   1 -
 mtsuku/A_7xTHnDsa0.html                |   1 -
 mtsuku/CtDQ5NjWSN0.html                |   1 -
 mtsuku/DCo2KDamq3o.html                |   1 -
 mtsuku/ElS68JMR6hU.html                |   1 -
 mtsuku/GNCqIFbBV_A.html                |   1 -
 mtsuku/HS7-Pzt7ER0.html                |   1 -
 mtsuku/HgJy7ik0Mno.html                |   1 -
 mtsuku/I-vp4otBNlE.html                |   1 -
 mtsuku/JCOU7JAtX9o.html                |   1 -
 mtsuku/Jaqa7GSADGw.html                |   1 -
 mtsuku/L9i7xb_vQcc.html                |   1 -
 mtsuku/LnSprcpb-vs.html                |   1 -
 mtsuku/NJpTd5674gM.html                |   1 -
 mtsuku/Nz9U6-LN25A.html                |   1 -
 mtsuku/PDosFq1iPpE.html                |   1 -
 mtsuku/PRGvzT03Bqo.html                |   1 -
 mtsuku/PdjCcGfO3Lk.html                |   1 -
 mtsuku/PfhwIrrbxyA.html                |   1 -
 mtsuku/SynIHxAbJ-g.html                |   1 -
 mtsuku/U_Ld5wK_sFo.html                |   1 -
 mtsuku/VPWAMK0IYm4.html                |   1 -
 mtsuku/VokQ75dRDe4.html                |   1 -
 mtsuku/WDhXSMjPL7s.html                |   1 -
 mtsuku/WsBTyiXi7hE.html                |   1 -
 mtsuku/XABpqoyq9xE.html                |   1 -
 mtsuku/Y7iwKGCJrcE.html                |   1 -
 mtsuku/ZeFtFG2ov_Q.html                |   1 -
 mtsuku/_4goLG5hfCI.html                |   1 -
 mtsuku/aHYTmlSv-1o.html                |   1 -
 mtsuku/ajudu3HtbVY.html                |   1 -
 mtsuku/axj88qHRd6A.html                |   1 -
 mtsuku/bNPj2Xh_MKg.html                |   1 -
 mtsuku/dOi0lO1Q_oI.html                |   1 -
 mtsuku/dXxTEgDutcc.html                |   1 -
 mtsuku/f7__Vg7EUNE.html                |   1 -
 mtsuku/g5Gsj2SOf4g.html                |   1 -
 mtsuku/hjytEWke6zg.html                |   1 -
 mtsuku/i27fJ7i_LfE.html                |   1 -
 mtsuku/iNeMLvZhIf8.html                |   1 -
 mtsuku/jb12pGzUg80.html                |   1 -
 mtsuku/kZbGqbKXG64.html                |   1 -
 mtsuku/lG9c_Am70ek.html                |   1 -
 mtsuku/llZtbAU8gto.html                |   1 -
 mtsuku/lwcu2uTGCic.html                |   1 -
 mtsuku/nLeGJz-nt-E.html                |   1 -
 mtsuku/nOAcTjVjxH0.html                |   1 -
 mtsuku/poVkDif7aes.html                |   1 -
 mtsuku/s4KFDtLIIRs.html                |   1 -
 mtsuku/v0EI5aVIYMs.html                |   1 -
 mtsuku/vSduV7-kLls.html                |   1 -
 mtsuku/vVgdlR8u3H8.html                |   1 -
 mtsuku/vtE90-iTtRo.html                |   1 -
 mtsuku/wfHEXQ3Eg3U.html                |   1 -
 mtsuku/wg9JOw4Sb68.html                |   1 -
 mtsuku/yILN6DE-wew.html                |   1 -
 scripts/generate_exam.py               |   2 +-
 scripts/generate_exam_episodes.py      |   9 ++++-----
 scripts/generate_mtsuku.py             |   2 +-
 scripts/generate_mtsuku_episodes.py    |   9 ++++-----
 scripts/generate_study.py              |   2 +-
 scripts/generate_study_episodes.py     |   9 ++++-----
 scripts/generate_video_wayhome.py      |   4 ++--
 scripts/generate_wayhome_episodes.py   |  20 +++++++-------------
 scripts/lib/exam.py                    |  17 +----------------
 scripts/lib/mtsuku.py                  |  24 +++---------------------
 scripts/lib/study.py                   |  25 +++----------------------
 scripts/lib/wayhome.py                 |  25 ++++---------------------
 scripts/tests/test_exam.py             |   7 +++++++
 scripts/tests/test_mtsuku.py           |  11 ++++++-----
 scripts/tests/test_study.py            |  14 +++++++++-----
 scripts/tests/test_wayhome_links.py    |   4 ++--
 scripts/tests/test_wayhome_prevnext.py |   7 +++++++
 sitemap-exam.xml                       |  24 ++++++++++++------------
 sitemap-mtsuku.xml                     | 122 +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++-------------------------------------------------------------
 sitemap-wayhome.xml                    |  84 ++++++++++++++++++++++++++++++++++++++++++------------------------------------------
 study/1QVc_wj9T-c.html                 |   1 -
 study/1VvnRLtH-TQ.html                 |   1 -
 study/1pR-jH-8TTo.html                 |   1 -
 study/21s7Jp2k8Jw.html                 |   1 -
 study/44nvOE_uzQw.html                 |   1 -
 study/631Orl1nAcY.html                 |   1 -
 study/8DA7hhmgpSg.html                 |   1 -
 study/ATZYuEI9xxs.html                 |   1 -
 study/DwZ9Ar0Mf2I.html                 |   1 -
 study/Ec9ysd6onvM.html                 |   1 -
 study/F4JBz9uIGf0.html                 |   1 -
 study/I-_naO3ZRvs.html                 |   1 -
 study/Kl1pVrnC2og.html                 |   1 -
 study/OzhWd7bziaw.html                 |   1 -
 study/PfzblnV6sxQ.html                 |   1 -
 study/SDBm9RdMHsQ.html                 |   1 -
 study/U9scMSs91MQ.html                 |   1 -
 study/V_fiubA5lPk.html                 |   1 -
 study/_pubksWpBM0.html                 |   1 -
 study/aqYdaUGysf4.html                 |   1 -
 study/bhSc3uczr7U.html                 |   1 -
 study/cv85Z0_tL9U.html                 |   1 -
 study/fjc9JM3oZNA.html                 |   1 -
 study/hDQ44guKvpA.html                 |   1 -
 study/hZJKtrU0g_Q.html                 |   1 -
 study/izEdhkeR_pY.html                 |   1 -
 study/kR_5Dq3FGI0.html                 |   1 -
 study/lBg2N4wewto.html                 |   1 -
 study/mfHiDaQiUlc.html                 |   1 -
 study/mqYX_6erG04.html                 |   1 -
 study/p4IcH0-7iYw.html                 |   1 -
 study/q6HE5IiiZzs.html                 |   1 -
 study/r0jqLa9_H5Q.html                 |   1 -
 study/rGNZWAdpk_o.html                 |   1 -
 study/rrBlY_X4lFA.html                 |   1 -
 study/sXrbaLpP5rY.html                 |   1 -
 study/tahEI_Q93n4.html                 |   1 -
 study/ugnkkgWmLtI.html                 |   1 -
 study/uhQ0j1l6xYo.html                 |   1 -
 study/zkj1p7o2Ooo.html                 |   1 -
 wayhome/0OPca1Bl5xk.html               |   1 -
 wayhome/0R4-myf-D2g.html               |   1 -
 wayhome/2Bn3SktouP4.html               |   1 -
 wayhome/6WAPjcxT78A.html               |   1 -
 wayhome/6yiTh0oGwjo.html               |   1 -
 wayhome/76OsWTSSnso.html               |   1 -
 wayhome/8w7SW0XgX64.html               |   1 -
 wayhome/9drvti0iySM.html               |   1 -
 wayhome/AvqcVlrwU4g.html               |   1 -
 wayhome/E_R3Kj0cuno.html               |   1 -
 wayhome/FrZWVou_Z6g.html               |   1 -
 wayhome/GjzVKdJ5jSM.html               |   1 -
 wayhome/MpdXIrcVPlo.html               |   1 -
 wayhome/NzbQhNLRS-c.html               |   1 -
 wayhome/OkKAh8yP8Uk.html               |   1 -
 wayhome/OoK3O2BCm8M.html               |   1 -
 wayhome/PPtNFoOwtyc.html               |   1 -
 wayhome/RVXat9zRycQ.html               |   1 -
 wayhome/S4m-_ZTP52I.html               |   1 -
 wayhome/T286EG9Kv-4.html               |   1 -
 wayhome/UtxpVoWy2GY.html               |   1 -
 wayhome/WDRBtJJME7I.html               |   1 -
 wayhome/WztsfHBisUM.html               |   1 -
 wayhome/Yjp_PKHvldw.html               |   1 -
 wayhome/atD2e-NgnKw.html               |   1 -
 wayhome/d8N1SwtQtZU.html               |   1 -
 wayhome/dxkS1JZW-U0.html               |   1 -
 wayhome/e0BTqZbxB_A.html               |   1 -
 wayhome/fQnv5PivP30.html               |   1 -
 wayhome/kf3RaFtz8o8.html               |   1 -
 wayhome/lPYh4hrZe50.html               |   1 -
 wayhome/lrIjJzddSGQ.html               |   1 -
 wayhome/m8OoOeMOWNc.html               |   1 -
 wayhome/mj3hZ_udzKM.html               |   1 -
 wayhome/neHYj-A9Z7c.html               |   1 -
 wayhome/o28svvuVI0M.html               |   1 -
 wayhome/scmXoh6zpKY.html               |   1 -
 wayhome/tXbbwFA-bjg.html               |   1 -
 wayhome/uzYFufi6liQ.html               |   1 -
 wayhome/vVin6qbFY8s.html               |   1 -
 wayhome/xLdmYKsyd_4.html               |   1 -
 wayhome/yVTZqwKpLBY.html               |   1 -
 183 files changed, 210 insertions(+), 403 deletions(-)
```

### issue

- #629: 変更の要約・比較 URL・「それ以外」（なし。docs/new-site-design.md の記述だけ）をコメントした（閉じるのはマージ後の本番確認のとき）
- #173: VideoObject への反映が無くなった旨をコメントした（状態は変えていない）

## 報告

- 状態: 判断待ち / 続き: CHAT-1025-VOD-04
- ブランチ: work/1024-vod
- ログ: https://github.com/retroeater/mj/blob/work/1024-vod/docs/logs/CHAT-1024-VOD-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1024-vod
- 確認用URL: プレビューあり（URL は最終報告）。確認したページは各話4件と一覧4ページ。印: 各話（例 mtsuku/ZeFtFG2ov_Q.html）の `<head>` の JSON-LD が BreadcrumbList の1行だけ（`"@type": "VideoObject"` が無い）
- マージ: 未（平野さんの判断待ち）
- issue: #629（変更をコメント。マージ後の本番確認で閉じる）、#173（コメント。状態は変えていない）
- 判断が必要なこと:
  - work/1024-vod を cloudflare にマージしてよいか
  - work/1017-lvp（判断待ち）はこの変更の後 `scripts/lib/study.py`・`scripts/tests/test_study.py` でも衝突する（解き方は経過に書いた）。あちらを進めるときの指示に含めるか
- 未確認の項目: 本番への反映（マージ前のため）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 3b42da42）: https://github.com/retroeater/mj-logs/tree/main/guide/3b42da42

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/3b42da42/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/070c0aa9.md
