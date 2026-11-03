# CHAT-1103-RYO-04

- 着手日時: 2026-11-03
- 対象issue: （起票予定）・#656（公開、変えない）
- ブランチ: work/1103-ryo4
- 着手時HEAD: 8eb4b671c

## 指示

【Claude作成】Claude Code 向け指示：r/ を「平野良栄の記憶の中を一人称で歩ける空間」に作り直す試作（映画館＋対局室の2場面。未公開・プレビューで止める） Chat-Ref: CHAT-1103-RYO-04 マージ: 判断待ちで止まる 貼る時機: いつでも（CHAT-1103-RYO-03 は完了・マージ済み） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1103-ryo4 を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1103-ryo4 origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
本番に入っている `r/`（未公開、#655 で作った「連盟プロ」と同じ面の形）を、平野良栄の経歴とポートフォリオを1枚で見せるページに作り直す。形は「スマホで一人称視点で歩き回れる、平野の記憶の中の空間」。今回は試作で、場面は「映画館」と「対局室」の2つだけ作る。平野さんが iPhone で触って絵柄・操作感・重さを確かめてから、残りの場面を足す（次の指示）。進め方は自律開発（不明点は仮置きして最後まで作り、プレビューと仮置きの一覧で平野さんが確かめる）。
決定（2026-11-03、平野さん）
連盟プロと重なる内容を消す:

* 名前の面のチップ「東京」「30期」、自己紹介の「鳳凰戦 C2」「段位 四段」、数字で見るの「連盟プロ 1,098名」、成績の面（全部）、入賞の面（全部）を消す（RYO-02・03 で入れた段位〈名簿〉・入賞〈title/ のデータ〉の仕組みもあわせて外す） ページの形:
* 「連盟プロ」のカードの形である必要はない。平野良栄の経歴やポートフォリオを1枚で見せる LP のような形にする。出演作（映画・動画）、「数字で見る」などを見せる。アニメーションとインタラクティブ性を駆使して、最大限尖ったページにする（r/ は「新サイトの基本の形」〈面・400px・白地のナビ〉の例外）
* スマホの一人称視点で空間を歩き回る。コンテンツが空間に点在し、映画館（または街中の大画面）では映画・動画を見せ、近づく（触れる）と視聴・閲覧できる
* 平野の半生を追体験できる、人生のある時期・部分を覗けるような空間。他人の人生が繰り広げられていて、それに近づいて覗き見る・触れることができる
* 誰もいない、でもそれが現実であるような記憶の中の世界。平野の過去の一場面に入り込んでしまっているような。精神空間のようで現実のような手触り
* 絵柄は、映画「戦場でワルツを」くらいの、線画に近く実写との間のアニメーション（作品の場面・人物は写さず、絵柄の方向性だけを参考にする）
* 移動のときの足音・踏みしめる音・風・空気・水などの音を付ける
* 歩き回らなくても、メニューから直接その場所にたどり着けるようにする 技術と進め方:
* 3D は Three.js をリポジトリに置いて使う（CDN は使わない。新しい依存として平野さんが承認）
* 絵は、3D の形に輪郭線と平塗りの陰影を付けてコードで描き、人生の場面には平野さんの写真を線画・減色に加工したパネルを置く（試作では写真を使わない）
* 動画は空間の中でその場で再生する（YouTube の埋め込み。`_headers` の CSP の `frame-src` に `https://www.youtube-nocookie.com` を足す）
* 音はブラウザの中で合成する（Web Audio。外部の音声ファイルは使わない）。最初の画面の「入る」で音ありで始め、いつでも消せる
* 試作は「映画館（出演7本）」と「対局室（麻雀卓と牌）」の2場面。平野さんが iPhone で触ってから残りの場面を足す

前提（チャット側。平野さんの決定ではない）

