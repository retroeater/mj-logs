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
- 0: 指示欄の末尾は指示文の最後の行（「不明な点があれば…この行が指示文の最後の行です。」）と一致
- 止まる条件: `git branch -r --no-merged origin/cloudflare` の work/ は work/1002-cld（CHAT-1002-CLD-07、放送対局の件名の調査）と本ブランチだけ。告知動画の題材は無し

### 手順1 数字（origin/cloudflare 6bad769b の生成物から）

数え方: `title/search.json`（t=大会、m=期、p=選手〈別名適用後の名前ごと、各期の順位〉）と、期ページの HTML（`h2#title_lives`・`h2#title_games` の節の `class="mj-title-video"` の数、`.mj-title-holder-photo` の数と `src` が `avatar` のもの）を scratchpad のスクリプトで集計。

| 項目 | 値 | 数え方 |
|---|---|---|
| 大会数 | 20 | search.json の t |
| 期数 | 363 | search.json の m（期ページの数とも一致） |
| title/ のページ数 | 384 | `find title -name '*.html'`（入口1＋大会20＋期363）。`sitemap-title.xml` の `<loc>` も384 |
| 決勝に出た選手の実人数 | 581 | search.json の p（名前「—」の行は p に入らない） |
| 決勝に出た延べ人数 | 1,485（名前「—」の2枠を含めると1,487） | p の各選手の期の数の合計／期ページの写真カードの合計。「—」は新人王戦 第10期 |
| 決勝ライブ | 94期・139本 | 期ページの title_lives 節 |
| 決勝動画 | 88期・480本 | 期ページの title_games 節 |
| 合計 | 146期・619本（両方ある期 36） | |
| いちばん古い年 | 1973（第1期王位戦） | m の年。年の無い期は2（リーチ麻雀世界選手権 第1・2回） |
| いちばん新しい年 | 2026（18期） | 同上 |
| 優勝回数の上位 | 前原雄大 11、魚谷侑未 10、荒正義・仲田加南 8（3位が同数2名） | p の順位=1 の数 |
| 決勝進出回数の上位 | 荒正義 33、前原雄大 31、藤崎智 30 | p の期の数 |
| 写真なし（代替アバター） | 期ページの写真カード1,487枠中511枠 | src が img/avatar.svg |

docs/notes/title-pages.md との差: 期数 362→363、年を持つ期 360→361、決勝ライブ 68期96本→94期139本、決勝動画 70期413本→88期480本、合計 108期509本→146期619本、両方ある期 30→36、検索の「約420ページ」→384ページ。数えた値を正とする（title-pages.md は直していない。コードを変えない指示のため）。

告知・SNS 向け動画の open issue: 無し（open 202件の題・本文を「告知・動画・SNS・宣伝・ショート・ティザー・promo・video」で照合。動画の語で当たったのは /live・帰り道・VideoObject などの別件。近いのは #82「SNSシェア・URLコピーボタンを設置する」だけで動画ではない。MCP の search_issues は0件）
### 手順2 (a) 録画（Playwright）

- 道具: セッションに入っている Playwright 1.56.1（`/opt/node22/lib/node_modules/playwright`）と Chromium（`/opt/pw-browsers`）。端末は Playwright の `iPhone 13`（viewport 390×664・deviceScaleFactor 3・タッチ・モバイルの UA）
- 1回目は `net::ERR_CERT_AUTHORITY_INVALID` で本番が開けなかった。`/root/.ccr/README.md` は「ブラウザの NSS ストアは設定済み」とするが、`/root/.pki/nssdb` は空（証明書0件）だった。
  `apt-get install libnss3-tools` で certutil を入れ、`/root/.ccr/agent-proxy-ca.crt`（プロキシの CA）を `/root/.pki/nssdb` に登録して通った（TLS の検証は外していない。セッションのコンテナの設定で、リポジトリは変えていない）。あわせて headless_shell ではなく `channel: 'chromium'` で起動した
