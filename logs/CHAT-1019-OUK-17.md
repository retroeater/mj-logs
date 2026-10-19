# CHAT-1019-OUK-17

- 着手日時: 2026-10-19
- 対象issue: なし（OUK 系列の申送り）
- ブランチ: work/1019-ouk17（origin/cloudflare から作成）
- 着手時HEAD: fbae687ec

## 指示

【Claude作成】Claude Code 向け指示：OUK の振り返りの申送り（3大会の比較の文書を大会の追加で必ず更新させるテスト・0章の許す差分の書き方・シートに貼る手順の書き方）
Chat-Ref: CHAT-1019-OUK-17
マージ: 判断待ちで止まる（容量制限のある文書を変えるため、足した行の全文をログに貼り、平野さんが読み比べてから別のマージの指示で cloudflare へ入れる）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1019-ouk17 の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1019-ouk17 を origin/cloudflare から作る。手順は docs/notes/cloud-sessions.md「作業ブランチの用意」。

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
2026-10-19 の OUK 系列（女流桜花 ouka/ の作り直しと公開、CHAT-1017-OUK-01〜CHAT-1019-OUK-16）の振り返りと、その Fable 5.1 のレビューで出た知見のうち、機械的に確かめられる手順に落とせるものだけを書き残す（docs/notes/chat-routines.md「申送り」）。

### 決定（2026-10-19、平野さんが「申送り」と指示）
- 振り返りの知見を、docs/notes/chat-routines.md「申送り」の手順で残す

### 知見 → 選んだ層 → 理由（chat-routines.md「申送り」の表。仮置き。実物に合わせて変えてよい）

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| A. 3大会の比較（`docs/notes/league-pages-compare.md`、CHAT-1018-OUK-13）は、大会を足す・大会ごとの設定を変えるときに更新しないと古くなる。今は読む仕組みが無い | 計算的センサー（テスト）＋ガイド文1行 | `generate_houou_pages.py` の大会の設定（`Contest` の各インスタンス）の名前が、比較の文書の表の列にすべてあるかをテストで確かめられる。新しい大会を足すと落ちるので、文書の更新を忘れない。ガイド文は `docs/new-page-checklist.md` に「大会を横展開するときは比較の文書を読み、差を表に足す」の1行だけ |
| B. 0章の確かめに「許す差分」を行で列挙すると、平野さんの決定が1つ変わるたびに指示文が無効になる（OUK-05・07 が欠番になった） | ガイド文（`docs/instruction-template.md` の0章の注記に1句） | 指示文の書き方なので、テストやフックでは確かめられない。受け手の確認手順でも扱えない（指示文を書くのはチャット側）。規則だけを1句で書く: 「比べる相手（前の TSV・ログ）を指し、想定外の行は全部書いて止まる形にする。許す行を指示文に焼き付けない」 |
| C. 平野さんにシートへ貼ってもらう手順を1文にまとめると、2か所のうち1か所だけ貼られる（OUK-10 が0章で止まった） | 推論的センサー（受け手の0章の確認）は既にある（OUK-10 はこれで止まった）。足すのはガイド文1行（`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の「シートを用意したと言われた」の近く） | 受け手の確かめは働いたので、チャット側の書き方だけを足す: 「貼る手順は1列1行で書き、どのセルから貼るかと、貼った後の件数（例: Y が49行）を添える」 |

書かないもの（心得で、機械的に確かめられないため）: データの形を最初の質問に含めること、選択肢の幅を広く取ること、横展開では houou/ との一致をおすすめの基本にすること、件数を自分の数えで断定しないこと。

### 前提（チャット側。実物に合わせて変えてよい）
- A のテスト: `scripts/tests/` に足す。`Contest` のインスタンスの一覧の取り方（モジュールの属性を走査、または大会の一覧の定数があればそれ）は実物から決める。比較の文書の表の列見出し（例: houou/・ouka/・jwrc/）と、大会の設定の名前（または URL の頭）を突き合わせる。突き合わせのキーは実物の表から決め、文書の表の列見出しを変える必要があれば最小限にする
- 追記先の残り容量: `docs/instruction-template.md`・`docs/new-page-checklist.md`・`docs/notes/chat-routines.md` のそれぞれで、容量の上限・警告域があるかを確かめ（`scripts/` の容量の検査や CLAUDE.md の規則）、前後のサイズと上限・警告域との差をログに書く。上限を超えるなら書かずに止まる
- 規則と理由の一句だけを書く。事例（OUK-05・07・10 の経緯）は `docs/notes/handover-archive-2026.md` の該当の節に1行で残すか、書かない（archive の「外した記録」を先に確かめ、前に意図して外した行と重ならないか見る）

## 手順
1. A のテストを足し、今の cloudflare で通ることを確かめる。試しに大会の設定を1つ足した状態で落ちることも確かめる（確かめた後は元に戻す）
2. A・B・C のガイド文を足す。追記先の前後のサイズを測る
3. `python3 -m unittest discover -s scripts/tests` を回す。足した行・テストの全文をログの `## 経過` に貼る