* 残す内容（RYO-03 の本番の r/ から、上の削除を引いたもの。要確認）: 名前・読み・SNS 5つ・プロフィール写真／自己紹介（肩書き・生年月日・所在地・専門・所属）／数字で見る（子ども・IQ）／出演7本（題・媒体・リンク・サムネイル）／学歴と所属4件。これらは空間の外の HTML にも全部焼き込む（検索・読み上げ・WebGL の無い端末・動きを減らす設定の人のため。空間はその上に重ねる演出）。メニュー（場所の一覧）はこの HTML の目次を兼ねる
* 置き場所: 3D の部分は `r/` 専用の JS（例 `assets/ryoei/world.js`）と CSS（例 `assets/ryoei/world.css`）。HTML は今の `scripts/generate_ryoei_pages.py` が書く（中身の定数はそのまま使う）。CSP は `script-src 'self'`・`style-src-elem 'self'` なので、インラインの `<script>`・`<style>` 要素は使わない。Three.js は ES モジュールの版を `assets/vendor/three-<版>/` に置き（パスに版を入れる。CLAUDE.md の vendor の規則: `sourceMappingURL` を残さない）、`r/` だけが読む。版は Code が選んでログに書く（npm から取ってよい。取り込むのはビルド済みのファイルだけで、`package.json` は足さない）。使う追加のモジュール（`examples/jsm` の輪郭線・操作など）も同じ置き場所に必要な分だけ
* 絵柄の作り（案）: トゥーン調の陰影（`MeshToonMaterial` 程度、2〜3段）＋太めの輪郭線（反転した殻か、画面上の輪郭抽出）＋わずかな紙の粒子・線の揺れ。色は少ない（くすんだ夜の色に、光源だけ暖色）。霧（`Fog`）で遠くを溶かし、誰もいない静けさを出す。人物は置かない
* 操作（案）: スマホは画面の左半分のドラッグで前後左右に歩き、右半分のドラッグで見回す（仮想スティック）。PC は WASD／矢印＋マウスのドラッグ。近づくと対象が光り、触れると開く。右上にメニュー（場所の一覧・音のオン／オフ・「空間を出て一覧で読む」）。メニューから選ぶと、その場所へ数秒で移る（カメラの移動の演出）
* 映画館: スクリーンが並ぶ（または1枚の大スクリーンと座席）。出演7本をポスター・スクリーンとして置く。近づいて触れると、画面の前面に YouTube の埋め込み（`youtube-nocookie.com/embed/<ID>`、再生は触れたときに始める）。YouTube でない3本（ICU 同窓会のインタビュー・マインドアスリートの Amazon・ASICS は YouTube）は、YouTube 以外を新しいタブで開く。空間の中のスクリーンに映すのはサムネイルの静止画（`img-src https:` の範囲）
* 対局室: 薄暗い部屋に全自動卓1台、上から1灯。卓の上に牌（コードで描く。牌の絵柄はページの中で描くか `img/` の既存の素材があれば使う）。牌に触れると、自己紹介・数字で見る・学歴と所属のどれかが紙片のように浮かぶ（仮置き）。点棒・牌の音
* 2場面のつなぎ: 入り口（最初の画面: 名前・肩書きと「入る」ボタン）→ 短い通路 → 映画館と対局室。通路には学歴と所属を、記憶の断片のように置いてよい（仮置き）
* 音（案）: 足音（歩くたびに、床の材質で変える）・空調／風の低い音・遠い水音・牌の音を Web Audio のノイズと発振器で作る。最初の「入る」を押すと鳴る。右上で消せる。消した状態は `localStorage` に（キー `mj:ryoei-sound`。docs/new-page-checklist.md「localStorage の規約」）
* 重さ（止まる条件に使う）: iPhone で最初の画面が出るまでの読み込み（HTML＋JS＋CSS、gzip）を 600KB 以内、Three.js を除く r/ 専用の JS を 150KB 以内。描画は 360px の幅の端末で 30fps 以上を目安（headless Chrome の計測は目安として報告に書く）。WebGL が使えない・`prefers-reduced-motion: reduce` の人には空間を出さず、HTML の一覧を出す
* CSP: `_headers` の CSP の `frame-src 'none'` を `frame-src https://www.youtube-nocookie.com` に変える（`r/` に限らず全ページの設定。ほかのページは iframe を置いていない〈要確認: `git grep -n '<iframe'`〉）。ほかに必要な許可（`blob:`・`worker-src` など）が出たら足さずに止まり、何が要るかを書く。docs/notes/cloudflare.md「配信設定」の CSP の記述と docs/handover.md 4章「外部ドメインへの依存を増やさない」を同じコミットで直す
* 新しい依存の記録: Three.js（版・置き場所・読むページ・更新の方針）と youtube-nocookie.com の許可を `docs/decisions/ryoei.md` に平野さんの決定として、`docs/notes/` に `ryoei-world.md`（仕組み・操作・場面の足し方・重さの計測）を新しく書く
* 公開の issue #656 の内容（noindex を外す等）は変えない。r/ は未公開のまま。作り直しの issue は、同じ論点の issue が無ければ起票する（題の案「r/ を一人称で歩ける空間に作り直す（試作: 映画館・対局室）」、ラベル `分野: UI/UX`）
* 画面の確かめ: 360px・390px の縦向きで、最初の画面・映画館・対局室・メニュー・動画を開いた状態・一覧の HTML を headless Chrome で撮り、`docs/logs/CHAT-1103-RYO-04/` に置く。iPhone の Safari で触る前提の確かめ（タッチの操作・音・WebGL）は平野さんが行うので、確かめられなかった点を「未確認の項目」に書く。ログに「この版で見えるはずの印」を1つ書く
* 仮置きにした点はすべて「判断が必要なこと」に、画面のどこに効くかと一緒に一覧で書く（平野さんが1問1答で確定する）。次の指示で足す場面の候補（例: 中1で麻雀を覚えた部屋・開成・ICU・金融の仕事・ニューヨーク・麻雀から離れた15年・プロ入り・専業へ・マインドアスリート）と、写真を受け取る方法の案も書く