- 操作: 入口 → プルダウン（`#title_select`）で「麻雀グランプリMAX」→ 大会ページで第16期 → 期ページ（決勝ライブ・決勝動画まで送る）→ 検索欄（`#title_filter`）に「白鳥翔」を1字ずつ入力 → 写真を押して出場した期の一覧。約22秒で最後まで通った
- 録画（`recordVideo`）: vp8 の webm、390×844 指定・25fps・22.9秒・1.43MB。**下の約1/5が灰色の余白**になった（iPhone 13 の viewport は 390×664 で、指定の高さとの差が余白になる）。
  また録画の解像度は CSS ピクセルのまま（390×664）で、大きさを3倍（1170×1992）に指定しても拡大されず余白が増えるだけだった（5.2秒・0.3MB の試しで確認）
- 別の手段を2つ試した: (1) CDP の `Page.startScreencast`（maxWidth 1170）→ 390×664 のまま、2秒で23フレーム。(2) スクリーンショットの連番 → **1170×1992**（deviceScaleFactor 3 が効く）、30枚で5.0秒（約6枚/秒）。
  30fps で mp4 にまとめると 1170×1992・30fps・1.0秒・288KB。1フレームずつ操作して撮れば、実時間に縛られない高解像度の素材が作れる（遅いのは撮る側だけ）
- 静止画を見た結果（`page.screenshot` 7枚と録画の3秒ごとのフレーム）: 入口・大会ページ・期ページの選手の写真（X の画像）、決勝ライブ・決勝動画の YouTube のサムネイル、検索結果の写真と期の一覧が映っていた。日本語の欠けは無し。
  ただしコンテナの日本語フォントは WenQuanYi Zen Hei などで、iPhone（ヒラギノ）とは字形が違う。読み込み直後の数フレームは写真が白い（遅延読み込み）。サムネイルは画面外の分は読み込まれていない（`loading="lazy"`）
- 気づいた点: 検索で完全一致の1人だけが出るとき、写真の期の一覧は最初から開いており、試しの「写真を押す」で閉じてしまった（制作時の台本で、押さずに見せるか押して開き直す）

### 手順2 (b) 描画（HyperFrames）

- 取得: `npm install hyperframes@0.8.114`（2026-10-03 公開の最新、Apache-2.0、Node 22 以上）を scratchpad に入れた。registry.npmjs.org はプロキシの対象外で通る。
  `hyperframes doctor` は Chrome Headless Shell が無いと出たが、`HYPERFRAMES_BROWSER_PATH` に Playwright の `chromium_headless_shell-1194/chrome-linux/headless_shell` を渡して描画できた（`browser ensure` のダウンロードは試していない）。ffmpeg 6.1.1・ffprobe は入っている。Docker は動いていない（不要）
- 既定で匿名の利用統計を送り、`init` は GitHub に AI の skill を確かめに行く。`HYPERFRAMES_NO_TELEMETRY=1`・`HYPERFRAMES_SKIP_SKILLS=1`・`HYPERFRAMES_NO_UPDATE_CHECK=1` を付けて実行した。
  `init` で作られたのはプロジェクトのフォルダ（`index.html`・`hyperframes.json`・`AGENTS.md`・`CLAUDE.md` など）だけで、`~/.claude/skills` と リポジトリの `.claude/` は変わっていない
- 雛形の `index.html` は gsap を cdn.jsdelivr.net から読む。試しでは npm の gsap 3.15.0 をプロジェクトの `assets/` に置いて読ませた
- フォント: Noto Sans JP Bold・Black の OTF を `raw.githubusercontent.com/notofonts/noto-cjk/main/Sans/SubsetOTF/JP/` から取得（各 4.6MB・4.9MB）し `@font-face` で読んだ
- 試しの構成（8秒・1080×1920）: 0〜3秒「タイトル戦 リニューアル」と数字のカウントアップ（0→363「期の決勝」、0→619「本の決勝の映像」、GSAP の tween の onUpdate で数字を書き換え）、3〜7秒 (a) の録画を mp4 にした素材（`<video>`）、7〜8秒「ryoei.pro/title/」
- 結果: `hyperframes render -f 30` で **h264（High）・yuv420p・1080×1920・30fps・8.0秒・731KB**。描画に1分55秒（うち compile 1分8秒、フレームの取り込み34秒、ソフトウェア GPU、2 worker）
- 静止画を見た結果（フレーム 3・20・45・80・120・190・230）: 日本語（Noto Sans JP）の欠けは無し。数字は 0 → 180/306 → 350/596 → 363/619 と進み、シークしても正しい値になる。
  録画の素材は取り込めて、写真も映る。ただし素材の最初が白（読み込み中）で、下に (a) の灰色の余白がそのまま出る。末尾の URL の画面で「ほか20大会」が折り返した（字の大きさの調整は制作時）
