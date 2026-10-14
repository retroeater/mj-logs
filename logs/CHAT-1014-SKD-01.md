# CHAT-1014-SKD-01

- 着手日時: 2026-10-14
- 対象issue: #296
- ブランチ: work/1014-skd
- 着手時HEAD: 3f0d362d9

## 指示

【Claude作成】Claude Code 向け指示：最強戦（saikyo/）に新サイトのデザインの基本の形を当てはめる前の調べ — 識別子・issue・並行作業の確かめと、今の見た目の棚卸し（ログのみ） Chat-Ref: CHAT-1014-SKD-01 マージ: ドキュメントのみ（ログ）なので完了報告のうえ cloudflare へ入れてよい（下の「止まる条件」に当たれば止まる） 貼る時機: いつでも（CHAT-1014-DSN-12 は完了・マージ済み〈ab6bf27 で報告〉） 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-skd の作成と push と、cloudflare へのマージの push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。 作業ブランチ: クラウドセッションで実行する。work/1014-skd を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-skd origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。このセッションが新しく始めたものかをログの経過に書く。

目的
DSN（DSN-01〜12）で決めた新サイトのデザインの基本の形を、houou/ に続いて最強戦 saikyo/ に当てはめる（#296 のコメント〈DSN-12〉の残り2）。この指示では、当てはめの grill と見本の前に、重なりの確かめと今の見た目の棚卸しだけを行う。コード・ページ・issue の本文は変えない。
決定（2026-10-14、平野さん）

* 最強戦の作業は新しい Chat-Ref 系列で進める（DSN-12 の決定。系列の接頭辞は SKD を使う見込みで、手順1で確かめる）

前提（チャット側。平野さんの決定ではない）

* SKD は mj-logs の `chat-ids/` の最新の一覧（HISTORY の最後の行の版）に無い（2026-10-14 11:50 JST にチャット側で確認。近いものに SK・SKL・SKS がある）。本命の確かめは手順1
* 決定の正: `docs/decisions/design-renewal.md`（基本の形、Q1〜Q29）・`docs/notes/design.md`（「2. 色」の「新サイトの色」、「3. 文字・寸法」の「新サイトの文字の4段」「新サイトの面」「部品の状態ごとの高さ」、「4. 部品」の「`hidden` で消す要素」「最強戦の見た目」「最強戦の対局のまとまり」「固定バー（絞り込み）」、「2. 色」の「最強戦の帯」「順位の金」）・`docs/decisions/saikyo.md`・`docs/notes/saikyo-page-design.md`（「3. ページ構成」「4. 年度ページの表示」「5. アクセシビリティ」）
* 同じ目的に近い issue の候補（チャット側が REST で題名だけを見たもの。本文・コメントは未確認）: #296（親、DSN-12 のコメント issuecomment-6159740650 に残り3件）、#528（デザインの不足・不整合の洗い出し、Open）、#270（デザイントークンの集約、Open）、#417（共通ナビの position、Open）、#412（/live・title/・saikyo/ の UI の平仄、Closed）、#383（年度ページの content-visibility、Open）、#425（最強戦の個別ページ、Open）、#549（#425 の段2の公開、Open）。最強戦の見た目を面の形に寄せる専用の issue は見当たらなかった（要確認）
* 未マージの work/ ブランチ（2026-10-14 11:50 JST にチャット側が shallow fetch で見た範囲）: work/1009-swp-526・1011-swp-hnd・1011-swp-nav・1014-gds・1014-wfl。saikyo 系のファイルに触れていないように見えたが、shallow のため確かでない（要確認。手順1で確かめる）
* #425（ABM）の最後の作業は work/1013-abm-handover（振り返りの申送り）と handover にある（要確認）。ABM のチャットが saikyo/ の生成スクリプト・CSS を変える予定があるかは、ABM の最新のログと #425 の最新のコメントから読む（推測で書かない）
* 未公開の `saikyo/<年度>/`（#425、noindex・未リンク）の卓のページは /live の個別ページと同じ作り（`style.css` の「卓のページ…は /live の個別ページと同じ作り(CHAT-1012-ABM-09)」、濃色）で、`.mj-live-players` など live/ と共有の部品を使っている（チャット側がローカルの画面と CSS で見た。要確認）
* 当てはめで分かれ目になりそうなこと（チャット側の案。grill で平野さんが決める。この指示では決めない）: 写真カードの形を残すか、対局名の帯 `#3c4043` と卓の帯 `#e8eaed` を「意味のある色」とみなすか、共通ナビを saikyo/ にも白地にするか（2026-10-13 Q19 の次）、絞り込みの固定バー（不透明 `#131316`）、共有ボタン・順位の金・三角の扱い、未公開の `saikyo/<年度>/` に同時に当てはめるか

手順

