# CHAT-1014-SNM-22

- 着手日時: 2026-10-14
- 対象issue: なし
- ブランチ: work/1014-snm-handover
- 着手時HEAD: 66e8343cd

## 指示

【Claude作成】Claude Code 向け指示：SNM の振り返りの申送り。(1) 鍵の要るスクリプトの確かめはワークフローの dry-run で行う規則を文書に足す、(2) その他マスタの「リーグ」タブの並びの決定を記録する、(3) 「使われていない登録」の検査の利用先の読み漏れを機械的に検出できるかを調べる（文書と調査のみ。コードは変えない） Chat-Ref: CHAT-1014-SNM-22 マージ: ドキュメントのみ（docs/notes/・docs/decisions/・docs/instruction-template.md・ログ）なので完了報告のうえ cloudflare へ入れてよい 貼る時機: いつでも 共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/1014-snm-handover の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。識別子 SNM は同じチャットの CHAT-1011-SNM-01・CHAT-1012-SNM-02〜08・CHAT-1013-SNM-09〜19・CHAT-1014-SNM-20〜21 で使ったもので、重複ではない（SNM-07 は欠番）。 作業ブランチ: クラウドセッションで実行する（新しいセッションは任意。SNM-21 のセッションの続きでもよい）。work/1014-snm-handover を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1014-snm-handover origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

目的
SNM のチャットの振り返り（2026-10-14）で、機械的に確かめられる形に落とせる知見を3つ書き残す。(1) CHAT-1013-SNM-17 で、鍵（Google の認証）の要る `update_sns_book.py` をコンテナで直接動かそうとして確かめられず、SNM-19 でワークフローの dry-run で確かめ直した。規則は docs/notes/cloud-sessions.md「鍵」にあるが「確かめ」のときにも同じことが当てはまると読めず、指示文にも書かれなかった。(2) CHAT-1013-SNM-16 の報告「その他マスタの『リーグ』タブは先頭のままでも、並べ替えてもよい」が決定として記録されていない。(3) CHAT-1014-SNM-21 で、「使われていない登録」の検査（`check_unused_names.py`）が ABEMA ブックを読んでおらず、誤って消してよい候補を出した。「利用先を足したときは検査にも足す」の規則は docs/notes/live-page-design.md にあるが、守られなかったことを機械的に見つける仕組みが無い。
決定（2026-10-14、平野さん）

* 振り返りの知見を申送りにする（機械的に確かめられる手順に落とせるものだけ。抽象的な心得は書かない）
* その他マスタの「リーグ」タブは、先頭のままでも並べ替えてもよい（CHAT-1013-SNM-16 で、生成が先頭のタブの名前に頼らなくなったため。SNM-16 の報告の内容を決定として残す）

前提（チャット側。平野さんの決定ではない）

* docs/notes/cloud-sessions.md「鍵」: 鍵は環境の変数に置かず GitHub Actions のシークレットにだけ置き、鍵の要る処理はワークフローを `actions_run_trigger` で起動して行う（上限なし）。docs/notes/sns-book.md「手動実行」に `update-sns-book.yml` の mode と dry_run の説明がある
* docs/instruction-template.md は 13,734 バイト（mj-logs の guide/916667a9 で測った値）。上限 16KB・警告域 14KB（CLAUDE.md「上限」）。警告域まで約 600 バイト。雛形には行の文面と書き方だけを置き、規則の本文は docs/notes/ に置く（雛形の冒頭の注記）
* docs/notes/live-page-design.md「使われていない登録」: 利用先の一覧と「利用先を足したときは `check_unused_names.py` の読み込みにも足す」。CHAT-1014-SNM-21 のログの表: 読んでいない利用先は books/ の著者（凍結中、数えない）と ABEMA ブック【1】（ページに出ない）。wayhome・dojo などは「プロ」だけを読み「連盟プロ以外」「別名」を使わない
* 使う skill は無い