- プロキシに拒否されたドメイン（`recentRelayFailures` の全件）: `redirector.gvt1.com`、`www.google.com`、`android.clients.google.com`（Chromium の裏の通信）、`static.cloudflareinsights.com`（ryoei.pro が読む Cloudflare Web Analytics のビーコン）。HyperFrames の描画中の拒否は無し

### 手順2 (c) /brag（latent-spaces/brag）を読んだ結果

- 取得: `git clone --depth 1 https://github.com/latent-spaces/brag`（先頭 cb89b9f、2026-10-01）を scratchpad に。GitHub API（`api.github.com/repos/latent-spaces/...`）はセッションの対象外で 403、raw と git clone は通った。リポジトリ全体は 112MB（例の mp4 など）
- 中身: `skills/brag/`（SKILL.md・slim.md・references 6本・scripts〈`analyze_music_cues.py`・pyproject.toml・uv.lock〉・assets〈music 5曲と cues、sfx 約260ファイル〉、計 17MB）と `skills/brag-slim/SKILL.md`（12KB、同梱物なし）
- **SKILL.md の冒頭に「Claude Opus 5.5 なら（`--full`・`--voice` の指定が無い限り）brag-slim に切り替える」とある。** このセッションのモデルは Opus 5.5 なので、そのまま使うと brag-slim（HyperFrames も同梱の音も使わず、手元の道具で全部作る）になる
- ライセンス: リポジトリは MIT（Shunit Haviv Hakimi）。
  音楽5曲は ende.app の「Happy Beats / Business Moves」で、`assets/music/README.md` に「公開・再配布の前に正確なライセンスを確かめて書き添えること」とあり、**条件は書かれていない**（ende.app の規約は確かめていない）。
  効果音は Kenney（`sfx-analysis.md` に出典）、キーボードの打鍵音は opengameart の unicae_games「Keyboard Soundpack #1」で CC0（`references/audio.md`）。Kenney の素材のライセンスの文面は同梱されていない（Kenney は一般に CC0 だが、同梱物では確かめられない）
- 依存する道具: `/brag` は HyperFrames（`npx hyperframes check` が描画前の関門、`render`）と HyperFrames の domain skill（`hyperframes-core`・`-animation`・`-creative`・`-keyframes`・`-cli`。`npx hyperframes skills update` で入れる）、ffmpeg、曲の拍の解析に uv と Python（librosa・numpy・scipy・soundfile）か `npx hyperframes beats`、`--voice` のとき Kokoro の TTS。brag-slim は道具を指定せず、ヘッドレスブラウザと ffmpeg 程度を想定
- `.claude/skills/` へ複写するときの範囲の案: `skills/brag/` を丸ごと（SKILL.md が `<skill-dir>/assets`・`references`・`scripts`・`slim.md` を参照するため、一部だけでは動かない。17MB、うち mp3 が大半）。
  音を使わないなら `skills/brag-slim/SKILL.md` 1つ（12KB）で足りる。どちらも HyperFrames の domain skill は別に要る（/brag の場合）。ライセンスの確認が済むまでは、mp3 を入れない形（brag-slim か、music を除いた複写）が無難

### 手順3 構成の材料

(i) 推す数字の候補
1. **「363期の決勝」**（20大会）: リニューアルの中身（全期に期ページがある）を1つの数で言える。1973年（第1期王位戦）からの記録と組み合わせると「50年分」の重みも出せる
2. **「619本の決勝の映像」**（決勝ライブ139本＋決勝動画480本、146期）: 今回の目玉の「期ページから決勝の放送をすぐ見られる」を表す。YouTube のサムネイルが並ぶ操作デモへそのままつながる
3. **「581人の決勝進出者」**（延べ1,485人）: 検索欄で選手名から出場した期を引ける機能につながる。優勝回数の最多（前原雄大 11回）や決勝進出の最多（荒正義 33回）を添える案もあるが、荒正義は写真が無い（代替アバター）ため顔を映す演出には向かない