1. 重なりの確かめ（当たれば止まる。下の「止まる条件」）:
   * Chat-Ref の識別子 SKD が未使用であること（CLAUDE.md「Chat-Ref」節）
   * 同じ目的（最強戦の見た目を面の形に寄せる・新サイトのデザインを最強戦に当てはめる・共通ナビを houou/ 以外に広げる）の issue を、クローズ済みとコメントを含めて検索する。検索語に「最強戦」「saikyo」「デザイン」「見た目」「面」「影」「Material」「共通ナビ」「白地」を入れる。上の候補の issue は本文と全コメントを読み、最強戦の見た目に関わる記述・決定・着手中のコメントを `## 経過` に引用する。この作業を新しい issue で扱うか #296 のコメントで扱うかの判断材料（同じ目的の issue があるか、#528・#270 がこの作業を含むか）をまとめる（起票はしない）
   * `git branch -r --no-merged origin/cloudflare` の各ブランチ（十分な深さで fetch する）について、cloudflare との差に `style.css`（最強戦の節と、共有の部品の節: 共有ボタン・パンくず・`.mj-live-*`・固定バー）・`assets/saikyo*.js`・`assets/navbar.js`・`assets/share.js`・`scripts/generate_saikyo*.py`・`scripts/lib/share.py`・`saikyo/` があるかを見て、ブランチごとに表にする。ABM の最新のログの `## 報告` と #425 の最新のコメントを読み、saikyo/ の生成・CSS を変える予定（判断待ち・続きの指示）があるかを書く
2. 今の見た目の棚卸し（読むだけ。ページは変えない）:
   * ページの種類ごと（トップ `saikyo/index.html`、年度ページ `saikyo/<年度>.html`、未公開の年度・対局・卓のページ `saikyo/<年度>/`）に、使っている部品（共通ナビ・絞り込みの固定バー・パンくず・対局のまとまり・対局名の帯・卓の帯・写真カード・順位の三角と金・通過／優勝のラベル・共有ボタン・動画の紹介・ヒーロー）と、その値（地・角丸・影・枠・字の大きさと太さ・色）を `style.css` の行で表にする。各部品が基本の形（`docs/notes/design.md` の「新サイトの面」「新サイトの文字の4段」「新サイトの色」）とどこが違うかを1列で書く
   * 各部品のクラスが saikyo/ 以外（title/・live/・帰り道・houou/）でも使われているかを `grep` で洗い出し、変えたときにほかのページの見た目が変わる部品を印す（例: `.mj-live-players`・共有ボタン・パンくず・`--mj-saikyo-*` を参照するほかの節）
   * 暗い配色（`prefers-color-scheme: dark`）で、今の saikyo/ の各種類のページが何色で出るか（明るい配色のままか、濃色固定か）を headless Chrome の 390px で確かめて書く。共通ナビの白地（`.mj-nav-new`）が navbar.js でどのパスに付くかを、関数名と条件の行で書く
   * 2つの代表のページ（年度ページ1枚と未公開の対局のページ1枚。どれを選んだかを書く）で、390px の明・暗の全体の高さと、対局のまとまり1つの高さを測って書く（後の見本と比べるため）
3. ログを仕上げて、cloudflare へマージする（docs のみ。`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめてから `git push origin work/1014-skd:cloudflare`）。`#296` に着手中と完了のコメントを書く（本文・状態・ラベルは変えない。コメントの末尾に `Chat-Ref: CHAT-1014-SKD-01`）

止まる条件

* SKD がほかのセッションで使われている
* 最強戦の見た目を面の形に寄せることを目的とする issue（クローズ済みを含む）がすでにあり、着手中のコメントがある、または決定が上の前提と食い違う（見つけた issue と食い違いを報告して止まる）
* 未マージの work/ ブランチが、最強戦の節の `style.css` や `scripts/generate_saikyo*.py` を変えている、または ABM の続きの指示が saikyo/ の生成・CSS を変える予定でいる（止まらずに、ブランチ・ファイル・変えている箇所を表にして「判断が必要なこと」に書く。この指示は読むだけなので重なりで止めない）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の SKD の確かめ・issue の検索の結果と引用・ブランチの表・ABM の予定、手順2の部品の表・ほかのページと共有する部品・暗い配色の現状・ナビの切り替えの条件・高さの計測がログにある
* 変更は docs/logs（と、決定があれば docs/decisions/）のみ
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SKD-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SKD-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0. 着手前

- このログの「指示」欄の末尾は、指示文の最後の行「不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。」と一致する
- このセッションが新しく始めたもの（クラウドセッション、session_01LjQF3LYvdJM7VPNtxPNcVj。前のセッションからの再開ではない）
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）はすべてある
- クローンは浅かったため `git fetch origin` と `git fetch --unshallow origin` を行った（以下の判定はその後。`origin/cloudflare` は 3f0d362d9）
- `origin/work/1014-skd`・ローカルの `work/1014-skd` とも無かったため `git checkout -b work/1014-skd origin/cloudflare` で作った（拒否されなかった）。ログを先に push（d2a99752d）
- #296 に着手中のコメント（issuecomment-6160475819）。#296 に他セッションの着手中のコメントは無かった

### 1. 重なりの確かめ

#### 1-1. 識別子 SKD

- `git log --all -E --grep 'CHAT-[0-9]{4}-SKD-' --oneline`: 0件
- `git log --all --diff-filter=A --format= --name-only -- 'docs/logs/CHAT-*-SKD-*.md'`: 0件
- `git log --all --grep="CHAT-1014-SKD-01"`: 0件
- → SKD は未使用。止まる条件に当たらない

#### 1-2. issue の検索

検索 API（`/search/issues`）はセッションのプロキシが「sessions are bound to their configured repositories」で拒否したため、
REST の `GET /repos/retroeater/mj/issues?state=all`（563件、PR を除く）と `GET /repos/retroeater/mj/issues/comments`（2,177件）を全件取り、手元で検索した。

