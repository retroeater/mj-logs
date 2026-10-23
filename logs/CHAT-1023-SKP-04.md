# CHAT-1023-SKP-04

- 着手日時: 2026-10-23
- 対象issue: #628（起票）・#627（公開）
- ブランチ: work/1023-skp（マージ済みのため origin/cloudflare まで進めて使う）
- 着手時HEAD: d8536d379

## 指示

【Claude作成】Claude Code 向け指示：最強戦の連盟外の出場者124名の「名前かな」を入れる issue を起票し、チャット側が調べた読みの案と出典を載せる（#627 から切り出す）
Chat-Ref: CHAT-1023-SKP-04
マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（変わるのは docs/logs と docs/decisions だけ）
貼る時機: いつでも（CHAT-1023-SKP-03 は完了済み）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1023-skp の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1023-skp を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1023-skp origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
最強戦の個人成績で読みの無い連盟外の出場者124名（CHAT-1023-SKP-02 のログの表）について、「連盟プロ以外」の「名前かな」を入れる作業を、#627（公開）から切り出して別の issue にする。チャット側が Web で調べた読みの案と出典を issue に載せ、平野さんが確かめるときに使えるようにする。

### 決定（2026-10-23、平野さん）
- 「名前かな」を124名分入れる作業は、別の issue として起票する
- 空欄が残っても公開（#627）は止めない（CHAT-1023-SKP-03 の決定「入力は公開までに行う」を置き換える）
- 読みの案は、チャット側が各団体の公式サイトなどで可能な限り調べ、出典のリンクを付ける。平野さんはそのリンクで確かめてからシートに入れる

### 前提（チャット側。平野さんの決定ではない）
- 下の「読みの案」の表は、チャット側が 2026-10-23 に Web で調べた結果（確実 29名・ウィキ 54名・推定 41名）。確度の意味は表の上の説明のとおり。チャット側の調べで、平野さんはまだ確かめていない
- 「時岡史郎」は Web で見つからず、「時岡史明」（中国最強位）の誤記の可能性がある（要確認。シートの名前を直すかは平野さんが決める）
- ラベルは対象を最強戦のもの（`gh label list` の正）、分野は「整理・保守」の見込み（要確認。合うものが無ければ付けずに書く）

## 手順
1. CLAUDE.md「Chat-Ref」の識別子確認の後、同じ論点の open issue（最強戦の名前かな・読み）を検索し（クローズ済みも含めて確認）、あれば使って下の表をコメントする。無ければ起票する。題は「最強戦: 連盟外の出場者124名の「名前かな」を入れる」。本文は、目的（個人成績の選手を探す部品でひらがなで引けるようにする。SKP-01〜03、#626）・上の決定・進め方（平野さんが出典で確かめてシートに入れる。入れた分は翌朝の `regenerate-saikyo.yml` で個人成績に反映される）・下の「読みの案」の説明と表をそのまま載せる。#626 と #627 からリンクする
2. #627 に、「名前かな」の入力は新しい issue に移したこと、空欄が残っても公開は止めないこと（2026-10-23 の決定）をコメントする。#627 の本文にこの項目のチェックリストがあれば、新しい issue を指す形に直す（ほかの項目は変えない）
3. 上の「決定」を docs/decisions/saikyo.md に足す（CHAT-1023-SKP-03 の決定「入力は公開までに行う」を置き換えることを書く）。シートは読み書きしない

## 止まる条件
- 同じ論点の issue があり、中身が上の決定と食い違う
- 下の表の行数が124でない、または名前が CHAT-1023-SKP-02 のログの表の名前と一致しない（違う名前を全件書いて止まる）
- 他セッションの着手中コメントが #627 にある

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1023-SKP-04.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1023-SKP-04 を書く

## 読みの案（issue に載せる表）

- 確度の「確実」: 本人・所属団体・事務所などの公式の表記で確かめた
- 「ウィキ」: Wikipedia（日本語版）で確かめた
- 「推定」: 読みを書いたページが見つからなかった、または同一人物かを確かめられなかった（出典が空のものはチャット側の推測）