手順

1. 鍵の規則: docs/notes/cloud-sessions.md「鍵」の節に、「直す前後の確かめ（dry-run）も同じで、鍵の要るスクリプトはコンテナで直接動かさず、ワークフローを書き込まない入力で起動して run のログを読む（例: `update-sns-book.yml` の mode `update`・dry_run 真。docs/notes/sns-book.md「手動実行」）」の趣旨を、規則と理由の一句で足す（追記先の今の文を読んでから）。docs/instruction-template.md の「前提」の例に「鍵の要るスクリプトの確かめはワークフローの dry-run で（cloud-sessions.md「鍵」）」の一句を足せるかを、足した後のサイズを測って決める（警告域 14KB〈14,336 バイト〉を超えるなら雛形には足さず、cloud-sessions.md だけにしてログに書く）
2. リーグの決定: docs/decisions/pros.md に上の「決定」の2つ目を足す（CHAT-1013-SNM-16 の節があればそこに、無ければ 2026-10-14 の節で）
3. 読み漏れの検出（調査のみ。コードは変えない）: `scripts/` の生成・検査・同期スクリプトのうち、「連盟プロ以外」「別名」を名前の解決に使う所（`lib/names.NameBook` を作る所・`load_name_book()`・`abema_book.cast_names()` など）を洗い出して表にする（スクリプト・読む元・画面か issue に名前を出すか）。そのうえで、`check_unused_names.py` の利用先の一覧と機械的に突き合わせる方法（例: 名前の辞書を作る関数の呼び出し元を列挙し、`check_unused_names.py` が読む利用先の定数と比べるテスト。`generate_*.py` が名前の辞書で解決した名前を記録して比べる、など）を2〜3案挙げ、それぞれの見つけられる漏れ・見つけられない漏れ・誤検知・手間を書く。実装するかは平野さんの判断なので、ログの `## 報告` の項目の後ろに案と推す案を書く（決定は書かない）

止まる条件

* docs/notes/・docs/decisions/・docs/instruction-template.md・docs/logs/ 以外のファイルを変える必要が出た（変えずに書く）
* docs/instruction-template.md が警告域を超える（足さずに書く）
* cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

完了条件

* 手順1の足した文と雛形のサイズ（足す前・後）、手順2の足した箇所、手順3の表と案がログにある
* ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する
* マージは冒頭の「マージ:」の行のとおり
* ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1014-SNM-22.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1014-SNM-22 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

### 0章

- 指示欄の末尾は指示文の最後の行と一致している。Chat-Ref の重複なし（`git log --all --grep`）。work/1014-snm-handover はローカル・リモートとも無かったので origin/cloudflare（66e8343c）から作った
- 雛形の行（Chat-Ref・マージ・貼る時機・共通手順）は揃っている

### 手順1 鍵の規則

`docs/notes/cloud-sessions.md`「鍵」の節（今の文は「鍵は環境の変数に置かない。GitHub Actions のシークレットにだけ置き、鍵の要る処理はワークフローを `actions_run_trigger` で起動して行う。」の2行）の後ろに足した:

> **直す前後の確かめも同じで、鍵の要るスクリプトはコンテナで直接動かさず、ワークフローを書き込まない入力（dry-run）で起動して run のログを読む**
> （例: `update-sns-book.yml` の mode `update`・dry_run 真。docs/notes/sns-book.md「手動実行」）。コンテナには鍵が無く、直接動かすと確かめられないまま終わるため（2026-10-13 に起きた）。

`docs/instruction-template.md`: 足す前 13,734 バイト（作業ブランチの上で測った値。前提の値と同じ）→ 足した後 13,849 バイト（警告域 14,336 未満）なので足した。「前提」欄の書き方の行の末尾に「例: 鍵の要るスクリプトの確かめはワークフローの dry-run で〈cloud-sessions.md「鍵」〉」を足した