| 検索語 | 本文・題・コメントに含む issue の数 |
|---|---|
| 最強戦 | 83 |
| saikyo | 146 |
| デザイン | 23 |
| 見た目 | 63 |
| 面 | 173 |
| 影 | 128 |
| Material | 2 |
| 共通ナビ | 18 |
| 白地 | 6 |

「最強戦」か「saikyo」に当たり、かつ「デザイン・見た目・Material・共通ナビ・白地」のどれかに当たる issue は41件。題名で見ると、最強戦の見た目を面の形に寄せること・新サイトのデザインを最強戦に当てはめること・共通ナビを houou/ 以外に広げることを**目的とする issue は無い**。
「面の形」「新サイトの面」「基本の形」「Material 寄り」の語を含むのは #107（closed）・#518・#296 のコメントだけで、いずれも houou/ の当てはめか、残りを #296 に書いたもの。

41件の題名（closed は c）: #7・#9・#97・#101・#104・#107c・#108c・#111c・#158c・#160・#163c・#183c・#222c・#259・#296・#312c・#319c・#339c・#341c・#343c・#344c・#355c・#357・#358c・#362・#409c・#412c・#417・#425・#429・#431c・#441c・#442・#492・#514・#524c・#525c・#526c・#527・#528・#529

2026-10-12 以降の「着手中」のコメントで、最強戦の見た目・共通ナビを扱うものは無い（#425 の ABM は卓のページの中身・動画・司会実況解説、#107・#518 は houou/）。

**候補の issue の本文と全コメントから、最強戦の見た目に関わる記述の引用:**

- **#296**（open、親）: コメント issuecomment-6159740650（DSN-12）
  > 2. 最強戦（saikyo/）・title/ の Material 寄りの見た目（影・角丸 8px）を、面の形（角丸 12px・影なし・1px の区切り線）に寄せる（2026-10-12 の「いずれこちらに寄せる」）。最強戦は次に別のチャットで進める予定

  > 1. 共通ナビの新しい形（白地・下の細い線・今いる項目が紺・日本語1行）を houou/ 以外のページにも広げる（2026-10-13 に「まず houou/ だけ」と決めた。濃色の例外は帰り道・live/）

  同じコメントに「#107 は閉じ、方針から出た残り3件をここに書き残す（新しい issue は起こさない）」。ほかのコメント（LW-04・CW-19・RUN-04）は最強戦の見た目に関わらない
- **#107**（closed）: 閉じたコメント issuecomment-6159741352
  > 本文の項目のうち、カード UI は「面」（角丸 12px・影なし・1px の区切り線）に、ダークモードは「最初から両方の色を決める」に決まった。

  > 方針から出た残り3件（共通ナビの新しい形を houou/ 以外へ広げる、最強戦・title/ を面の形に寄せる、#101 のトップをこの形で作る）は親の #296 にコメントで書いた
- **#528**（open、デザインの不足・不整合）: 本文の系統 C＝`saikyo/`。最強戦に関わる行の引用
  > | カード | C・B: 写真カード 226×226px・角丸 12px・elev 影・背景 `#e9ecef`（画像の下地） | D・E: 動画カード 286〜324px 幅・角丸 8px・枠 `#3a3a3a`・影なし・地 `#1e1e1e` | … | C は Material 3 に合わせると決定（SK-09）。D は未決。混在時の指針なし |

  > | 影 | A・F: なし | B・C・E の写真カード: elev-1/3（Material 3 の値、4か所に複製）。D のカード: なし | … | 値は決定済み（SK-09）。置き場所（トークン化）は未決 |

  > | 固定バー（絞り込み） | — | B・C: `position:fixed`・不透明 `#131316`・高さ 61px（390px で 115px）、D・E: `sticky`・… | … | 見た目をそろえる方針は決定済み（SK-18）。実装が系統ごとに別 |

  > | 本文のフォントサイズ | A: 表 13px、F: 16px | B: 16px、C: 14px（`.mj-saikyo`）、D・E: 16px … | 表は決定済み（13px）。ほかは未決 |

  ほかに「灰色のパレット」（Google 系 `#202124` `#3c4043` `#e8eaed` `#f1f3f4` が C）、「本文の列」（C: 960px 左寄せ）、G1-10（写真の上の名前のコントラスト、`saikyo/2022` ほか）。コメントは HOU-05 の1件（houou/ の部品の値）だけ。着手中のコメントは無い
- **#270**（open、トークンの集約）: 本文は「見た目を変えない純粋なリファクタ」。コメント（SWP-03）
  > 角丸を何種類にするか・カードの2系統（G1-08）は本文から決まらないため、#528（デザインの不足・不整合）で決める。

  最強戦の見た目を変えることは含まない。着手中のコメントは無い
- **#417**（open、新サイト送り、共通ナビの position）: 本文の表で saikyo/（トップ・年度）は `fixed`。コメント（SWP-03）
  > 共通ナビの position が fixed（型A・title/・saikyo/）・sticky（動画系・live/）・relative（型C・B・D・手書き）の3通り。

  白地のナビ（新しい形）は扱っていない。着手中のコメントは無い
- **#412**（closed、/live・title/・saikyo/ の UI の平仄）: コメント issuecomment-5754629672（SG-03）
  > **`/live` は既にこの形だった。** `/live` は `.mj-saikyo-card` を共用したうえで、名前のラベルを `title/` と同じ見せ方に（判断 LP-11）…**最強戦だけが金の帯と左上の札を残していた**ので、最強戦を動かすと3系統がそろう

  コメント issuecomment-5756727660（SG-15）
  > **この issue は「そろえるか決める」場であって、実装の issue ではない**ため、この形にした。同じ主題で今後手を入れるときも、決定はここに、値は設計メモに書くのがよい。

  2026-09-27 の11項目の決定（パンくず・ページ上部の構成・戻る矢印など「今のまま」）は CHAT-0924-TQ-06 のコメント。2026-09-28 に閉じた
