# CHAT-1031-PRO-02

- 着手日時: 2026-10-31
- 対象issue: #219・#641
- ブランチ: work/1030-pro
- 着手時HEAD: b3f138b

## 指示

【Claude作成】Claude Code 向け指示：pro/ の自律開発の続き（英字名の無い選手・空白と「-」の slug の規則を入れて最後まで作り、プレビューで止める）
Chat-Ref: CHAT-1031-PRO-02
マージ: 判断待ちで止まる
貼る時機: いつでも（CHAT-1030-PRO-01 のセッションの続きでも、新しいセッションでもよい）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → 作業ブランチ〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1030-pro への push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。未マージの work/1030-pro を続けて使う（CHAT-1030-PRO-01 の生成スクリプトの初版 0dde05a から続けるため）。`git merge-base --is-ancestor origin/cloudflare HEAD` が偽なら merge で取り込んでよい（rebase しない）。リモートに無ければ止まる（ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。取り込みで生成物でない文書が衝突したとき、両方の変更が両立する衝突（追記どうし・隣り合う行）は両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。あわせて CHAT-1030-PRO-01 のログ（作業ブランチの `docs/logs/CHAT-1030-PRO-01.md`）の `## 報告` の状態が「判断待ち」であることを確かめ、違えば止まる。確かめたら、その状態の行の末尾に ` / 続き: CHAT-1031-PRO-02` を足す

## 目的
CHAT-1030-PRO-01 は、英字の名が空の在籍者9名の slug を作れずに止まった（止まる条件に当たった）。その判断待ちの回答として slug の規則を確定し、PRO-01 の指示の残り（手順2・3）を最後まで行い、プレビューで止める。

### 決定（2026-10-31、平野さん）
- slug は今の決定どおり `<名>.<姓>.<入会期>`（名・姓は名簿の英字。同姓同名を避けるため3つ組にする）。ただし、名簿に英字名が無い選手（芸名1語で、英字姓だけがある9名: ガース・グリフィン・こうちゃん・ジェン・チャップマン・ポロリ・美里麻・大和・るうこ）だけは `<姓>.<入会期>`（例 `pro/garthe.22/`）にする
- 英字に空白・「-」がある選手（スカルリーパーA-Ji・李峻榮・王政芳）は、語の間を「-」でつなぐ（例 `jun-young.lee.40`）。PRO-01 の仮置き（空白と「-」を除いて詰める形）は採らない
- 生成スクリプトの初版（0dde05a）から続けてよい
- 重なりの検査（slug の重複で生成を止める）はそのまま残す。2つ組と3つ組は点の数が違うので重ならないが、検査は全件に当てる
- CHAT-1030-PRO-01 の「決定」の欄（2026-10-30）はすべてそのまま有効（pro/、幅 400px、要約だけ焼き込み、全員を一度に公開〈この指示でも未公開〉、共有ボタン、第一弾 ①③④⑤⑧⑨、属性は支部・入会期・誕生日〈月日〉）

### 前提（チャット側。平野さんの決定ではない）
- 残りの作業の範囲・面の案・検査・画面の確かめ・止まる条件は CHAT-1030-PRO-01 の指示（PRO-01 のログの `## 指示`）の「前提」と手順2・3のとおり。この指示はその続きで、規則だけを上の決定に置き換える
- slug の規則の書き方の案: 英字は小文字にし、各語の中の空白と「-」は「-」にそろえ（続く「-」は1つにする）、英字・数字・「-」以外の文字があれば止まる（名前の一覧を書く）。英字名が空なら `<姓>.<期>`、英字姓が空・入会期が空なら止まる（今の止まる条件のまま）。テスト（`scripts/tests/`）に、3つ組・2つ組・空白・「-」・大文字・重複の検査を足す
- PRO-01 の `## 経過` にある前提の食い違い（コンテナに鍵が無いので名簿・「プロ」は `regenerate-page.yml` の手動実行で読む／選手別 OGP は生成〈Actions〉が書く文の一覧からコンテナで描く2段）は、PRO-01 で Code が選んだ方法のまま進めてよい。字体の取得は docs/notes/title-pages.md の手順のとおり
- 未マージの work/ ブランチとの重なり（PRO-01 の `## 経過` の表: work/1017-lvp・work/1028-lgn・work/1029-lgn-page の style.css・regenerate.py・check-assets.yml）は使う直前に確かめ直す（その後マージされたり増えたりしていることがある）
- 仮置きにした点はすべて「判断が必要なこと」に、画面のどこに効くかと一緒に一覧で書く（平野さんが1問1答で確定する）。公開の issue は #641（PRO-01 で起票済み）

## 手順
1. 上の slug の規則を生成スクリプトと検査に入れ、テストを足す。`regenerate-page.yml` を作業ブランチで手動実行し（target_page は PRO-01 と同じ、check 真）、在籍者の数・slug の全件の表（名前・slug。後で照合に使うので略さない）・2つ組の9名・「-」の入った名前・重複の件数を `## 経過` に書く
2. PRO-01 の手順2の残り（CSS・JS・OGP・全員分の生成）を行う。生成時間・`pro/` の合計サイズ・OGP の合計サイズを報告に書く（OGP の10分・300MB の扱いは PRO-01 の前提のとおり）
3. PRO-01 の手順3（検査・360px と 390px の明暗の画面の確かめ・仮置きの一覧・公開の指示で要る変更の一覧〈#641 にもコメント〉）を行い、ブランチ名の別名 URL でプレビューできる状態にして止まる

## 止まる条件
- 0章の確かめが通らない（PRO-01 のログの状態が「判断待ち」でない）
- 前提が実物と食い違い、決定（上と PRO-01 の「決定」の欄）のどれかが守れない（どれが・なぜを書いて止まる）
- slug に英字・数字・「-」以外の文字が残る、英字姓か入会期の無い在籍者がいる、または slug が重複する（名前の一覧を書いて止まる）
- 名簿と「プロ」の在籍者の照合が通らない（`lib/meibo` の既存の条件）
- テスト・`--check`・`check_asset_limits.py` が通らない（原因を書いて止まる）
- 未マージの work/ ブランチが `scripts/lib/page.py`・`scripts/lib/meibo.py`・`scripts/lib/pro_stats.py`・`scripts/regenerate.py`・`style.css`・`workers/ask/` の同じ行・同じ関数を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）
- 他セッションの着手中コメントが #219 にある
- `workers/ask/` の変更が要るとき（変えずに「判断が必要なこと」に書いて進む）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まり、cloudflare へは入れない）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1031-PRO-02.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1031-PRO-02 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 着手前の確認（2026-10-31）

- 0章: ログの「指示」欄の末尾は指示文の最後の行と一致。PRO-01 のログの状態は「判断待ち」→ 末尾に「 / 続き: CHAT-1031-PRO-02」を足した
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠けなし
- 同じ Chat-Ref のコミット: 0件。CLAUDE.md は origin/cloudflare から変わっていない
- 作業ブランチ: ローカルの work/1030-pro は origin/work/1030-pro（b3f138b）と一致。`git merge-base --is-ancestor origin/cloudflare HEAD` は偽 → 取り込む
- 取り込み: `git merge origin/cloudflare`（data/live_channel_raw.jsonl の1件。衝突なし）
- #219: 他セッションの着手中コメントは無し → 着手中（続き）のコメントを書いた（issuecomment-6534686152）
- 未マージの work/ ブランチの重なりを確かめ直した（`git branch -r --no-merged origin/cloudflare`）: 対象のファイルを変えているのは PRO-01 の表と同じ work/1017-lvp・work/1028-lgn・work/1029-lgn-page だけ。この指示で足した箇所は、どれとも同じ行・隣り合う行に当たらない位置に置いた（style.css は新サイトの基本の形の節の前・末尾には足さない、regenerate.py は辞書の別の位置と `RUN_LAST`・`run_order()`、_redirects は /wrcr の行の後ろ、check-assets.yml は `case` の先頭の行、navbar.js は /wrcr/ の分岐の後ろ）。work/1029-lgn-page の check-assets.yml・_redirects・navbar.js の追記とは別の位置なので、後からマージする側でも衝突しない見込み（取り込みを試してはいない）

### slug の規則と実データでの検査（手順1）

- `make_slug()`・`slug_problems()` を決定の規則に置き換えた（97a843a）。テスト `scripts/tests/test_pro_pages.py` の `SlugTest`（3つ組・2つ組・空白・「-」・大文字・英字姓や入会期の欠け・英字以外の文字・重複）。初版（0dde05a）の `make_slug()` は (slug, bool) を返し2つ組を作らないので、このテストは初版では通らない
- `regenerate-page.yml` を作業ブランチで手動実行（target_page `pro_pages`・check 真、run 41137352240）。ログの末尾（引用）:

```
2つ組(英字名なし): ['ガース', 'グリフィン', 'こうちゃん', 'ジェン', 'チャップマン', 'ポロリ', '美里麻', '大和', 'るうこ']
「-」入り: ['スカルリーパーA-Ji', '李峻榮', '王政芳']
在籍者 1098名
検査: 通りました(ファイルは書いていません)
- pro_pages: 通る(--check)
```

- 在籍者 1,098名・slug 1,098件（重複 0件）。2つ組 9名: ガース `garthe.22`, グリフィン `griffin.40`, こうちゃん `kochan.40`, ジェン `jenn.22`, チャップマン `chapman.42`, ポロリ `porori.37`, 美里麻 `milima.37`, 大和 `yamato.26`, るうこ `ruko.41`。「-」入り 3名: スカルリーパーA-Ji `a-ji.skull-reaper.27`, 李峻榮 `jun-young.lee.40`, 王政芳 `zheng-fang.wan.22`
- slug の全件（生成した `pro/search.json` の並び＝「プロ」のソートキーの順。Actions の check のログの一覧と同じ規則で作った値）:

