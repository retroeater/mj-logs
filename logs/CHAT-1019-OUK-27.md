# CHAT-1019-OUK-27

- 着手日時: 2026-10-19
- 対象issue: なし（振り返りの申送り。起票する issue は報告に書く）
- ブランチ: work/1019-ouk27（origin/cloudflare から作成）
- 着手時HEAD: 2da08c0fa

## 指示

【Claude作成】Claude Code 向け指示：OUK 後半（OUK-18〜26）の振り返りの申送り（途中で終わる選手の多い表のテスト・比較の文書のテストの強化・末尾追記の衝突の既定・後始末の文書の検索・撮り方の測り・文書の手直し・内部名の issue）
Chat-Ref: CHAT-1019-OUK-27
マージ: 判断待ちで止まる（容量制限のある文書を変えるため、足した行の全文をログに貼り、平野さんが読み比べてから別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk27 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-ouk27 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-19 の OUK 後半（告知動画・順位変動の PO・#582 の後始末、CHAT-1019-OUK-18〜26）の振り返りと Fable 5.1 のレビューの知見のうち、機械的に確かめられる手順に落とせるものを書き残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-19、平野さんが「申送り」と指示）
- 振り返りの知見を、docs/notes/chat-routines.md「申送り」の手順で残す

### 知見 → 選んだ層 → 理由（仮置き。実物に合わせて変えてよい）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| A. 女流桜花 A のプレーオフ（上位8人だけが最後の節を打つ）を、開発中（OUK-08〜16）に誰も拾えず、告知動画で初めて分かった。生成は「途中で終わった選手の一覧」を出していた（docs/notes/houou-race.md）が、OUK-08 の報告には載らなかった | 計算的センサー（テスト） | 表ごとに「途中で終わった選手」の人数を数え、3人以上の表がすべて説明済み（PO として扱った表、または理由つきの許す一覧にある表）であることをテストで確かめる。新しい大会やデータで同じ形が出るとテストが落ちる |
| B. 3大会の比較（`league-pages-compare.md`）は OUK-17 で「大会の設定の名前が表の列にある」テストを入れたが、`Contest.race_po` を足しても比較の文書に PO の行が無いまま通った | 計算的センサー（テストの強化） | 「`Contest` の項目名（dataclass の field）がすべて比較の文書に出てくる」ことも確かめる。項目を足して文書を直し忘れると落ちる |
| C. OUK-22 が、長く続く作業ブランチと cloudflare の両方が `docs/decisions/houou.md` の末尾に追記して衝突し、止まった。末尾への追記どうしは両方を残せば必ず両立する | 推論的センサー（受け手の既定の解き方）＋ガイド文1行 | `docs/notes/branch-operations.md` の衝突の規則に、`docs/decisions/*.md` と `docs/logs/` の末尾への追記どうしの衝突だけは、指示文に無くても両方の節を残して日付・番号の順に並べて解く、を足す（コード・生成物・ほかの文書は今のまま止まる）。チャット側には「長く続く作業ブランチの指示には、末尾追記の衝突の解き方を最初から書く」を1行 |
| D. OUK-25（後始末）で直す文書を指示文で名指しした結果、名指ししなかった `houou-top.md`・`jwrc-top.md` と `ouka-top.md` の別の行に「旧ページのファイルと生成は残す」が残った | ガイド文（チャット側）1行 | 指示文の書き方なのでテストにできない。「後始末・削除の指示では、直す文書を名指しせず、issue 番号と消すファイル名で docs 全体を検索させ、出た所を全部直させる」 |
| E. 告知動画の最終結果を「表を送って16位まで映す」に変えたら、画面の上端に貼り付く節の番号の段の下に1位が隠れた（OUK-21）。撮ってから分かった | ガイド文（`docs/notes/page-announcement.md`）1行 | 撮り方の手順なので受け手が守る。「止める位置・送る量を変えるときは、先に貼り付く要素と画面に入る行数を測ってから決める」 |

書かないもの（心得で、機械的に確かめられないため）: 案を勧める前にログの画面の作りを読むこと。