- **#383**（open、状況: 保留）: `.mj-saikyo-match` の高さの記録（2018年度・390px で中央値 1,231px）。見た目の決定は無い
- **#425**（open）: 本文の「決まったこと（2026-10-11）」4「個別ページに共有ボタンは付けず、年度ページの対局ごとの共有ボタンは対局のページへのリンクに置き換えて外す」、5「年度ページの選手名の絞り込みは外す」。見た目の決定は卓のページを /live と同じ作りにしたこと（コメント 2026-10-12、ABM-09）
- **#549**（open、状況: 待ち、#425 の段2）: 本文
  > 年度ページを `saikyo/<年度>/`（`index.html`）へ移し、今の `saikyo/<年度>.html` の生成をやめる（対局ごとの共有ボタン・`?match=` の絞り込み・選手名の絞り込みも一緒に無くなる。`assets/saikyo.js` の今の年度ページの処理と `share.js` の読み込みも外す）

  公開の時期は平野さんが決める。コメントは無い

**新しい issue で扱うか #296 のコメントで扱うかの判断材料:**

- 同じ目的の issue は無い（クローズ済みを含む）。止まる条件（同じ目的の issue があり着手中・決定が食い違う）に当たらない
- DSN-12 の決定（`docs/decisions/design-renewal.md` の 2026-10-14〈CHAT-1014-DSN-12〉）は「残り3件は新しい issue を起こさず親の #296 にコメントで書き残す」。#296 の本文は「この issue は着手対象ではない…作業は各子 issue で行う」と書いており、作業の場として #296 を使い続けると本文と食い違う
- #528 は値を決める・違いを意図として残すかを決める場で、最強戦の値（カード・影・固定バー・灰色）を行に持つが、当てはめ（実装）は範囲に無い（「現行サイトで直すかは決めた後に判断する」）。#270 は見た目を変えないリファクタで含まない
- #412 の先例（SG-15）: 写真カードをそろえた作業は専用の issue を立てず #412 のコメントと設計メモで記録した
- 時期の材料: #549（公開）で今の年度ページ `saikyo/<年度>.html` は `saikyo/<年度>/` に置き換わり、対局の帯の共有ボタン・`?match=`・選手名の絞り込みが無くなる。当てはめを #549 より前に今の年度ページにも行うと、公開で消える部品にも手を入れることになる

#### 1-3. 未マージの work/ ブランチ

`git branch -r --no-merged origin/cloudflare`（unshallow の後）: 自分の `work/1014-skd` のほかに4本。チャット側の前提の5本のうち `work/1014-gds`（7a3dac288）・`work/1014-wfl`（72c5db48e）はマージ済みで、`work/1014-gds-492` が新しく未マージ。

| ブランチ | 先頭 | cloudflare との差のファイル | style.css | assets/saikyo*.js | navbar.js | assets/share.js | scripts/generate_saikyo*.py | scripts/lib/share.py | saikyo/ |
|---|---|---|---|---|---|---|---|---|---|
| work/1009-swp-526 | 8fdb23d89（2026-10-10） | docs/logs/CHAT-1010-SWP-12.md のみ | なし | なし | なし | なし | なし | なし | なし |
| work/1011-swp-hnd | 364ae47f3（2026-10-11） | docs/logs/CHAT-1011-SWP-18.md のみ | なし | なし | なし | なし | なし | なし | なし |
| work/1011-swp-nav | f949abb05（2026-10-11） | docs/logs/CHAT-1011-SWP-14.md のみ | なし | なし | なし | なし | なし | なし | なし |
| work/1014-gds-492 | da026d7a7（2026-10-14） | docs/decisions/operations.md・docs/logs/CHAT-1014-GDS-06.md | なし | なし | なし | なし | なし | なし | なし |

（`git diff --stat origin/cloudflare...origin/<branch> -- style.css 'assets/saikyo*.js' assets/navbar.js navbar.js assets/share.js 'scripts/generate_saikyo*.py' scripts/lib/share.py saikyo/` が4本とも空。指示文の `assets/navbar.js` は実在せず、ナビはリポジトリ直下の `navbar.js`）

`origin/cloudflare` の 2026-10-12 以降のコミットで上のファイルに触れたもの: houou/ の当てはめ（DSN、2be5acc86・e01e660c9 など）、帰り道・title/ の直し、ABM-17 の司会・実況・解説（4791a154c）、SNM の登録の数え方（e426439fe）。どれもマージ済み。

**ABM の予定:** 最新のログは CHAT-1013-ABM-19（work/1013-abm-handover、マージ済み 346c65b13）。`## 報告` は「状態: 完了」「判断が必要なこと: なし（未決の3点は #425 に残した）」。
#425 の最新のコメント（2026-10-13、ABM-19）の未決は (1) ABEMA の新しい回を【1】へ入れる流れ、(2) ABEMA の回が無い卓のヒーロー（#549 の前に決める）、(3) 片方にしか無い対局・卓。
**saikyo/ の生成・CSS を変える続きの指示は見当たらない。** ただし (2) は卓のページのヒーロー（生成と CSS）に関わり、#549 の公開の作業は `generate_saikyo_pages.py`・`assets/saikyo.js`・`_redirects` を変える予定（本文の「段2の作業」）。どちらも時期は未定。
→ 止まる条件（未マージのブランチが最強戦の節・生成を変えている、ABM の続きが saikyo/ の生成・CSS を変える予定）に当たらない。#549 との順序は「判断が必要なこと」に書く。