| # | 名前 | slug |
|---|---|---|
| 1 | 藍ありさ | arisa.ai.42 |
| 2 | 合澤雄貴 | yuki.aizawa.38 |
| 3 | 會田亮介 | ryosuke.aida.35 |
| 4 | 相場雄大 | yudai.aiba.31 |
| 5 | 蒼井ゆりか | yurika.aoi.24 |
| 6 | 青木いちな | ichina.aoki.41 |
| 7 | 蒼木翔子 | shoko.aoki.34 |
| 8 | 青木惇 | jun.aoki.42 |
| 9 | 青嶋宏樹 | hiroki.aoshima.33 |
| 10 | 蒼山秀佑 | shusuke.aoyama.28 |
| 11 | 青山大 | dai.aoyama.17 |
| 12 | 青山めぐみ | megumi.aoyama.28 |
| 13 | 赤木クロ | kuro.akagi.38 |
| 14 | 明石定家 | teika.akashi.13 |
| 15 | 赤司美奈子 | minako.akashi.23 |
| 16 | 明石雄亮 | yusuke.akashi.36 |
| 17 | 赤星俊樹 | toshiki.akahoshi.42 |
| 18 | 秋場みなみ | minami.akiba.39 |
| 19 | 秋谷恒輔 | kosuke.akiya.40 |
| 20 | 秋山華乃 | kano.akiyama.42 |
| 21 | 秋山淑子 | toshiko.akiyama.13 |
| 22 | 阿久津翔太 | shota.akutsu.34 |
| 23 | 浅岡勇矢 | yuya.asaoka.41 |
| 24 | 安里静竜 | seiryu.asato.42 |
| 25 | 朝野叶 | kanau.asano.39 |
| 26 | 浅野耕太 | kota.asano.37 |
| 27 | 浅野文雅 | fumimasa.asano.28 |
| 28 | 朝比奈優斗 | yuto.asahina.42 |
| 29 | 朝比奈ゆり | yuri.asahina.40 |
| 30 | 網代将悟 | shogo.ajiro.42 |
| 31 | 東克明 | katsuaki.azuma.40 |
| 32 | 東幸一郎 | koichiro.azuma.20 |
| 33 | 麻生知花 | chika.aso.39 |
| 34 | 新大地 | daichi.atarashi.33 |
| 35 | 足立純哉 | junya.adachi.32 |
| 36 | 厚谷昇汰 | shota.atsuya.29 |
| 37 | 吾妻さおり | saori.azuma.21 |
| 38 | 安部久美子 | kumiko.abe.34 |
| 39 | 阿部謙一 | kenichi.abe.28 |
| 40 | 阿部純平 | jumpei.abe.40 |
| 41 | 阿部竜也 | tatsuya.abe.38 |
| 42 | 安部颯斗 | hayato.abe.37 |
| 43 | 安部みゆ | miyu.abe.42 |
| 44 | 阿部龍之介 | ryunosuke.abe.41 |
| 45 | 天沢沙恵花 | saeka.amasawa.41 |
| 46 | 天野美星 | mihoshi.amano.42 |
| 47 | 天野ヨシアキ | yoshiaki.amano.38 |
| 48 | 雨宮礼奈 | rena.amamiya.42 |
| 49 | 鮎川卓 | taku.ayukawa.19 |
| 50 | 新井駿一 | shunichi.arai.30 |
| 51 | 荒井伶太 | ryota.arai.36 |
| 52 | 荒木宏祐 | kosuke.araki.38 |
| 53 | 荒谷誠 | makoto.aratani.22 |
| 54 | 荒正義 | masayoshi.ara.1 |
| 55 | 有田将之 | masayuki.arita.35 |
| 56 | 安城るい | rui.anjo.29 |
| 57 | 安藤銀一 | ginichi.ando.37 |
| 58 | 安藤翔 | sho.ando.35 |
| 59 | 安藤大貴 | daiki.ando.28 |
| 60 | 安藤友一 | tomokazu.ando.23 |
| 61 | 安藤秀行 | hideyuki.ando.42 |
| 62 | 安東裕允 | hironobu.ando.18 |
| 63 | 安東郁敬 | fumitaka.ando.41 |
| 64 | 安藤りな | rina.ando.35 |
| 65 | 安秉参 | byonsan.an.23 |
| 66 | 伊井功雅 | kuga.ii.28 |
| 67 | 飯田爽太郎 | sotaro.iida.41 |
| 68 | 飯盛裕美子 | yumiko.iimori.38 |
| 69 | 家田みゆき | miyuki.ieda.20 |
| 70 | 伊尾健太朗 | kentaro.io.40 |
| 71 | 伊賀則夫 | norio.iga.20 |
| 72 | 池沢麻奈美 | manami.ikezawa.29 |
| 73 | 池田来斗 | raito.ikeda.33 |
| 74 | 池田隆 | ryu.ikeda.42 |
| 75 | 池辺吾春 | masaharu.ikebe.38 |
| 76 | 池本友馬 | yuma.ikemoto.39 |
| 77 | 井坂彰宏 | akihiro.isaka.36 |
| 78 | 石井良樹 | yoshiki.ishii.24 |
| 79 | 石川豪士 | goshi.ishikawa.35 |
| 80 | 石川愛 | naru.ishikawa.39 |
| 81 | 石川正明 | masaaki.ishikawa.17 |
| 82 | 石川雄基 | yuki.ishikawa.39 |
| 83 | 石川雄也 | yuya.ishikawa.36 |
| 84 | 石川遼 | ryo.ishikawa.34 |
| 85 | 石亀玄樹 | genki.ishigame.38 |
| 86 | 石黒恵三 | keizo.ishiguro.40 |
| 87 | 石田亜沙己 | asami.ishida.29 |
| 88 | 石立岳大 | takehiro.ishidate.23 |
| 89 | 石田知幸 | tomoyuki.ishida.36 |
| 90 | 石津寿人 | hisato.ishizu.20 |
| 91 | 石原和輝 | kazuki.ishihara.41 |
| 92 | 石原康申 | yasunobu.ishihara.25 |
| 93 | 石橋和也 | kazuya.ishibashi.21 |
| 94 | 石森隆雄 | takao.ishimori.32 |
| 95 | 石山真也 | shinya.ishiyama.42 |
| 96 | 石渡正志 | masashi.ishiwata.7 |
| 97 | 泉裕斗 | yuto.izumi.37 |
| 98 | 和泉由希子 | yukiko.izumi.19 |
| 99 | 泉亮多 | ryota.izumi.26 |
| 100 | 礒川雄介 | yusuke.isokawa.40 |
| 101 | 板谷祐斗 | yuto.itaya.34 |
| 102 | 市川敦士 | atsushi.ichikawa.26 |
| 103 | 市川博之 | hiroyuki.ichikawa.41 |
| 104 | 一ノ瀬葵 | aoi.ichinose.41 |
| 105 | 一瀬由梨 | yuri.ichinose.33 |
| 106 | 一宮さくら | sakura.ichimiya.40 |
| 107 | 一森かな | kana.ichimori.39 |
| 108 | 樹優希 | yuki.itsuki.41 |
| 109 | 井出一寛 | kazunori.ide.12 |
| 110 | 井出康平 | kohei.ide.21 |
| 111 | 伊藤享祐 | kyosuke.ito.40 |
| 112 | 居藤洸 | ko.ito.29 |
| 113 | 伊藤康介 | kosuke.ito.41 |
| 114 | 伊藤俊介 | shunsuke.ito.37 |
| 115 | 伊藤泰斗 | taito.ito.38 |
| 116 | 伊藤大輔 | daisuke.ito.24 |
| 117 | 伊藤鉄也 | tetsuya.ito.22 |
| 118 | 伊藤直輝 | naoki.ito.34 |
| 119 | 伊東宏倫 | hironori.ito.20 |
| 120 | 伊藤佑樹 | yuki.ito.36 |
| 121 | 伊藤優孝 | yuko.ito.1 |
| 122 | 伊藤佑太 | yuta.ito.41 |
| 123 | 稲岡ミカ | mika.inaoka.29 |
| 124 | 稲垣諒彦 | akihiko.inagaki.32 |
| 125 | 犬飼あやの | ayano.inukai.34 |
| 126 | 犬飼直紀 | naoki.inukai.36 |
| 127 | 犬見武史 | takeshi.inumi.26 |
| 128 | 井上絵美子 | emiko.inoue.28 |
| 129 | 井上真孝 | naoyuki.inoue.31 |
| 130 | 井上泰秀 | yasuhide.inoue.35 |
| 131 | 伊原達矢 | tatsuya.ihara.29 |
| 132 | 猪鼻拓哉 | takuya.ibana.36 |
| 133 | 伊福正起 | masaki.ifuku.40 |
| 134 | 井深翔基 | shoki.ibuka.42 |
| 135 | 今泉誠一 | seiichi.imaizumi.29 |
| 136 | 今岡英忠 | hidetada.imaoka.27 |
| 137 | 今里邦彦 | kunihiko.imazato.42 |
| 138 | 今村壮吾 | sogo.imamura.40 |
| 139 | 岩崎健太 | kenta.iwasaki.39 |
| 140 | 岩崎翔太 | shota.iwasaki.37 |
| 141 | 岩﨑仁 | jin.iwasaki.39 |
| 142 | 岩村美智子 | michiko.iwamura.34 |
| 143 | 岩村義久 | yoshihisa.iwamura.39 |
| 144 | 上田宗太朗 | sotaro.ueda.38 |
| 145 | 植田稔宏 | toshihiro.ueda.34 |
| 146 | 上田直樹 | naoki.ueda.32 |
| 147 | 上田まみ | mami.ueda.38 |
| 148 | 上田稜 | ryo.ueda.32 |
| 149 | 上村政雄 | masao.uemura.28 |
| 150 | 魚谷侑未 | yuumi.uotani.25 |
| 151 | 鵜飼倖輔 | kosuke.ukai.42 |
| 152 | 氏家義成 | yoshinari.ujiie.32 |
| 153 | 宇高ぜん吉 | zenkichi.udaka.42 |
| 154 | 内川幸太郎 | kotaro.uchikawa.22 |
| 155 | 内田海斗 | kaito.uchida.40 |
| 156 | 内田みこ | miko.uchida.33 |
| 157 | 内村翠 | midori.uchimura.39 |
| 158 | 内山えみ | emi.uchiyama.27 |
| 159 | 内山藤次朗 | tojiro.uchiyama.42 |
| 160 | 台直輝 | naoki.utena.36 |
| 161 | 宇都ちょめのすけ | chomenosuke.uto.42 |
| 162 | 鵜野健一 | kenichi.uno.40 |
| 163 | 梅中悠介 | yusuke.umenaka.37 |
| 164 | 梅本翔 | sho.umemoto.35 |
| 165 | 浦田豊人 | toyohito.urata.8 |
| 166 | 浦野修平 | shuhei.urano.35 |
| 167 | 栄田勇作 | yusaku.eida.39 |
| 168 | 江隈亨 | toru.ekuma.10 |
| 169 | 江崎しんのすけ | shinnosuke.ezaki.36 |
| 170 | 悦喜真之介 | shinnosuke.etsuki.39 |
| 171 | 蛯原朗 | akira.ebihara.25 |
| 172 | 襟川麻衣子 | maiko.erikawa.32 |
| 173 | 遠藤昭太 | shota.endo.22 |
| 174 | 及川慶次 | keiji.oikawa.31 |
| 175 | 老月貴紀 | takanori.oitsuki.14 |
| 176 | 大石隆也 | takanari.oishi.38 |
| 177 | 大枝史 | fumi.oeda.35 |
| 178 | 大鹿糠文也 | fumiya.okanuka.31 |
| 179 | 大川佳弥乃 | kayano.okawa.40 |
| 180 | 大川裕次郎 | yujiro.okawa.36 |
| 181 | 大木亮典 | akinori.oki.26 |
| 182 | 大久保朋美 | tomomi.okubo.29 |
| 183 | 大久保隼人 | hayato.okubo.40 |
| 184 | 大倉綾菜 | ayana.okura.40 |
| 185 | 大崎菜緒 | nao.osaki.42 |
| 186 | 大下恭佑 | kyosuke.oshita.40 |
| 187 | 大下新司 | shinji.oshita.40 |
| 188 | 大島琉雅 | ryuga.oshima.42 |
| 189 | 大空太郎 | taro.ozora.42 |
| 190 | 大高啓 | kei.otaka.36 |
| 191 | 大滝聡 | satoshi.otaki.24 |
| 192 | 太田峻也 | shunya.ota.27 |
| 193 | 大谷剛志 | takeshi.otani.41 |
| 194 | 太田寛子 | hiroko.ota.36 |
| 195 | 太田昌樹 | masaki.ota.21 |
| 196 | 太田充 | mitsuru.ota.25 |
| 197 | 太田優介 | yusuke.ota.22 |
| 198 | 太田涼聖 | ryosei.ota.40 |
| 199 | 大塚將訓 | masakuni.otsuka.38 |
| 200 | 大槻あいみ | aimi.otsuki.37 |
| 201 | 大月れみ | remi.otsuki.33 |
| 202 | 大伴翔太 | shota.otomo.40 |
| 203 | 大西義則 | yoshinori.onishi.22 |
| 204 | 大沼慎 | makoto.onuma.35 |
| 205 | 大野彩乃 | ayano.ono.28 |
| 206 | 大野剛史 | takeshi.ono.38 |
| 207 | 大野徳多郎 | tokutaro.ono.32 |
| 208 | 大野雄輝 | yuki.ono.37 |
| 209 | 大橋慶一郎 | keiichiro.ohashi.28 |
| 210 | 大橋幸正 | kosei.ohashi.33 |
| 211 | 大原伴哉 | tomoya.ohara.39 |
| 212 | 大庭三四郎 | sanshiro.oba.27 |
| 213 | 大町勇太 | yuta.omachi.38 |
| 214 | 大宮蓮 | ren.omiya.41 |
| 215 | 大本祐介 | yusuke.omoto.40 |
| 216 | 岡崎圭吾 | keigo.okazaki.36 |
| 217 | 岡崎涼太 | ryota.okazaki.34 |
| 218 | 岡田啓佑 | keisuke.okada.38 |
| 219 | 岡田紗佳 | sayaka.okada.33 |
| 220 | 岡田茂 | shigeru.okada.22 |
| 221 | 岡田拓也 | takuya.okada.37 |
| 222 | 岡田智和 | tomokazu.okada.32 |
| 223 | 岡田美紅 | miku.okada.42 |
| 224 | 岡田裕太 | yuta.okada.40 |
| 225 | 岡本和也 | kazuya.okamoto.27 |
| 226 | 岡本佳祐 | keisuke.okamoto.39 |
| 227 | 岡本真一 | shinichi.okamoto.24 |
| 228 | 岡本丈司 | takeshi.okamoto.23 |
| 229 | 岡本悠輝 | yuki.okamoto.36 |
| 230 | 岡竜介 | ryusuke.oka.24 |
| 231 | 岡リョウタ | ryota.oka.42 |
| 232 | 小笠原奈央 | nao.ogasahara.28 |
| 233 | 小笠原樹 | itsuki.ogasawara.42 |
| 234 | 小川慈瑛 | jiei.ogawa.42 |
| 235 | 小川尚哉 | naoya.ogawa.22 |
| 236 | 小川裕也 | yuya.ogawa.37 |
| 237 | 沖野健行 | takeyuki.okino.35 |
| 238 | 沖ヒカル | hikaru.oki.34 |
| 239 | 奥潤次 | junji.oku.34 |
| 240 | 奥田裕貴 | yuki.okuda.35 |
| 241 | 奥津勇輝 | yuki.okutsu.33 |
| 242 | 奥野真語 | shingo.okuno.36 |
| 243 | 奥村恵士 | satoshi.okumura.40 |
| 244 | 小熊良衡 | yoshihira.oguma.36 |
| 245 | 小倉慶一郎 | keiichiro.ogura.42 |
| 246 | 小栗隆成 | ryusei.oguri.37 |
| 247 | 小車祥 | sho.oguruma.25 |
| 248 | 桶本篤史 | atsushi.okemoto.25 |
| 249 | 長内真実 | mami.osanai.24 |
| 250 | 長村大 | dai.osamura.37 |
| 251 | 押手まり | mari.oshite.40 |
| 252 | 小高佑貴 | yuki.odaka.37 |
| 253 | 小田悟志 | satoshi.oda.21 |
| 254 | 小田尚生 | shoki.oda.40 |
| 255 | 音羽なお | nao.otoha.33 |
| 256 | 小野塚永遠 | towa.onozuka.36 |
| 257 | 小野将輝 | masaki.ono.42 |
| 258 | 小野雅峻 | masatoshi.ono.29 |
| 259 | 小原航太 | kota.ohara.33 |
| 260 | 開澤拓弥 | takuya.kaizawa.41 |
| 261 | 貝原香 | kaoru.kaibara.35 |
| 262 | 加賀美幸孝 | yukitaka.kagami.29 |
| 263 | 加賀谷春歌 | haruka.kagaya.37 |
| 264 | 柿添誠 | makoto.kakizoe.33 |
| 265 | 柿元裕介 | yusuke.kakimoto.34 |
| 266 | 柿本幸宏 | yukihiro.kakimoto.40 |
| 267 | 加来千香子 | chikako.kaku.34 |
| 268 | 覚野陽生 | hibiki.kakuno.40 |
| 269 | 掛樋忠雄 | tadao.kakehi.23 |
| 270 | 掛水洋徳 | hironori.kakemizu.21 |
| 271 | 加護優愛 | yua.kago.38 |
| 272 | 笠原拓樹 | hiroki.kasahara.36 |
| 273 | 風見あやな | ayana.kazami.42 |
| 274 | かしのなぎ | nagi.kashino.40 |
| 275 | 樫林愛子 | aiko.kashibayashi.35 |
| 276 | 粕谷侑生 | yuki.kasuya.41 |
| 277 | 粕谷勇吉 | yukichi.kasuya.20 |
| 278 | 片倉まち | machi.katakura.31 |
| 279 | 加田涼 | ryo.kada.42 |
| 280 | 勝又健志 | kenji.katsumata.17 |
| 281 | 香月佑海 | yumi.kazuki.37 |
| 282 | 加藤晋平 | shimpei.kato.27 |
| 283 | 加藤貴一 | takahito.kato.36 |
| 284 | 加藤拓海 | takumi.kato.38 |
| 285 | 加藤哲郎 | tetsuro.kato.39 |
| 286 | 加藤泰史 | yasushi.kato.27 |
| 287 | 加藤勇飛 | yuhi.kato.38 |
| 288 | 加藤ゆみ | yumi.kato.37 |
| 289 | 加藤凌賀 | ryoga.kato.42 |
| 290 | 角谷陽介 | yosuke.kadoya.33 |
| 291 | 叶璃々 | riri.kanai.40 |
| 292 | 金澤翔 | sho.kanazawa.42 |
| 293 | 金澤隼人 | hayato.kanazawa.36 |
| 294 | 金澤陸 | riku.kanazawa.39 |
| 295 | 金杉空 | sora.kanasugi.37 |
| 296 | 金子貴行 | takayuki.kaneko.10 |
| 297 | 金子正明 | masaaki.kaneko.28 |
| 298 | 金田智紀 | tomoki.kaneda.38 |
| 299 | 金平裕樹 | yuki.kanehira.22 |
| 300 | 鏑木秀成 | hidenari.kaburagi.36 |
| 301 | 鎌谷貴也 | takaya.kamatani.41 |
| 302 | 釜山甲太郎 | kotaro.kamayama.38 |
| 303 | 神代陽向 | hinata.kamishiro.42 |
| 304 | 上村慎太郎 | shintaro.kamimura.22 |
| 305 | 上村宜久 | yoshihisa.kamimura.18 |
| 306 | 神森天心 | tenshin.kamimori.38 |
| 307 | 亀井来寿 | raizu.kamei.40 |
| 308 | 鴨舞 | mai.kamo.38 |
| 309 | 川合克久 | katsuhisa.kawai.40 |
| 310 | 河合慎悟 | shingo.kawai.25 |
| 311 | かわいめぐみ | megumi.kawai.34 |
| 312 | 川奥修二 | shuji.kawaoku.42 |
| 313 | 川上剛洋 | takahiro.kawakami.41 |
| 314 | 川上直也 | naoya.kawakami.29 |
| 315 | 川上レイ | rei.kawakami.37 |
| 316 | 川崎友也 | tomoya.kawasaki.40 |
| 317 | 川﨑裕紀 | yuki.kawasaki.41 |
| 318 | 河野みのり | minori.kawano.28 |
| 319 | 川原舞子 | maiko.kawahara.28 |
| 320 | 川端祥平 | shohei.kawabata.39 |
| 321 | 河村知宏 | tomohiro.kawamura.42 |
| 322 | 川村直寛 | naohiro.kawamura.36 |
| 323 | 川村芳範 | yoshinori.kawamura.42 |
| 324 | 貫上洋志 | hiroshi.kanjo.23 |
| 325 | 漢那邦洋 | kunihiro.kanna.42 |
| 326 | 菅野爽 | akira.kanno.39 |
| 327 | 菅野直 | sunao.kanno.22 |
| 328 | 神林剛 | tsuyoshi.kambayashi.23 |
| 329 | ガース | garthe.22 |
| 330 | 菊田政俊 | masatoshi.kikuta.32 |
| 331 | 菊池豪 | go.kikuchi.28 |
| 332 | 菊地美羽 | miu.kikuchi.37 |
| 333 | 菊原真人 | masato.kikuhara.32 |
| 334 | 如月明日香 | asuka.kisaragi.34 |
| 335 | 如月靖之 | yasuyuki.kisaragi.38 |
| 336 | 木島甲太郎 | kotaro.kijima.33 |
| 337 | 北川かりん | karin.kitagawa.41 |
| 338 | 北川星哉 | seiya.kitagawa.40 |
| 339 | 北川裕太 | yuta.kitagawa.40 |
| 340 | 喜多清貴 | kiyotaka.kita.28 |
| 341 | 北角浩一 | koichi.kitazumi.43 |
| 342 | 北野剛志 | takeshi.kitano.42 |
| 343 | 北野由実 | yumi.kitano.22 |
| 344 | 北村弦騎 | genki.kitamura.37 |
| 345 | 北村祐二 | yuji.kitamura.34 |
| 346 | 木戸僚之 | tomoyuki.kido.23 |
| 347 | 喜戸信彦 | nobuhiko.kido.38 |
| 348 | 木下恭子 | kyoko.kinoshita.25 |
| 349 | 木下新ヱ門 | shinemon.kinoshita.39 |
| 350 | 木下智裕 | tomohiro.kinoshita.42 |
| 351 | 木下遥 | haruka.kinoshita.38 |
| 352 | 木原翼 | tsubasa.kihara.34 |
| 353 | 木村圭吾 | keigo.kimura.42 |
| 354 | 木村幸平 | kohei.kimura.39 |
| 355 | 木村さき | saki.kimura.40 |
| 356 | 木室哲朗 | tetsuro.kimuro.35 |
| 357 | 木本大介 | daisuke.kimoto.33 |
| 358 | 客野直 | naoki.kyakuno.23 |
| 359 | 清末紫咲 | shisaki.kiyosue.40 |
| 360 | 清原継光 | tsugumitsu.kiyohara.25 |
| 361 | 日下健司 | kenji.kusaka.24 |
| 362 | くさのいおり | iori.kusano.21 |
| 363 | 櫛田利太 | toshita.kushida.27 |
| 364 | 櫛橋孝平 | kohei.kushihashi.38 |
| 365 | 楠木一朗 | ichiro.kusunoki.21 |
| 366 | 楠原遊 | yu.kusuhara.30 |
| 367 | 工藤清一 | shinichi.kudo.38 |
| 368 | 久野智子 | tomoko.kuno.42 |
| 369 | 久保隆徳 | takanori.kubo.38 |
| 370 | 久保有平 | yuhei.kubo.39 |
| 371 | 倉田梨央 | rio.kurata.40 |
| 372 | 栗島有紀 | yuki.kurishima.36 |
| 373 | 来海怜 | rei.kurumi.40 |
| 374 | 黒石翔 | sho.kuroishi.38 |
| 375 | 黒川裕輝 | yuki.kurokawa.42 |
| 376 | 黒木真生 | masao.kuroki.13 |
| 377 | 黒崎荘太 | sota.kurosaki.40 |
| 378 | 黒崎れむ | remu.kurosaki.42 |
| 379 | 黒沢咲 | saki.kurosawa.21 |
| 380 | 黒田瑛太郎 | eitaro.kuroda.42 |
| 381 | 黒田英輝 | hideki.kuroda.37 |
| 382 | 黒田良 | ryo.kuroda.35 |
| 383 | 黒羽翔 | kakeru.kuroba.36 |
| 384 | 桑田憲汰 | kenta.kuwada.35 |
| 385 | 桑原宏貴 | hiroki.kuwahara.36 |
| 386 | グリフィン | griffin.40 |
| 387 | ケネス徳田 | kenneth.tokuda.29 |
| 388 | こうちゃん | kochan.40 |
| 389 | 河野智士 | satoshi.kono.36 |
| 390 | 河野まや | maya.kono.30 |
| 391 | 香野蘭 | ran.kono.37 |
| 392 | 神山俊 | shun.koyama.37 |
| 393 | 木暮智貴 | tomoki.kogure.31 |
| 394 | 越川清一 | seiichi.koshikawa.24 |
| 395 | 越野沙耶佳 | sayaka.koshino.42 |
| 396 | 越野智紀 | tomonori.koshino.23 |
| 397 | 古小路亜美 | ami.koshoji.39 |
| 398 | 小島広宣 | hironobu.kojima.25 |
| 399 | 小杉昂裕 | takahiro.kosugi.40 |
| 400 | 小菅隼弥 | shunya.kosuge.40 |
| 401 | 小平螢 | hotaru.kodaira.35 |
| 402 | 児玉佳宏 | yoshihiro.kodama.37 |
| 403 | 児玉隆哉 | ryuya.kodama.37 |
| 404 | 小玉渉 | wataru.kodama.33 |
| 405 | 小兎めい | mei.koto.41 |
| 406 | 小林和樹 | kazuki.kobayashi.36 |
| 407 | 小林克己 | katsumi.kobayashi.39 |
| 408 | 小林大地 | daichi.kobayashi.34 |
| 409 | 小林正和 | masakazu.kobayashi.31 |
| 410 | こばりしん | shin.kobari.28 |
| 411 | 駒田真子 | mako.komada.35 |
| 412 | 小松崎舞 | mai.komatsuzaki.42 |
| 413 | 小松心音 | shion.komatsu.42 |
| 414 | 小松翔一 | shoichi.komatsu.38 |
| 415 | 小松武蔵 | takezo.komatsu.22 |
| 416 | 小宮山一美 | kazumi.komiyama.9 |
| 417 | 小屋史 | tsukasa.koya.42 |
| 418 | 近田陸 | riku.konda.40 |
| 419 | 近藤久春 | hisaharu.kondo.17 |
| 420 | 近藤美香 | mika.kondo.34 |
| 421 | 近藤光将 | mitsumasa.kondo.38 |
| 422 | 近藤廉 | ren.kondo.38 |
| 423 | 紺野真太郎 | shintaro.konno.16 |
| 424 | 紺野光則 | mitsunori.konno.33 |
| 425 | 後藤あゆみ | ayumi.goto.37 |
| 426 | 後藤咲 | saki.goto.34 |
| 427 | 後藤俊孝 | toshitaka.goto.26 |
| 428 | 後藤正博 | masahiro.goto.22 |
| 429 | 後藤真帆 | maho.goto.41 |
| 430 | 後藤竜太 | ryuta.goto.41 |
| 431 | 西城凛 | rin.saijo.33 |
| 432 | 齊藤匡蔵 | kyozo.saito.41 |
| 433 | 斎藤桂史 | keiji.saito.18 |
| 434 | 齋藤豪 | go.saito.28 |
| 435 | 齋藤太一 | taichi.saito.41 |
| 436 | 斉藤隆 | takashi.saito.34 |
| 437 | 齋藤光 | hikaru.saito.37 |
| 438 | 斎藤寛生 | hiroki.saito.26 |
| 439 | 齋藤麻衣子 | maiko.saito.26 |
| 440 | 斉藤理絵 | rie.saito.25 |
| 441 | 佐伯壱晟 | issei.saeki.41 |
| 442 | 坂井孝太郎 | kotaro.sakai.41 |
| 443 | 桜井瑛一 | eiichi.sakurai.42 |
| 444 | 桜井カズキ | kazuki.sakurai.39 |
| 445 | 桜井紘己 | koki.sakurai.18 |
| 446 | 櫻井勇馬 | yuma.sakurai.35 |
| 447 | 桜川姫子 | himeko.sakurakawa.31 |
| 448 | 桜木里咲 | risa.sakuragi.34 |
| 449 | 桜坂優花 | yuka.sakurazaka.42 |
| 450 | さくら美緒 | mio.sakura.34 |
| 451 | 佐々木大輝 | daiki.sasaki.40 |
| 452 | 佐々木寿人 | hisato.sasaki.22 |
| 453 | 佐々木啓文 | hirofumi.sasaki.29 |
| 454 | 佐々木亮 | makoto.sasaki.15 |
| 455 | 佐々木将幸 | masayuki.sasaki.38 |
| 456 | 佐々木諒輔 | ryosuke.sasaki.39 |
| 457 | 定行七海 | nanami.sadayuki.42 |
| 458 | 佐藤あいり | airi.sato.25 |
| 459 | 佐藤晃大 | akihiro.sato.29 |
| 460 | 佐藤健治 | kenji.sato.22 |
| 461 | 佐藤滉一朗 | koichiro.sato.40 |
| 462 | 佐藤純希 | junki.sato.40 |
| 463 | 佐藤孝行 | takayuki.sato.35 |
| 464 | 佐藤拓馬 | takuma.sato.40 |
| 465 | 佐藤浩樹 | hiroki.sato.39 |
| 466 | 佐藤学 | manabu.sato.39 |
| 467 | 佐藤凌 | ryo.sato.37 |
| 468 | 里木祐介 | yusuke.satoki.33 |
| 469 | 真田将成 | nobunari.sanada.37 |
| 470 | 真田悠暉 | yuki.sanada.36 |
| 471 | 猿川真寿 | masatoshi.sarukawa.17 |
| 472 | 猿渡輝也 | teruya.saruwatari.38 |
| 473 | 沢崎誠 | makoto.sawazaki.3 |
| 474 | 澤田唯 | yui.sawada.33 |
| 475 | 沢村侑樹 | yuki.sawamura.38 |
| 476 | 澤谷諒 | ryo.sawaya.35 |
| 477 | 三盃貴之 | takayuki.sambai.19 |
| 478 | 三盃志 | hitoshi.sambai.19 |
| 479 | 椎名健太 | kenta.shiina.38 |
| 480 | 志方歩 | ayumu.shikata.39 |
| 481 | 重原聡 | satoshi.shigehara.26 |
| 482 | 獅坂祐一 | yuichi.shizaka.32 |
| 483 | 志多木健 | takeshi.shidagi.32 |
| 484 | 篠崎高知 | kochi.shinozaki.40 |
| 485 | 篠原冴美 | saemi.shinohara.40 |
| 486 | 四ノ宮彩夏 | sayaka.shinomiya.41 |
| 487 | 柴田航平 | kohei.shibata.34 |
| 488 | 柴田太一 | taichi.shibata.35 |
| 489 | 柴田祐一朗 | yuichiro.shibata.28 |
| 490 | 柴田吉和 | yoshikazu.shibata.28 |
| 491 | 渋澤悟 | satoru.shibusawa.41 |
| 492 | 島口椋多 | ryota.shimaguchi.40 |
| 493 | 島崎涼 | ryo.shimazaki.31 |
| 494 | 嶋田卓也 | takuya.shimada.33 |
| 495 | 嶋津雄介 | yusuke.shimazu.40 |
| 496 | 島秀彰 | hideaki.shima.33 |
| 497 | 清水香織 | kaori.shimizu.13 |
| 498 | 清水哲也 | tetsuya.shimizu.28 |
| 499 | 清水智之 | tomoyuki.shimizu.36 |
| 500 | 清水陽菜 | haruna.shimizu.43 |
| 501 | 清水隆市 | ryuichi.shimizu.35 |
| 502 | 下田悠斗 | yuto.shimoda.41 |
| 503 | 下山哲也 | tetsuya.shimoyama.28 |
| 504 | 東海林正博 | masahiro.shoji.37 |
| 505 | 庄司結真 | yuma.shoji.40 |
| 506 | 庄田祐生 | yusei.shoda.32 |
| 507 | 白石ひより | hiyori.shiraishi.42 |
| 508 | 白鳥翔 | sho.shiratori.23 |
| 509 | 白銀紗希 | saki.shirogane.28 |
| 510 | 城下恭平 | kyohei.shiroshita.42 |
| 511 | 新宮英樹 | hideki.shingu.35 |
| 512 | 真光祐尚 | yusho.shinko.24 |
| 513 | 新城勇哉 | yuya.shinjo.42 |
| 514 | 新谷翔平 | shohei.shintani.24 |
| 515 | ジェン | jenn.22 |
| 516 | 実盛今日一 | kyoichi.jitsumori.41 |
| 517 | 須浦正裕 | masahiro.suura.22 |
| 518 | 末続ヒロトシ | hirotoshi.suetsugu.22 |
| 519 | 末永尚暉 | naoki.suenaga.38 |
| 520 | 末松隆文 | takafumi.suematsu.30 |
| 521 | スカルリーパーA-Ji | a-ji.skull-reaper.27 |
| 522 | 須賀智博 | tomohiro.suga.23 |
| 523 | 菅原啓資 | keisuke.sugawara.39 |
| 524 | 菅原直哉 | naoya.sugawara.28 |
| 525 | 菅原千瑛 | hiroe.sugawara.28 |
| 526 | 杉内甫 | hajime.sugiuchi.40 |
| 527 | 杉浦勘介 | kansuke.sugiura.20 |
| 528 | 杉浦貴紀 | takanori.sugiura.21 |
| 529 | 杉浦まゆ | mayu.sugiura.35 |
| 530 | 杉浦幸 | miyuki.sugiura.26 |
| 531 | 杉原直人 | naoto.sugihara.38 |
| 532 | 杉村泰治 | yasuharu.sugimura.12 |
| 533 | 杉山俊彦 | toshihiko.sugiyama.33 |
| 534 | 鈴木淳 | atsushi.suzuki.21 |
| 535 | 鈴木彩夏 | ayaka.suzuki.28 |
| 536 | 鈴木勝也 | katsuya.suzuki.36 |
| 537 | 鈴木里美 | satomi.suzuki.38 |
| 538 | 鈴木駿平 | shumpei.suzuki.42 |
| 539 | 鈴木孝幸 | takayuki.suzuki.41 |
| 540 | 鈴木大介 | daisuke.suzuki.39 |
| 541 | 鈴木秀幸 | hideyuki.suzuki.23 |
| 542 | 鈴木宏明 | hiroaki.suzuki.42 |
| 543 | 鈴木郁孝 | fumitaka.suzuki.19 |
| 544 | 鈴木誠 | makoto.suzuki.33 |
| 545 | 鈴木真都 | manato.suzuki.40 |
| 546 | 鈴木雄介 | yusuke.suzuki.23 |
| 547 | 鈴木裕也 | yuya.suzuki.37 |
| 548 | 砂子直信 | tadanobu.sunago.38 |
| 549 | 春原雄太 | yuta.sunohara.39 |
| 550 | 鷲見隼人 | hayato.sumi.28 |
| 551 | 角屋保人 | yasuhito.sumiya.20 |
| 552 | 関島義基 | yoshiki.sekijima.12 |
| 553 | 関根大地 | daichi.sekine.37 |
| 554 | 関本幸樹 | koki.sekimoto.38 |
| 555 | 関谷正信 | masanobu.sekiya.42 |
| 556 | 脊黒沙南 | sana.seguro.41 |
| 557 | 瀬下勝也 | katsuya.sejimo.30 |
| 558 | 瀬戸熊直樹 | naoki.setokuma.14 |
| 559 | 瀬戸麻衣 | mai.seto.40 |
| 560 | 仙田竜祐 | ryosuke.senda.38 |
| 561 | 早田さきこ | sakiko.soda.32 |
| 562 | 曽篠春成 | harunari.soshino.36 |
| 563 | 曽根彩加 | ayaka.sone.40 |
| 564 | 園田光 | hikaru.sonoda.41 |
| 565 | 染川航 | wataru.somekawa.37 |
| 566 | 泰井翔吾 | shogo.taii.42 |
| 567 | 孝岡三郎 | saburo.takaoka.38 |
| 568 | 高木規雄 | norio.takaki.37 |
| 569 | 高沢雅 | masashi.takazawa.26 |
| 570 | 高谷圭一 | keiichi.takatani.30 |
| 571 | 高田麻衣子 | maiko.takata.25 |
| 572 | 高田凜 | rin.takada.41 |
| 573 | 高地真生 | mao.takachi.40 |
| 574 | 小鳥遊麻央 | mao.takanashi.39 |
| 575 | 高野宏治 | koji.takano.36 |
| 576 | 高橋慧 | kei.takahashi.35 |
| 577 | 髙橋聡志 | satoshi.takahashi.39 |
| 578 | 髙橋悟志 | satoshi.takahashi.31 |
| 579 | 高橋大輔 | daisuke.takahashi.37 |
| 580 | 高橋智也 | tomoya.takahashi.40 |
| 581 | 高橋尚也 | naoya.takahashi.41 |
| 582 | 高橋信夫 | nobuo.takahashi.24 |
| 583 | 高橋侑希 | yuki.takahashi.29 |
| 584 | 高橋良介 | ryosuke.takahashi.26 |
| 585 | 高橋亮輔 | ryosuke.takahashi.38 |
| 586 | 高畑敬太 | keita.takabatake.38 |
| 587 | 鷹見としや | toshiya.takami.39 |
| 588 | 高宮まり | mari.takamiya.27 |
| 589 | 高村翔 | kakeru.takamura.30 |
| 590 | 高村龍一 | ryuichi.takamura.38 |
| 591 | 高柳寛哉 | tomoya.takayanagi.35 |
| 592 | 高山暖 | dan.takayama.38 |
| 593 | 多賀友哉 | tomoya.taga.39 |
| 594 | 田川将太 | shota.tagawa.38 |
| 595 | 滝沢和典 | kazunori.takizawa.16 |
| 596 | 瀧澤光太郎 | kotaro.takizawa.37 |
| 597 | 瀧田亮 | ryo.takita.32 |
| 598 | 瀧根克登志 | katsutoshi.takine.41 |
| 599 | 瀧原光 | hikaru.takihara.39 |
| 600 | 武石絵里 | eri.takeishi.23 |
| 601 | 竹内洋規 | hiroki.takeuchi.24 |
| 602 | 武下明弘 | akihiro.takeshita.40 |
| 603 | 竹下将巳 | masami.takeshita.41 |
| 604 | 武田雛歩 | hinaho.takeda.37 |
| 605 | 武田裕希 | yuki.takeda.21 |
| 606 | 田渋千歳 | kazutoshi.tashibu.42 |
| 607 | 田代航太郎 | kotaro.tashiro.24 |
| 608 | 多田理音 | rion.tada.38 |
| 609 | 刀川昌浩 | masahiro.tachikawa.23 |
| 610 | 橘太一 | taichi.tachibana.35 |
| 611 | 橘はるか | haruka.tachibana.39 |
| 612 | 橘来奈 | rana.tachibana.41 |
| 613 | 立田祥平 | shohei.tatta.35 |
| 614 | 立松伸英 | nobuhide.tatematsu.41 |
| 615 | 田中朝陽 | asahi.tanaka.42 |
| 616 | 田中健渡 | kento.tanaka.39 |
| 617 | 田中光太 | kota.tanaka.42 |
| 618 | 田中祐 | tasuku.tanaka.37 |
| 619 | 田中寛治 | hirotsugu.tanaka.35 |
| 620 | 田中史孝 | fumitaka.tanaka.23 |
| 621 | 田中羚 | rei.tanaka.42 |
| 622 | 田辺ゆい | yui.tanabe.42 |
| 623 | 谷岡育夫 | ikuo.tanioka.22 |
| 624 | 谷口弘昌 | hiromasa.taniguchi.39 |
| 625 | 谷誠之 | shigeyuki.tani.31 |
| 626 | 田村翔平 | shohei.tamura.40 |
| 627 | 田村良介 | ryosuke.tamura.33 |
| 628 | 多和田真悟 | shingo.tawada.42 |
| 629 | 伊達朱里紗 | arisa.date.35 |
| 630 | ダニーモレノ | dani.moreno.35 |
| 631 | ダンプ大橋 | dump.ohashi.18 |
| 632 | 知名亮 | ryo.china.42 |
| 633 | 知念りほ | riho.chinen.41 |
| 634 | 千葉みほ | miho.chiba.36 |
| 635 | チャップマン | chapman.42 |
| 636 | 中馬雄志 | yushi.chuma.42 |
| 637 | 塚越達也 | tatsuya.tsukakoshi.37 |
| 638 | 塚田悠介 | yusuke.tsukada.36 |
| 639 | 塚本将之 | nobuyuki.tsukamoto.20 |
| 640 | 月野桜悠 | miyu.tsukino.39 |
| 641 | 津島萌唯 | mei.tsushima.38 |
| 642 | 辻井和也 | kazuya.tsujii.30 |
| 643 | 辻本一樹 | itsuki.tsujimoto.36 |
| 644 | 辻本翔哉 | shoya.tsujimoto.25 |
| 645 | 筒井宏晶 | hiroaki.tsutsui.26 |
| 646 | 堤文吾 | bungo.tsutsumi.31 |
| 647 | 都築友和 | tomokazu.tsuzuki.30 |
| 648 | 津藤雅知 | masatomo.tsuto.39 |
| 649 | 坪原光 | hikaru.tsubohara.38 |
| 650 | 妻木怜 | rei.tsumaki.38 |
| 651 | 津村憲一 | kenichi.tsumura.36 |
| 652 | 津森慎一 | shinichi.tsumori.39 |
| 653 | 釣本契介 | keisuke.tsurimoto.37 |
| 654 | 鶴友貴 | tomotaka.tsuru.42 |
| 655 | 手塚紗掬 | sakiku.tezuka.28 |
| 656 | 寺下達規 | tatsuki.terashita.42 |
| 657 | 寺下龍太郎 | ryutaro.terashita.38 |
| 658 | 寺戸孝志 | takashi.terado.21 |
| 659 | 寺本光一 | koichi.teramoto.37 |
| 660 | 寺本将彦 | masahiko.teramoto.39 |
| 661 | 天音まこと | makoto.tenne.20 |
| 662 | 出越茂毅 | shigeki.degoshi.39 |
| 663 | 戸井田清光 | kiyomitsu.toida.22 |
| 664 | 東城りお | rio.tojo.29 |
| 665 | 藤堂菫 | sumire.todo.42 |
| 666 | 藤根梨沙 | risa.tone.35 |
| 667 | 時田拓和 | hirokazu.tokita.29 |
| 668 | 徳井栞人 | kanto.tokui.39 |
| 669 | 得居隆輔 | ryusuke.tokui.41 |
| 670 | 外嶋諒汰 | ryota.toshima.36 |
| 671 | 戸田誠輝 | masaki.toda.28 |
| 672 | 冨田淳一 | junichi.tomita.22 |
| 673 | 冨田智寛 | chihiro.tomita.37 |
| 674 | 冨田久志 | hisashi.tomita.27 |
| 675 | 富村つぐみ | tsugumi.tomimura.27 |
| 676 | ともたけ雅晴 | masaharu.tomotake.8 |
| 677 | 鳥越真仁 | shinji.torigoe.28 |
| 678 | 部谷幸則 | yukinori.toriya.31 |
| 679 | 童瞳 | ton.ton.26 |
| 680 | 土井悟 | satoru.doi.18 |
| 681 | 内藤正樹 | masaki.naito.34 |
| 682 | 中井功 | ko.nakai.42 |
| 683 | 中岡博一 | hirokazu.nakaoka.38 |
| 684 | 中垣吾郎 | goro.nakagaki.35 |
| 685 | 仲亀俊弥 | shunya.nakagame.39 |
| 686 | 中川保 | tamotsu.nakagawa.22 |
| 687 | 中川天基 | tenki.nakagawa.38 |
| 688 | 中川基輝 | motoki.nakagawa.31 |
| 689 | 中川由佳梨 | yukari.nakagawa.23 |
| 690 | 中川竜伍 | ryugo.nakagawa.40 |
| 691 | 中川竜平 | ryuhei.nakagawa.39 |
| 692 | 中岫宏文 | hirobumi.nakaguki.25 |
| 693 | 中島寿太郎 | jutaro.nakajima.37 |
| 694 | 中島剛 | tsuyoshi.nakajima.36 |
| 695 | 中嶋野乃 | nono.nakajima.39 |
| 696 | 中島寛基 | hiroki.nakajima.36 |
| 697 | 仲宗根雅己 | masaki.nakasone.42 |
| 698 | 仲田加南 | kana.nakata.21 |
| 699 | 中谷彰吾 | shogo.nakatani.29 |
| 700 | 中田裕介 | yusuke.nakata.39 |
| 701 | 中田花奈 | kana.nakada.37 |
| 702 | 中津真吾 | shingo.nakatsu.30 |
| 703 | 中西栄二 | eiji.nakanishi.26 |
| 704 | 中西亮介 | ryosuke.nakanishi.42 |
| 705 | 仲野孝兵 | kohei.nakano.34 |
| 706 | 中野秀司 | shuji.nakano.40 |
| 707 | 中野妙子 | taeko.nakano.30 |
| 708 | 中野孝治 | takaharu.nakano.26 |
| 709 | 中野巧 | takumi.nakano.40 |
| 710 | 中野のぶすけ | nobusuke.nakano.37 |
| 711 | 中原進二 | shinji.nakahara.38 |
| 712 | 中寿文 | hisafumi.naka.30 |
| 713 | 中平優佑 | yusuke.nakahira.37 |
| 714 | 中村朝海 | asami.nakamura.42 |
| 715 | 中村健太郎 | kentaro.nakamura.42 |
| 716 | 中村慎吾 | shingo.nakamura.27 |
| 717 | 中村泰詩 | taishi.nakamura.42 |
| 718 | 中村毅 | tsuyoshi.nakamura.19 |
| 719 | 中村裕之 | hiroyuki.nakamura.36 |
| 720 | 中村政時 | masatoki.nakamura.17 |
| 721 | 中村優花 | yuka.nakamura.41 |
| 722 | 中村優太 | yuta.nakamura.40 |
| 723 | 中村龍太 | ryuta.nakamura.23 |
| 724 | 中村亮 | ryo.nakamura.36 |
| 725 | 仲村澪哉 | ryoya.nakamura.36 |
| 726 | 中山あおい | aoi.nakayama.40 |
| 727 | 中山翔太 | shota.nakayama.40 |
| 728 | 永井卓也 | takuya.nagai.21 |
| 729 | 永井勝晴 | masaharu.nagai.30 |
| 730 | 長尾浩平 | kohei.nagao.21 |
| 731 | 永田泰志 | taishi.nagata.40 |
| 732 | 長山朔弥 | sakuya.nagayama.40 |
| 733 | 渚もも | momo.nagisa.40 |
| 734 | 凪瀬くるみ | kurumi.nagise.43 |
| 735 | 梛木広信 | hironobu.nagi.35 |
| 736 | 名倉徹 | toru.nagura.26 |
| 737 | 灘麻太郎 | asataro.nada.1 |
| 738 | 夏川夏未 | natsumi.natsukawa.38 |
| 739 | 夏野千代 | chiyo.natsuno.40 |
| 740 | 夏目一花 | ichika.natsume.38 |
| 741 | 夏目翠 | midori.natsume.34 |
| 742 | 奈良圭純 | keijun.nara.22 |
| 743 | 成田理良 | ryo.narita.33 |
| 744 | 成瀬湊 | minato.naruse.41 |
| 745 | 鳴海拓斗 | takuto.narumi.39 |
| 746 | 南里はるみ | harumi.nanri.16 |
| 747 | 二階堂亜樹 | aki.nikaido.15 |
| 748 | 二階堂瑠美 | rumi.nikaido.16 |
| 749 | 西岡慎泰 | noriyasu.nishioka.21 |
| 750 | 西川茂樹 | shigeki.nishikawa.42 |
| 751 | 西川淳 | jun.nishikawa.18 |
| 752 | 西倉あおい | aoi.nishikura.42 |
| 753 | 西澤慶記 | yoshiki.nishizawa.41 |
| 754 | 西島一彦 | kazuhiko.nishijima.19 |
| 755 | 西嶋ゆかり | yukari.nishijima.28 |
| 756 | 西園さくや | sakuya.nishizono.42 |
| 757 | 西田修 | osamu.nishida.24 |
| 758 | 西田みお | mio.nishida.32 |
| 759 | 仁科優太 | yuta.nishina.37 |
| 760 | 西野隆博 | takahiro.nishino.38 |
| 761 | 西野拓也 | takuya.nishino.11 |
| 762 | 西村和貴 | kazutaka.nishimura.38 |
| 763 | 西村諒太 | ryota.nishimura.40 |
| 764 | 西山あみ | ami.nishiyama.25 |
| 765 | 西山航瑠 | wataru.nishiyama.38 |
| 766 | 仁平宣明 | nobuaki.nihira.11 |
| 767 | 沼山亮 | ryo.numayama.34 |
| 768 | 根越英斗 | hideto.negoshi.33 |
| 769 | 野方祐介 | yusuke.nogata.18 |
| 770 | 野坂健一 | kenichi.nosaka.30 |
| 771 | 野沢友太朗 | yutaro.nozawa.39 |
| 772 | 能瀬美咲 | misaki.nose.39 |
| 773 | 野々川博之 | hiroyuki.nonokawa.6 |
| 774 | 野村創志 | soshi.nomura.39 |
| 775 | 野村駿 | takashi.nomura.36 |
| 776 | 野村祐介 | yusuke.nomura.39 |
| 777 | 野村遼吾 | ryogo.nomura.38 |
| 778 | 萩原智也 | tomoya.hagiwara.40 |
| 779 | 萩原聖人 | masato.hagiwara.34 |
| 780 | 橋本直樹 | naoki.hashimoto.40 |
| 781 | 蓮沼友樹 | yuki.hasunuma.32 |
| 782 | 長谷川夏音 | kanon.hasegawa.39 |
| 783 | 羽田龍生 | tatsuki.hata.35 |
| 784 | 畑谷翔太 | shota.hatadani.42 |
| 785 | 服部学 | manabu.hattori.27 |
| 786 | 花岡章生 | akio.hanaoka.16 |
| 787 | 花田裕貴 | yuki.hanada.37 |
| 788 | 波奈美里 | misato.hana.30 |
| 789 | 花宮海咲 | misaki.hanamiya.37 |
| 790 | 花村魁千 | kaichi.hanamura.41 |
| 791 | 浜上文吾 | bungo.hamagami.18 |
| 792 | 濱田将司 | masashi.hamada.38 |
| 793 | 濱中真志 | masashi.hamanaka.35 |
| 794 | 浜野太陽 | taiyo.hamano.34 |
| 795 | 早川健太 | kenta.hayakawa.35 |
| 796 | 早川航平 | kohei.hayakawa.39 |
| 797 | 早川林香 | rinka.hayakawa.30 |
| 798 | 林俊宏 | toshihiro.hayashi.19 |
| 799 | 林美沙希 | misaki.hayashi.39 |
| 800 | 林玲 | rei.hayashi.40 |
| 801 | 羽山真生 | makoto.hayama.27 |
| 802 | 葉山唯一 | yuiitsu.hayama.38 |
| 803 | 原えりか | erika.hara.38 |
| 804 | 原田潤次 | junji.harada.37 |
| 805 | 原田知彦 | tomohiko.harada.28 |
| 806 | 原佑典 | yusuke.hara.29 |
| 807 | 春名美紀 | miki.haruna.42 |
| 808 | 春原りん | rin.haruhara.38 |
| 809 | 萬代進一郎 | shinichiro.bandai.41 |
| 810 | 東口陽一 | yoichi.higashiguchi.40 |
| 811 | 東谷達矢 | tatsuya.higashitani.27 |
| 812 | 比嘉秀樹 | hideki.higa.42 |
| 813 | 疋野加奈子 | kanako.hikino.38 |
| 814 | 樋口徹 | tetsu.higuchi.28 |
| 815 | 樋口洋輔 | yosuke.higuchi.23 |
| 816 | 久山浩司 | koji.hisayama.25 |
| 817 | 聖川輝弥 | teruya.hijikawa.41 |
| 818 | 一井慎也 | shinya.hitotsui.17 |
| 819 | 日野太貴 | taiki.hino.38 |
| 820 | 日水亮輔 | ryosuke.himizu.40 |
| 821 | 比屋定秀太 | shuta.hiyajo.42 |
| 822 | 日吉辰哉 | tatsuya.hiyoshi.18 |
| 823 | 平石洋輔 | yosuke.hiraishi.38 |
| 824 | 平岡理恵 | rie.hiraoka.16 |
| 825 | 平川一樹 | kazuki.hirakawa.38 |
| 826 | 平野敬悟 | keigo.hirano.30 |
| 827 | 平野よしつね | yoshitsune.hirano.42 |
| 828 | 平野良栄 | ryoei.hirano.30 |
| 829 | 平山慶典 | keisuke.hirayama.36 |
| 830 | 廣岡璃奈 | rina.hirooka.36 |
| 831 | HIRO柴田 | hiro.shibata.17 |
| 832 | 弘中栄司 | eiji.hironaka.30 |
| 833 | 深尾太雅 | taiga.fukao.41 |
| 834 | 深谷勇太 | yuta.fukaya.39 |
| 835 | 福島清子 | sayako.fukushima.26 |
| 836 | 福島佑一 | yuichi.fukushima.29 |
| 837 | 福田和真 | kazuma.fukuda.37 |
| 838 | 福田大志 | hiroshi.fukuda.41 |
| 839 | 福田諒 | makoto.fukuda.34 |
| 840 | 福田雄大 | yuta.fukuda.32 |
| 841 | 福原剛 | tsuyoshi.fukuhara.9 |
| 842 | 福光聖雄 | masao.fukumitsu.25 |
| 843 | 福本正雄 | masao.fukumoto.39 |
| 844 | 福山満幸 | mitsuyuki.fukuyama.13 |
| 845 | 福良翔 | kakeru.fukura.40 |
| 846 | 伏見誠一郎 | seiichiro.fushimi.32 |
| 847 | 藤居冴加 | saeka.fujii.37 |
| 848 | 藤井すみれ | sumire.fujii.25 |
| 849 | 藤井崇勝 | takanori.fujii.32 |
| 850 | 藤岡治之 | haruyuki.fujioka.20 |
| 851 | 藤川議次 | yoshitsugu.fujikawa.20 |
| 852 | 藤崎智 | satoshi.fujisaki.13 |
| 853 | 藤﨑龍一 | ryuichi.fujisaki.42 |
| 854 | 藤澤俊介 | shunsuke.fujisawa.42 |
| 855 | 藤沢晴信 | harunobu.fujisawa.37 |
| 856 | 藤島健二郎 | kenjiro.fujishima.17 |
| 857 | 藤田愛 | ai.fujita.37 |
| 858 | 藤永流馬 | ryuma.fujinaga.34 |
| 859 | 藤波美月 | mitsuki.fujinami.41 |
| 860 | 藤野善充 | yoshimitsu.fujino.41 |
| 861 | 藤間大徳 | hironori.fujima.36 |
| 862 | 藤本修二 | shuji.fujimoto.20 |
| 863 | 藤本哲也 | tetsuya.fujimoto.21 |
| 864 | 藤本鉄也 | tetsuya.fujimoto.10 |
| 865 | 藤原英司 | eiji.fujiwara.23 |
| 866 | 藤原隆弘 | takahiro.fujiwara.3 |
| 867 | 藤原正允 | masanobu.fujiwara.25 |
| 868 | 藤原洋一 | yoichi.fujiwara.21 |
| 869 | 二木悠太 | yuta.futagi.40 |
| 870 | 船木伸一 | shinichi.funaki.29 |
| 871 | 船田大斗 | hiroto.funada.42 |
| 872 | 文月愛美 | manami.fumizuki.36 |
| 873 | 麓征生 | yukio.fumoto.21 |
| 874 | 古川彩乃 | ayano.furukawa.31 |
| 875 | 古川孝次 | koji.furukawa.1 |
| 876 | 古谷知美 | tomomi.furutani.25 |
| 877 | 古橋崇志 | takashi.furuhashi.21 |
| 878 | 古本和宏 | kazuhiro.furumoto.29 |
| 879 | 別府要 | kaname.beppu.39 |
| 880 | 北條恵美 | emi.hojo.16 |
| 881 | 法原創 | so.hobara.41 |
| 882 | 外間完樹 | kanki.hokama.42 |
| 883 | 外間孝之 | takayuki.hokama.42 |
| 884 | 星井つばさ | tsubasa.hoshii.42 |
| 885 | 星乃あみ | ami.hoshino.37 |
| 886 | 星野佑太 | yuta.hoshino.43 |
| 887 | 堀慎吾 | shingo.hori.41 |
| 888 | 堀田翔平 | shohei.horita.39 |
| 889 | 堀場康平 | kohei.horiba.37 |
| 890 | 堀部雄太 | yuta.horibe.41 |
| 891 | 堀祐大 | yuta.hori.42 |
| 892 | 本田知己 | tomoki.honda.42 |
| 893 | 本田朋広 | tomohiro.honda.28 |
| 894 | 本多竜平 | ryohei.honda.41 |
| 895 | ポロリ | porori.37 |
| 896 | 前粟藏優輔 | yusuke.maeawakura.42 |
| 897 | 前澤涼 | ryo.maesawa.41 |
| 898 | 前田直哉 | naoya.maeda.17 |
| 899 | 増田真人 | masato.masuda.37 |
| 900 | 増田隆一 | ryuichi.masuda.17 |
| 901 | 間瀬聖太 | shota.mase.41 |
| 902 | 町田志織 | shiori.machida.36 |
| 903 | 松井直大 | naohiro.matsui.38 |
| 904 | 松井亮太 | ryota.matsui.39 |
| 905 | 松井亮弥 | ryoya.matsui.42 |
| 906 | 松岡昭彦 | akihiko.matsuoka.20 |
| 907 | 松岡千晶 | chiaki.matsuoka.28 |
| 908 | 松岡学 | manabu.matsuoka.38 |
| 909 | 松岡優太 | yuta.matsuoka.40 |
| 910 | 松尾樹宏 | shigehiro.matsuo.28 |
| 911 | 松崎良文 | yoshifumi.matsuzaki.17 |
| 912 | 松澤悠介 | yusuke.matsuzawa.41 |
| 913 | 松下一真 | kazuma.matsushita.39 |
| 914 | 松島桃花 | momoka.matsushima.38 |
| 915 | 松田彩花 | ayaka.matsuda.33 |
| 916 | 松田健太郎 | kentaro.matsuda.38 |
| 917 | 松田卓也 | takuya.matsuda.42 |
| 918 | 松原健志 | kenshi.matsubara.16 |
| 919 | 松本和樹 | kazuki.matsumoto.39 |
| 920 | 松本京也 | kazuya.matsumoto.38 |
| 921 | 松本峻 | shun.matsumoto.37 |
| 922 | 松本大 | hiro.matsumoto.40 |
| 923 | 松本遊助 | yusuke.matsumoto.20 |
| 924 | 松本幸大 | yukihiro.matsumoto.34 |
| 925 | 真鍋明広 | akihiro.manabe.18 |
| 926 | 丸井一輝 | ikki.marui.35 |
| 927 | 丸山直 | nao.maruyama.26 |
| 928 | 三浦大輔 | daisuke.miura.23 |
| 929 | 三浦智博 | tomohiro.miura.28 |
| 930 | 御子柴佑梨 | yuri.mikoshiba.38 |
| 931 | 美咲優菜 | yuna.misaki.34 |
| 932 | 水口崚 | ryo.mizuguchi.33 |
| 933 | 水澤明彦 | akihiko.mizusawa.20 |
| 934 | 水野孝一 | koichi.mizuno.37 |
| 935 | 水町慎一 | shinichi.mizumachi.27 |
| 936 | 三田晋也 | shinya.mita.25 |
| 937 | 三田不二夫 | fujio.mita.11 |
| 938 | 三井亮 | ryo.mitsui.37 |
| 939 | 光岡舞織 | maori.mitsuoka.39 |
| 940 | 三ツ谷繭 | mayu.mitsuya.37 |
| 941 | 三苫太矩也 | takuya.mitoma.34 |
| 942 | 三戸亮祐 | ryosuke.mito.11 |
| 943 | 皆川直毅 | naoki.minagawa.20 |
| 944 | 皆川侑亮 | yusuke.minagawa.36 |
| 945 | 水都まりん | marin.minato.40 |
| 946 | 南和之 | kazuyuki.minami.34 |
| 947 | 南ていか | teika.minami.40 |
| 948 | 美波智子 | tomoko.minami.21 |
| 949 | 箕輪亜加梨 | akari.minowa.42 |
| 950 | 御船翔太 | shota.mifune.40 |
| 951 | 御船晴斗 | haruto.mifune.40 |
| 952 | 宮内こずえ | kozue.miyauchi.18 |
| 953 | 宮川悟 | satoru.miyakawa.40 |
| 954 | 宮川富太郎 | tomitaro.miyakawa.37 |
| 955 | 宮越大輔 | daisuke.miyakoshi.41 |
| 956 | 宮﨑惇 | atsushi.miyazaki.38 |
| 957 | 宮澤太佑 | daisuke.miyazawa.35 |
| 958 | 宮田雅也 | masaya.miyata.37 |
| 959 | 宮成さく | saku.miyanari.38 |
| 960 | 宮村信 | shin.miyamura.34 |
| 961 | 宮本義章 | yoshinori.miyamoto.42 |
| 962 | 美里麻 | milima.37 |
| 963 | 向中野嶺 | ren.mukainakano.40 |
| 964 | 向井譲 | yuzuru.mukai.28 |
| 965 | 武藤武 | takeshi.muto.18 |
| 966 | 宗雪靖弘 | yasuhiro.muneyuki.38 |
| 967 | 村上良 | makoto.murakami.6 |
| 968 | 村上玲央 | reo.murakami.36 |
| 969 | 村瀬寛哉 | kanya.murase.41 |
| 970 | 村瀬寛光 | hiromitsu.murase.23 |
| 971 | 室伏理麻 | rima.murofushi.14 |
| 972 | 望月雅継 | masatsugu.mochizuki.14 |
| 973 | 元氏なづは | nazuha.motouji.42 |
| 974 | 茂原誠 | makoto.mohara.41 |
| 975 | 百瀬あい | ai.momose.41 |
| 976 | 盛合麻理奈 | marina.moriai.38 |
| 977 | 森岡貞臣 | sadami.morioka.28 |
| 978 | 森川ことり | kotori.morikawa.40 |
| 979 | 森下剛任 | masato.morishita.22 |
| 980 | 森島啓悟 | keigo.morishima.42 |
| 981 | 森貴大 | takahiro.mori.39 |
| 982 | 森田健太郎 | kentaro.morita.38 |
| 983 | 守田成希 | masaki.morita.42 |
| 984 | 森田未来 | mirai.morita.36 |
| 985 | 森東賢一 | takahito.morito.42 |
| 986 | 森勇人 | hayato.mori.42 |
| 987 | 森本夕楓 | yuka.morimoto.40 |
| 988 | 森山茂和 | shigekazu.moriyama.1 |
| 989 | 守屋芳彦 | yoshihiko.moriya.35 |
| 990 | 森義久 | yoshihisa.mori.40 |
| 991 | 森脇司 | tsukasa.moriwaki.10 |
| 992 | 門田大輝 | daiki.monden.41 |
| 993 | 門眞翔 | sho.momma.40 |
| 994 | 八神夏姫 | natsuki.yagami.40 |
| 995 | 八木ヒカル | hikaru.yagi.39 |
| 996 | 矢崎航之介 | konosuke.yazaki.29 |
| 997 | 安井駿 | shun.yasui.42 |
| 998 | 安ヶ平浩希 | hiroki.yasugahira.29 |
| 999 | 安田麻里菜 | marina.yasuda.22 |
| 1000 | 安田玲香 | reika.yasuda.41 |
| 1001 | 安本きらり | kirari.yasumoto.41 |
| 1002 | 安良岡真功 | makoto.yasuraoka.35 |
| 1003 | 矢野拓郎 | takuro.yano.26 |
| 1004 | 山井弘 | hiroshi.yamai.14 |
| 1005 | 山内大誠 | taisei.yamauchi.37 |
| 1006 | 山内梨乃香 | rinoka.yamauchi.42 |
| 1007 | 山川高志郎 | koshiro.yamakawa.36 |
| 1008 | 山神剛 | tsuyoshi.yamagami.24 |
| 1009 | 山口真 | shin.yamaguchi.41 |
| 1010 | 山口哲也 | tetsuya.yamaguchi.42 |
| 1011 | 山口やよい | yayoi.yamaguchi.23 |
| 1012 | 山下敬介 | keisuke.yamashita.30 |
| 1013 | 山下将浩 | masahiro.yamashita.33 |
| 1014 | 山下棟健 | munetake.yamashita.38 |
| 1015 | 山田樹 | itsuki.yamada.37 |
| 1016 | 山田航輔 | kosuke.yamada.39 |
| 1017 | 山田陽斗 | haruto.yamada.41 |
| 1018 | 山田裕紀 | hiroki.yamada.40 |
| 1019 | 山田浩之 | hiroyuki.yamada.17 |
| 1020 | 山田優駿 | masatoshi.yamada.34 |
| 1021 | 山田学武 | manabu.yamada.29 |
| 1022 | 山田祐輝 | yuki.yamada.38 |
| 1023 | 山田蓮 | ren.yamada.41 |
| 1024 | 大和 | yamato.26 |
| 1025 | 山中翼 | tsubasa.yamanaka.25 |
| 1026 | 山野達喜 | tatsuki.yamano.38 |
| 1027 | 山宮雅之 | masayuki.yamamiya.38 |
| 1028 | 山室太二 | taiji.yamamuro.26 |
| 1029 | 山本歩夢 | ayumu.yamamoto.39 |
| 1030 | 山本えりか | erika.yamamoto.27 |
| 1031 | 山本駿 | shun.yamamoto.42 |
| 1032 | 山本拓哉 | takuya.yamamoto.29 |
| 1033 | 山本裕之 | hiroyuki.yamamoto.33 |
| 1034 | 山本祐輔 | yusuke.yamamoto.36 |
| 1035 | 山本吉彦 | yoshihiko.yamamoto.42 |
| 1036 | 山元龍 | ryu.yamamoto.38 |
| 1037 | 山本涼介 | ryosuke.yamamoto.41 |
| 1038 | 山屋洋平 | yohei.yamaya.33 |
| 1039 | 山脇千文美 | chifumi.yamawaki.29 |
| 1040 | 雪野伊織 | iori.yukino.40 |
| 1041 | 湯田拓也 | takuya.yuda.39 |
| 1042 | 優月みか | mika.yuzuki.30 |
| 1043 | 柚木正仁 | masahito.yunoki.20 |
| 1044 | 湯村浩章 | hiroaki.yumura.39 |
| 1045 | 横田幸太朗 | kotaro.yokota.36 |
| 1046 | 横山毅 | takeshi.yokoyama.15 |
| 1047 | 吉井友直 | tomotada.yoshii.19 |
| 1048 | 吉井優 | yu.yoshii.37 |
| 1049 | 吉岡翔一 | shoichi.yoshioka.43 |
| 1050 | 吉川和輝 | kazuki.yoshikawa.41 |
| 1051 | 吉川裕太 | yuta.yoshikawa.34 |
| 1052 | 吉沢康次 | koji.yoshizawa.22 |
| 1053 | 吉沢彩月 | satsuki.yoshizawa.40 |
| 1054 | 吉澤彰真 | shoma.yoshizawa.41 |
| 1055 | 吉澤竜也 | ryuya.yoshizawa.36 |
| 1056 | 吉田彩乃 | ayano.yoshida.30 |
| 1057 | 吉田勝弥 | katsuya.yoshida.22 |
| 1058 | 吉田圭吾 | keigo.yoshida.30 |
| 1059 | 吉田光太 | kota.yoshida.39 |
| 1060 | 吉田祥子 | shoko.yoshida.34 |
| 1061 | 吉田大樹 | taiju.yoshida.42 |
| 1062 | 吉田直 | tadashi.yoshida.23 |
| 1063 | 吉田幸雄 | yukio.yoshida.5 |
| 1064 | 吉成敏 | satoru.yoshinari.37 |
| 1065 | 吉野敦志 | atsushi.yoshino.26 |
| 1066 | 吉村卓美 | takumi.yoshimura.41 |
| 1067 | 吉村悠佑 | yusuke.yoshimura.42 |
| 1068 | 吉本卓矢 | takuya.yoshimoto.28 |
| 1069 | 米川基紀 | motoki.yonekawa.18 |
| 1070 | 頼さくら | sakura.rai.38 |
| 1071 | 李佳 | ka.li.39 |
| 1072 | 李峻榮 | jun-young.lee.40 |
| 1073 | 林源 | gen.rin.38 |
| 1074 | るうこ | ruko.41 |
| 1075 | 流水聖人 | masato.rumi.19 |
| 1076 | 若井翼 | tsubasa.wakai.36 |
| 1077 | 若松慶悟 | keigo.wakamatsu.40 |
| 1078 | 若松正和 | masakazu.wakamatsu.24 |
| 1079 | 和久津晶 | akira.wakutsu.23 |
| 1080 | 渡辺篤志 | atsushi.watanabe.37 |
| 1081 | 渡辺詠一 | eiichi.watanabe.43 |
| 1082 | 渡辺英梧 | eigo.watanabe.37 |
| 1083 | 渡辺健太 | kenta.watanabe.34 |
| 1084 | 渡邉浩史郎 | koshiro.watanabe.35 |
| 1085 | 渡邉準也 | junya.watanabe.37 |
| 1086 | 渡邉翔士己 | toshiki.watanabe.38 |
| 1087 | 渡辺洋巳 | hiromi.watanabe.20 |
| 1088 | 渡邉博之 | hiroyuki.watanabe.38 |
| 1089 | 渡辺史哉 | fumiya.watanabe.37 |
| 1090 | 渡邊真央 | mao.watanabe.42 |
| 1091 | 渡部美樹 | miki.watanabe.38 |
| 1092 | 渡邊悠 | yu.watanabe.41 |
| 1093 | 渡辺雄輝 | yuki.watanabe.38 |
| 1094 | 渡邊亮 | ryo.watanabe.36 |
| 1095 | 和田健一 | kenichi.wada.38 |
| 1096 | 和田聡一隆 | soichiryu.wada.42 |
| 1097 | 和田直樹 | naoki.wada.40 |
| 1098 | 王政芳 | zheng-fang.wan.22 |