(ii) 操作デモの候補（条件: 決勝ライブと決勝動画の両方があり、決勝の全員に写真がある〈期ページの写真カードに代替アバターが無い〉期。該当は26期）
1. **麻雀グランプリMAX 第16期（2026年3月22日）＋検索「白鳥翔」**: 決勝ライブ2本（初日・最終日）・決勝動画8本と最多、最新の年。白鳥翔は第42期鳳凰位で写真あり・決勝進出20回（今回の録画で通しを確認済み）
2. **麻雀マスターズ 第32期 ＋検索「前原雄大」**: ライブ1本・動画5本。優勝 本田朋広、4位に白鳥翔。前原雄大は優勝11回で最多・写真あり・決勝進出31回で、一覧が長く見せ場になる
3. **桜蕾戦 第11期（2026年）＋検索「魚谷侑未」**: ライブ1本・動画4本、女流の大会。魚谷侑未は優勝10回・決勝進出26回・写真あり。男女どちらの大会もあることを見せられる
- 鳳凰戦・十段戦は最近の期（鳳凰戦 第39〜42期、十段戦 第40〜43期）に決勝動画が無く、十段戦は写真の無い選手がいる期が多い。看板の大会を映すなら、入口やプルダウンで名前を見せる形になる

(iii) 制作の環境
- **クラウドセッション（今回試した）**: 録画（Playwright・Chromium）・描画（HyperFrames・ffmpeg）・日本語フォントの取得まで全部できた。足りないもの: プロキシの CA をブラウザの NSS に登録する手順（毎回のコンテナで要る。certutil は apt で入る）、iPhone の実機の字形（コンテナは WenQuanYi 等）、音を聞いて確かめる手段。許可ドメインの追加は不要（拒否は analytics と Google の裏の通信だけ）。描画は8秒で約2分なので、25秒でも10分前後の見込み（未計測）。**おすすめ**
- **Codespace**: 今回は試していない。Node はあるはずだが、Playwright の Chromium・ffmpeg・日本語フォントは入れる必要があるかもしれない（未確認）。クラウドより優れる点は特に無い
- **平野さんの Windows 11 の PC**: 出来上がりの mp4 を見る・音を聞く・X へ投稿するのはここ。実機の iPhone で画面収録すれば、本物のヒラギノの字形と実際のスクロールの動きで操作デモを撮れる（Playwright の録画は 390×664 の低解像度で、高解像度にはスクリーンショット連番が要る）。HyperFrames を PC で動かす必要は無い

(iv) 1本の指示で進めるときの段取りの案
1. 素材: クラウドセッションで、(ii) で選んだ期と検索の操作を、スクリーンショットの連番（1170×1992、1フレームずつ操作）で撮って mp4 にする。読み込み待ち（写真・サムネイル）を済ませてから撮り始める。代わりに平野さんが iPhone の画面収録を渡す形もある（その場合は受け渡しの方法を先に決める）
2. 描画: HyperFrames で 1080×1920・30fps の構成（数字のカウントアップ 3〜5秒 → 操作デモ 12〜18秒 → URL 2〜3秒）を作り、`hyperframes check` のあと `render`。gsap とフォントはローカルに置き、利用統計は切る。音を入れるかと、入れるならライセンスの確かな素材（CC0 等）にするかは事前に決める
3. 受け取り: 動画はコミットしない前提なら、セッションの `SendUserFile`（Claude のアプリにファイルを送る）で平野さんに渡すのが手軽（今回は送っていない）。ほかに、指示で許可したうえで作業ブランチの外（例: 公開されない一時のブランチや issue への添付）に置く方法もあるが、どれも未確認。X の仕様（縦横比・長さ・ビットレート）に合わせた書き出しはチャット側の決定待ち