### 2. 今の見た目の棚卸し（2026-10-14、`origin/cloudflare` 3f0d362d9）

#### 2-1. ページの種類と使っている部品

ページの HTML のクラスを数えた（代表: トップ `saikyo/index.html`、年度 `saikyo/2025.html`、未公開の年度 `saikyo/2025/index.html`・対局 `saikyo/2025/20250816.html`・卓 `saikyo/2025/20250816-s1-a.html`）。

| 部品 | トップ | 年度 `<年度>.html` | 未公開の年度 `<年度>/index.html` | 未公開の対局 | 未公開の卓 |
|---|---|---|---|---|---|
| `<body>` / `<main>` | `body.mj-saikyo-page` | 同左 | 同左 | 同左 | `<body>` に印なし、`main.mj-video-page.mj-live.mj-saikyo-sub.mj-saikyo-table-page` |
| 共通ナビ | 濃色・fixed | 濃色・fixed | 濃色・fixed | 濃色・fixed | 濃色・sticky（/live と同じ） |
| 絞り込みの固定バー | 年度プルダウン＋検索 | 年度プルダウン＋検索 | 年度プルダウンだけ | 年度プルダウンだけ | なし |
| パンくず | なし | なし | Bootstrap `.breadcrumb`（`.mj-saikyo-breadcrumb`） | 同左 | `.mj-live-breadcrumb`（ヒーローの中） |
| 対局のまとまり `.mj-saikyo-match` | 1つ（歴代最強位の枠） | 17 | 17 | 1 | なし |
| 対局名の帯 `.mj-saikyo-match-title` | 1（「歴代最強位」） | 17 | 17（リンク） | 1 | なし（見出しはヒーロー） |
| 卓の帯 `.mj-saikyo-table-title` | なし | 49 | なし | 3（リンク） | なし |
| 写真カード `.mj-saikyo-card` | 16（＋検索結果） | 196 | 27 | 12 | 9（出場者4＋司会・実況・解説） |
| 順位の三角 `.mj-saikyo-rank` と金 | なし（年度の帯 `.mj-saikyo-champion-year`） | 196 | なし（数字なしの金の三角） | 12 | 4 |
| 通過／優勝のラベル | 検索結果の `.mj-saikyo-result-badge` | なし（三角の色と alt） | `.mj-saikyo-winner-label`＋`.mj-saikyo-result-badge.is-pass`（27） | なし | なし |
| 共有ボタン `.mj-share` | なし | 17（対局の帯の右端）＋トースト | なし | なし | なし |
| 動画の紹介 | なし | なし | なし | `.mj-saikyo-video`（ABEMA のサムネイル 3） | ヒーロー＋「関連動画」`.mj-video-card`（15） |
| ヒーロー | なし | なし | なし | なし | `.mj-video-hero`・`.mj-saikyo-hero` |
| 末尾の説明文 `.mj-lead` | あり | あり | あり | あり | あり |
| 読む JS | saikyo.js・navbar.js | saikyo.js・share.js・navbar.js | saikyo.js・navbar.js | 同左 | 同左 |

#### 2-2. 部品の値と基本の形との違い

基本の形は `docs/notes/design.md` の「新サイトの面」（角丸 12px・影なし・枠なし・行は 1px の区切り線・押せる行 44px 以上・左右 16px・題名は枠なし左寄せ 28px で下に 16px）、「新サイトの文字の4段」（28 太字／16／13 灰色／12 太字）、「新サイトの色」（地 `#f2f2f7`・面 `#ffffff`・字 `#1d1d1f`・灰色の字 `#555555`・区切り `#e5e5e5`・紺 `#14459b`。暗い配色あり）。行番号は 3f0d362d9 の `style.css`。