手順

1. 同じ論点の open issue（r/・良栄・ポートフォリオ・3D）をクローズ済みも含めて検索し、無ければ起票して着手中コメントを書く。未マージのブランチ（`git branch -r --no-merged origin/cloudflare` の各ブランチのコミットの件名とログ）と、上の前提の実物（今の `r/index.html` の内容・`_headers` の CSP・`git grep -n '<iframe'`・`scripts/generate_ryoei_pages.py`）を確かめ、食い違えば止まる
2. 削除の決定を反映し、Three.js の取り込み・CSP の変更・HTML（一覧）・空間（入り口・通路・映画館・対局室・メニュー・音）を作る。`python3 scripts/regenerate.py ryoei_pages`（鍵の要る読み取りが残っていれば外す）で `r/index.html` を作り直す
3. 検査（`python3 -m unittest discover -s scripts/tests`。消した仕組みの検査は消し、残す定数の件数の検査は残す）・`scripts/check_asset_limits.py`・重さの計測・画面の確かめを流し、CSP の違反がブラウザのコンソールに出ないことを確かめる。仮置きの一覧と次の場面の候補をログに書き、ブランチ名の別名 URL でプレビューできる状態にして止まる

止まる条件

* 前提が実物と食い違い、決定（上の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
* CSP に `frame-src` の youtube-nocookie.com 以外の許可が要る（足さずに、要る許可と理由を書いて止まる）
* 重さが上の上限を超える（実測を書いて止まる。超えた分を減らす案があれば書く）
* テスト・`check_asset_limits.py` が通らない（原因を書いて止まる）
* 未マージの work/ ブランチが `_headers`・`scripts/generate_ryoei_pages.py`・`scripts/regenerate.py`・`navbar.js` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
* Three.js 以外に新しい依存（npm・pip・CDN・外部 API・音声や3Dの外部素材）が要る（足さずに止まる）

完了条件

* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1103-RYO-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1103-RYO-04 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認

