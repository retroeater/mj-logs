# CHAT-1019-WRR-14

- 着手日時: 2026-10-20
- 対象issue: なし
- ブランチ: work/1019-wrr14
- 着手時HEAD: e18c2138a

## 指示

【Claude作成】Claude Code 向け指示：告知の期間を確定し（終わりは投稿完了の時点）、承認の書き方を chat-side-operations.md に1行足す。WRR-13 を完了にする（文書だけ）
Chat-Ref: CHAT-1019-WRR-14
マージ: ドキュメントのみなので完了報告のうえ cloudflare へ入れてよい（変えるのは docs/logs・docs/notes/page-announcement.md・docs/notes/chat-side-operations.md・docs/decisions だけ。コード・生成物は変えない）
貼る時機: いつでも（CHAT-1019-WRR-13 はマージ済み・判断待ち）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-wrr14 の作成・push と、cloudflare へのマージを許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-wrr14 を origin/cloudflare から作る。rebase・force push はしない。`docs/decisions/` の末尾への追記どうしの衝突は、両方の節を残して日付・番号の順に並べて解いてよい。それ以外の衝突は解かずに止まる。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1019-WRR-13 のログの `## 報告` を読み、状態が「判断待ち」でなければ止まる。判断待ちなら状態を「完了」にし、末尾に ` / 続き: CHAT-1019-WRR-14` を足す。

## 決定（2026-10-20、平野さん）

- 告知の期間（WRR-13 の決まりで、告知に挙げるページ・機能を変えるマージに平野さんの明示的な指示・判断が要る間）は、**告知動画の初版を作る指示に着手した時点から、投稿が完了した時点まで**（WRR-13 の仮置き「投稿の翌日まで」を置き換える。投稿の後の直しは、この決まりの対象外）
- 承認の書き方は、指示の雛形（instruction-template.md）でなく docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」に1行で書く（雛形は容量の警告域の近くのため）。CLAUDE.md には足さない

## 手順

1. docs/notes/page-announcement.md「進め方」の告知の期間の「仮置き」を、上の決定（終わりは投稿完了の時点）に直し、「仮置き」の印を外す
2. docs/notes/chat-side-operations.md「平野さんの判断とマージの許可」に1行足す。案: 「告知の期間（告知動画の初版の指示に着手してから投稿完了まで。page-announcement.md）に、告知に挙げるページ・機能を変える指示では、冒頭の『マージ:』の行に『告知の期間中の変更を承認（<日付>、平野さん）』と書く。書いていなければ Code はマージせずに止まる」
3. 上の「決定」を docs/decisions の WRR-13 の節の後に「## 2026-10-20（CHAT-1019-WRR-14）」として足す
4. 足す前後のバイト数と、容量の検査（`check-assets.yml` と同じもの）の警告域・上限との差をログに書く。CLAUDE.md「ブランチ運用」節のとおり cloudflare にマージして push する。check-assets の成否を書く

## 止まる条件

- 0章の確かめに当たる
- 変更が、冒頭の「マージ:」の行の文書のほかに出る。容量の検査が上限に入る
- 追記先に矛盾する記述がある
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-WRR-14.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-WRR-14 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

- 識別子確認: 同じ Chat-Ref のコミットは 0 件
- 作業ブランチ: work/1019-wrr14 はローカル・リモートとも無し → `git checkout -b work/1019-wrr14 origin/cloudflare`（e18c2138a）
- 0. 「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。CHAT-1019-WRR-13 の状態は「判断待ち」→「完了 / 続き: CHAT-1019-WRR-14」にした
- ガイド文（docs/notes/chat-side-operations.md）に規則を足す指示だが、「知見 → 選んだ層 → 理由」の表は無い。WRR-12 の表（告知した機能を投稿の直前・直後に変えた → ガイド文、センサーにできない）の続きで、この指示の「決定」が層（chat-side-operations.md、雛形でも CLAUDE.md でもない）と理由（雛形が警告域の近く）を書いているので、止まらずに進めた（報告の「判断が必要なこと」）
- 1. page-announcement.md「進め方」の期間の行を「期間（決定、2026-10-20、平野さん）: 告知動画の初版を作る指示に着手した時点から、投稿が完了した時点まで。投稿の後の直しは、この決まりの対象外。承認の書き方は chat-side-operations.md「平野さんの判断とマージの許可」」に置き換えた（「仮置き」と「投稿の翌日まで」を外した）
- 2. chat-side-operations.md「平野さんの判断とマージの許可」の箇条（「判断待ち」で止めた報告…の次、`.claude/` の hook…の前）に1行足した。矛盾する記述は無し
- 3. docs/decisions/houou.md の WRR-13 の節の後（末尾）に「## 2026-10-20（CHAT-1019-WRR-14）」
- 4. バイト数: page-announcement.md 12612 → 12737、chat-side-operations.md 21976 → 22349（警告域 26624 まで 4275・上限 28672 まで 6323。純増の警告の床 24576 の下）、docs/decisions/houou.md 156021 → 156805。ほかの3文書は変えていない: CLAUDE.md 24859（1765・3813）、handover.md 22937（3687・5735）、instruction-template.md 14325（警告域まで 11・上限まで 2059）。`scripts/check_asset_limits.py` はすべて OK
- check-assets: 作業ブランチの push（256f317b0、chat-side-operations.md を含む）で「公開対象の検査」が走り success（run 39407129736）。注記は Node.js 20 の非推奨と ubuntu-latest の移行の通知だけで、ガイド文書のサイズの警告は無し
- マージ: push 直前に再 fetch して祖先を確かめ `git push origin work/1019-wrr14:cloudflare`（e18c2138a..256f317b0）

## 報告

- 状態: 完了 / 続き: CHAT-1019-WRR-15
- ブランチ: work/1019-wrr14
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1019-WRR-14.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-wrr14
- 確認用URL: なし
- マージ: 済（256f317b0）
- issue: なし
- 判断が必要なこと:
  - ガイド文（chat-side-operations.md）に規則を足す指示に「知見 → 選んだ層 → 理由」の表が無かった（CLAUDE.md「Chat-Ref」節では着手せず止まる条件）。WRR-12 の表の続きで、指示の「決定」に層と理由が書かれていたので止まらずに進めた。この扱いでよいか（よくなければ、chat-side-operations.md の1行を戻す） → 2026-10-20 平野さん: この扱いでよい（CHAT-1019-WRR-15）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 33a5608e）: https://github.com/retroeater/mj-logs/tree/main/guide/33a5608e

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/33a5608e/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/33a5608e.md