## 止まる条件
- 追記先が容量の上限を超える
- A のテストが今の cloudflare で落ちる（比較の文書と大会の設定が既に食い違っている。食い違いを書いて止まる。文書は直さない）
- cloudflare への push はしない

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する。「判断が必要なこと」に、足した行の要約、追記先の前後のサイズ、上の表から変えたこと（あれば）を書く
- マージは冒頭の「マージ:」の行のとおり（判断待ちで止まる）
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1019-OUK-17.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1019-OUK-17 を書く

不明な点は仮置きで進め、報告の「判断が必要なこと」に書いてください。この行が指示文の最後の行です。

## 経過

### 0章

- Chat-Ref の重複なし。「指示」欄の末尾は指示文の最後の行と一致。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている
- `git checkout -b work/1019-ouk17 origin/cloudflare`（fbae687ec）で作業ブランチを作った

### 追記先の容量

- `docs/instruction-template.md`: 14,325 bytes。警告域 14,336・上限 16,384（`check-assets.yml`）。警告域まで 11 bytes しか無く、B の1句（100 bytes 超）を足すと警告域に入る（CLAUDE.md の規則では警告が出たら4文書の整理が要る）。**B はここに足さず**、`instruction-template.md` の冒頭が正と指す `docs/notes/chat-side-operations.md`「止まる条件と検証の指定」の、許す差分の種類を書く項目に足した
- `docs/notes/chat-side-operations.md`: 22,155 → 22,155 bytes（警告域 26,624・上限 28,672）。冒頭の「純増したら同じ量を docs/notes/ へ移す」に合わせ、場面限定の1行（画像やページから値を読み取らせるとき）を `chat-routines.md`「指示の種類ごとの確かめ」へ移し、足した句を詰めて純増0にした
- `docs/new-page-checklist.md`: 12,978 → 13,264 bytes（上限・警告域なし）
- `docs/notes/chat-routines.md`: 15,393 → 15,831 bytes（上限・警告域なし）
- `docs/notes/league-pages-compare.md`: テストへの参照を1句足した（上限なし）
- 事例（OUK-05・07・10 の経緯）は archive に書かなかった（指示の「書かない」を選んだ。経緯は各ログと docs/decisions/houou.md にある）

### 1. A のテスト

- 大会の設定の一覧は `generate_houou_pages.py` のモジュールの属性のうち `Contest` のもの（`HOUOU`・`OUKA`・`JWRC`。`CONTEST` は今の大会の別名なので出力先 `dir` の集合で数える）。突き合わせのキーは `Contest.dir` + `/`（比較の文書の表の列見出し `houou/`・`ouka/`・`jwrc/` と同じ形）。文書の列見出しは変えていない
- 比較の文書の2つの表（「意図した差」「公開でそろえる差」）の両方で、列が大会の集合と一致することを確かめる
- 今の cloudflare（fbae687ec）で通る。試しに `generate_houou_pages.py` の末尾に `TEST_EXTRA = dataclasses.replace(OUKA, dir="extra")` を足すと `AssertionError: Lists differ: ['houou/', 'jwrc/', 'ouka/'] != ['extra/', 'houou/', 'jwrc/', 'ouka/']` で落ちた（確かめた後に元へ戻した。`git status` で差が無いことを確かめた）
- `python3 -m unittest discover -s scripts/tests` 1,427件 OK

足したテストの全文（`scripts/tests/test_league_pages_compare.py`）:

```python
"""3大会の比較の文書(docs/notes/league-pages-compare.md)の表の列に、generate_houou_pages.py の大会の設定(Contest)が
すべてあるかのテスト。大会を足すと落ちるので、比較の文書の更新を忘れない(2026-10-19 の申送り)。ネットワークには出ない。

    python3 -m unittest discover -s scripts/tests
"""
import pathlib
import re
import sys
import unittest

sys.path.insert(0, str(pathlib.Path(__file__).resolve().parent.parent))
import generate_houou_pages as pages  # noqa: E402

COMPARE_DOC = pages.REPO_ROOT / "docs" / "notes" / "league-pages-compare.md"
TABLE_HEAD = re.compile(r"^\| ページ・タブ \| 観点 \|(.*)\| よりどころ \|$|^\| ページ・タブ \| 観点 \|(.*)\| 決定と入れる時 \|$")


def contest_dirs():
    """モジュールにある大会の設定の出力先("houou" など)。CONTEST は今の大会を指す別名なので集合にする"""
    return {v.dir for v in vars(pages).values() if isinstance(v, pages.Contest)}


def table_columns(text):
    """比較の文書の表ごとの大会の列("houou/" など)"""
    tables = []
    for line in text.splitlines():
        m = TABLE_HEAD.match(line)
        if m:
            tables.append([c.strip() for c in (m.group(1) or m.group(2)).split("|")])
    return tables


class LeaguePagesCompareTest(unittest.TestCase):
    def test_every_contest_is_a_column(self):
        tables = table_columns(COMPARE_DOC.read_text(encoding="utf-8"))
        self.assertTrue(tables, "比較の文書に大会の列のある表が無い")
        expected = sorted(f"{d}/" for d in contest_dirs())
        for columns in tables:
            self.assertEqual(sorted(columns), expected,
                             "大会の設定を足した・変えたら、docs/notes/league-pages-compare.md の表に列を足し、差を書く")


if __name__ == "__main__":
    unittest.main()
```

