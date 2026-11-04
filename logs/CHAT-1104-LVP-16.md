# CHAT-1104-LVP-16

- 着手日時: 2026-11-04
- 対象issue: #362
- ブランチ: work/1104-lvp
- 着手時HEAD: 96e0bc84b

## 指示

【Claude作成】Claude Code 向け指示：live/ の見た目の直し（行の日付・題・検索欄の文と余白）を条件つきでマージし、第24期プロクイーン決定戦の最終日が出ない理由を調べる Chat-Ref: CHAT-1104-LVP-16 マージ: 承認済み（チャットで）。下の「止まる条件」に当たらなければ、プレビューを見ずにマージしてよい（シートは変えない） 貼る時機: いつでも（毎朝の取り込み〈update-live-channel、04:00 JST〉の直後の30分は避ける） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1104-lvp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1104-lvp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-lvp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。 作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
公開した live/（#362）を、平野さんが本番で見て決めた形に直す。あわせて、第24期プロクイーン決定戦の最終日が個別ページに出ない理由を調べ、平野さんが【3】に入れる値を示す。告知動画は、この直しの後に撮る。
決定（2026-11-04、平野さん）

* 対局の行（トップと期のページ）の日付は、その対局の最後の配信日に「更新」を付けた形（例「2026年1月31日更新」）にし、「配信」と本数を外す（今は最初の配信日と本数「2026年1月7日配信・4本」。第42期鳳凰戦の決定戦は4日分あるのに1日分に見えるため）
* 大会のページの期の行は「2026年1月31日更新」の形にし、対局数・本数を外す（今は「2026年1月31日更新・1対局・4本」）
* トップの検索欄の案内の文を「大会・選手・実況・解説等で検索」にする（今は「タイトル戦・対局者・実況・解説・日付等で検索」）
* トップの検索欄と、その下の1行目の行がくっついて見えるので、間を空ける
* トップの題（`<title>`・og:title）を「放送対局 | ryoei.pro」にする（2026-10-16 の B8「放送対局 | 番組 | ryoei.pro」を置き換える）
* iPhone の実機での見え方は確かめた（OK）。X のカードは、トップ・大会・期が「放送対局」の画像、個別ページが動画のサムネイルになっていることを確かめた
* 下の「止まる条件」に当たらなければ、プレビューを見ずに cloudflare へマージしてよい

前提（チャット側。平野さんの決定ではない）

* 層1（`data/live_channel_raw.jsonl`、2026-11-04 の cloudflare）の第24期プロクイーン決定戦の最終日は2本: `WnnCehyq0yU`（「限定」Y、5時間59分、題名に【メンバー限定】が無い）と `y4v0by0gW_Y`（「限定」N、1時間37分）。どちらも配信開始 2026-10-31。本番の `live/pro-queen/24/f.html` には初日・二日目だけが出ている（チャット側が生成物で見た）。【2】【3】の値は見ていない。CHAT-1016-LVP-02 より後に増えた行で【3】の掲載が空欄（未確認）のまま、または題名に【メンバー限定】が無いため【2】の判定が他のメンバー限定版と違う、のどちらかと見ている（要確認）
* 第42期鳳凰戦の決定戦は、個別ページ `live/houou/42/f.html` に4日分が出ている（行の日付が初日のため1日分に見えていた。上の決定で直る）
* 期の行の日付・本数の書き方は `scripts/generate_live_pages.py` の行の部品（要確認）。日付の検索（`data-info`）は今の形のまま両方の書き方で引ける（docs/notes/live-page-design.md「2-8」）
* 告知動画の制作はまだ始めていない（docs/notes/page-announcement.md の告知の期間の決まりには当たらない）

手順

1. #362 に着手中のコメントを書く（本文・状態・ラベルは変えない）。【2】【3】を読み、第24期プロクイーン決定戦の6本（`VeNmTHHuYZI`・`8B1vpKcr228`・`-K8QmXaw8uU`・`Wqn3RAv8RLE`・`WnnCehyq0yU`・`y4v0by0gW_Y`）の【2】の判定と【3】の値を表にし、最終日が出ない理由を書く。あわせて、公開している大会で【3】の掲載が空欄（未確認）の行と、「限定」Y なのに題名に【メンバー限定】が無い行を全件の表にし、平野さんが【3】に入れる値の案を付ける。規則（【2】の判定）を直すべきかの見立ても書く（この指示では直さない）
2. 上の決定どおり直す（使われなくなる部品・CSS は消す）。トップ（検索前・検索中・0件）・大会・期・個別ページを 375px と 1280px の明・暗で撮る。全ページを再生成し、テストを回す。docs/notes/live-page-design.md の行の形・題・検索欄の記述と、2-1 付近の古い共有ボタンの記述（CHAT-1104-LVP-15 の報告）を直す
3. 止まる条件に当たらなければ、CLAUDE.md「ブランチ運用」のとおり cloudflare へマージする。マージ後、Workers Builds の check-run の結果と、本番の `/live/`・`/live/houou/`・`/live/houou/42.html` の行の日付と題を確かめる（CLAUDE.md「判断・作業の原則」の確認範囲で書く）。上の「決定」を docs/decisions/live.md に足す

止まる条件