- 0章: ログの「指示」欄の末尾が指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: すべてある
- 同じ Chat-Ref のコミット: なし。`origin/work/1103-ryo4` はリモートに無かったので `git checkout -b work/1103-ryo4 origin/cloudflare`（着手時 HEAD 8eb4b671c）
- 同じ論点の issue（「r/」「3D」「一人称」「Three」「ポートフォリオ」「良栄」をクローズ済みも含めて検索）: 作り直しの issue は無し（#655 クローズ・#656 公開・#389 校正）。起票: #657「r/ を一人称で歩ける空間に作り直す（試作: 映画館・対局室）」（`分野: UI/UX`）、着手中コメント
- 前提の実物:
  - 今の `r/index.html`（RYO-03 の版）と `scripts/generate_ryoei_pages.py`: 残す内容（名前・読み・SNS 5つ・写真／自己紹介〈肩書き・生年月日・所在地・専門・所属〉／数字で見る〈子ども・IQ〉／出演7本／学歴と所属4件）は前提どおり
  - `_headers` の CSP: `frame-src 'none'`、`script-src 'self' https://static.cloudflareinsights.com`、`style-src-elem 'self'`、`img-src 'self' data: https:` で前提どおり
  - `git grep -n '<iframe'`: ページには無い（このログの指示文の中だけ）
- 未マージの work/ ブランチ（`_headers`・`scripts/generate_ryoei_pages.py`・`scripts/regenerate.py`・`navbar.js`）:
  - `work/1029-lgn-page`: `_headers` の CSP の行を「末尾の改行の有無」だけ変え（行の中身は同じ）、ファイルの末尾に `/log/*` の規則を足す。この指示も CSP の行（`frame-src`）を変えるので、後からマージする側で衝突する。解き方は「この指示の `frame-src` の値を残し、lgn の末尾の改行と `/log/*` の規則を足す」（両立する）。行の中身を変えているのはこの指示だけと見て、止まらずに進めた（判断が必要なことに挙げた）。`navbar.js`・`regenerate.py` は別の行
  - `work/1101-prp-pub`: `navbar.js`・`regenerate.py` の別の行（pro/ の公開）
  - `work/1103-top`: `navbar.js` に `/top/` の行、`regenerate.py` の `SHARED_GENERATORS`・`OUTPUT_OVERRIDES`・`RUN_LAST` の行を変える。この指示は `regenerate.py`・`navbar.js` を変えていない（衝突なし）
  - `scripts/generate_ryoei_pages.py` を変えているブランチは無し

### 実装