### 2. 足した行の全文

`docs/new-page-checklist.md`「作る段の確かめ」（「規約（下の3つ）に合わせる…」の次）:

> - 大会のページを横展開する（`generate_houou_pages.py` の `Contest` を足す・変える）ときは `docs/notes/league-pages-compare.md` を読み、差を表に足す（表の列に全大会があるかは `scripts/tests/test_league_pages_compare.py` が確かめる）

`docs/notes/league-pages-compare.md` の冒頭の太字の文の後に足した句:

> （表の列に全大会があるかは `scripts/tests/test_league_pages_compare.py`）

`docs/notes/chat-side-operations.md`「止まる条件と検証の指定」の「マージ・止まる条件は、見込みで説明できる差分の範囲として書く。」の項目に足した行（B）:

> 許す行は焼き付けず、比べる相手（前の TSV・ログ）を指して想定外の行を全部書かせる（決定が変わると無効になる）。

同じ節から消した行（`chat-routines.md` へ移した）:

> - **画像やページから値を読み取らせるときは、元の資料に書かれている値と、こちら側が持つ値を項目ごとに分けて書く**

`docs/notes/chat-routines.md`「指示の種類ごとの確かめ」の末尾（「平野さんがシート（タブ）を用意した・直したと言ったとき」の次）に足した2行（C と移した行）:

> - 平野さんにシートへ貼ってもらう手順: 1列1行で書き、どのセルから貼るかと、貼った後の件数（例: Y 列が49行）を添える（1文にまとめると2か所のうち1か所だけ貼られる）
> - 画像やページから値を読み取らせる指示: 元の資料に書かれている値と、こちら側が持つ値を項目ごとに分けて書く（chat-side-operations.md から移した）

- 成果物のコミット e03d632fe（work/1019-ouk17 に push。cloudflare には push していない）

## 報告

- 状態: 判断待ち / 続き: CHAT-1019-OUK-18
- ブランチ: work/1019-ouk17
- ログ: https://github.com/retroeater/mj/blob/work/1019-ouk17/docs/logs/CHAT-1019-OUK-17.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1019-ouk17
- 確認用URL: なし（テストと docs のみ）
- マージ: 未（平野さんが足した行を読み比べてから、別のマージの指示で）
- issue: なし
- 判断が必要なこと:
  - マージしてよいか。足した行の要約: A はテスト `test_league_pages_compare.py`（比較の文書の2つの表の列が大会の設定と一致するか）と `new-page-checklist.md` の1行・`league-pages-compare.md` の参照の1句。B は許す差分の行を焼き付けず比べる相手を指す1句。C はシートに貼る手順を1列1行で件数を添える1行。全文は `## 経過`「2. 足した行の全文」
  - 追記先の前後のサイズ: `instruction-template.md` 14,325（変えていない。警告域 14,336 まで 11 bytes）、`chat-side-operations.md` 22,155 → 22,155、`new-page-checklist.md` 12,978 → 13,264、`chat-routines.md` 15,393 → 15,831
  - 表から変えたこと: B の追記先を `docs/instruction-template.md` の0章の注記から `docs/notes/chat-side-operations.md`「止まる条件と検証の指定」に変えた（テンプレートが警告域の手前 11 bytes で、足すと警告域に入るため。テンプレートの冒頭は止まる条件の規則の正をこの節としている）。純増0にするため、同じ節の「画像やページから値を読み取らせるとき」の1行を `chat-routines.md` へ移した（場面限定の手順と判断した。移すのが不適切なら戻す）。B の句は字数を詰めたため「止まらせる」を前の文の「ほかが出たら止める」に任せた
  - 事例を archive に書かなかった（指示の選択肢の「書かない」）
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4fd73851）: https://github.com/retroeater/mj-logs/tree/main/guide/4fd73851

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4fd73851/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbae687e.md
