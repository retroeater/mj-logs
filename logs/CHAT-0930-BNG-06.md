# CHAT-0930-BNG-06

- 着手日時: 2026-09-30
- 対象issue: #486（コメント）
- ブランチ: work/0930-bng
- 着手時HEAD: 465b0617

## 指示

【Claude作成】Claude Code 向け指示：BNG のチャット（Bing 関連、2026-09-30）の申送り — 決定の遡り記入、文書の規則の追記、issue への記録 Chat-Ref: CHAT-0930-BNG-06 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/0930-bng を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/0930-bng origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-BNG-05 のログの `## 報告` を読み、マージ済みでなければ止まる。

目的
BNG-01〜05（#126 のクローズ、#486 の起票と決定、#5 の再オープン）の振り返りで得た知見のうち、今後の開発と指示文づくりに効くものを書き残す。コードは変えない。変更は docs/ 配下（logs・decisions・notes・handover・instruction-template）と issue のコメントのみ。
決定（2026-09-30、平野さん）

* 振り返りの知見を申送りとして書き残す（この指示）。
* 変更は docs/ 配下と issue 操作のみ。マージ: 承認済み（チャットで）。push が権限判定で拒否されたら、別の手段を試さずに止まる。

前提（チャット側。平野さんの決定ではない）

* 書き場所と文面はチャット側の案。追記先の現在の内容を読み、同じ趣旨の記述があれば新しく足さず既存の記述を最小限に直す。矛盾していてどちらが正か判断が要るときだけ止まる。
* 容量: `docs/notes/chat-side-operations.md`・`docs/handover.md`・CLAUDE.md は上限が近い。追記前に残り容量を測り、収まらなければ事例の部分は `docs/notes/archive/` 側（既存の慣行に合わせる）へ書き、本文には規則だけ残す。
* 振り返りで挙げた「別の提案」（Bing Recommendations を月次の GSC 取得と同じ周期で見る、Crawler Hints を効果なしなら Off にする）は決定ではないので、issue に「案」として書くにとどめる。

手順

1. 決定の遡り記入: `docs/decisions/seo-bing.md`（BNG-05 で新規）に、BNG-01〜04 の決定を日付付きで足す。(a) #126 の結論（2026-09-30）: `/title/` は Bing 登録済み、IndexNow の自前送信（案 c）は行わない、Crawler Hints は On のまま（Bing 側に送信の記録は無く効果は未確認）、10/12 の確認は不要。(b) #486 の決定: タイトルの長さは #5 に含める（短すぎるページだけ共通の末尾を検討、50〜60 文字は目標にしない）、h1 の無い 11 ページは #283 と一緒に h1 だけ先に足す（順序 #283 → #486 → #7）、短い description は優先度低く #5 と同時に見直す程度で `404.html` には足さない、`/jpml_logs.html` は 404 のまま（301 は張らない）、被リンク不足と IndexNow は対応なし。(c) Bing の指摘との差の結論（BNG-04）: h1 複数と description 無しは古い HTML、alt 無しは空 `alt=""` を数えていると推定、実態として残るのは h1 無しの 11 ページのみ。出典として各ログの Chat-Ref を添える。`docs/handover.md`「次にやること」に #486・#5 の順序（#283 → #486 → #7、#5 は title の末尾の検討）が1行で分かる行が無ければ足す（容量に注意）。
2. 指示文の規則: `docs/instruction-template.md` を読み、次の2点が無ければ足す（あれば直す）。(a) 指示文の「変更は docs のみ」の範囲は `docs/decisions/`（CLAUDE.md「作業ログ」節の決定の記録）を含む。(b) 指示文に出てくる issue 番号は、Code が着手前に標題と Open/Closed を確かめ、食い違えば止まらず「前提との食い違い」として経過と報告に書く（BNG-04 で #5〈Closed〉・#150〈無関係〉の参照違いをそう扱った実例。実例は本文に書かず、この Chat-Ref を添えるだけにする）。
3. チャット側の規則: `docs/notes/chat-side-operations.md` を読み、次の2点が無ければ足す（残り容量を測ってから。事例は本文に書かず Chat-Ref だけ添える）。(a) チャット側は issue 番号・ページ数などを記憶から指示文に書かない。洗い出しの結果（ログ）にあるものだけ使い、無いものは「（要確認）」を付けて Code に確かめさせる。(b) ユーザー側の手作業（ダッシュボードの画面を見るだけ等）が安価で結論を左右するときは、記録・文書の指示より先にその手作業を頼む（BNG-02 → BNG-03 で、記録した直後に画面確認で覆り書き直した）。
4. issue への記録: #486 に「## 申送り（CHAT-0930-BNG-06）」のコメントを1件: 次回の見直し（Bing Recommendations で古い指摘〈h1 複数・description 無し・alt 無し〉が消えたか、10 月末ごろに平野さんが画面で見る）、案として「月次の GSC 取得（#304）と同じ周期で Bing の Recommendations も画面で一度見る」、案として「Crawler Hints は次の見直しでも Bing 側に記録が無ければ効果なしとみなし Off にする（docs/notes/cloudflare.md の記録も更新）」。#304 の本文は変えない（案の段階のため）。

止まる条件

* 作業ブランチの条件を満たさない、または BNG-05 がマージ済みでない。
* 追記先と矛盾していて、どちらが正か判断が要る。
* 容量の上限の警告が出て、archive への移しでも収まらない（案を書いて止まる）。
* 変更が docs/ 配下と issue のコメント以外に及ぶ。
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）。

完了条件

* `python3 scripts/check_asset_limits.py` とテストを通し、変更が docs/ だけであることと、上限付き文書の残り容量（追記前後）を報告に書く。
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。
* マージ: 承認済み（チャットで）。docs のみの変更なので、完了報告のうえ cloudflare へマージする。
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-0930-BNG-06.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-0930-BNG-06 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前確認: 同じ Chat-Ref のコミット無し。BNG-05 は「マージ: 済」、`origin/work/0930-bng` は `origin/cloudflare` の祖先。`git merge --ff-only origin/cloudflare` で 465b0617 へ進めた。指示欄の末尾は指示文の最後の行と一致。

## 報告

- 状態: 中断（着手直後）
- ブランチ: work/0930-bng
- ログ: https://github.com/retroeater/mj/blob/work/0930-bng/docs/logs/CHAT-0930-BNG-06.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-bng
- 確認用URL: なし
- マージ: 未
- issue: #486
- 判断が必要なこと: なし
- 未確認の項目: 手順1〜4すべて
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 465b0617）: https://github.com/retroeater/mj-logs/tree/main/guide/465b0617

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/465b0617/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/ad723967.md