## 手順
1. A のテスト: 順位変動の生成（`generate_houou_pages.py`・`scripts/lib/houou_race.py`）が出す「途中で終わった選手」を表ごとに数え、3人以上の表を、PO として扱った表（`race_po` で `po` が付く表）と、テストに書く許す一覧（表のキーと理由の1行。今の鳳凰戦・女流桜花・WRC で当たる表があれば全部書く）に分ける。どちらにも入らない表があれば落ちるテストにする。今の cloudflare で通ること、試しに許す一覧から1つ外すと落ちることを確かめる（確かめた後は戻す）。閾値の3人は仮置き（今のデータで当たる表の人数の分布をログに書き、合わなければ変えてよい）
2. B のテスト: `scripts/tests/test_league_pages_compare.py` に、`Contest` の field 名（`dataclasses.fields`）がすべて `docs/notes/league-pages-compare.md` に出てくることの確かめを足す。足りない項目（少なくとも `race_po`）があれば、比較の文書に行を足して通す（女流桜花 A の PO は、houou/・jwrc/ には無い意図した差）
3. C・D・E のガイド文を足す（C は `docs/notes/branch-operations.md` と `docs/notes/chat-side-operations.md`、D は `docs/notes/chat-side-operations.md`、E は `docs/notes/page-announcement.md`。置き場所は容量と既存の節に合わせて変えてよい）。追記先の前後のサイズと、容量の上限・警告域との差をログに書く。上限を超えるなら書かずに止まる
4. 文書の手直し（D の実例）: `#582`・`ouka_results`・`houou_results`・`wrc_results`・`league_ranking.js`・`leagues.js` で docs 全体（`docs/logs/`・`docs/gsc/` などの記録は除く）を検索し、「ファイルと生成は残す」など今の事実（OUK-25 で削除済み）と合わない記述をすべて直す。少なくとも `docs/notes/ouka-top.md` の冒頭の段落、`docs/notes/houou-top.md` の「旧4ページのファイルと生成は公開の後もしばらく残す」の行、`docs/notes/jwrc-top.md` の冒頭の段落。直した所を一覧でログに書く
5. issue: houou/ の内部名（`houou_ask.*`・`.mj-houou-*` のクラス・`houou_search` などの id・`generate_houou_pages.py` の名前など、ouka/・jwrc/ と共有なのに鳳凰戦の名前が付いているもの）を大会に依らない名前にそろえる issue を起票する（2026-10-17 の決定「公開後に別 issue でそろえる」、docs/decisions/houou.md の OUK の節）。先に同じ趣旨の issue が無いかを確かめ、あればそこにコメントする。本文に、今の内部名の一覧（grep の結果）と、#582 で `lib/houou_race.py`・`lib/houou_leagues.py` に移った件を書く。末尾に `Chat-Ref: CHAT-1019-OUK-27`
6. `python3 -m unittest discover -s scripts/tests` を回す。足した行・テストの全文をログの `## 経過` に貼る

## 止まる条件
- 追記先が容量の上限を超える
- A のテストで、PO でも許す一覧でも説明できない表が今の cloudflare で見つかった（その表と人数を書いて止まる。直さない）
- cloudflare への push はしない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、足した行の要約、追記先の前後のサイズ、A の閾値と許す一覧、上の表から変えたこと（あれば）、起票した issue の番号を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-27.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-27 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし（`git log --all --grep`・`docs/logs/`）。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている。ガイド文に規則を足す指示に「知見 → 選んだ層 → 理由」の表がある
- work/1019-ouk27 を origin/cloudflare（2da08c0fa）から作った
- 未マージの work/ ブランチ: work/1019-exm が `docs/notes/static-generation.md` の別の箇所（257・269行の後への追記）を変えている。ほかの7本と、この作業で変えるファイルの重なりは無い。この作業のコミットの後、各ブランチとの `git merge-tree` の衝突は、origin/cloudflare との衝突から増えていない

### 1. A のテスト（`scripts/tests/test_race_stopped.py`、新規）

- コミット済みの `<大会>/race/<期>.json` を読み、表ごとに「最後の節まで打っていない選手」（`end < rounds`）を数える。3人以上の表は、`po` が付く（プレーオフとして扱った）か、`ALLOWED` の（出力先, リーグ）にあるかのどちらかでなければ落ちる。当たらなくなった `ALLOWED` の行も落ちる
- 今のデータの分布（表の数、打っていない人数ごと）:
  - houou/: 0人 442・1人 3
  - ouka/: 0人 60・1人 1（ほかに po の付いた A の表が20。PO の表は出ない選手も最後の節まで順位に残すので0〜1人）
  - jwrc/: 4〜15人（予選の19期すべて。途中敗退）
  - wrcr/: 2〜8人（予選の8期。2人の1期を除いて3人以上）