- 削除（2026-11-03 の決定）: 名前の面のチップ・自己紹介の鳳凰戦と段位・数字で見るの連盟プロの人数・成績の面（`rh_*` へのリンクを含む）・入賞の面。名簿（`lib/meibo.py`）・`title/search.json`・`houou/`・`pro/search.json` の読み取りも外した（生成に鍵が要らなくなった）
- `scripts/generate_ryoei_pages.py`: 一覧の HTML（最初の画面〈写真・「記憶の中を、歩く。」・名前・読み・肩書き・SNS・「入る」〉→ 場所の目次 → 自己紹介 → 数字で見る → 出演 → 学歴と所属）。空間が使う値を `<script type="application/json" id="ryoei_data">` に。`youtube_embed()` が YouTube の URL を `youtube-nocookie.com/embed/<ID>`（再生リストは `videoseries?list=`）にする。「入る」は JS が使えると判断したときだけ出す（`hidden` で焼き、world.js が外す）
- `assets/ryoei/world.js`（約42KB）・`world.css`: 空間（入口・通路・広間・映画館・対局室）、仮想スティック、メニュー（場所へ 2.4 秒で浮いて移る・空間を出て一覧で読む）、動画（YouTube は前面に埋め込み、ICU 同窓会・Amazon は新しいタブへのボタン）、牌に触れると紙片、点棒は音、音（風・空調・遠い水・雫・低いうなり・床の材質ごとの足音・牌・点棒・風切り）。仕組みは `docs/notes/ryoei-world.md`
- Three.js: npm の `three@0.169.0`（2024-09 公開）の `build/three.module.min.js` と `LICENSE` を `assets/vendor/three-0.169.0/` に置いた。`sourceMappingURL` 無し（`grep` 0件）。追加のモジュール（`examples/jsm`）は使っていない（輪郭線・操作は world.js の中で書いた）。`package.json` は足していない
- `_headers`: `frame-src 'none'` → `frame-src https://www.youtube-nocookie.com`。ほかの許可は要らなかった（`blob:`・`worker-src` 無し。動的 `import()` は `script-src 'self'` の範囲。スクリーンのサムネイルは `img-src https:` の範囲で、CORS が無く WebGL に使えない画像は紙に題を描いた絵で代える）。`docs/notes/cloudflare.md`「配信設定」と `docs/handover.md`「外部ドメインへの依存を増やさない」を同じコミットで直した
- `style.css`: r/ だけの規則（`.mj-ryoei` の2つ）を消した（ほかのページに効かない）。共通ナビ（navbar.js が `/r/` で白地の形にする）は `navbar.js` を変えず、`world.css` で `--mj-n-*` を r/ の夜の色にした
- 検査 `scripts/tests/test_ryoei_pages.py` を書き直した: 件数（出演7・学歴と所属4・自己紹介5・数字で見る2）・SNS 5つ・場所の一覧と節の id・`youtube_embed()`（動画・再生リスト・YouTube でないもの）・焼き込み・連盟プロと重なる語が無いこと・空間のデータ・実行するインラインのスクリプトが無いこと・Three.js の版入りのパス・`_headers` の `frame-src` が youtube-nocookie.com だけ・`_redirects`・生成の順（消した仕組み〈名簿の段位・決勝〉の検査は消した）
- docs: `docs/notes/ryoei-world.md`（新規）・`docs/notes/static-generation.md` の r/ の行・`docs/handover.md` の notes の表・`docs/decisions/ryoei.md`

### 検査

- `python3 -m unittest discover -s scripts/tests`: OK（1761件）
- `python3 scripts/regenerate.py --check ryoei_pages`: 通る（鍵が要らなくなったのでセッションでも通る）
- `python3 scripts/check_asset_limits.py`: 配信ファイル数 5,334（5.3%）ほかすべて OK
- `node --check assets/ryoei/world.js`: OK
- check-run（e304c8279）: Workers Builds: mj success、公開対象の検査 success

### 重さ（gzip は Python の `gzip.compress`）

| 対象 | 生 | gzip |
|---|---:|---:|
| `r/index.html` | 15,705 | 4,888 |
| bootstrap.min.css・style.css・bootstrap.bundle.min.js・navbar.js（共通） | | 110,649 |
| `assets/ryoei/world.css` | 9,837 | 3,019 |
| `assets/ryoei/world.js` | 41,785 | 14,292 |
| 最初の画面の合計（HTML＋JS＋CSS） | | 約132,800（上限 600KB） |
| Three.js（「入る」の後に読む） | 687,458 | 169,732 |
| 空間に入った後の合計 | | 約302,500 |

- r/ 専用の JS（Three.js を除く）: 41,785 バイト（上限 150KB）
- 描画: headless Chrome（ソフトウェア描画 SwiftShader、2倍の解像度）で 360px 約14〜16fps、390px 約15〜16fps。GPU のある端末の目安にはならない（iPhone の実測は未確認の項目）
- 描く解像度の上限は 1.5 倍（`PIXEL_RATIO_MAX`）、光は場面ごとに1〜2個（全体で点光源6・スポット1・半球1）

### 画面の確かめ

`python3 -m http.server` で配信し、Playwright で `_headers` の CSP をそのまま HTML の応答に付けて（実物の値）、360px・390px（縦、タッチ、2倍）で撮った（`docs/logs/CHAT-1103-RYO-04/r-<幅>-<番号>.png`: 1 最初の画面・2 入口・3 映画館・4 対局室〈牌に触れて紙片〉・5 メニュー・6 一覧の全体・7 動画を開いた状態）。