### 指示とルールの食い違い

- 指示の「作業ブランチに入れるのはこの指示のログだけ」と、CLAUDE.md「作業ログ」節の「指示の完了時に、その指示の『決定』節を `docs/decisions/<分野>.md` に足す（ログと同じコミットでよい）」が食い違った。
  ルールの側を優先し、指示の「決定」節の3項目を `docs/decisions/title.md` に足した（チャット側の「前提」は平野さんの決定ではないため入れていない）

## 報告

- 状態: 判断待ち
- ブランチ: work/1003-vid-01
- ログ: https://github.com/retroeater/mj/blob/work/1003-vid-01/docs/logs/CHAT-1003-VID-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1003-vid-01
- 確認用URL: なし（ログと docs/decisions のみ。生成物・コードは変えていない）
- マージ: 未（指示の「マージ: 判断待ちで止まる」のとおり）
- issue: なし（告知・SNS 向け動画の open issue は無し）
- 結果の要点:
  - 手順1: 20大会・363期・title/ 384ページ・決勝進出 581人（延べ1,485人）・決勝ライブ 94期139本・決勝動画 88期480本（合計146期619本）・1973〜2026年・優勝回数 前原雄大11／魚谷侑未10／荒正義・仲田加南8・決勝進出 荒正義33／前原雄大31／藤崎智30（表と title-pages.md との差は「経過」）
  - 手順2 (a): 本番を iPhone 幅で開き、入口→大会→期（ライブ・動画）→検索→期の一覧を通しで録画できた。写真と YouTube のサムネイルも映る。録画は 390×664 の低解像度で、高解像度（1170×1992）はスクリーンショットの連番で得られる。ブラウザにプロキシの CA の登録が要った
  - 手順2 (b): HyperFrames 0.8.114 で、Noto Sans JP とカウントアップと録画の素材を含む 1080×1920・30fps・8秒の mp4（731KB）を書き出せた（描画約2分）
  - 手順2 (c): /brag は MIT。同梱の音楽（ende.app）は利用条件の記載が無い。Opus 5.5 では brag-slim に切り替わる。複写するなら skills/brag 全体（17MB）か brag-slim の1ファイル
  - 手順3: 数字の候補は「363期」「619本」「581人」、デモは GPMAX 第16期＋白鳥翔ほか2組、制作の環境はクラウドセッションがおすすめ（詳細は「経過」）
- 判断が必要なこと:
  - 動画の構成で推す数字（(i) の3つから）とデモの組（(ii) の3組から）
  - 操作デモの素材を、クラウドのスクリーンショット連番で作るか、平野さんの iPhone の画面収録にするか（字形・解像度・手間が違う）
  - 音を入れるか。入れるなら /brag 同梱の ende.app の曲は条件が書かれていないため使わず、条件の確かな素材にするか
  - 出来上がった動画の受け取り方（`SendUserFile` でよいか）
  - docs/notes/title-pages.md の古い数値（期数・本数・ページ数）を直すか（今回は直していない）
  - 指示とルールの食い違い（「経過」の最後）: docs/decisions/title.md への追記をこのブランチに入れた。不要なら外す
- 未確認の項目:
  - ende.app の音楽と Kenney の効果音のライセンスの原文（同梱物に無く、サイトも読んでいない）
  - Codespace での録画・描画（試していない）
  - 15〜30秒の描画にかかる時間（8秒で約2分からの見込みだけ）
  - X に投稿したときの見え方（投稿しない指示のため）
  - `hyperframes browser ensure`（Chrome のダウンロード）が通るか（Playwright の headless_shell で足りたため試していない）
- エラー:
  - 録画の1回目が `net::ERR_CERT_AUTHORITY_INVALID`（ブラウザの NSS ストアが空）。certutil を入れてプロキシの CA を登録して解消（`/root/.ccr/README.md` の「NSS ストアは設定済み」と実物が違った）
  - GitHub API の `api.github.com/repos/latent-spaces/brag/...` と `api.github.com/search/issues` が 403（セッションの対象リポジトリ外）。git clone・raw と、repo 単位の API・MCP で代えた

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