- 閾値は3人のまま（houou/・ouka/ は PO を除けば最大1人、予選は途中敗退で毎期3人以上になるため）
- 指示の表からの変更: 許す一覧を表ごとではなく（出力先, リーグ）ごとにした（`("jwrc", "予選")`・`("wrcr", "予選")`）。予選は途中敗退で毎期同じ形になり、表ごとに書くと期が進むたびにテストが落ちるため
- 確かめ:
  - 今の cloudflare で通る
  - `("jwrc", "予選")` を外すと jwrc/ の19表で落ち、戻すと通る
  - PO を入れる前の ouka/race/（540e3dd96 の親の版）で数えると、A の20表（7〜19人、po なし）が説明されず落ちる。今回の見落としを拾えたことになる
- wrcr/ は指示の後に cloudflare に入っていたので対象に含めた

```python
"""順位変動(<大会>/race/<期>.json)の表ごとに「最後の節まで打っていない選手」を数え、多い表がすべて説明済みかのテスト。
説明済みは、プレーオフとして扱った表(po が付く。Contest.race_po)か、下の ALLOWED にある大会・リーグ。
女流桜花 A のプレーオフ(上位だけが最後の節を打つ)を開発中に拾えなかったため(CHAT-1019-OUK-27 の申送り)。
コミット済みの JSON を読むだけで、ネットワークには出ない。

    python3 -m unittest discover -s scripts/tests
"""
import json
import pathlib
import sys
import unittest

sys.path.insert(0, str(pathlib.Path(__file__).resolve().parent.parent))
import generate_houou_pages as pages  # noqa: E402

# 最後の節まで打っていない選手がこの人数以上の表を調べる(2026-10-19 の分布: houou/・ouka/ は PO の表を除くと0〜1人、
# jwrc/・wrcr/ の予選は2〜15人)
STOPPED_MIN = 3
# (出力先, リーグ): 途中で終わる選手が多いことの理由。表ごとでなくリーグごとにするのは、毎期同じ形で出るため
ALLOWED = {
    ("jwrc", "予選"): "予選は途中敗退がある(第n節まで。docs/notes/jwrc-top.md)",
    ("wrcr", "予選"): "jwrc/ と同じ予選の形(docs/notes/wrcr-top.md)",
}


def contests():
    """モジュールにある大会の設定。CONTEST は今の大会を指す別名なので出力先で重複を除く"""
    return {v.dir: v for v in vars(pages).values() if isinstance(v, pages.Contest)}.values()


def stopped_tables(contest):
    """(表の名前, 打っていない人数, po か) の一覧"""
    out = []
    for path in sorted((pages.REPO_ROOT / contest.dir / "race").glob("*.json")):
        for unit in json.loads(path.read_text(encoding="utf-8")):
            n = sum(1 for p in unit["players"] if p["end"] < unit["rounds"])
            name = f"{contest.dir}/race/{path.stem} {unit['league']}{unit.get('group') or ''}"
            out.append((name, unit["league"], n, bool(unit.get("po"))))
    return out


def unexplained(contest, allowed):
    return [(name, n) for name, league, n, po in stopped_tables(contest)
            if n >= STOPPED_MIN and not po and (contest.dir, league) not in allowed]


class RaceStoppedTest(unittest.TestCase):
    def test_tables_with_many_stopped_players_are_explained(self):
        for contest in contests():
            with self.subTest(contest=contest.dir):
                self.assertTrue(stopped_tables(contest), f"{contest.dir}/race/ の JSON が無い")
                self.assertEqual(unexplained(contest, ALLOWED), [],
                                 "最後の節まで打っていない選手の多い表がある。プレーオフなら Contest.race_po、"
                                 "大会の形なら ALLOWED に理由を足す(docs/notes/houou-race.md)")

    def test_allowed_entries_are_used(self):
        used = {(c.dir, league) for c in contests() for _, league, n, po in stopped_tables(c) if n >= STOPPED_MIN and not po}
        self.assertEqual(sorted(set(ALLOWED) - used), [], "当たらなくなった ALLOWED の行は消す")


if __name__ == "__main__":
    unittest.main()
```

### 2. B のテスト（`scripts/tests/test_league_pages_compare.py`）と比較の文書

- `Contest` の項目名（`dataclasses.fields`）がすべて `docs/notes/league-pages-compare.md` にバッククォート付きで出てくることを確かめるテストを足した。足す前の文書では24項目のうち16項目が無かった（`race_po` を含む）。足す前の文書で落ちることを確かめた
- 文書に「大会の設定（`Contest`）の項目」の節（24項目の4大会の値と、上の表のどの行か）と、意図した差の表に順位変動「最後の節」の行（ouka/ の A はプレーオフ）を足した（7,633 → 10,535 bytes。上限なし）