### 作ったもの（手順2）

- 生成: `scripts/generate_pro_pages.py`（トップ・個別ページ・`pro/search.json`・`data/pro_ogp.json`）。大会ごとの値は各ページの生成物から読む（PRO-01 の `## 経過` のとおり）。`regenerate.py` に `pro_pages`（出力 `pro/ data/pro_ogp.json`）・`RUN_LAST`（最後に作る）を足した
- 部品: `assets/pro.js`（切り口・並べ替え・リンクのコピー・写真の差し替え）、`assets/league.js`（`data-houou="pro"` の検索。候補を押すと個別ページ、索引の9つ目が slug）、`assets/houou_ask.js`（`data-ask-prefix`）、`style.css`（pro/ の節。新サイトの基本の形の節の前）、`navbar.js`（/pro/ も白地のナビ）、`_redirects`（`/pro`・`/pro/`・`/pro/:slug/`）、`check-assets.yml`（`pro` を許可）
- OGP: `scripts/build_pro_ogp.py`（コンテナで描く）・`scripts/tests/test_og_image.py` に選手別の画像の形を足した。docs/notes/ogp.md に「選手（pro/）」の節
- 文書: docs/notes/pro-pages.md（仕様）・static-generation.md「ページの一覧」・new-page-checklist.md（`kana`・`joined`・`by`）・handover.md の表の1行
- 生成の手順と計測:
  - `regenerate-page.yml`（target_page `pro_pages`）run 41138136885 → e3197f7「chore: regenerate pro/ …」。「対象ページを再生成」の手順は 13秒（22:26:44〜22:26:57、シートの読み込みを含む）
  - コンテナで `python3 scripts/build_pro_ogp.py`: 「描いた画像 1098枚・消した画像 0枚(72秒)。img/ogp/pro/ は 1098枚・36.3MB」（10分・300MB の内）→ bf1a0e7
  - 再生成 run 41138394799 → aef8e8e。`grep -L "img/ogp/pro/" pro/*/index.html` は0件（全員が選手別の画像を指す）
  - レビューの直しの後に再生成 run 41139346023 → faac8a1
  - 合計: `pro/` 20,290,478 バイト（1,100ファイル: トップ 約0.7MB・search.json 約0.17MB・個別ページ平均 約17.7KB）、`img/ogp/pro/` 38,061,442 バイト（1,098枚）

