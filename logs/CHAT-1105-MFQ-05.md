# CHAT-1105-MFQ-05

- 着手日時: 2026-11-05
- 対象issue: #671
- ブランチ: work/1105-mfq
- 着手時HEAD: 201acc32f2

## 指示

【Claude作成】Claude Code 向け指示：title/houou/（鳳凰戦の大会ページ）の title と description に「鳳凰位」を入れる（#671。プレビューで止める） Chat-Ref: CHAT-1105-MFQ-05 マージ: 判断待ちで止まる 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/1105-mfq〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1105-mfq の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1105-mfq を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1105-mfq origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
「鳳凰位 歴代」「歴代鳳凰位」などの検索が `title/houou/`（鳳凰戦の歴代優勝者のページ）に着地するように、そのページの `<title>` と description に「鳳凰位」の語を入れる（#671 のやること 1）。
決定（2026-11-05、平野さん）

* #671 のやること 1（`title/houou/` の title・description に「鳳凰位」を入れる）をやる
* #671 のやること 2（`houou/ranking/total/` から `title/houou/` へリンクを1行足す、案 c）は採らない。理由: ランキングのページと歴代優勝者は関係ないため

前提（チャット側。平野さんの決定ではない）

* 今の生成（origin/cloudflare、要確認）: `scripts/generate_title_pages.py` の大会ページは、title が `f"{t.name} 歴代優勝者（{t.span}）"`（`page_meta()` が「 | 日本プロ麻雀連盟 | ryoei.pro」を足す）、description が `taikai_description()` の「{大会名}について、歴代優勝者（{期の範囲}）をまとめています。現タイトルホルダーは{名前}。」。全大会で共通の形
* スプレッドシートの「タイトル戦」シートの列は 大会名・slug・表示順・区分・状態 で、「鳳凰位」のような称号の列は無い（`EXPECTED_HEADERS`）
* 範囲は鳳凰戦（slug `houou`）だけ。ほかの大会（十段位・王位など）には広げない（平野さんの決定は鳳凰戦だけ。広げたほうがよいと考えたら、実装せず報告に書く）
* 文面の案（チャット側の案。実物の長さ・既存の形を見て変えてよい。最終の文面は平野さんがプレビューで決める）:
   * title: 「鳳凰戦 歴代鳳凰位・優勝者（第1期〜第42期） | 日本プロ麻雀連盟 | ryoei.pro」
   * description: 「鳳凰戦について、歴代鳳凰位（歴代優勝者、第1期〜第42期）をまとめています。現鳳凰位は白鳥翔。」
   * 見えている h1（大会名）と読み上げの語、og:title（X のカードの帯）は変えない
* 語の持ち方は、既存の形（`scripts/lib/` の共通関数・定数、ほかの大会のページの作り）で足りるか先に確かめ、最小の形にする（CLAUDE.md「コード規約」。称号の列をシートに足すのは平野さんの作業が要るので、この指示ではしない）
* `title/` の入口・期ページ・`title/search.json` は変えない。期の範囲（第1期〜第42期）は今のデータから組み立てたものをそのまま使う

手順

1. #671 の状態と直近7日のコメントを読み、着手中のコメントを書く（本文・ラベルは変えない）。未マージの `work/` ブランチが `scripts/generate_title_pages.py` の同じ関数（大会ページの title・description を組み立てる箇所）を変えている、または取り込みで衝突するなら止まる。上の前提がコードと食い違えば止まる
2. 鳳凰戦の大会ページだけ title と description に「鳳凰位」が入るように直し、検査を `scripts/tests/` に1本残す（鳳凰戦は入る・ほかの大会は今のまま）。先に、直す前のコードでその検査が落ちることを確かめる（CLAUDE.md、#310）。`python3 -m unittest discover -s scripts/tests` を流す
3. `title_pages` を再生成し（名簿・スプレッドシートの鍵が無ければ `regenerate-page.yml` を作業ブランチで手動実行）、差分が `title/houou/index.html` の `<title>`・`og` 以外の meta description・JSON-LD の説明など、意図した行だけであることを確かめる（差分の全行をログに書く）。ほかの大会のページに差分が出たら止まる
4. 文面が docs/notes/title-pages.md などのガイド文の記述と食い違えば、同じ指示の範囲で直してよい（ドキュメントのみ、成果物と別のコミット）
5. プレビュー（Workers Builds の別名 URL）で `title/houou/` の `<title>` と description を確かめ、ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push し、マージの可否と最終の文面を平野さんの判断待ちにして止まる（マージしない）。CLAUDE.md の差分のレビュー（docs/notes/self-review.md）は止まる前に行う

完了条件: 最終報告の最後の行が `Chat-Ref: CHAT-1105-MFQ-05`

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 652fd4a6）: https://github.com/retroeater/mj-logs/tree/main/guide/652fd4a6

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/652fd4a6/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/201acc32.md
