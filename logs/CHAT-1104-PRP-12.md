# CHAT-1104-PRP-12

- 着手日時: 2026-11-04
- 対象issue: #641
- ブランチ: work/1104-prp-desc
- 着手時HEAD: a73233863（origin/cloudflare）

## 指示

【Claude作成】Claude Code 向け指示：pro/ のトップの説明文（description）を今の絞り込みに合わせて直し、未公開のまま本番に入れる
Chat-Ref: CHAT-1104-PRP-12
マージ: 承認済み（チャットで。2026-11-04 に平野さんが、トップの説明文を「今すぐ直す（未公開のまま）」と単独の問いで選んだ。下の「止まる条件」に1つでも当たればマージしない）
貼る時機: いつでも（新しいセッションでも、PRP-11 と同じセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1104-prp-desc を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1104-prp-desc origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
pro/ のトップの説明文が、今の絞り込みのタブ（よみ・所属・入会・鳳凰・桜花・★）と合っていない（CHAT-1104-PRP-11 の報告）。今の形に直す。

### 決定（2026-11-04、平野さん）
- pro/ のトップの説明文（description）を、今の絞り込みに合わせて今すぐ直す（未公開のまま本番に入れる）

### 前提（チャット側。平野さんの決定ではない）
- 今の説明文は「…五十音・所属・入会期・鳳凰戦のリーグで探せます。」（PRP-11 の報告。要確認）。直した文の案: 「日本プロ麻雀連盟の在籍する麻雀プロ1,098名の個別ページの一覧。よみ・所属・入会期・鳳凰戦／女流桜花のリーグで絞り込め、お気に入りの選手も保存できます。」（人数は今の生成の値を使い、焼き付けない。長さはほかのトップの説明文〈50〜80字程度〉に合わせて詰めてよい）
- og:description も同じ値なら一緒に変わる（既存の形に従う）
- 未マージの work/1101-prp-pub（公開の準備）はトップの説明文の行を変えていない見込み（要確認。変えていれば止まる）

## 手順
1. `scripts/generate_pro_pages.py` のトップの説明文を直し、テストを直す・足す（説明文に「桜花」「お気に入り」が入る）。pro/ を Actions の生成（`regenerate-page.yml`、`target_page: pro_pages`）で作り直す
2. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る。生成物の差分が `pro/index.html` の description（と og:description）の行だけであること。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）
3. マージと記録: 止まる条件に当たらなければ cloudflare へマージする（CLAUDE.md「ブランチ運用」）。本番の `pro/` の説明文が新しくなり、noindex が残っていることを確かめる（15分で本番が古いままなら「未確認の項目」に回して完了にする）。上の「決定」を docs/decisions/pros.md に足す

## 止まる条件（1つでも当たればマージしない）
- 生成物の差分が `pro/index.html` の description・og:description の行のほかに出た（毎朝の生成などで動いた差分は、この指示の前の origin/cloudflare の生成物と比べて分け、分けられなければ止まる）
- `pro/index.html` に `<meta name="robots" content="noindex">` が無い
- 未マージの work/ ブランチ（work/1101-prp-pub を含む）が、トップの説明文の同じ行を変えている
- テストが1つでも落ちる
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-12.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-12 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1104-prp-desc
- ログ: https://github.com/retroeater/mj/blob/work/1104-prp-desc/docs/logs/CHAT-1104-PRP-12.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1104-prp-desc
- 確認用URL: 作業中
- マージ: 作業中
- issue: #641
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a7323386）: https://github.com/retroeater/mj-logs/tree/main/guide/a7323386

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a7323386/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/dfabc859.md