### 検査（手順3）

- `python3 -m unittest discover -s scripts/tests`: 全件 OK（test_pro_pages は22件）
- `regenerate-page.yml` の check（run 41139485140、4d368bb）: 「pro_pages: 通る(--check)」「check: 止まるページなし」。警告なし
- `python3 scripts/check_asset_limits.py`: 配信ファイル数 6,405 / 100,000（6.4%）、最大のファイル resource_logs.html、_redirects 静的128・動的12、すべて OK
- push の check-run（8c60619）: check（check-assets.yml）success・Workers Builds: mj success
- プレビュー（ブランチの別名 URL。URL は最終報告にだけ書く）: `/pro/` 200・noindex あり、`/pro/ryoei.hirano.30/` 200、`/pro/a-ji.skull-reaper.27/`（点と「-」の slug）200、`/pro` 301、`/pro/search.json` 200、`/img/ogp/pro/ryoei.hirano.30-906e00a8.png` 200

### 画面の確かめ

- headless Chromium（同梱の Playwright）で、生成物をローカルの `python3 -m http.server` から開き、360px・390px × 明・暗を撮った（44枚、`docs/logs/CHAT-1031-PRO-02/`）。X の画像・YouTube・解析は読み込ませていない（頭の1字の丸で写る）
- 対象: トップ（既定 `top`・絞り込み `top-place`〈?place=北海道〉・すべて `top-all`。トップは上 1400px だけ）、個別ページ（名前の最も長い スカルリーパーA-Ji `longest`・鳳凰戦だけ 會田亮介・桜花だけ 安部久美子・両方 蒼井ゆりか・どちらも無い 藍ありさ・放送対局の最も多い HIRO柴田〈123件〉・SNS の無い 明石定家〈SNS の無い在籍者は237名〉・進行中の期だけ 朝比奈優斗）
- はみ出し（横スクロール・画面の外に出る要素）: 0件。重なり: 32件の検出はすべて「質問欄の注意書き（閉じた `<details>` の中）× ページ末尾の説明文」で、見えない要素の誤検出（houou/ のトップでも同じ検出が出る）。見える重なりは0件
- 撮影スクリプトの不具合（URL の「=」で分けていた）で1回目の絞り込みの画面が「すべて」になっていたのを直して撮り直した

