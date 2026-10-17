# CHAT-1017-PAD-03

- 着手日時: 2026-10-17
- 対象issue: #428・#525（新規起票 2件）
- ブランチ: work/1017-pad-iss
- 着手時HEAD: bd0ffc531

## 指示

【Claude作成】Claude Code 向け指示：`title/` の入口の上余白のずれと、文字を大きくしたときの `/live` の絞り込みのバーの崩れを起票する（issue とログのみ）
Chat-Ref: CHAT-1017-PAD-03
マージ: ドキュメントのみ（ログと docs/decisions/）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（CHAT-1017-PAD-01 と同じセッションに貼ってよい。この後に CHAT-1017-PAD-02 を同じセッションに貼る）
作業ブランチ: クラウドセッションで実行する。work/1017-pad-iss を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1017-pad-iss origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-pad-iss の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的

CHAT-1017-PAD-01 の計測と 2026-10-17 の iPhone の実機確認で見つかった、#428 の範囲外の2件を issue に起こす。コード・CSS・生成物は変えない。

### 決定（2026-10-17、平野さん）

1. `title/` の入口の 390px で、上余白のフォールバック（バー1行分）と実際のバー（年のプルダウンで2行）の差で毎回出るずれ（CLS 0.0551）と、JS 無効で本文の先頭 38px が固定バーの下に隠れる件は、#428 に入れず新しい issue にする
2. 文字を大きくしたときの `/live` の絞り込みのバーの崩れは、新しい issue に起票だけする。直し方は後日、比較ページで見比べて決める
3. `live/` の写真カードの文字を大きくしたときの見え方（#525 の案C）は、iPhone の実機で OK だった（`live/ourai/3/f.html`。対局者は名前が写真の下、実況・解説は帯が写真の上に出て、顔に重ならない。ふだんの大きさでは前と同じ見た目）。これで第3弾（#525）の実機確認はすべて済んだ

### 前提（チャット側。平野さんの決定ではない）

- issue A（決定 1）の中身は、CHAT-1017-PAD-01 のログの `## 経過` の「2. 計測」の結果の表の `title/` の行、「JS 無効」の表、「3. まとめ」の「別件」の `title/` の項から引用する（要約し直さない）。最強戦の #381（幅ごとのフォールバック）と同じ形であることと、直すにはバーが2行になる幅の境目を測る必要があることを書く。ラベルの案: `分野: パフォーマンス`・`分野: UI/UX`・`対象: title`（正は `gh label list`。無いラベルは作らず、近いものにする）
- issue B（決定 2）の中身（平野さんの iPhone の Safari で 2026-10-17 に確かめた。スクショはチャットにあり、issue には付けられない）:
  - ページ: `live/ourai/3/f.html`。Safari の「ぁあ」で文字を一番大きくした状態
  - 絞り込みのバーの検索欄が縦長の細い枠に潰れ、プレースホルダが1文字ほどしか見えず、入力しにくい（大会のプルダウン・虫眼鏡・共有のアイコンは大きく出る）
  - navbar と絞り込みのバーが上に固定されたままで、2つで画面の上半分ほどを占め、本文の見える範囲が狭くなる
  - 同じバー（`body:has(.mj-video-list, .mj-live) .mj-filterbar`）は動画の一覧（`wayhome/`・`mtsuku/`・`study/` の入口）でも使っているので、同じ崩れが出る可能性がある（未確認。この指示では測らない）
  - 直し方の案（チャット側の案、未決）: 文字が大きいときは検索欄を2行目に回す、固定をやめる、など。比較ページで見比べて決める
  - ラベルの案: `分野: UI/UX`・`対象: live`（正は `gh label list`）
- 決定 3 は #525（閉じたまま）にコメントする
- 決定の書き先は PAD-01 と同じ `docs/decisions/site-review.md`
- 期日は付けない（カレンダーには作らない）
- 使う skill は無い

## 手順

