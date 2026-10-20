# CHAT-1019-WRR-12

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1019-wrr12
- 着手時HEAD: 991a26522

## 指示

【Claude作成】Claude Code 向け指示：WRR-02〜11 の振り返りの申送り（質問欄の固定の問いの範囲と大会ごとの問い、告知の前後の変更、新しい大会の確かめの観点、告知文の書き始め）をガイド文に足す
Chat-Ref: CHAT-1019-WRR-12
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/notes/ask-worker.md・docs/notes/page-announcement.md・docs/notes/league-pages-compare.md〈または手順3で選んだ文書〉だけ。コード・生成物・シートは変えない）
貼る時機: いつでも（CHAT-1019-WRR-11 は完了・マージ済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrr12 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-wrr12 を origin/cloudflare から作る。rebase・force push はしない。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

WRC-R リーグ（wrcr/）の作成・公開・告知・質問欄の直し（CHAT-1019-WRR-02〜11）の振り返りと、そのレビュー（2026-10-20）で出た知見を、次のチャットと Code が読む文書に残す。

### 振り返りで分かったこと（事実）

- 鳳凰戦の「現在の鳳凰位は？」が、CHAT-1019-AIC-07 のマージ（1回目の指示に `currentLine()` を足した）から WRR-10 の直しまで「答えられません。」になっていた（D1 の記録: id 201・205 は league 型・第43期前期）。AIC-07 は docs/notes/ask-worker.md「本番で送る固定の問い」の5問を送っておらず、自分の改善に合わせた10問を送っていた。固定の問いの決まりの範囲が「AI への指示（`ANSWER_SYSTEM`）・一覧の切り方・並べ替え」で、1回目の指示の変更が対象か読み取れなかった
- WRR-08 のチャット側の指示も固定の5問を書かず別の4問を指定した。Code が文書の決まりを優先して5問を送り、不具合が見つかった
- 固定の5問はすべて鳳凰戦で、女流桜花・WRC・WRC-R の答えは固定の問いで見ていない。WRC-R の同率（通過回数4回の9人）は、WRR-06 の本番の確かめでたまたま同率の部門を聞いて見つかった
- WRR-08 の Worker の変更は 2026-10-19 21:58 に cloudflare に入り、告知の投稿は 22:00、WRR-10 の Worker の変更は 22:4x。告知で「AI自然言語検索（β版）」を挙げた機能を、投稿の直前・直後に変えていた（結果は本番で確かめて問題なし）
- 告知文の最初の案が、平野さんが直して投稿した WRC リーグの投稿文（CHAT-1019-WRC-18 の記録）とずれていた（「AI検索」・「を1ページで」なし）

### 申送り（知見 → 選んだ層 → 理由）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 固定の問いの決まりの範囲が狭く、1回目の指示を変えた AIC-07 が送らなかった | ガイド文（docs/notes/ask-worker.md「本番で送る固定の問い」の範囲を「`workers/ask/src/` を変える指示すべて」に広げる） | 本番の Claude の答えはテストで見られず、指示を作る側と Code が読む決まりで守る。範囲の書き方の穴なので、その文を直す |
| 固定の問いが鳳凰戦だけで、他の大会の答えの崩れを見られない | ガイド文（同じ節に「大会ごとの問い」の表を足す。その大会の data・指示・型に関わる変更のときに、固定の5問に加えて送る） | 1日の上限（IP 10問）があり、全大会の問いを毎回送ると自分の確かめが残らない。関わる大会の分だけ送る形にする |
| 告知した機能を、投稿の直前・直後に本番で変えた | ガイド文（docs/notes/page-announcement.md「進め方」に1行） | 告知の手順の中の順番の決まりで、センサーにできない |
| 新しい大会の作成で、同率・境目の値の答え方を考えず、公開後に見つかった | ガイド文（新しい大会を足すときの確かめの観点がある文書に1行） | 作成の指示を作る側が読む観点の一覧に足す |
| 告知文の最初の案が、前の大会で実際に投稿した文とずれた | ガイド文（docs/notes/page-announcement.md の投稿文の節に1行） | 投稿文の実例は同じ文書にあるので、書き始めの手順として足す |
| 指示の前提の数字（同率の人数）を確かめずに書いた。指示を書く前に関係する文書（ask-worker.md）を読まなかった | 層なし（既にある決まり〈指示の雛形の「前提（要確認）」、chat-routines.md の着手前の確かめ〉に反しただけ） | 新しい決まりを足しても同じ。WRR-01 の振り返りに続き2回目なので、ログに残すだけにする |