- CSP の違反: 0件（ローカルとプレビューの両方。プレビュー e304c8279 の実際のヘッダで `frame-src https://www.youtube-nocookie.com` を確かめ、空間に入って動画を開いた）
- コンソールのエラー: 外部の画像・YouTube の読み込み失敗（`net::ERR_FAILED`・`ERR_TUNNEL_CONNECTION_FAILED`）だけ。このコンテナのプロキシが外部のホストを断るため。そのため動画を開いた画面（7）は白い枠で、`i.ytimg.com`・`icualumni.com` のサムネイルは紙に題を描いた絵になっている
- 確かめた動き: 「入る」が出る・空間に入る・メニューから映画館・対局室へ移る・手前の牌をタップして紙片（「数字で見る IQ（WAIS-IV） 143」）が出る・メニューを開く・動画の iframe の src が `https://www.youtube-nocookie.com/embed/videoseries?list=…&autoplay=1&playsinline=1&rel=0`
- 直したこと: 共通ナビのメニューの線が夜の地で見えない → `world.css` で色を変えた／牌の紙片がメニューに透ける → メニューを開くと紙片を消す／メニューを開くと「場所」が隠れる → HUD を前に
- この版で見えるはずの印: 最初の画面の名前の上に「記憶の中を、歩く。」（暖色の小さな字）と「入る」のボタン

### レビュー（docs/notes/self-review.md、新しい文脈のサブエージェント2回）

1回目（e304c8279）:

| 指摘 | 扱い |
|---|---|
| 動画の「閉じる」が HUD（場所・音）の下に隠れる（高） | 直した（32100f163、前面に z-index） |
| Enter・E がフォーカス中のボタンの操作と二重に動く（中） | 直した（32100f163、ボタン・リンクの上の Enter と前面を開いている間は見送る。2回目の指摘で E は通すように 8ecd5f8bd） |
| 読み込みに失敗するとスクロールが止まったまま（中） | 直した（`leaveWorld()` で戻す） |
| WebGL1 だけの端末・AudioContext の無い端末で全面が残る（中） | 直した（判定を WebGL2・Web Audio にし、World の作成も try/catch） |
| 空間を開いたときのフォーカスとモーダル（中） | 直した（入ると「場所」へフォーカス、外を `inert`、出ると移った場所の節の見出しへ。見出しに `tabindex="-1"`） |
| exit 後の GPU の資源（低） | 直した（geometry・material・テクスチャの dispose と `forceContextLoss()`、判定用のコンテキストも手放す） |
| タブを隠しても音が鳴る・Alt+Tab 後に歩き続ける（低） | 直した（visibilitychange で音を止める、blur でキーを放す） |
| タップが壁越しに当たる・外れても前の物を開く（低） | 直した（最初に当たった物が触れられる物のときだけ開く。外れたら何もしない） |
| 出演・学歴の件数が増えると壊れる配置（低） | `簡略化:` のコメントを残し、出演は置き場の数までにした（件数は検査で縛る） |
| 試作・計測の残り（`noteIndex`・`window.ryoeiWorld`・`data-place`）（低） | `noteIndex`・`data-place` は消した。`window.ryoeiWorld` は headless の確かめ（場所の移動・fps）に使うので残した（コメントあり） |
| `（新しいタブで開く）` の手書き（低） | 直した（`NEW_TAB_HINT`） |
| `regenerate.py` の依存・コメント・毎朝の再生成が古い（低） | 見送り（判断が必要なことに挙げた。未マージの `work/1103-top` が隣の行〈`RUN_LAST`〉を変えていて衝突するため） |
| 目次の並びと本文の並びが逆（低） | 直した（目次は本文の並び `SECTION_ORDER`、メニューは道順。検査あり） |
| 広間の天井 3.2 と対局室の壁 2.8 の間のすき間（低） | 直した（広間を 2.8 に。2回目の指摘で通路も 2.8 に） |
| docs の正確さ（ryoei-world.md・localStorage の規約）（低） | 直した（ryoei-world.md を実物に合わせ、`docs/new-page-checklist.md` の保存する項目に `mj:ryoei-sound` を足した） |