### 差分のレビュー（新しい文脈のサブエージェント、2回）

1回目（cloudflare...8c60619、生成物とログを除く19ファイル。テスト 1651件 OK・check_asset_limits OK を確かめたうえで）:

| 指摘 | 扱い |
|---|---|
| 中: 足した分岐・解析の検査が無い（now_term・load_titles・page_json・related・facet_values・top_row、JS の suggestMeta・state） | 直した（039b86d。Python の7つ。JS は CI でテストが回っていないので見送り） |
| 低: top_row のリーグの順の引き方が確かめなし | 直した（039b86d） |
| 低: 不正なクエリでタブ・値が押されない | 直した（039b86d。既定の「あ」行へ） |
| 低: 遅延の画像が complete=true になるブラウザで丸になる | 意図どおり（仕様では未読み込みは false。プレビューで確かめる） |
| 低: 読む生成物だけの再生成で pro/ が追随しない | 直した（039b86d。regenerate.py に `簡略化:` を残した。毎日の all で追いつく） |
| 低: OGP が黙って共通画像に戻る | 直した（039b86d・4d368bb。生成と --check で警告） |
| 低: `lib.birthdays._parse_month_day`・`generate_jpml_pros.QUERY_ESCAPES` を外から読む | 見送り（判断が必要なこと） |
| 低: 「いま」の期と直近の節の期が食い違って見える／description の文言 | 期は仮置き K1。description は直した（039b86d） |
| 低: remove_stale が slug 以外のディレクトリも消す | 直した（039b86d） |