## 手順

1. docs/notes/ask-worker.md「変更の指示を作るときの前提: 本番で送る固定の問い」:
   - 範囲の文を「`workers/ask/src/` を変える指示（1回目・2回目の指示、型と引数、検索、一覧の切り方・並べ替えを含む）では」の趣旨に直す
   - 「大会ごとの問い」の表を足す（その大会の data・指示・型・検索に関わる変更のとき、固定の5問に加えて送る。期待は 2026-10-19 の本番の答え〈WRR-08・10 のログ〉）。案（実物と期待を WRR-10 のログで確かめて直してよい）:

     | 大会 | 問い | 見る点 |
     |---|---|---|
     | 女流桜花 | 現女流桜花は？ | 清水香織（第20期）。優勝3回（第5・19・20期） |
     | WRC | 現タイトルホルダーは？ | WRC リーグの最新の決勝の優勝者。優勝の期と回数を取り違えない |
     | WRC | 通過回数が2番目に多いのは？ | 10回で3人が並ぶ（客野直・山脇千文美・末続ヒロトシ） |
     | WRC-R | 現タイトルホルダーは？ | 小川尚哉（第7期） |
     | WRC-R | 通過回数がいちばん多いのは？ | 4回で9人が並ぶ（大和さんほか） |

     WRC の「現タイトルホルダーは？」の期待は、実物（決勝の最大の期の優勝者）を data で確かめて書く
   - 1日の上限（IP 10問）との関係を1行書く（固定の5問＋関わる大会の問いで足りなければ、自分の確かめの問いを減らす）
2. docs/notes/page-announcement.md:
   - 「進め方」に「告知の投稿の前後は、告知で挙げる機能（質問欄など）を変えるマージを入れない。直しが要れば投稿の翌日以降に回す（2026-10-19、WRR-08・10 で投稿の直前・直後に Worker を変えた）」の趣旨を1行（文言は案。平野さんの決定ではなく振り返りの提案と分かるように「提案（2026-10-20）」を付ける）
   - 投稿文の節に「投稿文の案は、前の大会で平野さんが実際に投稿した文（この文書の実例）から書き始め、違う所だけ替える」の趣旨を1行
3. 新しい大会のページを足すときの確かめの観点が書いてある文書（league-pages-compare.md か、static-generation.md・houou-top.md など。実物を探してどこにしたかログに書く。無ければ league-pages-compare.md の末尾）に「同率・境目の値（ランキングの下限・上限の境、同じ値が並ぶ部門）で、ページと質問欄の答えがどうなるかを作成の時点で確かめる」の趣旨を1行
4. 足す前後のバイト数をファイルごとにログに書く。容量の検査（`check-assets.yml` と同じもの）を手元で回し、警告域・上限との差を書く。上限を超えるなら書かずに止まる。同じ趣旨の記述が既にあれば置き換え・拡張してよい（処理をログに書く）。矛盾する記述があれば止まる
5. CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。check-assets の成否を書く

## 止まる条件

