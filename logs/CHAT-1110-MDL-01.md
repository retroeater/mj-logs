# CHAT-1110-MDL-01

- 着手日時: 2026-11-10
- 対象issue: #677（起票）
- ブランチ: work/1110-mdl
- 着手時HEAD: 535e861a

## 指示

【Claude作成】Claude Code 向け指示：モデルと effort の使い分けの規則を直し、使用量の節約案（保留）を起票する
Chat-Ref: CHAT-1110-MDL-01
マージ: ドキュメントのみの変更なので、完了報告のうえ cloudflare へ入れてよい（CLAUDE.md「ブランチ運用」）
貼る時機: いつでも
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈手順は docs/notes/cloud-sessions.md〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。平野さんは、この指示のための作業ブランチ work/<識別子> の作成と push を許可している（セッションに割り当てられた claude/… のブランチは使わない）。
作業ブランチ: クラウドセッションで実行する。work/1110-mdl を使う。リモートに無ければ origin/cloudflare から作る。リモートにあってマージ済み（`git merge-base --is-ancestor origin/work/1110-mdl origin/cloudflare` が真）なら origin/cloudflare から作る（`checkout -B` は使わない。ローカルにあるときを含め手順は docs/notes/cloud-sessions.md「作業ブランチの用意」）。マージ済みでなければ止まる
作業ブランチの片付け: 作業ブランチは削除しない。マージ済みなら cleanup-branches.yml が消す（docs/notes/cloud-sessions.md「ブランチの削除」）

0. 着手前に、このログの「指示」欄の末尾が、この指示文の末尾（最後の行）と一致しているか確認し、一致しなければ作業せず報告する。

## 目的
記事「Which Claude model should you build with?」（https://claude.dev/blog/which-claude-model-should-you-build-with/ 、2026-11-09）と今の運用を突き合わせて平野さんが決めたことを、チャット側の文書に入れる。採らなかった節約案は1つの issue にまとめて保留する。変更は docs（docs/decisions/ を含む）と issue の起票だけ。コード・ワークフロー・`.claude/` は変えない。

### 決定（2026-11-10、平野さん）
- 振り返りのレビューは Fable 5.2 で行う（Fable 5.1 から置き換え）
- 申送りのときに Opus へ戻さない（Fable のまま申送りを作る）。申送りの後も残作業があるときは、引継ぎ文を作り、新しいチャットを Opus で始める（2026-10-17 の「申送りを作るときは原則 Opus に戻す」を置き換える）
- チャットの途中で下位のモデルへ下げない。下げるときは引継ぎ文を書いて新しいチャットに移る
- モデルを上げる前に effort を上げる。収束しないなどの場面はチャット側が判断し、effort を上げることを平野さんに提案する
- Sonnet 5.5 の採用（定型の系列、朝の報告〈OHA、06:00〉）は保留
- advisor tool と `opusplan` は保留
- 上の2つの保留は、1つの issue（使用量の節約案）にまとめて保留する
- 背景（平野さん）: 週末に使用量の残りが少なく、一部の作業を週明けに回すことがたまにある

