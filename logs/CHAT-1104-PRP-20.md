# CHAT-1104-PRP-20

- 着手日時: 2026-11-04
- 対象issue: #641・#195
- ブランチ: work/1101-prp-pub
- 着手時HEAD: 06f7dceb4

## 指示

【Claude作成】Claude Code 向け指示：公開のブランチ work/1101-prp-pub で、帰り道の X の ID が無い在籍者（森山茂和）の名前を個別ページへのリンクにする。マージはしない
Chat-Ref: CHAT-1104-PRP-20
マージ: 判断待ちで止まる（このブランチのマージが pro/ の公開になる。告知動画の後に、別の指示でマージする）
貼る時機: いつでも（PRP-19 と同じセッションでも、新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1101-prp-pub を続けて使う（公開の準備のため）。リモートに無ければ止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。この指示では `origin/cloudflare` を取り込まない（公開のマージの指示の着手時に取り込む）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。CHAT-1104-PRP-19 のログの `## 報告` の状態の末尾に ` / 続き: CHAT-1104-PRP-20` を足して push する。

## 目的
CHAT-1104-PRP-19 の「判断が必要なこと」の1つ目への回答。帰り道で X の ID が無い在籍者も、個別ページへ行けるようにする。

### 決定（2026-11-04、平野さん）
- 帰り道で X の ID が無い在籍者（今は森山茂和の1名。一覧のヒーローと第3期達人戦の #1・#2）は、名前の文字だけを個別ページへのリンクにする。写真は足さない。写真の確かめ（`check_player_photos()`）の対象は変えない

### 前提（チャット側。平野さんの決定ではない）
- 名前をリンクにするのは「X の ID が無く、個別ページがある」選手だけ。X の写真がある選手は今のまま（写真が個別ページへのリンク、名前はリンクにしない。WH-53）。個別ページも X の ID も無い人は今のまま名前だけ
- 判定は PRP-19 で入れた `PlayerLinks.slug`（`with_pro_slugs()`・`pro_search.page_path()`）を使う。X の ID が無い選手が `{選手名: PlayerLinks}` に入っていなければ、`pro_search.load_slugs()` の名前の表から引く形にし、新しい名前の表は作らない
- リンクは同じタブ（`target="_blank"` なし）。名前の見た目（大きさ・色）はヒーローの今の名前と同じに保ち、リンクだと分かる最小の印（下線など）は既存の CSS にある形を使う。CSS を足すなら `style.css` などの共有のファイルではなく、帰り道の既存の CSS の中で足す
- Mつく（`generate_mtsuku.py`・`generate_mtsuku_episodes.py`）は同じ関数を使うが slug を入れないので、変わらないこと（差分 0）を確かめる

## 手順
1. 上の形で入れ、検査を足す（X の ID が無く slug がある → 名前がリンク、X の ID がある → 今のまま、どちらも無い → 名前だけ。足した検査は修正前のコードで失敗することを確かめる）
2. `regenerate-page.yml` で wayhome/ を作り直す（`check` で止まらないこと）
3. 確かめ: テスト（`python3 -m unittest discover -s scripts/tests`）がすべて通る。生成物の差分は wayhome/ の森山茂和の出る3ページ（一覧と第3期達人戦 #1・#2）だけ（ほかが出たら止まる）。プレビュー（付かなければローカル）で一覧と #1 を 360px で見る。新しい文脈のサブエージェントの差分レビュー（CLAUDE.md「判断・作業の原則」）。上の「決定」を docs/decisions/pros.md に足す。#195 にコメントする

## 止まる条件
- 生成物に、上の3ページのほかの差分が出た（Mつくを含む）
- テストが落ちる（直せる範囲は直してよい。直せなければ止まる）
- この指示では `cloudflare` へマージしない（完了の報告は「判断待ち」）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。プレビューの URL はターミナルへの最終報告の「確認用:」の行にだけ書く
- マージは冒頭の「マージ:」の行のとおり（しない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1104-PRP-20.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1104-PRP-20 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

## 報告

- 状態: 作業中
- ブランチ: work/1101-prp-pub
- ログ: https://github.com/retroeater/mj/blob/work/1101-prp-pub/docs/logs/CHAT-1104-PRP-20.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1101-prp-pub
- 確認用URL: 作業中
- マージ: 未（判断待ち）
- issue: #641・#195
- 判断が必要なこと: 作業中
- 未確認の項目: 作業中
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a4a9ed1b）: https://github.com/retroeater/mj-logs/tree/main/guide/a4a9ed1b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a4a9ed1b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/95028c1a.md