| 部品 | 行 | 地 | 角丸 | 影 | 枠 | 字（大きさ・太さ・色） | 基本の形との違い |
|---|---|---|---|---|---|---|---|
| 本文 `.mj-saikyo` | 1677 | 白（body `#fff`、ページの地＝面の区別なし） | — | — | — | 14px・本文 `#212529`・リンク `#14459b` | 地が1色（ページの地 `#f2f2f7` と面 `#fff` の2段が無い）。字 14px は4段に無い（本文 16px）。字の色 `#212529`（基本 `#1d1d1f`）。最大幅 960px・左寄せ（`--mj-saikyo-max-w`、1663） |
| 対局のまとまり `.mj-saikyo-match` | 1712 | 透明（白） | 12px | なし | 1px `#a8adb3` | — | 枠あり（基本は枠なしの面）。中の帯・カードに影があり、「面に行を区切り線」の形でない。まとまりの間 24px・内側 8px |
| 対局名の帯 `.mj-saikyo-match-title` | 1729 | `#3c4043` | 12px | elev-2 | なし | 1.1rem（17.6px）・700・白・中央揃え | 濃い帯＋影＋中央揃え。基本は題名を枠なし・左寄せ・28px 太字（節の見出しの段は4段に無い） |
| 卓の帯 `.mj-saikyo-table-title` | 1914 | `#e8eaed` | 8px | elev-1 | なし | 0.9rem（14.4px）・700・`#202124`・中央揃え | 淡い帯＋影・角丸 8px（基本は 12px のみ）・14.4px は4段に無い |
| 写真カード `.mj-saikyo-card` | 1947 | `#e9ecef`（画像の下地） | 12px | elev-1、ホバーで elev-3＋`translateY(-2px) scale(1.03)`（2059） | なし | — | 影と持ち上がる動き（基本は影なし。design.md「動き」は `transition` を付けないが、ここは `transition: box-shadow .2s, transform .2s`、2056）。写真の無い選手は代替アバター画像（houou/ は頭の1字の灰色の丸） |
| 名前 `.mj-saikyo-name` | 1976 | 下端から透明のグラデーション `rgba(0,0,0,.55)` | — | 字に `text-shadow` | — | 13〜22px（13cqi）・700・白 | 写真の上に字を重ねる形（houou/ の表は名前を面の行に置く）。G1-10（コントラスト）が #528 に未決で残る |
| 順位の三角 `.mj-saikyo-rank` | 2007 | `#3c4043`（通過・優勝は金 `#f2c230`＋`#2b2100`、2029） | — | — | — | max(12px,18cqi)・700・白 | 基本の形に相当する部品なし。金は「意味のある色」の候補（決定は design.md「順位の金」） |
| フォーカスリング（カード） | 2066 | — | — | 紺 3px の外輪＋elev-3 | 白 3px（offset -3） | — | 基本は紺 2px（共通ナビ（新しい形）のフォーカス枠） |
| 歴代最強位の年度の帯 `.mj-saikyo-champion-year` | 2213 | `rgba(0,0,0,.75)` | — | — | — | 14〜20px・700・白 | title/ の大会名の帯と同じ形（#412） |
| 共通ナビ `body.mj-saikyo-page nav.navbar` | 2091 | `#212529`（`navbar-dark bg-dark`） | — | — | — | — | 基本は面の色の地＋下の 1px の線（`.mj-nav-new`、5280）。位置は fixed（#417 で未決） |
| 固定バー `body.mj-saikyo-page .mj-filterbar` | 2145 | 不透明 `#131316` | — | — | 下 1px `rgba(255,255,255,.14)` | 字 `#f5f5f5` | 白いページに濃色のバー。基本の形に固定バーの値なし。入力欄 `#121212`・枠 `#3a3a3a`（2171）、年度プルダウン 16px・700・濃色（2181） |
| パンくず（未公開の年度・対局） `.mj-saikyo-breadcrumb .breadcrumb` | 1764 | — | — | — | — | Bootstrap の既定（16px）・リンク紺 | 基本は題名の上に 13px 灰色の1行（「パンくず（新しい形）」） |
| パンくず（卓） `.mj-live-breadcrumb` | 3054 | ヒーローの上 | — | — | — | 0.85rem（13.6px）・700・白 | 濃色のヒーローの中（/live と同じ） |
| 通過／優勝のラベル `.mj-saikyo-result-badge` | 2369 | `#e8eaed`（通過・優勝は金、2379） | 4px | — | — | 0.85rem（13.6px）・700・`#202124` | 基本の「ピル」は 12px 太字。角丸 4px は基本に無い |
| 検索結果の卓のカード `.mj-saikyo-result-table` | 2338 | `#fff`（ホバー `#f1f3f4`） | 8px | elev-1（ホバー elev-3） | — | 本文 14px、対局 `#40464d` | 1件ずつ影付きのカード（基本は1枚の面に 1px の区切り線の行） |
| 開いた写真の印 | 2294 | — | — | 紺 3px の外輪＋elev-3 | — | — | 影を伴う |
| 共有ボタン `.mj-share-btn-round` | 1154（重ね方 1743） | 透明（ホバー `rgba(255,255,255,.16)`） | 50% | — | — | アイコン `var(--mj-v-fg,#fff)` | 濃い帯の上の白いアイコン。#549 の公開で年度ページから無くなる |
| 動画の紹介 `.mj-saikyo-video-thumb` | 1827 | `#e8eaed` | 8px | elev-1 | — | リンク 700・出典 0.75rem `#5f6368`（1890） | 角丸 8px・影 |
| 卓のページのヒーロー `.mj-video-hero` | 977 | ABEMA のサムネイル（濃色） | — | — | — | 白 | 濃色固定の例外（/live と同じ作り）。基本の形の外 |
| 卓のページの写真カード `.mj-live-players` | 3080 | 濃色のページ `#121212` の上 | 12px | elev-1/3（変数を複製、3081-3082） | — | — | 同上 |
| 関連動画のカード `.mj-video-card` | 1260 | `--mj-v-surface`（`#1e1e1e`） | 8px | なし | 1px `--mj-v-border` | — | 同上 |
| 末尾の説明文 `body:has(.mj-saikyo) .mj-lead` | 1671 | — | — | — | 上 0.5px `#e5e5e5` | 13px・`#555555` | 字は基本の「小 13px 灰色」と同じ。幅は本文 960px |

影の値（1680-1682）: elev-1 `0 1px 2px rgba(0,0,0,.3), 0 1px 3px 1px rgba(0,0,0,.15)`、elev-2 `0 1px 2px rgba(0,0,0,.3), 0 2px 6px 2px rgba(0,0,0,.15)`、elev-3 `0 1px 3px rgba(0,0,0,.3), 0 4px 8px 3px rgba(0,0,0,.15)`。

