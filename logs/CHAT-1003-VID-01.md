# CHAT-1003-VID-01

- 着手日時: 2026-10-03
- 対象issue: なし
- ブランチ: work/1003-vid-01
- 着手時HEAD: 6bad769b

## 指示

【Claude作成】Claude Code 向け指示：「タイトル戦」（title/）の告知動画の下調べ（数字を数え、録画と描画が動くかを試す。成果物はログのみ）
Chat-Ref: CHAT-1003-VID-01
マージ: 判断待ちで止まる（成果物はログのみ。試しに作った動画・道具・設定はコミットしない）
貼る時機: いつでも
作業ブランチ: クラウドセッションで実行する。work/1003-vid-01 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1003-vid-01 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1003-vid-01 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
X に投稿する「タイトル戦」（https://ryoei.pro/title/）リニューアルの告知動画（15〜30秒）を作る前の下調べ。動画に出す数字を実物から数え、画面の録画と動画の描画がこの環境でできるかを確かめる。告知動画そのものは作らない（制作は、この結果を見て構成を決めた後の別の指示）。

### 決定（2026-10-03、平野さん）
- 「タイトル戦」のリニューアルを、X に投稿する15〜30秒程度の短い動画で告知する
- 構成は「数字のモーショングラフィックス → 実画面の操作デモ → URL」の合わせ技にする
- 動画に選手の写真（title/ のページに出ている X の画像）が映るのは、そのまま映してよい

### 前提（チャット側。平野さんの決定ではない）
- title/ の OGP を文字だけにした決定（docs/decisions/title.md）は変えない。上の写真の決定は告知動画だけの話
- 描画の候補は HyperFrames（HTML を動画にする枠組み。/brag skill〈latent-spaces/brag〉が描画に使う）、録画の候補は Playwright などのブラウザ自動操作。ほかに良い手段があれば挙げてよい
- 操作デモは iPhone 幅（縦長）の画面を想定。X 向けの形式（縦横比・長さ・コーデック）は制作の指示までにチャット側で決める
- plugin は使わない決定（docs/notes/skills.md）のため、/brag を使うなら `.claude/skills/` への複写になる。今回は入れない（読むだけ）
- 数字の現在値はチャット側では確かめていない。docs/notes/title-pages.md にある値（ページ数・期数・決勝の映像の本数）は古い時点のもの

### やらないこと
- 試しに作った動画・画像・導入した道具・設定をコミットしない（作業ブランチに入れるのはこの指示のログだけ）
- `.claude/` 配下（skills・hooks・settings）と、リポジトリのコード・生成物・シートを変えない
- X など外部へ投稿・送信しない

## 手順
1. **数字を数える。** その時点の origin/cloudflare で、生成と同じ経路（`scripts/generate_title_pages.py` が読むデータ、または生成物の `title/`・`title/search.json`・`sitemap-title.xml`）から次を数え、数え方と一緒に表でログに書く: 大会数／期数／title/ のページ数／決勝に出た選手の実人数と延べ人数／決勝ライブ・決勝動画それぞれの期数と本数（合計も）／記録のいちばん古い年といちばん新しい年／優勝回数と決勝進出回数の上位3名。docs/notes/title-pages.md の値と食い違っても止まらず、数えた値を正として差を書く。あわせて、告知・SNS 向け動画に関する open issue があるかを検索し、番号と題を書く（無ければ「無し」。あっても止まらない）。
2. **動くかを試す（どれも数秒の最小の試し）。**
   - (a) 録画: ブラウザの自動操作で本番 https://ryoei.pro/title/ を iPhone 幅で開き、入口 → プルダウンで大会ページ → 期ページ（決勝ライブ・決勝動画まで）→ 検索欄に選手名 → 出場した期の一覧、の操作を録画できるか。選手の写真と YouTube のサムネイルが録画に映るかも確かめる
   - (b) 描画: HyperFrames を取得し、日本語の文字（Noto Sans JP）と数字のカウントアップを mp4 に書き出せるか。(a) の録画を素材として取り込めるかも見る
   - (c) /brag の SKILL.md と同梱物を読み、同梱の音楽・素材の利用条件、依存する道具、`.claude/skills/` へ複写するときの範囲を書く
   - できた試しは、長さ・解像度・フレームレート・ファイルサイズ（ffprobe など）と、静止画を自分で見た結果（日本語の字が欠けていないか、写真が出ているか）を書く。プロキシに拒否されたドメインは `recentRelayFailures` から全部挙げる。道具が入らないときは別の手段を2つまで試し、だめなら記録して先へ進む。外部の完了を待つ処理は15分を上限にし、超えたらその時点の状態を「未確認の項目」に書いて先へ進む
3. **構成の材料を出す。** (i) 動画で推す数字の候補を3つ、理由と一緒に。(ii) 操作デモで映す候補（大会・期・検索する選手名）を3組、選んだ条件（決勝ライブと決勝動画の両方がある、決勝の全員に写真がある、など）と一緒に。(iii) 制作の環境（クラウドセッション／Codespace／平野さんの Windows 11 の PC）ごとに、できること・足りないもの（許可ドメイン・道具）・おすすめ。(iv) 制作を1本の指示で進めるときの段取りの案（素材 → 描画 → 平野さんが動画を受け取って見る方法）。

## 止まる条件
- 未マージの work/ ブランチ（`git branch -r --no-merged origin/cloudflare` で一覧を出す）に、告知動画と同じ題材の作業がある
- 試すために「やらないこと」のどれかが必要になった（必要になったものを書いて、その項目だけ飛ばして先へ進む。全部が進められないときだけ止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（手順1の表、手順2の (a)(b)(c) の結果、手順3の (i)〜(iv) を含める）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1003-VID-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1003-VID-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の確認: `git fetch --unshallow origin` の後、`git log --all --grep` と Chat-Ref トレーラ・`docs/logs/` の履歴に `VID` は無し
- ブランチ: ローカル・リモートとも work/1003-vid-01 が無いため `git checkout -b work/1003-vid-01 origin/cloudflare`（6bad769b）

## 報告

- 状態: 作業中
- ブランチ: work/1003-vid-01
- ログ: https://github.com/retroeater/mj/blob/work/1003-vid-01/docs/logs/CHAT-1003-VID-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-vid-01
- 確認用URL: なし
- マージ: 未
- issue: なし
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 815188dc）: https://github.com/retroeater/mj-logs/tree/main/guide/815188dc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/815188dc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/c162d893.md