### 前提（チャット側。平野さんの決定ではない）
- 既定モデルの規則は docs/notes/chat-side-operations.md「指示文の書き方・渡し方」の最後の項目（「既定モデルは Opus 5.5。…定型作業に Sonnet 5.5 を使うかは後日平野さんと相談する」）にある（origin/cloudflare 535e861 で読んだ）
- 振り返りのレビューの手順は docs/notes/chat-routines.md「振り返り」の「成果物の形」の最後の段落（「報告の後、必ず Fable 5.1 のレビュー（advisor）を入れる。…`/model claude-fable-5-1`…」）にある（同じ版で読んだ）
- Fable 5.2 のモデル ID は `claude-fable-5-2`
- 関連する issue: #360（closed、トークン使用量の削減。Explore の Haiku 固定は実施済みで、`.claude/agents/Explore.md` が今もある。B〈大きいファイルの全体読みを hook で止める〉は保留のまま）。チャット側が 2026-11-10 に REST の検索（Sonnet・使用量・opusplan・利用枠）で見た範囲では、ほかに同じ論点の open issue は無かった
- 記事の該当箇所（issue の本文に要約して書く。引用はしない）: 定型で範囲の決まった作業と、繰り返すエージェントの作業は Sonnet 5.5 の `medium` から始める／advisor tool は、記事の表の4つの組み合わせのどれも実行役だけの場合をはっきり上回らず、先に相談の頻度を測る／`opusplan` はコードの編集を Sonnet に回すので、既定にする前に自分の作業で測る／比べ方は、同じ作業を2つのモデルで `/model` で実行し `/usage` を比べる
- 文面は下の案でよいが、追記先の今の内容に合わせて変えてよい

知見 → 選んだ層 → 理由:

| 知見 | 選んだ層 | 理由 |
|---|---|---|
| モデルを上げる前に effort を上げる。上げる場面はチャット側が判断して提案する | 文書（chat-side-operations.md の既定モデルの項目を置き換え） | チャット側の判断と平野さんへの提案で、テスト・hook で確かめられない |
| チャットの途中で下位のモデルへ下げない（引継ぎ文を書いて新しいチャットへ） | 文書（同じ項目） | モデルの切り替えは平野さんの操作で、リポジトリから見えない |
| 振り返りのレビューは Fable 5.2、申送りは Fable のまま | 文書（chat-routines.md「振り返り」「申送り」） | 同上 |
| Sonnet 5.5・advisor tool・`opusplan` | issue（保留） | 決定が保留で、規則にしない |

## 手順
1. 同じ論点（モデルの使い分け・使用量の節約）の open issue を検索する（クローズ済みも確認。語: Sonnet、使用量、トークン、opusplan、advisor、effort）。#360（closed）は前提のとおり確認済みなので止まらない。ほかに同じ論点の open issue があれば、起票せず止まって報告する。前提に書いた2つの文書の該当箇所を読み、前提と食い違えば止まる。
2. 文書を直す（どちらも今の記述を置き換える。追記型にしない）。
   - docs/notes/chat-side-operations.md の既定モデルの項目を、次の趣旨の1項目に置き換える: 「**既定モデルは Opus 5.5、effort は既定のまま。別のモデル・effort は指示文の手前のチャット本文で伝える**（指示文に欄は入れない）。**モデルを上げる前に effort を1段上げる**: レビューが2回で収束しなかった続き・同じ論点で指示が3本を超えた・難しい移行などは、チャット側が判断して `/effort high` を平野さんに提案し、それでも足りないときだけ Fable 5.2 を提案する。**チャットの途中で下位のモデルへ下げない**（下げるときは引継ぎ文を書いて新しいチャットに移る。切り替えた最初の手番で会話全体を読み直し、上位のモデルの推論は引き継がれない）。Sonnet 5.5・advisor tool・`opusplan` は保留（#<手順3の番号>）」
   - docs/notes/chat-routines.md「振り返り」の「成果物の形」の最後の段落の Fable 5.1 を Fable 5.2（`/model claude-fable-5-2`）に直す。「申送り」の「何をするか」に次の趣旨を足す: 「申送りは Fable 5.2 のまま作り、Opus へ戻さない。申送りの後も残作業があるときは、引継ぎ文を作り、新しいチャットを Opus で始める」
   - `git grep -n 'Fable 5.1'` と `git grep -n 'claude-fable-5-1'`（リポジトリ全体）で残りを洗い出す。docs/decisions・docs/logs・docs/notes/handover-archive-2026.md の過去の行は直さない。それ以外（CLAUDE.md・docs/handover.md を含む）に今の手順として書かれた行があれば直し、直した行の一覧を報告に書く
   - 上限は作業ブランチの上で測る（CLAUDE.md「CLAUDE.md / handover.md の更新ルール」）。chat-side-operations.md が純増したら、その文書の冒頭の規則のとおり、足した分と同じ量を docs/notes/ へ移す。変更の前後のバイト数を報告に書く