- 0章の確かめに当たる
- 追記先に矛盾する記述がある。容量の検査が上限に入る
- 変更が、冒頭の「マージ:」の行の文書のほかに出る
- 未マージの work/ ブランチが追記先の同じ節を変えていて、取り込みで衝突する（同じファイルの別の箇所なら止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、手順2の告知の前後の決まり（提案として書いた）を平野さんが決まりとして確定するか、を書く
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-12 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件
- 作業ブランチ: work/1019-wrr12 はローカル・リモートとも無し → `git checkout -b work/1019-wrr12 origin/cloudflare`（991a26522）
- 0. 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。ガイド文に規則を足す指示で「知見 → 選んだ層 → 理由」の表がある
- 未マージの work/ ブランチで ask-worker.md・page-announcement.md・league-pages-compare.md を変えるものは無し
- 1. docs/notes/ask-worker.md「変更の指示を作るときの前提: 本番で送る固定の問い」: 範囲の文を「`workers/ask/src/` を変える指示（1回目・2回目の指示、型と引数、検索、一覧の切り方・並べ替えを含む）では」に置き換え（理由を括弧で）。節の末尾に「大会ごとの問い」の段落と表（5行）と、1日の上限（IP 10問）に入りきらないときは自分の確かめの問いを減らす1行を足した。WRC の「現タイトルホルダーは？」の期待は data-jwrc.json の決勝の最大の期（第19期）の優勝者 紺野真太郎（WRR-05 の本番の答えと同じ）。女流桜花の見る点は WRR-10 の本番の答え（清水香織・第5・19・20期）。矛盾する記述は無し
- 2. docs/notes/page-announcement.md:「進め方」の6の後に「提案（2026-10-20、振り返り WRR-12。平野さんの決定ではない）: 告知の投稿の前後は…」の1行。「投稿文の型」の箇条に「案は、前の大会で平野さんが実際に投稿した文（下の実例）から書き始め、違う所だけ替える」の1行
- 3. 確かめの観点の置き場所: 新しい大会を足すときの観点を集めた節は見つからなかった（houou-top.md・jwrc-top.md・wrcr-top.md の「確かめ方」は大会ごと、static-generation.md はページの生成の仕組み）。大会の差の一覧の docs/notes/league-pages-compare.md の末尾（「比べ方」の箇条）に1行足した
- 4. バイト数: ask-worker.md 45434 → 46851、page-announcement.md 11153 → 11735、league-pages-compare.md 10508 → 10856（3つとも check-assets の上限・警告域の対象外）。対象の4文書は変えていない: CLAUDE.md 24883（警告域 26624 まで 1741・上限 28672 まで 3789）、handover.md 22937（3687・5735）、chat-side-operations.md 21976（4648・6696）、instruction-template.md 14325（警告域 14336 まで 11・上限 16384 まで 2059）。`scripts/check_asset_limits.py` はすべて OK
- 5. 変更は docs/logs と docs/notes の3文書だけ。push 直前に再 fetch して祖先を確かめ `git push origin work/1019-wrr12:cloudflare`（f2ff236cf..b7212ecfa）。check-assets（`check-assets.yml`）は走らない: push の paths で `docs/**` を外していて（handover.md・chat-side-operations.md・instruction-template.md だけ戻す）、今回の変更はどれにも当たらない

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-wrr12
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-WRR-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-wrr12
- 確認用URL: なし
- マージ: 済（b7212ecfa）
- issue: なし
- 判断が必要なこと:
  - docs/notes/page-announcement.md「進め方」の「提案（2026-10-20、振り返り WRR-12。平野さんの決定ではない）: 告知の投稿の前後は、告知で挙げる機能（質問欄など）を変えるマージを入れない。直しが要れば投稿の翌日以降に回す」を、決まりとして確定するか（確定なら「提案」の印を外し、docs/decisions に足す）
  - 仮置き: 新しい大会の確かめの観点の置き場所を league-pages-compare.md の末尾（「比べ方」）にした（観点を集めた節が見つからなかった）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj eddf9d00）: https://github.com/retroeater/mj-logs/tree/main/guide/eddf9d00

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/eddf9d00/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/98282ed8.md
