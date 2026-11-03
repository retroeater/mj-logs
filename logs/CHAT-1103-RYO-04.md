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

## 報告

- 状態: 作業中
- ブランチ: work/1103-ryo4
- ログ: https://github.com/retroeater/mj/blob/work/1103-ryo4/docs/logs/CHAT-1103-RYO-04.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1103-ryo4
- 確認用URL:
- マージ: 未
- issue:
- 判断が必要なこと:
- 未確認の項目:
- エラー:

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 8eb4b671）: https://github.com/retroeater/mj-logs/tree/main/guide/8eb4b671

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/8eb4b671/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/e4049b27.md