### 手順2 リーグの決定

`docs/decisions/pros.md` の既存の節「2026-10-13（CHAT-1013-SNM-16）」の末尾に1行足した:
「その他マスタの「リーグ」タブは、先頭のままでも並べ替えてもよい（生成が先頭のタブの名前に頼らなくなったため。2026-10-14 に決定として記録、CHAT-1014-SNM-22）」

### 手順3 名前の辞書を使う所（調査のみ）

「連盟プロ以外」「別名」で名前を解決する所（`NameBook(` を作る所・`load_name_book()` の呼び出し元・`build_aliases()` を使う所を `scripts/` で grep した。tests は除く）:

| スクリプト | 名前の辞書 | 照合する名前（読む元） | 名前を出す所 | `check_unused_names.py` が読むか |
|---|---|---|---|---|
| generate_live_pages.py | 自前の `load_name_book()` | 【2】【3】の対局者・実況・解説 | /live | 読む（層1の候補・【3】） |
| write_live_channel_candidate.py | generate_live_pages の辞書 | 層1の概要欄の名前 → 【2】 | 【2】のタブ・issue「/live の未登録の名前」 | 読む（層1の候補を book なしで） |
| sync_live_calendar.py | generate_live_pages の辞書 | 概要欄の対局者・実況・解説 | 放送対局カレンダーの予定 | 読む（全動画で近似） |
| generate_saikyo_pages.py | 自前の `load_name_book()` | 「最強戦」H列、ABEMA ブック【2】【3】の司会・実況・解説 | saikyo/ | 読む（H列・ABEMA は SNM-21 から） |
| update_abema_book.py | generate_saikyo_pages の辞書 | ABEMA【1】の「対局者など」（卓の割り当てにだけ使う） | 出さない（【2】の割り当て） | 読まない（ページに出ない） |
| generate_houou_race.py | 自前の `load_name_book()` | 「鳳凰」の名前 | 鳳凰戦 順位変動 | 読む |
| generate_houou_pages.py | generate_houou_race の辞書 | 「鳳凰」の名前。ほかに `name_aliases()` が変換後が在籍者の「別名」を全件ページに出す（`?name=<旧名>` で開くため） | houou/ | 「鳳凰」は読む。別名の書き出しは数えない |
| generate_ask_data.py | 自前の `load_name_book()`（X なし） | 「鳳凰」の名前。ほかに「別名」を全件 `aliases` に出す | workers/ask/data.json | 「鳳凰」は読む。別名の書き出しは数えない |
| generate_title_pages.py | 自前（「連盟プロ以外」「別名」のタブを `fetch_tab`・`build_aliases`） | 「タイトル」の名前 | title/ | 読む |
| generate_jpml_test.py | title の `build_aliases()`（「プロ」だけ引く） | 「テスト」の名前 | jpml_test | 読む |
| generate_books_pages.py | 自前の `load_name_book()` | 「書籍」の著者 | books/ | 読まない（2026-10-09 の決定、凍結中） |

- 名前の辞書を使わない: sync_dojo_calendar・wayhome など（「プロ」だけ）、update_sns_book.py（「連盟プロ以外」の名前を【1】に写すが照合はしない）
- houou/・data.json の「別名」の全件の書き出しは、「別名」の行がある限り出るもので、行を消せば出なくなるだけ（消してよい候補かを左右しない）。表に書くだけにした
- 今の生成物で7名を grep すると、どれも saikyo/ に出ている（梶本琢程 454・小山剛志 555・桜井章一 3・篠原みなみ 95・高見奈央 49・竹内幸輔 13 ファイル）。「梶本琢」は「梶本琢程」の部分一致で同じ数になる（別名の変換前は生成物に出ない）
- 決定: `docs/decisions/pros.md`（リーグ。上の手順2）と `docs/decisions/operations.md`「2026-10-14（CHAT-1014-SNM-22）」（申送りにすること）に足した