2回目（8c60619..039b86d）:

| 指摘 | 扱い |
|---|---|
| 低: OGP の警告が --check で出ない | 直した（4d368bb） |
| 低: 順序表に無いリーグのチップが鳳凰位の色のまま | 意図どおり（今のデータでは起きない。止まらないことだけをテスト） |

### 公開の指示で要る変更（#641 にコメント）

- noindex を外す（`generate_pro_pages.py` の `NOINDEX_TAG`）、navbar の「連盟プロ」の行き先を `/pro/`、サイトマップ（1,100 URL。`sitemap-pro.xml` を生成に足すか決める）、`llms.txt`
- `jpml_pros.html` → `/pro/` の 301、`?name=` の 301（Worker `mj`。名前→slug の表は `pro/search.json` から作れる、#159）
- 既存ページのリンクの向け替え: houou_race の `playerUrl()`・title/ の優勝者・live/ の選手名・wayhome（#195）・質問欄の答えのカード（今は houou/ の個人成績へ）
- canonical（K13）、OGP の画像の描き直しの運用（K16）

### 仮置きの一覧（画面のどこに効くか）

| # | 仮置き | 効く場所 | 今の形 | ほかの案 |
|---|---|---|---|---|
| K1 | 「いま」の期と直近の節 | 個別ページ「いま」 | 現在のリーグは各大会の `ask-players.json`（個人成績の名前の面のチップと同じ値。`current_league_table()` と同じ `current_league()` の結果）。期は進行中の期に出ていればその期、通期の A1・A2・鳳凰位は「43期」。直近の節は成績 JSON の最後の行（終わった期）の最後の節の得点と、その期の最終順位（「43期前期 第5節 ▲11.0 · 32位」）。進行中の期の節の成績は成績 JSON に無いので出せない | 進行中の期の節の速報（#223）が入ってからその値にする |
| K2 | 成績の面の並び | 個別ページ「成績」 | 「リーグ戦・大会」（鳳凰戦・女流桜花・WRCリーグ・WRC-Rリーグ・麻雀最強戦）と「タイトル戦の決勝」（title/ の大会ごとに「優勝 N回 · 決勝 N回」）の2つの小見出し。WRC-R は指示の大会の列挙に無いが、同じ型の個人成績があるので足した。title/ の大会名はリーグ戦と同じ名前（鳳凰戦・WRC-Rリーグ）があるので小見出しで分けた | WRC-R を外す・タイトル戦を1行にまとめる |
| K3 | 放送対局の件数 | 個別ページ「放送対局」の「すべて（N件）」 | live/ のトップのカードの検索語に名前が語として入るカードの数（語の一致）。live/?q= の検索は部分一致なので、名前が別の人の名前に含まれると live/ の件数の方が多くなりうる | live/ の検索と同じ部分一致で数える |
| K4 | 関連する選手の選び方 | 個別ページ「関連する選手」 | 同期・同じ支部・同じリーグ（鳳凰戦の現在のリーグ）を、それぞれソートキーの順の先頭5人（本人を除く）。`簡略化:` の印あり | 同じ組・近い順位の選手、ランダム |
| K5 | 名前の面 | 個別ページの上 | 読みとローマ字（「姓 名」の順）を「·」でつないで1行、支部と入会期はチップ、誕生日は「誕生日 6月1日」、SNS は文字のボタン（X・note・YouTube）、「リンクをコピー」は紺のボタンで面の右下。コピーしたら「コピーしました」 | SNS をアイコンにする、コピーをアイコンにする |
| K6 | 支部の書き方 | 名前の面・説明文・表・関連する選手 | 名簿の値のまま（「東京」）。指示の例の「東京支部」の形にはしていない | 「東京支部」（本部・支部の区別が要る） |
| K7 | description | 個別ページの meta description・og:description | 「日本プロ麻雀連盟の<名前>（東京・30期）。鳳凰戦 C2（43期後期）、最高 C1。女流桜花 …。成績・放送対局・関連する選手をまとめています。」 | — |
| K8 | 質問欄 | 個別ページの下 | 送る文の先頭に「<名前>について: 」を付けて鳳凰戦（既定の大会）に送る。入力欄の上限は 200字から前置きの字数を引いた数。例文「例：<名前>の鳳凰戦の最高到達は？」。答えに出た選手のカードは houou/ の個人成績へ | 答えのカードを pro/ の個別ページへ（公開の段で） |
| K9 | トップの切り口 | `pro/` | タブ「五十音・支部・入会期・リーグ」と値のボタン（各切り口の最後に「すべて」）。五十音は読みの頭の字の行（仮名でなければ「他」）、支部は人数の多い順、入会期は新しい順、リーグは鳳凰戦の現在のリーグ（リーグの無い選手は「すべて」でだけ出る）。URL は `?kana=`・`?place=`・`?joined=`・`?league=`・`?all=1`（`&by=`）で、`kana`・`joined`・`by` は新しいパラメータ（docs/new-page-checklist.md の規約に足した） | 女流桜花のリーグも切り口に足す |
| K10 | トップの表 | `pro/` | 列は 名前（32px の画像）・支部·期・鳳凰・桜花（リーグのチップ）。最初はソートキーの順、見出しで並べ替え（桜花の欄は桜花の色） | — |
| K11 | 支部・入会期ごとの静的なページ | — | 作らない（トップのクエリで絞るだけ） | `pro/place/<支部>/`・`pro/joined/<期>/` を作る（#285 の支部別ページと重なる。検索に載せたいなら静的なページが要る） |
| K12 | 選手別の OGP 画像 | og:image | 段は名前・「東京・30期」・「鳳凰戦 C2 · 最高 C1」（鳳凰戦の値がある選手だけ）と「ryoei.pro」。白地・黒字。名前は文のハッシュ | 女流桜花の値も入れる |
| K13 | canonical | 個別ページの head | 付けていない（#113）。個別ページはクエリの変種を持たないので wayhome/ の各話と同じ例外に当たりうる | 公開の段で og:url と同じ値を付ける |
| K14 | トップの OGP | `pro/` の og:image | `img/ogp/pros/index-white.png`（「連盟プロ」）をそのまま使う（写していない） | `img/ogp/pro/index-white.png` に写す |
| K15 | #219 と #296 | issue | #219 は #296（新サイト）の sub-issue のまま | 現行サイトで作ったので外す（#296 の本文「新サイト送りにするとき・やめるとき」の手順） |
| K16 | 選手別の OGP 画像の描き直し | og:image | Actions に字体が無いので、コンテナで `build_pro_ogp.py` を回してコミットする（手作業）。リーグが変わった選手は描き直すまで「連盟プロ」の画像に戻る（生成が警告を出す） | Actions で字体を取って描く（`regenerate-page.yml` に Pillow と字体の取得を足す。環境で PNG のバイトが変わりうる） |
| K17 | 共通の値の置き場所 | コード | 誕生日の読み取り `lib.birthdays._parse_month_day()`・クエリの書き方 `generate_jpml_pros.QUERY_ESCAPES` を外から読む | `lib/` に公開の関数・定数として移す（jpml_pros にも触れる変更） |