| # | 名前 | よみ | 確度 | 出典 | 備考 |
|---|---|---|---|---|---|
| 1 | しゅんしゅんクリニックP | しゅんしゅんくりにっくぴー | ウィキ | [リンク](https://ja.wikipedia.org/wiki/しゅんしゅんクリニックP) | ピン芸人・医師 |
| 2 | むく大樹 | むくだいじゅ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/むく大樹) | 麻将連合。「だいき」ではない |
| 3 | コージー冨田 | こーじーとみた | ウィキ | [リンク](https://ja.wikipedia.org/wiki/コージー冨田) | ものまねタレント |
| 4 | パクジュンシク | ぱくじゅんしく | 推定 |  | 何者か確認できなかった |
| 5 | ヒデオ銀次 | ひでおぎんじ | 推定 | [リンク](https://thetv.jp/person/1000014860/) | 演歌歌手。ザテレビジョンの人物ページ（公式ではない） |
| 6 | マギー審司 | まぎーしんじ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/マギー審司) | 手品師・タレント |
| 7 | 上平茉実 | かみひらまみ | 確実 | [リンク](https://www.ameba.jp/profile/general/kamirepo/) | 最高位戦。本人のブログのプロフィール |
| 8 | 上田浩二郎 | うえだこうじろう | 推定 | [リンク](https://www.weblio.jp/content/上田浩二郎) | お笑いコンビ Hi-Hi。Weblio（公式ではない） |
| 9 | 中出雄介 | なかでゆうすけ | 推定 | [リンク](https://mahjong-dora2.com/puro/950.html) | 麻将連合。愛好サイト。X の ID（nakade_yusuke）とも合う |
| 10 | 中村庸人 | なかむらのりと | 推定 | [リンク](https://saikouisen.com/members/nakamura-norito/) | 最高位戦の選手ページの URL から。ページは開けていない。要確認 |
| 11 | 中野浩一 | なかのこういち | ウィキ | [リンク](https://ja.wikipedia.org/wiki/中野浩一) | 元競輪選手 |
| 12 | 亀田大毅 | かめだだいき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/亀田大毅) | 元プロボクサー |
| 13 | 五十嵐毅 | いがらしたけし | 確実 | [リンク](https://npm2001.com/player/igarashi-takeshi/) | 日本プロ麻雀協会 |
| 14 | 井川意高 | いかわもとたか | ウィキ | [リンク](https://ja.wikipedia.org/wiki/井川意高) | 元大王製紙会長 |
| 15 | 伊藤太 | いとうふとし | 推定 |  | 何者か確認できなかった。要確認 |
| 16 | 佐々木信也 | ささきしんや | ウィキ | [リンク](https://ja.wikipedia.org/wiki/佐々木信也) | 元プロ野球選手・キャスター。最強戦に出た人と同一人物かは未確認 |
| 17 | 佐藤哲夫 | さとうてつお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/佐藤哲夫) | パンクブーブーの佐藤哲夫と思われる（同名の人物の一覧ページで確認） |
| 18 | 先崎学 | せんざきまなぶ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/先崎学) | 将棋棋士 |
| 19 | 児嶋一哉 | こじまかずや | ウィキ | [リンク](https://ja.wikipedia.org/wiki/児嶋一哉) | アンジャッシュ |
| 20 | 内海元 | うつみはじめ | 推定 |  | 日本プロ麻雀協会。楽天TVの検索結果に読みがあったが、ページは開けなかった |
| 21 | 冨本智美 | とみもとさとみ | 確実 | [リンク](https://npm2001.com/player/tomimoto-satomi/) | 日本プロ麻雀協会 |
| 22 | 加藤博己 | かとうひろき | 確実 | [リンク](https://note.com/himitunohanasi) | 本人の note。「ひろみ」ではない |
| 23 | 加藤鷹 | かとうたか | ウィキ | [リンク](https://ja.wikipedia.org/wiki/加藤鷹) | タレント |
| 24 | 原浩明 | はらひろあき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/原浩明) | 麻将連合 |
| 25 | 及川奈央 | おいかわなお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/及川奈央) | 女優 |
| 26 | 吉田昇平 | よしだしょうへい | 推定 |  | 最高位戦。読みを書いたページは見つからなかった |
| 27 | 名越稔洋 | なごしとしひろ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/名越稔洋) | ゲームクリエイター |
| 28 | 喜多剛士 | きたたけし | 確実 | [リンク](https://note.com/kita_keshi/n/ndff82208c275) | 麻将連合。本人の note。平凡社の著者ページは「キタコウジ」 |
| 29 | 堀江貴文 | ほりえたかふみ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/堀江貴文) | 実業家 |
| 30 | 塩原貴文 | しおはらたかふみ | 推定 |  | 最高位戦。「しおばら」の可能性もある。要確認 |
| 31 | 大平亜季 | おおひらあき | 確実 | [リンク](https://saikouisen.com/members/ohira-aki) | 最高位戦 |
| 32 | 大村朋宏 | おおむらともひろ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/大村朋宏) | トータルテンボス |
| 33 | 大谷ノブ彦 | おおたにのぶひこ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/ダイノジ) | ダイノジ。コンビの記事で確認 |
| 34 | 宮内悠介 | みやうちゆうすけ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/宮内悠介) | 小説家 |
| 35 | 小寺勇輝 | こでらゆうき | 推定 |  | RMU。読みは確認できなかった |
| 36 | 小川瑠以 | おがわるい | 確実 | [リンク](https://saikouisen.com/members/ogawa-rui) | 最高位戦 |
| 37 | 小川稜太 | おがわりょうた | 推定 |  | 何者か特定できなかった |
| 38 | 就活生@川村軍団 | しゅうかつせいかわむらぐんだん | 推定 |  | 天鳳位のネット雀士。「@」を除いて読んだ |
| 39 | 山本ひかる | やまもとひかる | ウィキ | [リンク](https://ja.wikipedia.org/wiki/山本ひかる) | 女優・RMU |
| 40 | 山田千明 | やまだちあき | 推定 |  | 何者か特定できなかった |
| 41 | 山部正人 | やまべまさと | 推定 |  | アマチュア（2024年の全国アマチュア最強位）。名字が「やんべ」の可能性あり。要確認 |
| 42 | 岩井勇気 | いわいゆうき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/岩井勇気) | ハライチ |
| 43 | 岩瀬航 | いわせわたる | 確実 | [リンク](https://mu-mahjong.jp/profile/?player_id=100179) | 麻将連合（公式の表記は「岩瀨航」） |
| 44 | 岸学 | きしまなぶ | 推定 | [リンク](https://bangumi.org/talents/71691) | どきどきキャンプ。番組表サイト（公式ではない） |
| 45 | 平木常正 | ひらきつねまさ | 推定 |  | 何者か特定できなかった |
| 46 | 平賀聡彦 | ひらがとしひこ | 確実 | [リンク](https://saikouisen.com/members/hiraga-toshihiko) | 最高位戦 |
| 47 | 広瀬章人 | ひろせあきひと | ウィキ | [リンク](https://ja.wikipedia.org/wiki/広瀬章人) | 将棋棋士 |
| 48 | 張敏賢 | ちょうとしまさ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/張敏賢) | 第19期最強位。「びんけん」ではない |
| 49 | 影山恒太 | かげやまこうた | 推定 |  | 何者か確認できなかった |
| 50 | 徳井健太 | とくいけんた | ウィキ | [リンク](https://ja.wikipedia.org/wiki/徳井健太) | 平成ノブシコブシ |
| 51 | 愛甲猛 | あいこうたけし | ウィキ | [リンク](https://ja.wikipedia.org/wiki/愛甲猛) | 元プロ野球選手 |
| 52 | 折山貴裕 | おりやまたかひろ | 推定 |  | RMU。名字は X の ID と合う。名前の読みは推定 |
| 53 | 時岡史郎 | ときおかしろう | 推定 |  | 見つからない。「時岡史明」の表記違いの可能性あり。シートの名前も要確認 |
| 54 | 月亭八光 | つきていはちみつ | 確実 | [リンク](https://kamigatarakugo.jp/directory/ichimon/tsukitei_hachimitsu/) | 上方落語協会 |
| 55 | 木原直哉 | きはらなおや | 推定 | [リンク](https://www.pokernews.com/poker-players/Naoya-Kihara) | ポーカープレイヤー。ローマ字で確認 |
| 56 | 木村和幸 | きむらかずゆき | 確実 | [リンク](https://mu-mahjong.jp/profile/?player_id=100114) | 麻将連合 |
| 57 | 木谷高明 | きだにたかあき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/木谷高明) | ブシロード創業者 |
| 58 | 村上公成 | むらかみこうせい | 推定 |  | 何者か特定できなかった。確度は低い |
| 59 | 東国原英夫 | ひがしこくばるひでお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/東国原英夫) | 元宮崎県知事 |
| 60 | 松山由希 | まつやまゆき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/松山由希) | 雀士（元最高位戦） |
| 61 | 松本圭世 | まつもとかよ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/松本圭世) | アナウンサー |
| 62 | 板川和俊 | いたがわかずとし | 確実 | [リンク](https://mcww2015.com/pro2) | 麻雀共同体WW。第21期最強位 |
| 63 | 林修 | はやしおさむ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/林修) | 予備校講師・タレント |
| 64 | 柴原大造 | しばはらたいぞう | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100170) | 麻将連合のツアー選手（2008年度）。「だいぞう」ではなく「たいぞう」 |
| 65 | 森川ジョージ | もりかわじょーじ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/森川ジョージ) | 漫画家（『はじめの一歩』）。Wikipediaの表記は「もりかわ ジョージ」 |
| 66 | 植田佳奈 | うえだかな | ウィキ | [リンク](https://ja.wikipedia.org/wiki/植田佳奈) | 声優 |
| 67 | 楢原和人 | ならはらかずと | ウィキ | [リンク](https://ja.wikipedia.org/wiki/楢原和人) | プロ雀士（RMU） |
| 68 | 水城恵利 | みずきえり | ウィキ | [リンク](https://ja.wikipedia.org/wiki/水城恵利) | プロ雀士（日本プロ麻雀協会） |
| 69 | 水崎綾女 | みさきあやめ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/水崎綾女) | 女優 |
| 70 | 水沼利晃 | みずぬまとしあき | 推定 |  | RMU。2012年の最強位。読みを書いたページは見つからなかった |
| 71 | 水瀬千尋 | みなせちひろ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/水瀬千尋) | 女流プロ雀士（日本プロ麻雀協会） |
| 72 | 池谷直樹 | いけたになおき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/池谷直樹) | 元体操選手・タレント |
| 73 | 河合保国 | かわいやすくに | 推定 |  | 何者か確認できなかった。河合正浩とは別人 |
| 74 | 河野高志 | こうのたかし | ウィキ | [リンク](https://ja.wikipedia.org/wiki/河野高志) | プロ雀士（RMU） |
| 75 | 浅見匠太 | あさみしょうた | 推定 |  | 何者か確認できなかった |
| 76 | 濱石樹 | はまいしいつき | 推定 |  | 「濵石樹」の表記もある。2011年に出場。「いつき」かどうかを含めて要確認 |
| 77 | 瀬川瑛子 | せがわえいこ | ウィキ | [リンク](https://ja.wikipedia.org/wiki/瀬川瑛子) | 演歌歌手 |
| 78 | 牛田寿明 | うしだとしあき | 推定 |  | RMU の成績ページに名前はあるが、読みの記載は無い |
| 79 | 玄田哲章 | げんだてっしょう | ウィキ | [リンク](https://ja.wikipedia.org/wiki/玄田哲章) | 声優 |
| 80 | 田本英輔 | たもとえいすけ | 確実 | [リンク](https://note.com/ettomato) | 本人のnoteで「Eisuke Tamoto」と表記（ローマ字で確認） |
| 81 | 田村洸 | たむらこう | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100178) | 麻将連合 |
| 82 | 白川道 | しらかわとおる | ウィキ | [リンク](https://ja.wikipedia.org/wiki/白川道) | 作家 |
| 83 | 白田みお | しろたみお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/白田みお) | プロ雀士（RMU） |
| 84 | 百田尚樹 | ひゃくたなおき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/百田尚樹) | 作家 |
| 85 | 矢神ゆの | やがみゆの | 確実 | [リンク](https://npm2001.com/player/yagami-yuno/) | 日本プロ麻雀協会。公式プロフィールのURLで読みを確認 |
| 86 | 矢野優花 | やのゆうか | ウィキ | [リンク](https://ja.wikipedia.org/wiki/矢野優花) | 女優。最強戦に出た人と同一人物かは確認できなかった |
| 87 | 石原真人 | いしはらまこと | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100160) | 麻将連合 |
| 88 | 石田雅人 | いしだまさと | 推定 |  | 同名のサッカー選手の読み。最強戦に出た人と同一人物かは未確認 |
| 89 | 福本伸行 | ふくもとのぶゆき | ウィキ | [リンク](https://ja.wikipedia.org/wiki/福本伸行) | 漫画家 |
| 90 | 福田聡 | ふくたさとし | ウィキ | [リンク](https://ja.wikipedia.org/wiki/福田聡) | プロ雀士（RMU）。「ふくだ」ではなく「ふくた」 |
| 91 | 秋葉啓之 | あきばひろゆき | 推定 |  | 何者か確認できなかった |
| 92 | 稲毛千佳子 | いなげちかこ | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100165) | 麻将連合 |
| 93 | 稲波崇 | いななみたかし | 推定 |  | アマチュア（2016年の全国アマチュア最強位）。読みを書いたページは見つからなかった |
| 94 | 綾辻行人 | あやつじゆきと | ウィキ | [リンク](https://ja.wikipedia.org/wiki/綾辻行人) | 推理作家 |
| 95 | 花本まな | はなもとまな | 推定 |  | 最高位戦の元女流最高位。読みの表記は見つからなかった |
| 96 | 茅森早香 | かやもりさやか | 確実 | [リンク](https://saikouisen.com/members/kayamori-sayaka) | 最高位戦・Mリーグ |
| 97 | 荒木栄二 | あらきえいじ | 推定 |  | 元プロ（2016年に退会）。名前の読みは確認できなかった |
| 98 | 藤崎和彦 | ふじさきかずひこ | 推定 |  | 何者か確認できなかった |
| 99 | 藤田宜永 | ふじたよしなが | 推定 | [リンク](https://kotobank.jp/word/藤田宜永-618046) | 直木賞作家。コトバンク（辞典） |
| 100 | 虫眼鏡 | むしめがね | ウィキ | [リンク](https://ja.wikipedia.org/wiki/虫眼鏡_(YouTuber)) | 東海オンエア |
| 101 | 蛭子能収 | えびすよしかず | ウィキ | [リンク](https://ja.wikipedia.org/wiki/蛭子能収) | 漫画家・タレント |
| 102 | 西嶋千春 | にしじまちはる | 確実 | [リンク](https://saikouisen.com/members/nishijima-chiharu) | 最高位戦 |
| 103 | 谷井茂文 | たにいしげふみ | 確実 | [リンク](https://rmu.jp/player/prof/1009.htm) | RMU |
| 104 | 赤城由実 | あかぎゆみ | 推定 |  | 何者か確認できなかった |
| 105 | 近藤千雄 | こんどうかずお | 確実 | [リンク](https://npm2001.com/player/kondo-kazuo/) | 日本プロ麻雀協会。2016年の最強位 |
| 106 | 逢川恵夢 | あいかわめぐむ | 確実 | [リンク](https://npm2001.com/player/aikawa-megumu/) | 日本プロ麻雀協会 |
| 107 | 酒井俊晴 | さかいとしはる | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100207) | 麻将連合。最強戦に出た人と同一人物かは未確認 |
| 108 | 里中花奈 | さとなかかな | 推定 |  | RMU。個人の紹介ページは見つからなかった |
| 109 | 野間一列 | のまいちれつ | 推定 |  | アマチュア（2018年）。確度は低い |
| 110 | 金太賢 | きむてひょん | 確実 | [リンク](https://npm2001.com/player/kimu-tehyon/) | 日本プロ麻雀協会 |
| 111 | 金子昇 | かねこのぼる | ウィキ | [リンク](https://ja.wikipedia.org/wiki/金子昇) | 俳優 |
| 112 | 金子達仁 | かねこたつひと | ウィキ | [リンク](https://ja.wikipedia.org/wiki/金子達仁) | スポーツライター |
| 113 | 金岡晋哉 | かなおかしんや | 推定 |  | 何者か確認できなかった。「かねおか」の可能性もある |
| 114 | 釼持丈 | けんもちじょう | 確実 | [リンク](https://www.nihonkiin.or.jp/player/htm/ki000259_2.html) | 囲碁棋士（日本棋院） |
| 115 | 鈴木隆将 | すずきたかまさ | 推定 |  | 元最高位戦（退会） |
| 116 | 阿部孝則 | あべたかのり | 確実 | [リンク](https://rmu.jp/player/prof/1002.htm) | RMU |
| 117 | 須貝駿貴 | すがいしゅんき | 確実 | [リンク](https://www.watanabepro.co.jp/mypage/61000060/) | QuizKnock。所属事務所のページ |
| 118 | 風間杜夫 | かざまもりお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/風間杜夫) | 俳優 |
| 119 | 飯嶋ひとみ | いいじまひとみ | 確実 | [リンク](https://mu-mahjong.jp/player/profile/?player_id=100230) | 麻将連合 |
| 120 | 香川愛生 | かがわまなお | ウィキ | [リンク](https://ja.wikipedia.org/wiki/香川愛生) | 女流棋士（将棋） |
| 121 | 高橋凌 | たかはしりょう | 推定 |  | 何者か確認できなかった |
| 122 | 高須克弥 | たかすかつや | ウィキ | [リンク](https://ja.wikipedia.org/wiki/高須克弥) | 高須クリニック院長 |
| 123 | 齋藤健人 | さいとうけんと | 推定 |  | 何者か確認できなかった |
| 124 | 齋藤勝久 | さいとうかつひさ | 推定 |  | フリーのプロ。読みは確認できなかった |

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- Chat-Ref の重複: 無し（`git log --all --grep=CHAT-1023-SKP-04`・docs/logs）
- 作業ブランチ: ローカル・リモートの work/1023-skp（0ee9af3f0）は origin/cloudflare の祖先（マージ済み）。ローカルにあり祖先なので `git merge --ff-only origin/cloudflare` で d8536d379 へ進めた
- 0章: 指示欄の末尾は指示文の最後の行と一致
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）: 欠け無し
- 表の確かめ: 124行。名前は CHAT-1023-SKP-02 のログの表の124名と一致（差 0）。確度の内訳は確実 29・ウィキ 54・推定 41（指示文の前提と同じ）
- 同じ論点の issue: 検索（クローズ済みを含む）で、最強戦の名前かなだけを扱う issue は無し。近いのは #397「「連盟プロ以外」の全員分のかな・所属団体・X を埋める」（open、/live 向けに「連盟プロ以外」の全員〈2026-09-19 で723名〉の名前かな・所属団体・X ID・X画像URL）。範囲が広く、中身（全員を埋める）は今回の決定（124名を別の issue にする・空欄があっても公開を止めない・出典で確かめてから入れる）と食い違わないので、止まらずに決定どおり新しい issue を起票し、#628 の本文から #397 にリンクした（重ねて入力しないよう。#397 のコメントに残る小川稜太・上平茉実の値の確かめも本文に書いた）
- #627 に他セッションの着手中コメントは無し
- 起票: #628「最強戦: 連盟外の出場者124名の「名前かな」を入れる」。本文は目的・決定・進め方・読みの案の説明と表（指示文の表をそのまま。起票後に本文を読み直し、表が一字一句含まれていることを確かめた）。ラベルは「対象: saikyo」と「分野: データ」（前提の見込みは「整理・保守」だったが、シートの値を埋める作業で、同じ種類の #397 が「分野: データ」なので、こちらにそろえた）
- #626 から #628 へリンクするコメント、#627 に「名前かなの入力は #628 に移した・空欄が残っても公開は止めない」のコメント。#627 の本文のチェックリストに名前かなの項目は無かった（項目は CHAT-1023-SKP-03 のコメントにだけあった）ので、本文は変えていない
- docs/decisions/saikyo.md に「2026-10-23（CHAT-1023-SKP-04）」を足した（SKP-03 の決定「入力は公開までに行う」を置き換えることを書いた）。シートは読み書きしていない

## 報告

- 状態: 完了
- ブランチ: work/1023-skp（cloudflare にマージ済み）
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1023-SKP-04.md
- 比較URL: https://github.com/retroeater/mj/compare/d8536d379...work/1023-skp
- 確認用URL: なし
- マージ: 済（docs/logs・docs/decisions のみ）
- issue: #628（起票）・#627（コメント）・#626（コメント）
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 0dd9194b）: https://github.com/retroeater/mj-logs/tree/main/guide/0dd9194b

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/0dd9194b/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/d8536d37.md