### マージ

- 文書のみのため、`git merge-base --is-ancestor origin/cloudflare HEAD` を確かめて `git push origin work/1014-snm-handover:cloudflare`（4e678159）

## 報告

- 状態: 判断待ち / 続き: CHAT-1014-SNM-23
- ブランチ: work/1014-snm-handover
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1014-SNM-22.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1014-snm-handover
- 確認用URL: なし
- マージ: 済（4e678159。ドキュメントのみ）
- issue: なし
- 判断が必要なこと:
  - 「使われていない登録」の読み漏れを機械的に見つける仕組みを作るか、作るならどの案か（下の「読み漏れの検出の案」。推すのは案C）
- 未確認の項目: なし
- エラー: なし

### 読み漏れの検出の案（決定ではない）

| 案 | やり方 | 見つけられる漏れ | 見つけられない漏れ | 誤検知 | 手間 |
|---|---|---|---|---|---|
| A 呼び出し元のテスト | unittest で `scripts/*.py` を ast で読み、`.resolve(`・`abema_book.cast_names(`・`build_aliases(` を呼ぶ関数（モジュール.関数）を列挙し、`check_unused_names.py` に置く表（関数 → 読む利用先、または外す理由）と比べる。表に無い関数があれば落ちる | 名前を解決する関数が新しく増えたとき（SNM-21 の `build_cast()` は ABM-17 で足した新しい関数なので見つかった） | 既にある関数が新しいシート・列を読み始めたとき。辞書を通さずに名前を出す所 | 関数の名前の変更・分割で落ちる（表を直すだけ）。update_abema_book のようにページに出さない所は「外す理由」で表に書く | 小（テスト1つ・表 15行前後。ネットワーク不要、毎回のテストで回る） |
| B 生成時の記録 | `NameBook.resolve()` が照合した名前を記録できるようにし、各 `generate_*.py` を実データで動かして、「連盟プロ以外」「別名」に当たった名前の集合を、`check_unused_names.py` の使われている名前と比べる（ワークフローで） | 実データで今出ている漏れはすべて（どの関数・どの列から来ても） | 辞書を通さない所（title/ は自前の `build_aliases`）。今はまだその名前がシートに無い利用先 | update_abema_book（【1】の割り当てだけ）を外す設定が要る | 大（lib/names と全生成の実行。鍵が要り Actions でしか回らず、数分かかる） |
| C 生成物との突き合わせ | `check_unused_names.py` が一覧を出す前に、候補の名前（「連盟プロ以外」の名前・別名の変換後）が公開している生成物（saikyo/・live/・title/・houou/ などの HTML と data.json）に出ていないかを grep し、出ていれば一覧から外さず「生成物に出ている（利用先の読み漏れの疑い）」として知らせ、検査を失敗にする | どの読み方で出たかを問わず、ページに出ている「連盟プロ以外」の行（SNM-21 の6行はすべて saikyo/ に出ていた） | 「別名」の `訂正` の行（変換前はページに出ない。SNM-21 の梶本琢）。books/ など凍結・外したページ。まだ生成し直していない変更 | 短い名前の部分一致（「梶本琢」が「梶本琢程」に当たる。名前の前後を区切って比べる必要がある）。名前と同じ文字列が別の意味で出る場合 | 小〜中（毎日の取り込みのワークフローはリポジトリを持っているので読むだけ。30行前後とテスト） |

推す案: **C**（実際に起きた「ページに出ているのに消してよい候補に出る」をそのまま止められ、利用先の数え方が増えても直さずに済む。毎日の実行で自分で確かめる）。別名の `訂正` は C で見つからないため、足すなら A を合わせる（A は手間が小さく、関数が増えたときに表へ書かせる）。B は手間に見合わない

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj be98b1fc）: https://github.com/retroeater/mj-logs/tree/main/guide/be98b1fc

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/be98b1fc/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/be98b1fc.md