`docs/notes/league-pages-compare.md`:

```
| 順位変動 | 最後の節 | 全員が打つ | A は上位だけのプレーオフ（出ない選手も順位に残し、節のラベルは「PO」） | 全員が打つ（途中敗退は「第n節まで」） | jwrc/ と同じ | `Contest.race_po`・houou-race.md「例外（女流桜花の A）」（2026-10-19） |
## 大会の設定（`Contest`）の項目

`generate_houou_pages.py` の `Contest` の項目ごとの4大会の値（houou/・ouka/・jwrc/・wrcr/ の順）。項目を足したらここに行を足す（項目の名前がこの文書に無いと `scripts/tests/test_league_pages_compare.py` が落ちる）。差の意図は上の表とその行の「よりどころ」。

| 項目 | 値 | 上の表の行 |
|---|---|---|
| `sheet` | 鳳凰・桜花・JWRC・WRCR | — |
| `name` | 鳳凰戦・女流桜花・WRCリーグ・WRC-Rリーグ | — |
| `dir` | houou・ouka・jwrc・wrcr | — |
| `top` | 鳳凰位・桜花・なし・なし | 共通「リーグ」 |
| `leagues` | A1〜E3・A〜C3・予選・予選 | 共通「リーグ」 |
| `halves` | あり・なし・なし・なし | 共通「期の表記」 |
| `span_leagues` | 鳳凰位・A1・A2／全リーグ／予選／予選 | 共通「期の表記」 |
| `rounds` | 13・8・5・5 | — |
| `rows` | 14,000〜20,000・1,000〜4,000・1,000〜4,000・400〜2,000 | —（シートの読み違いの検知） |
| `ampai` | 鳳凰戦・女流桜花・なし・なし | 個人成績「進行中の期の行のリンク」 |
| `og_image` | `img/ogp/<dir>/index-white.png` | 共通「OGP 画像」 |
| `ask_placeholder` | 大会ごとの例 | トップ「質問欄の例」 |
| `noindex` | なし・なし・なし・あり | 共通「公開・noindex」 |
| `leagues_moved` | あり・あり・なし・なし | 共通「公開・noindex」（`leagues/` の有無） |
| `race_po` | なし・あり・なし・なし | 順位変動「最後の節」 |
| `skip_back_half` | B1 の昇級・なし・なし・なし | —（名前の面のチップ、#584） |
| `divisions` | 9部門・8部門・9部門（通過）・9部門（通過） | ランキング「部門」 |
| `playoff` | あり・あり・なし・なし | 共通「入替戦の印・帯」 |
| `rank_cut` | 100・110／50・55／50・55／50・55 | ランキング「表示の上限」 |
| `script` | なし（JS の既定）・大会ごと | 共通「大会の印」 |
| `no_leagues` | なし・なし・あり・あり | 共通「リーグ」 |
| `final_titles` | なし・なし・WRCリーグ・WRC-Rリーグ | 個人成績「名前の面」「予選の行が無い決勝進出者」 |
| `result_urls` | なし・なし・公式の成績表・公式の成績表 | 個人成績「進行中の期の行のリンク」 |
| `result_index` | なし・なし・大会のページ・大会のページ | 個人成績「進行中の期の行のリンク」 |

```

`scripts/tests/test_league_pages_compare.py`:

```
すべてあるかのテスト。大会を足すと落ちるので、比較の文書の更新を忘れない(2026-10-19 の申送り)。
Contest の項目の名前がすべて文書に出てくることも確かめる(項目を足して文書を直し忘れると落ちる。CHAT-1019-OUK-27)。ネットワークには出ない。
import dataclasses
    def test_every_contest_field_is_in_doc(self):
        text = COMPARE_DOC.read_text(encoding="utf-8")
        missing = [f.name for f in dataclasses.fields(pages.Contest) if f"`{f.name}`" not in text]
        self.assertEqual(missing, [], "Contest に項目を足したら、docs/notes/league-pages-compare.md の「大会の設定（`Contest`）の項目」に行を足す")

```

### 3. C・D・E のガイド文

