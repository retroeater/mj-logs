# CHAT-1023-BIL-10

- 着手日時: 2026-10-23
- 対象issue: #298
- ブランチ: work/1023-bil
- 着手時HEAD: 66afe3b40

## 指示

【Claude作成】Claude Code 向け指示：Workers Paid を続ける判断（Free に戻すデメリットの比較と見直しの目安）を docs に記録する
Chat-Ref: CHAT-1023-BIL-10
マージ: ドキュメントのみ（docs/notes・docs/decisions・docs/logs）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈docs/notes/cloud-sessions.md に従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-bil の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-bil を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-bil origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-22 の `.python-version` の移動（CHAT-1022-BIL-09）でビルド1回が約30秒に戻り、ビルド時間は Free の枠（月3,000分）に収まる見込みになった。そこで Free に戻すかを検討し、Workers Paid を続けると決めた。その経緯・比較・見直しの目安を残す。

### 決定（2026-10-23、平野さん）
- Cloudflare の Workers Paid（月 $5）を続ける。Free には戻さない
- 見直しの目安（チャット側の案を平野さんがチャットで読んだうえで「残す」と決めた）: 並行するセッションが減ってビルドの順番待ちが気にならず、かつビルド時間が月 1,000 分を下回る月が続き、新サイト（#296）でファイルが増える前なら、Free に戻すことを検討する

### 前提（チャット側。平野さんの決定ではない）
- 比較（数字は docs/notes/cloudflare.md・scheduler-worker.md・docs/decisions/cloudflare.md の記録による）:

| 項目 | Paid（いま） | Free に戻すと | mj への影響 |
| --- | --- | --- | --- |
| 同時に走るビルド | 6本 | 1本 | 大。並行するセッションのプレビューの後ろで本番が待ち、順番待ちの間に次の push が来ると古い本番のビルドは飛ばされる（2026-10-20 までの実際の動き） |
| ビルド時間の枠 | 月6,000分、超過は1分 $0.005 | 月3,000分、超過の課金は無い | 大。今のペース（1日 40〜50分の見込み）なら Free にも収まるが、`.python-version` のような予想外の増え方が起きたとき、Free は本番の反映が止まり、Paid は少し課金されるだけ |
| 配信のファイル数の上限 | 100,000 | 20,000 | 中。2026-10-22 で 6,442。新サイトの選手ごとのページなどで増える見込み |
| Workers のログ（Observability） | 7日保存・月2,000万件 | 3日保存・1日20万件 | 小。週末をはさむと3日では後から追えないことがある |
| 戻す作業 | ― | WKP-01・03 で Paid の値に直した docs・検査（`scripts/check_asset_limits.py` の `MAX_FILES` など）を戻す指示が要る | 手間と食い違いのおそれ |
| Cron Triggers | 250本 | 5本 | なし（使っているのは2本） |

- 推奨の理由（チャット側）: 費用は年 $60。順番待ちは並行するセッションのたびに毎日効く。困ったときの結果が「本番の反映が止まる」（Free）か「少し課金される」（Paid）かで、止めない方を取る。docs・検査はすでに Paid に合わせてある
- 書き場所の案: docs/decisions/cloudflare.md に「決定」を足す。比較の表と理由は docs/notes/cloudflare.md「ビルド時間の見積もり」の Workers Paid の行の近くに短く置く（同じ内容を2か所に書かない。decisions には決定と見直しの目安だけ、notes に比較）。規則ではなく判断の記録なので、ガイド文の規則の追加には当たらない

## 手順
1. docs/notes/cloudflare.md「ビルド時間の見積もり」と docs/decisions/cloudflare.md の直近の節（BIL-03・WKP-01・WKP-03・BIL-06）を読み、上の「前提」と食い違いがないか確かめる（食い違えば止まる）
2. docs/notes/cloudflare.md に比較の表（短く）と理由を、docs/decisions/cloudflare.md に「決定」を書く
3. #298 に、この判断と書いた場所を1コメントで書く（本文・ラベル・状態は変えない）

## 止まる条件
- docs の記録が上の「前提」の数字と食い違っていて、どちらが正か判断が要る
- 未マージの work/ ブランチが docs/notes/cloudflare.md の同じ節を変えている、または取り込みで衝突する（別の節の変更では止まらず、ログに書く）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-BIL-10.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-BIL-10 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref のコミットは無し。BIL は同じセッションの BIL-01〜09 だけ
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- 0. 指示欄の末尾は指示文の最後の行と一致
- work/1023-bil はローカル・リモートとも無し → `git checkout -b work/1023-bil origin/cloudflare`（66afe3b40）

### 1. 前提との照合

- docs/notes/cloudflare.md「ビルド時間の見積もり」・docs/decisions/cloudflare.md（BIL-03・WKP-01・WKP-03・BIL-06）・`scripts/check_asset_limits.py`・cloudflare.md の Cron Triggers の行と照らした。同時6本／1本、月6,000分・$0.005／3,000分、ファイル数の上限 100,000／20,000、Cron Triggers 250／5 本（使うのは2本）は記録と同じ
- Workers Logs の 7日・月2,000万件／3日・1日20万件: docs には Paid の月2,000万件だけがあり、保存日数と Free の値は無かった。公式の Workers Logs のページ（2026-10-23 に WebFetch）で同じ値を確かめた
- 配信のファイル数「2026-10-22 で 6,442」: docs には記録が無い（出どころは BIL-07 の指示のビルドのログの「Read 6436 files」と見られる）。2026-10-23 の `python3 scripts/check_asset_limits.py` の配信ファイル数は 4,195（`.assetsignore` を除いた数。git の追跡は 5,960）。docs と食い違う記録ではないため止まらず、表には 4,195 と書き、ビルドのログの値は数え方が違う申告値として添えた
- 未マージの work/ ブランチで docs/notes/cloudflare.md・docs/decisions/cloudflare.md を変えているものは無い

### 2・3. 書いたもの

- docs/notes/cloudflare.md「ビルド時間の見積もり」の Workers Paid の行の後に、続ける決定・比較の表（5行）・理由（f5d14ef94）。前提の表の「戻す作業」の行は理由の文に入れた
- docs/decisions/cloudflare.md「2026-10-23（CHAT-1023-BIL-10）」に決定と見直しの目安
- #298 にコメントした。本文・ラベル・状態は変えていない

## 報告

- 状態: 完了
- ブランチ: work/1023-bil
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-BIL-10.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1023-bil
- 確認用URL: なし
- マージ: 済（ドキュメントのみ）
- issue: #298
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 77e42c54）: https://github.com/retroeater/mj-logs/tree/main/guide/77e42c54

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/77e42c54/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/77e42c54.md