1. 確かめる: 同じ主題の issue をクローズ済みも含めて検索する（`gh issue list --state all --search` の語の例: 「title 入口」「フォールバック」「年のプルダウン」「絞り込み」「検索欄」「文字を大きく」「filterbar」）。同じ目的の open issue があれば、その件だけ起票せずにその issue へコメントで足し、報告に書く。CHAT-1017-PAD-01 のログの引用する節を読む
2. 起票・コメントする（各コメントと本文の末尾に `Chat-Ref: CHAT-1017-PAD-03`）: issue A・B を起こす。#428 に「範囲外の `title/` の入口の件は issue A に移した」とコメントする。#525 に決定 3 をコメントする
3. 決定 1〜3 を `docs/decisions/site-review.md` に足す（issue A・B の番号を添える）

## 止まる条件

- issue A・B と同じ目的の open issue が見つかった（その件だけ起票せず、既存の issue にコメントして進める）
- CHAT-1017-PAD-01 のログに、上の前提で引用する節が無い
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件

- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。issue A・B の番号を「issue」の行に書く
- マージは冒頭の「マージ:」の行のとおり（変わるのは docs/logs/ と docs/decisions/ だけの見込み）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-PAD-03.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-PAD-03 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 同じ Chat-Ref（`CHAT-1017-PAD-03`）のコミット: 0件。識別子 PAD はこのセッションの CHAT-1017-PAD-01 で確認済み（このセッションの2つ目の指示なので、識別子の重複の確認は対象外）
- 作業ブランチ: ローカル・リモートとも `work/1017-pad-iss` が無いため `git checkout -b work/1017-pad-iss origin/cloudflare`
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 揃っている
- 0. 「指示」欄の末尾は指示文の最後の行と一致
- 番号: この指示は PAD-03 で、PAD-02 は後に貼られる予定（指示文の「貼る時機」）。PAD-02 のコミットはまだ無い

- 同じ主題の issue の検索: GitHub の検索 API はセッションから使えない（「sessions are bound to their configured repositories」）ため、REST の `repos/retroeater/mj/issues?state=all` で全586件（#1〜#587）を取り、題名と open の本文を語で探した（「title 入口」「入口」「フォールバック」「年のプルダウン」「絞り込み」「検索欄」「文字を大きく」「filterbar」「文字の拡大」「ぁあ」「文字サイズ」）。同じ目的の open issue は無い
  - 近いもの: #428（`:has()` の成立待ち。範囲外の件を移す元）、#417（共通ナビの position をそろえる方針、保留。issue B の「固定をやめる」案と関わるので本文から参照した）、#364・#358（title/ の入口の改修、閉じている）、#248（型A の検索欄の常時表示）
- PAD-01 のログの引用する節（「2. 計測」の表の `title/` の行・「他」の `title/` の項・「JS 無効」の表・「3. まとめ」の「別件」の `title/` の項）はある。issue A に引用した（ログは cloudflare にマージ済みで、リンクは版 e61b4b998 に固定した）
- ラベル（`repos/…/labels` で確かめた）: `対象: live` は無い。/live の既存の issue（#490・#446・#404 など）が使う `対象: video_live` にした。ほかは案のとおり
- 起票: #588（issue A、`分野: パフォーマンス`・`分野: UI/UX`・`対象: title`）、#589（issue B、`分野: UI/UX`・`対象: video_live`）。期日は付けていない
- コメント: #428（範囲外の `title/` の入口の件を #588 に移した）、#525（閉じたまま。決定 3 と、#589 を起票したこと）
- 決定 1〜3 を `docs/decisions/site-review.md` に足した

## 報告

- 状態: 完了
- ブランチ: work/1017-pad-iss
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1017-PAD-03.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1017-pad-iss
- 確認用URL: なし
- マージ: 済（origin/cloudflare を取り込んだうえで fast-forward、cloudflare 7884f8827）
- issue: #588（起票）・#589（起票）・#428（コメント）・#525（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 35b8a1bd）: https://github.com/retroeater/mj-logs/tree/main/guide/35b8a1bd

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/35b8a1bd/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e61b4b99.md