- 置き場所（指示の表から変えた所）:
  - C の受け手側は `docs/notes/branch-operations.md` に置いた
  - C のチャット側と D は、`chat-side-operations.md` ではなく `docs/notes/chat-routines.md`「指示の種類ごとの確かめ」に置いた。どちらも場面を限った手順で、chat-side-operations.md は「場面限定の手順は表の文書に置く」「純増したら同じ量を移す」の規則があるため
  - CLAUDE.md「ブランチ運用」の衝突の行は「それ以外が衝突したら止まる」と要約していて、branch-operations.md だけを変えると食い違う。そのため例外を1句足した
- 前後のサイズ（警告域・上限は CLAUDE.md・handover.md・chat-side-operations.md が 26,624・28,672、instruction-template.md が 14,336・16,384。ほかは上限なし）:
  - CLAUDE.md 24,786 → 24,883（警告域まで 1,741）
  - docs/notes/branch-operations.md 21,783 → 22,351（上限なし）
  - docs/notes/chat-routines.md 15,831 → 16,761（上限なし）
  - docs/notes/page-announcement.md 8,163 → 8,496（上限なし）
  - handover.md 22,811・chat-side-operations.md 22,155・instruction-template.md 14,325 は変えていない
- 足した行の全文:

`CLAUDE.md`:

