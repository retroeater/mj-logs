# CHAT-0930-CAL-26

- 着手日時: 2026-10-03 12:39（JST）
- 対象issue: なし
- ブランチ: work/1003-cal-del
- 着手時HEAD: 244c4103（origin/work/1003-cal-del と同じ。945bcadf を含む）

## 指示

【Claude作成】Claude Code 向け指示：削除の上限を手動実行で1回だけ変える入力（CAL-24、work/1003-cal-del）を cloudflare へ入れ、本番で入力が読めることを確かめる Chat-Ref: CHAT-0930-CAL-26 貼る時機: いつでも（CAL-25 と並行で可。05:30〜08:30 JST の毎朝の実行の時間帯は、手順2の試しの実行を始めずに止まる） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチの push、cloudflare へのマージ、書き込みなしのワークフローの手動実行を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。未マージの work/1003-cal-del を続けて使う（CHAT-0930-CAL-24 のコミット 945bcadf があるため）。`git checkout -b work/1003-cal-del origin/work/1003-cal-del` のうえ、`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無い、または 945bcadf を含まなければ止まる マージ: 承認済み（チャットで、2026-10-03。work/1003-cal-del を cloudflare へ。下の止まる条件に当たらない限り、確認を求めずに進めてよい）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-0930-CAL-24 のログの `### 手順1: マージ

- `git merge origin/cloudflare` を実行した（衝突なし）。テスト全件 OK、`check_asset_limits.py` OK
- origin/cloudflare との差分は次のとおりで、CAL-24 のものとログだけ。想定外の差分は無い:
  - `update-live-channel.yml`
  - `sync_live_calendar.py`
  - `test_sync_live_calendar.py`
  - `docs/notes/yotei-sheet.md`
  - `docs/decisions/broadcast-calendar.md`
  - ログ（CAL-24・CAL-26）
- 再 fetch し `merge-base --is-ancestor` が真なのを確かめて push した: **16b2dff5..73b539e6**（12:40 JST）

### 手順2: 本番の確かめ

- 12:40 JST（毎朝の時間帯の外）。実行中の実行が無いことを確かめた
- cloudflare で起動した: run 37093994427。apply・yotei_apply・calendar_apply は外し、calendar_max_delete=30（既定）
  - update・yotei success、regenerate skipped。エラーの行なし
- 環境変数は `CALENDAR_MAX_DELETE: 30`。**「削除の上限を…変えた」の行は出ていない**（既定のまま）
- 今の予定 2,617件・載せる予定 2,617件。**作る 0・直す 0・消す 0**
- 決定の記録: `docs/decisions/broadcast-calendar.md` にこの指示の決定を足した。CAL-24 の「未マージ」の行に済の印を付けた

## 報告

- 状態: 完了
- ブランチ: work/1003-cal-del（cloudflare へマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-0930-CAL-26.md
- 比較URL: https://github.com/retroeater/mj/compare/16b2dff5...73b539e6
- 確認用URL: なし
- マージ: 済（73b539e6、fast-forward）
- issue: なし
- 判断が必要なこと:
  - 本番の手動実行の入力に `calendar_max_delete`（既定 30）が増えた。意図して30件を超えて消すときは、先に `calendar_apply` を外してその数で見込みを出し、件数と一覧を確かめてから `calendar_apply` を付ける（docs/notes/yotei-sheet.md）
  - 参考: docs/decisions/operations.md に別のチャットの「#491 の『MAX_DELETES の件』を済にする」がある。同じ論点なら #491 の側で済にしてよい（この指示では #491 を触っていない）
- 未確認の項目: 上限を上げて実際に30件を超えて消す書き込みありの実行（今は消す予定が0件で試せない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj cf0f7e27）: https://github.com/retroeater/mj-logs/tree/main/guide/cf0f7e27

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/cf0f7e27/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/0384cc68.md