#### 2-3. saikyo/ 以外と共有している部品

HTML の `class="…"` を title/・live/・wayhome/・houou/・直下の *.html で、JS の参照を `assets/*.js`・`navbar.js` で `grep` した（ファイル数）。**印（★）は、変えるとほかのページの見た目が変わるもの。**

| 部品（クラス・変数） | saikyo/ 以外の HTML | JS | 印 |
|---|---|---|---|
| `.mj-saikyo-card`・`.mj-saikyo-photo`・`.mj-saikyo-name`・`.mj-saikyo-player`・`.mj-saikyo-players` | live/ 742〜743 ファイル（対局者・実況解説の写真カード） | live.js・saikyo.js | ★ live/ の写真カードがそのまま変わる（`style.css` 2999 のコメント「最強戦の写真カード…をそのまま使い」） |
| `.mj-saikyo-rank` | なし | — | 最強戦だけ |
| `.mj-saikyo-match`・`-match-title`・`-table-title`・`-champion-year`・`-result*`・`-winner-label`・`-video*`・`-breadcrumb` | なし | saikyo.js | 最強戦だけ |
| `--mj-saikyo-elev-1`・`--mj-saikyo-elev-3` | `.mj-live-players` が同じ値を自分で定義（3081-3082） | — | 変数を `.mj-saikyo` で変えても live/ は変わらない（複製のため）。写真カードの `box-shadow: var(--mj-saikyo-elev-1)` を変えると live/ も変わる（★） |
| `--mj-saikyo-overlay-font` | `.mj-live-role`（3121） | — | ★ `.mj-saikyo-card` の定義（1948）を変えると live/ の役の帯の字も変わる |
| `--mj-saikyo-max-w`・`--mj-saikyo-gutter` | `:root` に定義。title/ の節のコメント（2445・2567）は値を写したと書く（参照はしていない） | — | 最強戦だけ（title/ は値の写し） |
| `.mj-live-players .mj-saikyo-name` などの上書き（3099-3110）、`.mj-live-players.is-text-large .mj-saikyo-*`（2669-2690） | live/ | live.js `fitCardLabels()` | 最強戦の値を変えると live/ の上書きとの差が変わる |
| `.mj-filterbar`・`.mj-filterbar-icon`・`.mj-filter-input`・`.mj-filterbar-count` | title/ 384・live/ 908・wayhome/ 1（ほかに直下の表のページの `.mj-filter-input` 8） | live.js・saikyo.js・title.js | ★ 共通のクラスの値を変えると他系統も変わる。最強戦の固定バーの色・位置は `body.mj-saikyo-page .mj-filterbar`（2145）に閉じていて、そこだけなら最強戦だけ |
| `.mj-share`・`.mj-share-btn`・`.mj-share-btn-round`・`.mj-share-toast`（共有ボタン） | live/ 908 | share.js | ★ 共通部品（#409、1146-）。最強戦の重ね方（1743）だけなら最強戦だけ |
| `.breadcrumb`（Bootstrap） | title/ 383 | — | `.mj-saikyo-breadcrumb` の中だけを変えるなら最強戦だけ |
| `.mj-live-breadcrumb`・`.mj-live-players`・`.mj-live-staff`・`.mj-live-role`・`.mj-live-subheading`・`.mj-live-heading`・`.mj-live-other-head`・`.mj-live-more`・`.mj-video-hero`・`.mj-video-card`・`.mj-video-track`・`.mj-video-episodes`・`.mj-video-btn`・`.mj-video-page`（卓のページ） | live/ 742〜908・wayhome/ 19〜43 | live.js | ★ 卓のページは /live と帰り道の部品そのもの。卓のページだけを変えるには `.mj-saikyo-table-page` で閉じる必要がある |
| `.mj-lead` | title/・live/・wayhome/・houou/・直下 15 | — | ★ 共通。最強戦の幅は `body:has(.mj-saikyo) .mj-lead`（1671）に閉じている |
| `#3c4043`・`#e8eaed`・`#131316` の色 | 同じ値が 1733・1834・1918・2018・2045・2160・2273・2372（最強戦）と 2496・2786・2910（title/ の固定バー・検索結果）に別々に書かれている | — | 値の写しなので、最強戦の行を変えても title/ は変わらない（そろいは崩れる） |

#### 2-4. 暗い配色と共通ナビの切り替え

headless Chromium（`/opt/pw-browsers/chromium`、Playwright 1.56.1）、幅 390px・高さ 844px、`colorScheme` を light と dark で切り替え、`python3 -m http.server` で手元のファイルを配った（外部の画像は読まない。写真カードは `aspect-ratio: 1`、サムネイルは 16:9 で、高さは画像に依存しない）。
暗い配色の効き方の対照に `houou/index.html` も測った: light で body `#f2f2f7`・ナビ `#ffffff`、dark で body `#000000`・ナビ `#1c1c1e`（切り替えが効くことを確かめた）。

| ページ | 明るい配色 | 暗い配色 | `color-scheme` |
|---|---|---|---|
| トップ `saikyo/index.html` | body `#ffffff`・字 `#212529`・ナビ `#212529`・固定バー `#131316` | **明るい配色のまま**（同じ値） | normal |
| 年度 `saikyo/2025.html` | 同上 | **明るい配色のまま** | normal |
| 未公開の年度 `saikyo/2025/index.html` | 同上 | **明るい配色のまま** | normal |
| 未公開の対局 `saikyo/2025/20250816.html` | 同上 | **明るい配色のまま** | normal |
| 未公開の卓 `saikyo/2025/20250816-s1-a.html` | body `#121212`・字 `#f5f5f5`・ナビ `#212529`（sticky） | **濃色固定**（同じ値） | dark |