3. issue を1件起票する（起票の前に CLAUDE.md「issueの着手ルール」の検索を行う）。
   - 題: 「使用量の節約案（保留）: Sonnet 5.5 の採用・advisor tool・opusplan」
   - 本文に書くこと: 背景（「決定」の背景の行）／保留した案 (a) 定型の系列（ログ・docs・issue だけが変わる指示）を Sonnet 5.5 で実行する (b) 朝の報告（OHA、06:00、今は Opus 5.5）を Sonnet 5.5 で実行する (c) advisor tool (d) `opusplan`／各案について記事が言っていること（前提の要約）／試すときの比べ方（前提の最後の項目。#360 の 2026-09-18 の比較は試行1回で精度が無かったことも書く）／関連: #360／再開の条件: 平野さんが再開を決めたとき（期日は置かない）
   - ラベル: 「状況: 保留」に当たるラベルがあれば付ける。無ければ付けずに報告に書く（近いラベルで代えない）
   - 起票した番号を、手順2の chat-side-operations.md の「（#<番号>）」に入れる。issue のコメントの末尾に Chat-Ref の行を書く

## 止まる条件
- 前提の該当箇所が食い違う（項目・段落が無い、別の決定で置き換わっている）
- 同じ論点の open issue がある（#360 を除く）
- 追記先と矛盾していて、どちらが正か平野さんかチャット側の判断が要る（同じ趣旨の記述があるだけなら止めず、置き換え・拡張して処理を報告に書く）
- 未マージの work/ ブランチが chat-side-operations.md の既定モデルの項目、または chat-routines.md「振り返り」「申送り」の同じ段落を変えている、または取り込みで衝突する（同じファイルの別の箇所の変更では止まらず、ログに書く）。両方の変更が両立する衝突（追記どうし・隣り合う行）は、両方を残して解いてよい。解いた後の該当箇所をログに引用する。それ以外の衝突は解かずに止まる
- chat-side-operations.md が警告域（26KB）を超える
- cloudflare への push が権限判定で拒否された（別の手段を試さずに止まる）

## 完了条件
- ログの「## 報告」を CLAUDE.md「作業ログ」節のとおりに書いて push する（起票した issue の番号と題、直した行の一覧、バイト数を含める）
- マージは冒頭の「マージ:」の行のとおり
- ターミナルへの最終報告の Chat-Ref の行の直前に ログ（公開）: https://github.com/retroeater/mj-logs/blob/main/logs/CHAT-1110-MDL-01.md?v=<SHA>（CLAUDE.md「作業ログ」節）を書き、最後の行に Chat-Ref: CHAT-1110-MDL-01 を書く

不明な点があれば、着手前に質問してください。この行が指示文の最後の行です。

## 経過

- 着手前の確認: 同じ Chat-Ref のコミットなし／`MDL` の重複なし（全ブランチのコミット・docs/logs の履歴）／0章: 指示欄の末尾は指示文の最後の行と一致／指示文の issue は #360（経緯の参照のみで対象外）。雛形の行（Chat-Ref・マージ・貼る時機・共通手順）の欠けなし
- 作業ブランチ: ローカル・リモートとも無し → `git checkout -b work/1110-mdl origin/cloudflare`