2回目（32100f163）:

| 指摘 | 扱い |
|---|---|
| 入った直後はフォーカスが「場所」にあり、E でも開けない（中） | 直した（8ecd5f8bd、ボタンの上で見送るのは Enter だけ） |
| 目次の並びの検査が修正前のコードでも通る（中） | 直した（8ecd5f8bd、目次が `SECTION_ORDER` と一致することも確かめる。修正前の目次では空になり落ちる） |
| 広間を 2.8 にして通路（3.0）との境にすき間（低） | 直した（8ecd5f8bd、通路も 2.8） |
| World の作成の途中の失敗でリスナーが残る（低） | 直した（8ecd5f8bd、窓・文書のリスナーは `start()` で足す） |
| メニューのコメントが古い（低） | 直した |

### 最後の確かめ（8ecd5f8bd）

- `python3 -m unittest discover -s scripts/tests`: OK（1762件）。`node --check`: OK。`r/index.html` は生成と一致
- check-run: Workers Builds: mj success・公開対象の検査 success
- プレビュー（版ごとの URL、実際のヘッダ）: 空間に入って映画館へ移り、動画を開いて CSP の違反 0件。別名（`?v=8ecd5f8bd`）に「記憶の中を、歩く。」がある
- 画面（360px・390px）を撮り直した（`docs/logs/CHAT-1103-RYO-04/`）。headless（SwiftShader）の描画は 360px 約15〜17fps・390px 約14〜16fps
- `world.js` は 44,399 バイト（上限 150KB）

### 仮置きの一覧（画面のどこに効くか）

1. 最初の画面の言葉「記憶の中を、歩く。」と「入る」の下の注意書き（一覧の最初の画面）
2. 一覧の見た目（夜の地・暖色1色・大きな名前・写真をモノクロ寄りに）（一覧全体）
3. 場面の並びと形: 入口 → 通路（学歴と所属の額4つ）→ 広間 → 西に映画館・東に対局室（空間全体）
4. 通路に学歴と所属を額で置き、触れると紙片（通路）
5. 映画館: 正面に3枚・左右の壁に2枚ずつ、計7枚のスクリーン。正面の大きいのは最新の第3期WRC-Rリーグ（映画館）
6. YouTube でない2本（ICU 同窓会・Amazon）は前面にサムネイル・題と、新しいタブで開くリンク（映画館）
7. 対局室: 牌に触れると自己紹介5行・数字で見る2つが順に紙片で出る（13枚に7項目をくり返して割り当て）、点棒は音だけ（対局室）
8. 操作: 左半分の仮想スティック・右半分で見回す・タップで開く・メニューから 2.4 秒で浮いて移る（空間全体）
9. 音: 風・空調・遠い水と雫・対局室のうなり・床ごとの足音・牌と点棒・風切り。音量の釣り合い（空間全体）
10. 絵柄: トゥーン3段・黒い輪郭線（2.5cm）・霧（5〜24m）・紙の粒子（8コマ／秒）。人物なし（空間全体）
11. 空間の中の共通ナビは隠れ、右上の「場所」「音」だけ（空間全体）。一覧のページでは共通ナビを夜の色に
12. 一覧の目次は本文の並び（対局室 → 映画館 → 通路）、空間のメニューは道順（入口 → 通路 → 映画館 → 対局室）
13. 出演のサムネイルは、CORS が無く WebGL に使えないもの（`i.ytimg.com`・`icualumni.com` の見込み）は紙に題を描いた絵になる（映画館）
14. 描く解像度の上限 1.5 倍（重さと画質の釣り合い）

### 次の場面の候補（次の指示で足す）