`style.css` の `prefers-color-scheme` は 5250・5292・5983 の3か所で、どれも houou/（`:root:has(.mj-houou, .mj-houou-race)`・`.navbar.mj-nav-new`・`.mj-houou-rank`）。saikyo/ に効くものは無い。

**共通ナビの白地（`.mj-nav-new`）の付く条件:** `navbar.js` の関数の外（読み込み時に1回だけ動く最上位の文）にある
`if (location.pathname.indexOf('/houou/') === 0) {`（`navbar.js:94`）。条件はパスが `/houou/` で始まること（`/houou_ranking.html` などの旧ページは当たらない）。
中で `navbar-dark`・`bg-dark` を外して `mj-nav-new` を足し（96-97）、項目名を `<br>` の前の1行にし（99）、「鳳凰戦」に `aria-current="true"` を付ける（100-102）。関数名は無い。saikyo/ のページはどれも当たらず、5種類とも `navbar navbar-expand-lg navbar-dark bg-dark` のままだった（上の計測）。

#### 2-5. 高さの計測（後の見本と比べるため）

条件は 2-4 と同じ。代表に選んだのは、年度ページ `saikyo/2025.html`（今の公開中、17対局・2025年度は全対局が終わっている）と未公開の対局のページ `saikyo/2025/20250816.html`（2025-08-16 の対局、卓3・ABEMA のサムネイル3）。
「対局のまとまり」は `.mj-saikyo-match` の `getBoundingClientRect().height`。

| ページ | 配色 | 全体の高さ（`scrollHeight`） | ナビ | 固定バー | 対局のまとまり |
|---|---|---|---|---|---|
| `saikyo/2025.html` | 明 | 21,533px | 56px・fixed | 115px（2行） | 17個。先頭 482px、中央値 1,276px、最小 482px、最大 1,673px |
| `saikyo/2025.html` | 暗 | 21,533px | 同上 | 同上 | 同上 |
| `saikyo/2025/20250816.html` | 明 | 2,339px | 56px・fixed | 61px（年度プルダウンだけ） | 1個 2,047px |
| `saikyo/2025/20250816.html` | 暗 | 2,339px | 同上 | 同上 | 同上 |

参考（同じ条件）: トップ 1,761px（歴代最強位の枠 1,467px、固定バー 115px）、未公開の年度 `saikyo/2025/index.html` 6,099px（17個、中央値 273px・最大 872px、固定バー 61px）、未公開の卓 `saikyo/2025/20250816-s1-a.html` 3,311px（`.mj-live-players` 2つ: 出場者 520px・司会実況解説 348px）。明・暗で差は0px（暗い配色が効いていないため）。

### 3. まとめ（grill の材料）

- 重なり: SKD は未使用。同じ目的の issue・未マージのブランチ・ABM の続きの予定は無い（止まる条件に当たらない）
- 変えると saikyo/ 以外も変わる部品: 写真カード（`.mj-saikyo-card` ほか、live/ が使う）・`--mj-saikyo-overlay-font`（live/ の役の帯）・共有ボタン・`.mj-filterbar` の共通の値・`.mj-lead`・卓のページの /live・帰り道の部品。最強戦の節に閉じているのは、対局のまとまり・対局名と卓の帯・順位の三角・固定バーの色（`body.mj-saikyo-page` 付き）・パンくず（`.mj-saikyo-breadcrumb`）・検索結果
- 暗い配色: 卓のページ以外は明るい配色のまま、卓のページは濃色固定。共通ナビは5種類とも濃色（`.mj-nav-new` は `/houou/` で始まるパスだけ）

### 4. マージ

`git merge origin/cloudflare`（1d39d68a1 を取り込み、衝突なし）の後、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1014-skd:cloudflare`（1d39d68a1..b8124ccb1）。変更は docs/logs/CHAT-1014-SKD-01.md と docs/decisions/design-renewal.md だけ

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-SKD-04
- ブランチ: work/1014-skd
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-SKD-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-skd
- 確認用URL: なし
- マージ: 済（b8124ccb1、ドキュメントのみ）
- issue: #296（着手中・完了のコメント）
- 判断が必要なこと:
  - 最強戦の当てはめを記録する場: #296 のコメントで続けるか、新しい issue を起こすか（DSN-12 の決定は「新しい issue を起こさず #296 にコメント」。#296 の本文は「着手対象ではない、作業は子 issue で」。#412 の先例は issue のコメントと設計メモ。`## 経過` 1-2）
  - #549（年度ページを `saikyo/<年度>/` へ移す公開）との順序と、今の年度ページ `saikyo/<年度>.html`・未公開の `saikyo/<年度>/` のどちらに当てはめるか（公開で対局の帯の共有ボタン・`?match=`・選手名の絞り込みが無くなる。`## 経過` 1-2・1-3）
  - 写真カードを変えるとき live/ も一緒に変えるか、最強戦だけに閉じるか（`## 経過` 2-3）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj a1747ce9）: https://github.com/retroeater/mj-logs/tree/main/guide/a1747ce9

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/a1747ce9/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/59e33bdf.md