- 手順1（検索）: REST の search/issues で Sonnet・使用量・トークン・opusplan・advisor・effort・Fable・モデル を検索。同じ論点（チャット側のモデル・使用量の節約）の open issue は無し。Sonnet の当たり #665・#630・#628 は Claude API でデータを作る話、使用量の当たり #298 は Actions の使用量で別の論点。#360 は closed（前提どおり）
- 前提の照合: chat-side-operations.md 134行目の既定モデルの項目、chat-routines.md「振り返り」「成果物の形」の最後の段落とも前提どおり。2026-10-17 の「申送りを作るときは原則 Opus に戻す」はリポジトリ内（docs/decisions を含む）に記述が無い（ここからは所在を確かめられない）
- 未マージの work/ ブランチ（1009-swp-526・1011-swp-hnd・1011-swp-nav・1015-skd-notes・1101-prp-pub・1103-ryo5）はどれも2文書を変えていない
- `git grep 'Fable 5.1'`・`'claude-fable-5-1'`: 対象外（docs/decisions・docs/logs）を除くと chat-routines.md 54行目だけ。CLAUDE.md・docs/handover.md には無し
- 起票: #677「使用量の節約案（保留）: Sonnet 5.5 の採用・advisor tool・opusplan」、ラベル「状況: 保留」。コメント末尾に Chat-Ref
- 文書の変更（5faf4af）:
  - chat-side-operations.md 既定モデルの項目を置き換え（指示の案の文面を、純増を抑えるため少し詰めた: 「などは」→「は」、「足りないときだけ」→「足りなければ」、「収束しなかった」→「収束しない」、「切り替えた最初の手番で」→「切り替えると」）
  - chat-routines.md 54行目: Fable 5.1 → Fable 5.2（`/model claude-fable-5-2`）。「申送り」「何をするか」に1項目追加
  - 純増の相殺: chat-side-operations.md「指示文の書き方・渡し方」の2項目（他のチャットへの伝言は .md の添付／常設 issue に足す指示は担当の系列で作る、523バイト）を chat-routines.md「ほかのセッションへの共有」へ移した（「場面ごとに読む文書」の表の「ほかのチャットへの共有」の行から引ける）
  - バイト数: chat-side-operations.md 22,470 → 22,469（警告域 26,624 未満）／chat-routines.md 22,500 → 23,317（上限なし）
- docs/decisions/operations.md（315dc38）: 末尾に「## 2026-11-10（CHAT-1110-MDL-01）」を足し、2026-10-11（CHAT-1011-RGN-07）の Fable 5.1 の行の末尾に「→ 置き換え」の印を付けた（docs/decisions/README.md「書き方」の規則。指示の「過去の行は直さない」は内容を変えないことと読み、印だけ足した）
- 自己レビューのサブエージェント: docs のみの変更のため対象外（CLAUDE.md「判断・作業の原則」の対象はコード・生成物・ワークフロー・配信設定）
- マージ: `git push origin work/1110-mdl:cloudflare`（push 直前に再 fetch して `git merge-base --is-ancestor origin/cloudflare HEAD` を確認）

## 報告

- 状態: 完了
- ブランチ: work/1110-mdl
- ログ: https://github.com/retroeater/mj/blob/cloudflare/docs/logs/CHAT-1110-MDL-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/1110-mdl
- 確認用URL: なし
- マージ: 済（このログを含む work/1110-mdl の先頭を cloudflare へ fast-forward）
- issue: #677 起票「使用量の節約案（保留）: Sonnet 5.5 の採用・advisor tool・opusplan」（ラベル「状況: 保留」）。直した行: docs/notes/chat-routines.md 54行目（Fable 5.1 → 5.2）だけ（CLAUDE.md・handover.md に該当なし）。バイト数: chat-side-operations.md 22,470 → 22,469、chat-routines.md 22,500 → 23,317
- 判断が必要なこと: なし
- 未確認の項目: なし
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj 4eb5ef49）: https://github.com/retroeater/mj-logs/tree/main/guide/4eb5ef49

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/docs/notes/cloudflare.md
- docs/decisions/README.md: https://github.com/retroeater/mj-logs/blob/main/guide/4eb5ef49/docs/decisions/README.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/535e861a.md