## 報告

- 状態: 判断待ち（全員分を作り、未公開のままプレビューで止めた。仮置き K1〜K17 の確定と公開の判断を待つ）
- ブランチ: work/1030-pro
- ログ: https://github.com/retroeater/mj/blob/work/1030-pro/docs/logs/CHAT-1031-PRO-02.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1030-pro
- 確認用URL: プレビューあり（URL は最終報告）。確かめたページは `/pro/`・`/pro/ryoei.hirano.30/`・`/pro/a-ji.skull-reaper.27/`。この版の印: `/pro/` の「支部」のタブの値が「東京・関西・中部・九州・北海道…」の人数の多い順
- マージ: 未（平野さんの判断待ち）
- issue: #219（着手中・経過のコメント）・#641（公開の指示で要る変更をコメント）
- 判断が必要なこと:
  - `## 経過`「仮置きの一覧」の K1〜K17（1問1答で確定する）
  - 生成時間 13秒（シートの読み込みを含む）・`pro/` 20.3MB（1,100ファイル）・OGP 1,098枚 72秒・38.1MB（上限の10分・300MB の内なので選手別のまま）
  - #219 を #296（新サイト）の sub-issue から外すか（K15。外すなら #296 の本文「新サイト送りにするとき・やめるとき」の手順）
  - work/1029-lgn-page と同じファイル（check-assets.yml・_redirects・navbar.js）を別の位置で変えた。後からマージする側で衝突しない見込みだが、取り込みは試していない
- 未確認の項目:
  - 実機・X・LINE での見え方（headless Chromium の 360・390px だけ）
  - プレビューで X の画像を読み込んだときの写真の差し替え（撮影では読み込ませていない）
  - 質問欄に実際に問いを送った答え（Worker を呼ぶため送っていない）
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj bc1a6ae8）: https://github.com/retroeater/mj-logs/tree/main/guide/bc1a6ae8

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/bc1a6ae8/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/7147c20d.md