```
  `docs/decisions/*.md`・`docs/logs/` の末尾への追記どうしは両方を残して解く。それ以外が衝突したら止まる。指示文が解き方を書いている衝突はそのとおりに解き、該当箇所をログに引用する）
```

`docs/notes/branch-operations.md`:

```
  - **`docs/decisions/*.md` と `docs/logs/` の、両側が末尾に節を足しただけの衝突は、指示文に無くても両方の節を残し、見出しの日付・Chat-Ref の番号の順に並べて解く**（節の中身は変えない。解いた後に衝突の印が0であること〈`git diff --check`・`<<<<<<<` の検索〉と、並べた順をログに書く。末尾への追記どうしは必ず両立するため。CHAT-1019-OUK-22 はこの衝突で止まった）。同じ節の中身を両側が変えた衝突は、下のとおり止まる
```

`docs/notes/chat-routines.md`:

```
- 長く続く作業ブランチ（cloudflare を何度も取り込む）の指示: `docs/decisions/*.md`・`docs/logs/` の末尾の追記どうしの衝突は受け手が既定で解く（`docs/notes/branch-operations.md`「生成物を含む作業ブランチを取り込む・マージするとき」）。ほかに衝突しそうな文書があれば、解き方（どちらを残し、どの順に並べるか）を最初の指示に書く（書かないと止まり、解き方だけの指示が1本増える。OUK-22）
- 後始末・削除の指示: 直す文書を名指しせず、issue 番号と消すファイル名・関数名で docs 全体（`docs/logs/`・`docs/gsc/` などの記録を除く）を検索させ、出た所を全部直させる（名指しすると、名指ししなかった文書に「残す」などの古い記述が残る。#582 の後始末で `houou-top.md`・`jwrc-top.md` に残った）
```

`docs/notes/page-announcement.md`:

```
- 止める位置・送る量を変えるときは、撮る前に、画面の上端に貼り付く要素（節の番号の段など）の高さと、画面に入る行数を測ってから決める（表を送って16位まで映そうとしたら、貼り付く段の下に1位が隠れた。撮ってから分かった、OUK-21）
```

### 4. 文書の手直し（D の実例）

`#582`・`ouka_results`・`houou_results`・`wrc_results`・`league_ranking.js`・`leagues.js` と「ファイルと生成は残す」で docs を検索した。`docs/logs/`・`docs/gsc/`・`docs/decisions/`・`handover-archive-2026.md` は記録として除いた。今の事実と合わない記述を直した:

| ファイル | 直した所 |
|---|---|
| `docs/notes/ouka-top.md` | 冒頭の段落「旧3ページは 301 し、ファイルと生成は残す（後始末は #582）」→「301 する（ファイルと生成は #582 で消した）」 |
| `docs/notes/jwrc-top.md` | 冒頭の段落の同じ記述 |
| `docs/notes/houou-top.md` | 「旧4ページのファイルと生成は公開の後もしばらく残す」→ #582 で消し、部品を `scripts/lib/` へ移したこと |
| `docs/notes/static-generation.md` | ページの一覧の houou/・ouka/・jwrc/ の3行の「ファイルと生成は残す」→「#582 で消した」 |
| `docs/notes/houou-race.md` | 「houou_results も…どちらも変えていない」→ 旧ページで、#582 で消したこと |
| `docs/new-site-design.md` | `league_ranking.js` を今あるものとして書いた2か所に、#582 で消し、集計は `scripts/lib/ranking.py` に移植済みの注記 |
| `docs/notes/cloudflare.md` | 入力の書き方の例の URL `ryoei.pro/houou_results.html`（今は 301）→ `ryoei.pro/jpml_pros.html` |

直さなかったもの（記録・測った値・転送の一覧）: `docs/lighthouse-baseline.md`・`docs/review-followup-instructions.md`・`docs/astro-migration-study.md`・`docs/notes/site-findings.md`（2026-09 の測定）・`docs/notes/cloudflare.md` の測った値の表・`docs/notes/redirects.md`（301 の一覧）・`docs/notes/static-generation.md` の型C の移行の記録（OUK-25 で「当時の記録」と注記済み）・`docs/notes/mj-lead.md`・`a11y-manual-check.md`（OUK-25 で直し済み）

### 5. issue

- 同じ趣旨の issue を検索した（「内部名」「houou_ask」「mj-houou」「改名」など）。当たったのは #582（closed）だけだった
- 指示の「2026-10-17 の決定『公開後に別 issue でそろえる』」は docs/decisions/houou.md に見当たらなかった。近いのは次の2つ。issue にはこの2つを出典として書いた:
  - CHAT-1017-OUK-01 の決定（JS の2本を league.js・league_race.js に改名）と、同じ指示の前提の仮置き（「`houou_ask.js`・CSS のクラス名・localStorage のキーを同じ趣旨でそろえるかも仮置き」）
  - 2026-10-16 の study の方針（内部の名前は URL と対応が分かる形にそろえる）
- #614 を起票した: https://github.com/retroeater/mj/issues/614 （ラベルは付けていない）

### 6. テスト

- `python3 -m unittest discover -s scripts/tests`: 1,436件 OK

### そのほか

- 一度 `git stash list` を含むコマンドを打ち、hook に止められた（コマンドは実行されていない。同じ内容を stash なしで打ち直した）

## 報告

- 状態: 判断待ち
- ブランチ: work/1019-ouk27
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk27/docs/logs/CHAT-1019-OUK-27.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk27
- 確認用URL: なし（テストと docs のみ。生成物・ページは変えていない）
- マージ: 未（指示の「マージ:」の行のとおり判断待ち。足した行の全文は `## 経過` の1〜4）
- issue: #614 を起票（houou/ と共有する内部の名前をそろえる）
- 判断が必要なこと:
  - 足した行の読み比べとマージの可否
    - A: 新しいテスト `test_race_stopped.py`。3人以上が途中で終わる表は、PO か許す一覧にあること
    - B: `Contest` の24項目がすべて比較の文書にあること。文書に項目の表と、順位変動「最後の節」の行を足した
    - C: branch-operations.md と CLAUDE.md に、`docs/decisions`・`docs/logs` の末尾への追記どうしの衝突は両方を残して解く、を足した。chat-routines.md に、長く続くブランチの指示には解き方を書く、を足した
    - D: chat-routines.md に、後始末の指示では docs 全体を検索させる、を足した
    - E: page-announcement.md に、止める位置を変える前に貼り付く要素と行数を測る、を足した
    - 文書の手直し: 7ファイル
  - 追記先の前後のサイズ: CLAUDE.md 24,786 → 24,883（警告域まで 1,741）、branch-operations.md 21,783 → 22,351、chat-routines.md 15,831 → 16,761、page-announcement.md 8,163 → 8,496、league-pages-compare.md 7,633 → 10,535。上限のある残りの3文書は変えていない
  - A の閾値と許す一覧: 3人（仮置きのまま。houou/・ouka/ は PO を除いて最大1人）。許す一覧は (jwrc, 予選)・(wrcr, 予選)（途中敗退）
  - 指示の表から変えたこと:
    - A の許す一覧を、表ごとではなく大会・リーグごとにした（予選は毎期同じ形のため）
    - C のチャット側と D の置き場所を、chat-side-operations.md から chat-routines.md「指示の種類ごとの確かめ」に変えた（場面を限った手順のため。上限のある文書は増えていない）
    - C の受け手側に合わせて、CLAUDE.md「ブランチ運用」の衝突の要約にも1句足した
  - 指示の「2026-10-17 の決定『公開後に別 issue でそろえる』」は decisions に見当たらなかった。#614 には、実際にある出典（CHAT-1017-OUK-01 の仮置きと 2026-10-16 の study の方針）を書いた
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj d0e2538d）: https://github.com/retroeater/mj-logs/tree/main/guide/d0e2538d

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/d0e2538d/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/58fa24ff.md
