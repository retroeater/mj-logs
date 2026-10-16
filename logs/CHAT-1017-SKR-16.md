# CHAT-1017-SKR-16

- 着手日時: 2026-10-17
- 対象issue: #549、#581
- ブランチ: work/1017-skr-rec
- 着手時HEAD: f81b95961

## 指示

【Claude作成】Claude Code 向け指示：最強戦の X 告知の取りやめの記録と、SKR の振り返りで決めた比較ページの作り方・スクリーンショットの置き場所の規則（文書と issue だけ）
Chat-Ref: CHAT-1017-SKR-16
マージ: ドキュメントのみ（docs/notes/・docs/decisions/・ログ・issue のコメント）なので完了報告のうえ cloudflare へ入れてよい
貼る時機: いつでも（新しいセッションでも、SKR-15 の続きでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈下の行〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1017-skr-rec の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1017-skr-rec を使う。リモートに無ければ origin/cloudflare から作る。リモートにあれば止まる（手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦の公開（#549、2026-10-16）の後に平野さんが決めた「X の告知の取りやめ」を記録する。あわせて、SKR の振り返り（2026-10-16〜17、Fable 5.1 のレビュー済み）で決めた2つの規則を文書に足す。コードは変えない。

### 決定（2026-10-16〜17、平野さん）
1. 最強戦のリニューアルの X 告知は取りやめる（鳳凰戦の告知と時期が重なるため）。告知動画（CHAT-1016-SKR-05 で確定した `saikyo-promo-b.mp4`、平野さんが保存済み）は、別の機会にアピールするときに使う。2026-10-15（CHAT-1015-SKR-01）の #549 の公開の順番の「(5) 平野さんが X に投稿する」を置き換える
2. #549 のチェックリストの「X と LINE のカード（og:image が ABEMA の作品の画像で出るか）」は OGP の話なので、OGR（#581、OGP 画像の横断レビュー）で扱う。#549 では扱わない
3. Code が確かめのために撮ったスクリーンショットは、チャット側が見られるようにリポジトリ（作業ブランチの docs/logs/ の横）か issue に置いてよい（チャット側が Chrome を使えない間、プレビューを文字でしか確かめられなかったため）
4. 見た目の比較ページでは、見た目の分かれ目（置き場所・形・色など）を Code の「自然な方」に委ねず、分かれ目ごとに案を並べる。印（ピル・帯など）がどの要素のものかが問われる形では、上下・左右の要素との空き（px）を測って表に書く（SKR-10 でピルの中・外を Code に委ねて外になり、iPhone で見ると上下どちらのカードの結果か迷う形になった。SKR-13 で置き場所を5案並べ、空きを px で測って決め直した）

### 前提（チャット側。平野さんの決定ではない）
- 決定 1・2 は `docs/decisions/saikyo.md` に新しい節（2026-10-17、CHAT-1017-SKR-16）として足し、CHAT-1015-SKR-01 の節の「(5) 平野さんが X に投稿する」の行の末尾に「→ 置き換え: 2026-10-17（CHAT-1017-SKR-16）」を付ける（同ファイルの既存の書き方に合わせる）
- 決定 3 の書き場所の案: `docs/notes/chat-side-operations.md`「確認用 URL」の節（プレビューの URL をログ・issue に書かない規則〈#363〉がある所）に、「撮った画像は置いてよい。URL とは別」と一句足す。置き方は要確認: docs/logs/ の横に置くなら `docs/logs/img/<Chat-Ref>/` のような1か所に決め、mj-logs の `sync-from-mj.yml`（#440・#298）が画像も写すか確かめる。写さないなら、issue にコメントで貼る方を主にする（issue の画像は GitHub が保管する）。どちらを主にしたかと理由をログに書き、chat-side-operations.md には選んだ方だけを書く。純増した分は chat-side-operations.md の冒頭の規則に従い、前後のサイズを測ってログに書く
- 決定 4 の書き場所の案: `docs/notes/design.md`「7. チャット側の決め方」（比較ページの作り方がある所）に規則と理由の一句で足す。2026-10-14 の DSN の振り返りの「見比べたい分かれ目は図か比較ページで並べてから聞く」と同じ趣旨なので、その行があれば拡張し、無ければ足す
- 決定 1〜4 は、ガイド文（docs/notes/）と decisions に書く。知見の層の表（CLAUDE.md「Chat-Ref」節、#492）:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| 比較ページでは分かれ目を Code に委ねず案を並べ、所属が問われる印は空きを px で測る | ガイド文（docs/notes/design.md） | チャット側の指示の書き方で、テスト・hook では確かめられない |
| Code のスクリーンショットはリポジトリか issue に置いてよい | ガイド文（docs/notes/chat-side-operations.md） | 置き場所の取り決めで、仕組みにするものではない |

- issue のコメント（各コメントの末尾に `Chat-Ref: CHAT-1017-SKR-16`）:
  - #549（閉じたまま）: X の告知は取りやめ（決定 1）。チェックリストの「X と LINE のカード」は #581 で扱う（決定 2）。本文のチェックリストを直せるなら、その2項目に取り消し線か「→ 取りやめ」「→ #581」を付ける
  - #581: 「最強戦の卓・対局のページを X・LINE に貼ったときのカードの見え方（og:image が出るか）の確認は、#549 からこちらへ移した」と足す。#581 に着手中のコメント（未解除）があれば本文は触らずコメントだけにする

## 手順
1. 同じ論点の issue（「スクリーンショット」「画像の置き場所」「比較ページ」）を、クローズ済みも含めて検索し、同じ論点の open issue があれば止まって報告する。追記先（saikyo.md・chat-side-operations.md「確認用 URL」・design.md「7.」）の今の内容を読む。`git branch -r --no-merged origin/cloudflare` の各ブランチがこれらの節を変えていないか確かめる。mj-logs の `sync-from-mj.yml` が画像を写すかを確かめる
2. 上の前提のとおり書く。issue にコメントする
3. ログを仕上げ、cloudflare へ入れる

## 止まる条件
- 同じ論点の open issue がある
- 追記先の今の内容が上の決定と矛盾し、どちらが正か判断が要る（同じ趣旨の記述があるだけなら置き換え・拡張してよい）
- chat-side-operations.md が警告域（26KB）を超える
- 未マージの work/ ブランチが同じ節を変えている、または取り込みで衝突する（別の箇所なら止まらずログに書く。両立する衝突〈追記どうし〉は両方を残して解いてよい。解いた後の該当箇所をログに引用する）
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1017-SKR-16.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1017-SKR-16 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 43e4695b）: https://github.com/retroeater/mj-logs/tree/main/guide/43e4695b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/43e4695b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/f81b9596.md