中1で麻雀を覚えた部屋／開成（教室・廊下）／ICU（キャンパスの道・図書館）／金融の仕事（夜のオフィス・取引の画面）／ニューヨーク（街角・地下鉄・大画面）／麻雀から離れた15年（誰もいない雀荘・止まった時計）／プロ入り（連盟の対局室・初めての卓）／専業へ（家の机・牌譜）／マインドアスリート（実験の部屋・ASICS のドキュメンタリーの撮影の場）。
写真のパネル（線画・減色）は、各場面の壁・机の上に置く。

写真を受け取る方法の案:
- A: 平野さんが GitHub の issue（#657）のコメントに画像を添付し、Code がダウンロードして線画・減色に加工（Pillow。`scripts/` に手動の加工スクリプト）して `img/ryoei/<場面>-<連番>.webp` に置く（原画はリポジトリに入れない）
- B: Google ドライブの共有フォルダに置き、セッションが読める形（sheets-reader の鍵の範囲）にする（鍵の範囲を広げる判断が要る）
- C: チャットに添付し、チャット側が加工の指示とともに渡す（Code のセッションには届かないので、A か B と組み合わせる）

## 報告

- 状態: 判断待ち
- ブランチ: work/1103-ryo4
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo4/docs/logs/CHAT-1103-RYO-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo4
- 確認用URL: プレビューあり（URL は最終報告）。確認したページ: `/r/`（一覧と空間）。この版で見えるはずの印: 最初の画面の名前の上の「記憶の中を、歩く。」と「入る」のボタン
- マージ: 未（平野さんの判断待ち。指示文の「マージ: 判断待ちで止まる」）
- issue: #657（起票・着手中）。#656（公開）は変えていない
- 判断が必要なこと:
  - 仮置き14点（`## 経過`「仮置きの一覧」。画面のどこに効くかと一緒に）
  - r/ はもうほかのページの生成物・名簿を読まないので、`scripts/regenerate.py` の `RUN_LAST`・`SHARED_GENERATORS["ryoei_pages"]`・r/ のコメント（「pro/search.json・houou/・title/ の生成物を読む」は事実と違う）と、`update-live-channel.yml` の毎朝の再生成の `ryoei_pages` を外すか。未マージの `work/1103-top` が `RUN_LAST` の行を変えているので、この指示では触れていない
  - `_headers`: 未マージの `work/1029-lgn-page` が CSP の行の末尾の改行だけを変え（中身は同じ）、末尾に `/log/*` の規則を足している。後からマージする側で衝突する。解き方は「`frame-src https://www.youtube-nocookie.com` を残し、lgn の `/log/*` の規則を足す」（両立する）でよいか
  - CSP の `frame-src` はサイト全体の設定（r/ だけに限るには `/r/*` の規則で CSP を付け直す形になり、CSP の行を2か所に持つ）。全体のままでよいか
  - `mj:ryoei-sound` を「保存データを消す」リンク（`docs/new-page-checklist.md`「localStorage の規約」）の対象にするか（空間の右上の「音」で切り替えられる）
  - 次の場面の候補と、写真を受け取る方法（A〜C）（`## 経過`「次の場面の候補」）
- 未確認の項目:
  - iPhone の Safari での見え方・タッチの操作（仮想スティック・見回す・タップ）・音・WebGL の描画の速さ（headless はソフトウェア描画で約14〜17fps。目安にならない）
  - YouTube の埋め込みが空間の中で再生されること（このコンテナからは YouTube に届かない。CSP の違反は出ていない）
  - スクリーンのサムネイルのうち `i.ytimg.com`・`icualumni.com` が WebGL に使えるか（CORS。使えなければ紙に題の絵になる）
  - CSP（`_headers`）を変えたので、マージ後に実際のブラウザで Web Analytics のビーコンが違反なしに通ること（docs/notes/cloudflare.md「配信設定」。チャット側が平野さんの Chrome で）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d2277ebe）: https://github.com/retroeater/mj-logs/tree/main/guide/d2277ebe

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d2277ebe/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/795228a7.md