* 生成物の差分が、live/ のページ（行の日付・題・検索欄の文と余白）、`assets/live.js`・`style.css` の live/ の部分、テスト、文書のほかに出る（その差分を書いて、マージせずに止まる）
* pro/ の放送対局の欄が変わる（pro/ が live/ の行の日付を使っていれば、その差分を書いてマージせずに止まる）
* テストが落ちる（今回の変更で落ちると分かっているテストは直してよい。無関係な失敗は原因をログに書いて進めてよい）
* 未マージの work/ ブランチが、`scripts/generate_live_pages.py`・`assets/live.js`・`style.css` の live/ の部分・docs/notes/live-page-design.md の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* マージ後の check-run が今回の変更で失敗する（原因を書いて止まる。無関係な失敗は原因をログに書いて残りの確かめを進めてよい）
* #362 に他セッションの着手中コメントがある
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順1の表（最終日が出ない理由、未確認の行と【3】に入れる値の案、題名に【メンバー限定】が無い行、規則の見立て）を書く
* マージは冒頭の「マージ:」の行のとおり（止まる条件に当たったらマージしない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-LVP-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-LVP-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `CHAT-1104-LVP-16` のコミットは0件。`LVP` は同じセッションの続き
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 作業ブランチ: origin/work/1104-lvp は cloudflare にマージ済み。ローカルの work/1104-lvp は origin/cloudflare の祖先なので `git merge --ff-only origin/cloudflare` で進めた
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。着手は 2026-11-04 23:30 JST

- #362 に他セッションの着手中のコメントが無いことを確かめ、着手中のコメントを書いた
- 【2】【3】の読み取り（一時の出力 9267b059b で `--check` から出し、反映後に戻した。run 41848466943）: 第24期プロクイーン決定戦の6本、【3】の掲載が空欄の行（686行）、【2】の限定が Y で題名に【メンバー限定】が無い行（7行）。表は `## 報告` の「判断が必要なこと」
  - 最終日が出ない理由: 最終日の2行（4344 `WnnCehyq0yU`・4348 `y4v0by0gW_Y`）は【3】の掲載が空欄で、生成は「未確認」として飛ばしている（2026-10-17 の決定〈CHAT-1017-LVP-04〉で、掲載が空欄の行は N で埋めず未確認として数える）。【2】の判定は正しい: `WnnCehyq0yU` は題名に【メンバー限定】が無いが、【2】の限定は Y（層1の限定はメンバー限定の再生リスト〈UUMO〉に載っているかで決まり、題名は見ない。`fetch_live_channel_raw.py`）。`y4v0by0gW_Y` の冒頭も Y（同じ大会・期・配信日にメンバー限定の生放送があるので `opening_ids()` が付けた）。初日・二日目と同じ形で、掲載を入れれば載る
  - 掲載が空欄の686行は、すべて公開している大会の行。643行は麻雀日本シリーズ・女流プロ麻雀日本シリーズ（2026-10-16 の決定で公開の後に回した）、10行は小島武夫杯帝王戦（2026-10-17 の決定で #590 に回した）。残る33行は毎日の追記で増えた行（2026-10-17〜11-03 の配信が中心）
- 見た目の直し（28673e2ed・1bedc53a8）:
  - 対局の行（トップ・期）の2行目を「<最新の日付>更新」にし、「配信」と本数を外した。日付はその対局の動画の最新の日付（`Page.updated`。大会のページの期の行の「更新」と同じ決め方）
  - 大会のページの期の行は「<最新の日付>更新」だけにし、対局数・本数を外した（メンバー限定の札は残した）
  - トップの題を「放送対局 | ryoei.pro」、検索欄の案内を「大会・選手・実況・解説等で検索」にし、検索欄と面の間を 16px 空けた（`.mj-live-n-search ~ section`）
  - 行の日付を最新の日付にしたところ、トップの並び（最初の日付の降順）と行の日付が食い違い、隣り合う行の日付が逆になる箇所が118か所できた。トップだけ、行に出す日付（`Page.updated`）の降順に並べ替えた（1bedc53a8）。pro/ の放送対局の欄と個別ページの「他の対局」は今のまま最初の日付の順（`page_date_order`）にした（pro/ を変えないため。判断が必要なことに書いた）
  - テスト: `RowDateTest`（2日の決定戦で「2026年1月31日更新」になり、最初の日付と本数が出ない）、`TitleTest` の題、生成済みのトップの行の日付が降順であることの検査を足した。どれも直す前のコード・生成物では落ちる
- live/ の本実行（run 41849175130・41849771213、d89233167・53919e317）: 変わったのは live/ の177ページ（トップ・大会・期）だけ。個別ページは変わらない。本番の例: `live/houou/index.html` の第42期は「2026年1月31日更新」、`live/houou/42.html` の決定戦も「2026年1月31日更新」
- 撮影（ローカルの http.server、375px と 1280px の明・暗）: トップ（検索前・「佐々木寿人 十段戦」で検索中・0件）、大会 `live/houou/`、期 `live/houou/42.html`、個別 `live/houou/42/f.html`
- 文書（30612b576）: docs/notes/live-page-design.md の行の形・題・検索欄の文言を直し、古い共有ボタンの記述を「置かない（LVP-06 で外した）」にした
- テスト: `python3 -m unittest discover -s scripts/tests` は全件 OK
## 報告

- 状態: 作業中
- ブランチ: work/1104-lvp
- ログ: https://github.com/retroeater/mj/blob/work/1104-lvp/docs/logs/CHAT-1104-LVP-16.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-lvp
- 確認用URL: なし
- マージ: 未
- issue: #362
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bb265e15）: https://github.com/retroeater/mj-logs/tree/main/guide/bb265e15

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bb265e15/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/96e0bc84.md
