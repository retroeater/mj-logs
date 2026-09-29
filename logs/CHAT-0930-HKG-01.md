# CHAT-0930-HKG-01

- 着手日時: 2026-09-29
- 対象issue: なし
- ブランチ: work/0930-hkg-01
- 着手時HEAD: 263bee89
- 識別子: HKG は全ブランチのコミット・`docs/logs/` の履歴・リモートのブランチ名に無く、重複なし（替えていない）

## 指示

【Claude作成】Claude Code 向け指示文（HKG-01）
Chat-Ref: CHAT-0930-HKG-01 作業ブランチ: work/0930-hkg-01（origin/cloudflare 起点）
共通手順: CLAUDE.md「Chat-Ref」「ブランチ運用」「作業ログ」節のとおり（識別子確認 → origin/cloudflare を起点に work/<識別子>〈クラウドセッションでは worktree を使わず docs/notes/cloud-sessions.md の読み替えに従う〉 → ログ先行push → 最終報告の Chat-Ref の行の直前に「ログ（公開）」の行、最後の行に Chat-Ref）。 識別子 HKG は、チャット側が使用済み一覧を確かめずに付けたものです。重複していれば、別の3文字に替えてログの冒頭に書いてください。
目的
.claude/hooks/mj-git-guard.py は、cloudflare への push を一律で ask にしています。そのため、確認指示のログ（docs/logs のみ）を cloudflare へ入れるたびに承認を求められ、回数が多すぎる状態です。CLAUDE.md「ブランチ運用」では、ログのみの反映はもともとセッションに任されています。この運用に hook を合わせます。確認なしで通す範囲は docs/logs/ 配下だけにします（平野さん決定）。ガイド文書を含む docs/ のほかの場所は、引き続き ask にします。
手順

1. mj-git-guard.py、.claude/settings*.json の hooks 設定、docs/notes/skills.md、CLAUDE.md「ブランチ運用」「作業ログ」を読みます。そのうえで、今の判定（cloudflare・claude/* を ask にする箇所と、その理由文）と、ログを cloudflare へ入れる手順の現状（1回の指示で何回 cloudflare へ push する書き方になっているか）をログに書いてください。
2. hook に次の判定を足します。push 先が cloudflare のとき、次の3つをすべて満たす場合だけ allow にし、それ以外は今の ask のままにします。
   * push 元（コマンドに書かれた `<src>:cloudflare` の src。省略時は HEAD）が origin/cloudflare から fast-forward で入る。
   * origin/cloudflare..src の差分ファイルがすべて docs/logs/ 配下にある（追加・変更に限る。削除・リネームを含むなら ask）。
   * force 系オプション（--force・-f・--force-with-lease・+refspec）が無い。
   * git コマンドの失敗・解釈できないコマンド・複数 refspec などで判定できない場合は ask にしてください（fail closed）。hook の中では fetch しないでください。origin/cloudflare が古いと差分に他の変更が混ざりますが、その場合は ask 側に倒れるので問題ありません。
   * claude/* への push など、ほかの判定は変えないでください。
   * 直すときは writing-for-agents skill に従い、理由文は短く保ってください。
3. テストをします（手元で hook に JSON を与える形でよい）。少なくとも次の各ケースについて、期待どおりの結果になることをログに表で書いてください。
   * docs/logs のみで ff → allow
   * docs/handover.md を含む → ask
   * コードを含む → ask
   * 非 ff → ask
   * --force → 今までどおり
   * claude/* → ask
   * 画像のコマンド `git fetch -q origin && git merge-base --is-ancestor origin/cloudflare HEAD && git push origin work/…:cloudflare 2>&1 | tail -1` の形 → docs/logs のみなら allow
4. CLAUDE.md（と docs/notes/skills.md の hook の説明）を最小限だけ直します。「docs/logs のみの cloudflare への push は hook が確認なしで通す。それ以外は ask」と書き、1回の指示の中で cloudflare への push は最後の1回にまとめ、途中のログ先行 push は work ブランチにだけ行うことを明確にします。手順1で今の記述がすでにそうなっていれば、文言は足さずにその旨をログに書いてください。CLAUDE.md の容量上限（CLAUDE.md「更新ルール」）の判定を実行し、結果をログに書いてください。

止まる条件

* hook の構造上、push 元と範囲を安全に特定できない。
* 変更後に CLAUDE.md の判定が警告になる。
* 手順2の条件のほかに、緩めたほうがよさそうな箇所が見つかった（提案だけログに書き、実装はしない）。

完了条件
hook の変更はコードの変更なので、マージしないでください。work/0930-hkg-01 に push し、次の3つをログに貼って「判断待ち」で止まってください。平野さんのマージ判断を待ちます。

* 差分（hook・CLAUDE.md・skills.md の全体）
* テスト結果の表
* 容量判定の結果

内容を理解したら、着手前に作業ブランチ名と識別子の確認結果を一言返してから始めてください。

## 経過

### 手順1: 現状

- hook の設定: `.claude/settings.json` の `hooks.PreToolUse`（matcher `Bash`）が `python3 "$CLAUDE_PROJECT_DIR"/.claude/hooks/mj-git-guard.py` を呼ぶ。`settings.local.json` は無い
- 判定（`mj-git-guard.py` の `judge`）: コマンドを `&&`・`||`・`;`・`|`・改行で区切り、`git push` の押し先（refspec の `:` の右。refspec が無ければ現在ブランチ）で判定する
  - cloudflare → ask。理由文「cloudflare への push＝本番反映。マージの基準（CLAUDE.md「ブランチ運用」）を満たすか人が確認する」
  - `claude/` で始まる → ask。理由文「claude/* へは push しない（cloud-sessions.md「始め方」）」
  - gh-pages → deny。ほかは判定なし（通常の許可の流れ）
  - 複数の区切りで判定が出たら deny を優先し、無ければ最初のもの。allow を返す経路は無い
  - `--force`・`+refspec` は区別していない（cloudflare 宛なら ask のまま）
- ログを cloudflare へ入れる手順の現状:
  - CLAUDE.md「作業ログ」: 着手時のログ先行 push・節目の push は作業ブランチへ（「ログは作業ブランチにコミットし、`cloudflare`へのマージ時に成果物と一緒に入れる」）。つまり cloudflare への push は、1回の指示でマージの1回だけという書き方
  - CLAUDE.md「ブランチ運用」: ドキュメントのみの変更（docs/ 配下）は完了を報告したうえでセッションがマージしてよい。ログのみのマージもこれに当たる
  - ただし「cloudflare への push は最後の1回にまとめる」「途中の push は work ブランチにだけ」とは明記されていない（「マージ時に一緒に入れる」から読み取れるだけ）。手順4で明記する
  - docs/notes/skills.md「git の hook（mj-git-guard）」の表は cloudflare への push を一律 ask と書いている
- `docs/logs/` にはログのほか `_template.md` がある。これは `scripts/sync_guides.py` の `ALLOWED_PATTERNS` に入っているガイド文書なので、指示の「ガイド文書は引き続き ask」に合わせて allow の対象から外す

### 手順2: hook の変更

- `main` で、判定が cloudflare の ask 1件だけのときに限り `logs_only_push` を呼び、真なら allow にする。deny・claude/*・gh-pages・複数の push を含むコマンドは今までどおり（allow の経路に入らない）
- allow は hook がコマンド全体を確認なしで通すことになる。`git push ... && rm -rf x` のような連結も通ってしまうため、指示の3条件に加えて、コマンドの形を絞った（判定できない形は ask）:
  - 区切りごとに `git push`（1回だけ）・`git fetch`（`-q`・`--prune`・`origin` だけ。refspec 付きの fetch は手元の ref を書き換えられるので外す）・`git merge-base`・`tail -N`/`head -N` だけ
  - `2>&1` を除き、`` ` `` `$` `<` `>` `(` `)` `{` `}` `\` `*` `?` `[` `]` `~` `#` と単独の `&` を含まない。`cd`・`-C`・`-c`・環境変数の前置も外れる
  - push のオプションは `-q`・`-v`・`-u`・`--porcelain`・`--progress` 系だけ（`--force`・`-f`・`--force-with-lease`・`--mirror`・`--all`・`--delete` などは外れる）。remote は `origin`
  - refspec は1つで、`<src>:cloudflare` か `<src>:refs/heads/cloudflare`（`+` 付き・src 空〈削除〉・src に `~` などの式は外れる）。refspec 省略時は `@{push}` が `refs/remotes/origin/cloudflare` で `push.default` が simple/current のときだけ HEAD を src とする
- 範囲の判定: `origin/cloudflare` から src が fast-forward（`merge-base --is-ancestor`）であることを確かめ、`rev-list --ancestry-path origin/cloudflare..src` の各コミットと origin/cloudflare 自身から src までの差分（`git diff --no-renames --name-status`）が、すべて `docs/logs/` の A・M（`_template.md` を除く）であることを見る
  - 途中の各コミットからも見るのは、手元の origin/cloudflare が古いときのため。本物の cloudflare はこの途中のどこかにあり、そこからの差分が push で本番に入る差分になる。origin/cloudflare からの差分だけだと、取り込みで他セッションの変更を落としたマージ（巻き戻し）を見逃す（試験の「取り込みで他セッションの変更を落としたマージ」）
  - hook の中では fetch しない。git コマンドの失敗・タイムアウト（10秒）・途中のコミット100件超は ask
- 理由文は allow のとき「docs/logs のみの fast-forward（CLAUDE.md「ブランチ運用」）」。ask の理由文は変えていない

### 手順3: 試験

使い捨てのリポジトリ（bare の remote と clone）を scratchpad に作り、ケースごとの作業ブランチを用意して、hook に `{"tool_input":{"command":...},"cwd":<clone>}` を標準入力で渡した。「通過」は出力なし。

先に、変更前の hook（origin/cloudflare の版）で同じ試験を回し、allow を期待する8件がすべて ask になり、それ以外の行（ask・deny・通過）は変更後と同じ結果になることを確かめた（#310。ほかの判定を変えていないことの確認も兼ねる）。

結果（変更後の hook。すべて期待どおり）:

| ケース | コマンド | 期待 | 結果 |
|---|---|---|---|
| docs/logs のみで ff（追加・変更） | `git push origin work/logs:cloudflare` | allow | allow |
| 同・refs/heads 付き | `git push origin work/logs:refs/heads/cloudflare` | allow | allow |
| 同・HEAD（work/logs 上） | `git push origin HEAD:cloudflare` | allow | allow |
| 画像の形 | `git fetch -q origin && git merge-base --is-ancestor origin/cloudflare HEAD && git push origin work/logs:cloudflare 2>&1 \| tail -1` | allow | allow |
| cloudflare を取り込んだ作業ブランチ（差分は docs/logs のみ） | `git push origin work/merged:cloudflare` | allow | allow |
| docs/handover.md を含む | `git push origin work/handover:cloudflare` | ask | ask |
| コードを含む | `git push origin work/code:cloudflare` | ask | ask |
| docs/logs/_template.md（ガイド文書） | `git push origin work/template:cloudflare` | ask | ask |
| docs/logs の削除 | `git push origin work/delete:cloudflare` | ask | ask |
| docs/logs のリネーム | `git push origin work/rename:cloudflare` | ask | ask |
| 非 ff | `git push origin work/nonff:cloudflare` | ask | ask |
| 取り込みで他セッションの変更を落としたマージ | `git push origin work/evil:cloudflare` | ask | ask |
| --force | `git push --force origin work/logs:cloudflare` | ask | ask |
| -f | `git push -f origin work/logs:cloudflare` | ask | ask |
| --force-with-lease | `git push --force-with-lease origin work/logs:cloudflare` | ask | ask |
| +refspec | `git push origin +work/logs:cloudflare` | ask | ask |
| 削除の refspec（:cloudflare） | `git push origin :cloudflare` | ask | ask |
| 複数 refspec | `git push origin work/logs:cloudflare work/logs:work/logs` | ask | ask |
| remote が origin 以外 | `git push upstream work/logs:cloudflare` | ask | ask |
| src が式 | `git push origin work/logs~1:cloudflare` | ask | ask |
| src が存在しない | `git push origin work/none:cloudflare` | ask | ask |
| -C 付き | `git -C /tmp push origin work/logs:cloudflare` | ask | ask |
| ほかのコマンドと連結 | `git push origin work/logs:cloudflare && rm -rf x` | ask | ask |
| コマンド置換 | `git push origin $(echo work/logs):cloudflare` | ask | ask |
| fetch で手元の ref を書き換える | `git fetch origin work/code:work/logs && git push origin work/logs:cloudflare` | ask | ask |
| claude/* への push | `git push origin work/logs:claude/foo` | ask | ask |
| cloudflare と claude/* を同時に | `git push origin work/logs:cloudflare; git push origin work/logs:claude/foo` | ask | ask |
| work/* への push（変更なし） | `git push -u origin work/logs` | pass | pass |
| gh-pages（変更なし） | `git push origin gh-pages` | deny | deny |
| refspec 省略（cloudflare 上、docs/logs のみ） | `git push` | allow | allow |
| remote のみ（同） | `git push origin` | allow | allow |
| refspec 省略（cloudflare 上、コードを含む） | `git push` | ask | ask |
| origin/cloudflare が古い: 取り込んだ他セッションの変更が差分に混ざる | `git push origin work/merged:cloudflare` | ask | ask |
| origin/cloudflare が古い: 古い側から docs/logs のみの作業ブランチ | `git push origin work/pre:cloudflare` | allow | allow |

実リポジトリでの確認: 画像の形のコマンド（`work/0930-hkg-01:cloudflare`）を、hook・文書の変更をコミットする前（HEAD はログのコミットだけ）に渡すと allow、コミットした後は ask になった。

`python3 -m unittest discover -s scripts/tests`: 391件 OK（hook の試験は含まれない）。

### 手順4: 文書

- CLAUDE.md「ブランチ運用」の「マージの手順」に、hook が docs/logs のみの fast-forward を確認なしで通し、それ以外は ask にする旨を1行足した
- CLAUDE.md「作業ログ」の「ログは作業ブランチにコミットし、`cloudflare`へのマージ時に成果物と一緒に入れる」を、途中の push は作業ブランチだけ・cloudflare へは最後のマージ1回、と明記する形に置き換えた（今の記述は読み取れるが明記はしていなかったため）
- docs/notes/skills.md「git の hook（mj-git-guard）」の判定の表に allow の行と条件、試験の表に2行を足した。既存の `work/SKL:cloudflare → ask` の行は、今の判定では中身次第になるため「`.claude/` の変更を含む」と添えた

容量判定（`assets-check.yml` の「ガイド文書のサイズを確認」の run をそのまま実行）:

```
CLAUDE.md: 26361 bytes (警告域 30720 / 上限 32768)
docs/handover.md: 21187 bytes (警告域 26624 / 上限 28672)
docs/notes/chat-side-operations.md: 17196 bytes (警告域 26624 / 上限 28672)
exit=0
```

CLAUDE.md は 26107 → 26361 bytes（+254）。警告なし。

### 差分（hook・CLAUDE.md・skills.md の全体、`git diff origin/cloudflare`）

```diff
diff --git a/.claude/hooks/mj-git-guard.py b/.claude/hooks/mj-git-guard.py
index 1e670bb2..a7b090a6 100755
--- a/.claude/hooks/mj-git-guard.py
+++ b/.claude/hooks/mj-git-guard.py
@@ -3,6 +3,7 @@
 
 deny: reset --hard / clean / branch -D / stash / checkout . / gh-pages への push
 ask : cloudflare への push（押し先を省いた push は現在ブランチで判定。マージの手順 `git push origin <作業ブランチ>:cloudflare` は正規の操作のため、止めずに人の確認へ回す）
+      ただし docs/logs のみの fast-forward は allow（logs_only_push。判定できなければ ask のまま）
       claude/* への push（docs/notes/cloud-sessions.md「始め方」で push しない決まり）
 それ以外（work/* への push、ログ先行 push を含む）は通す。
 """
@@ -14,6 +15,7 @@ import sys
 
 SEGMENT_SPLIT = re.compile(r"&&|\|\||;|\||\n")
 PROTECTED = "cloudflare"
+ASK_PROTECTED = ("ask", "cloudflare への push＝本番反映。マージの基準（CLAUDE.md「ブランチ運用」）を満たすか人が確認する")
 
 
 def git_args(segment):
@@ -64,19 +66,115 @@ def judge(args):
         if t == "gh-pages":
             return "deny", "gh-pages に触らない（handover.md）"
         if t == PROTECTED:
-            return "ask", "cloudflare への push＝本番反映。マージの基準（CLAUDE.md「ブランチ運用」）を満たすか人が確認する"
+            return ASK_PROTECTED
         if t.startswith("claude/"):
             return "ask", "claude/* へは push しない（cloud-sessions.md「始め方」）"
     return None
 
 
+# allow はコマンド全体を確認なしで通すため、判定できる形に限る（それ以外は ask のまま）
+LOGS_DIR = "docs/logs/"
+LOGS_GUIDE = "docs/logs/_template.md"  # ガイド文書（scripts/sync_guides.py）なので ask
+BASE = "refs/remotes/origin/cloudflare"
+DEST = ("cloudflare", "refs/heads/cloudflare")
+PUSH_OPTS = {"-q", "--quiet", "-v", "--verbose", "-u", "--set-upstream", "--porcelain", "--progress", "--no-progress"}
+FETCH_WORDS = {"-q", "--quiet", "-p", "--prune", "origin"}
+FD_DUP = re.compile(r"\s\d?>&\d\b")  # 2>&1
+SHELL_META = re.compile(r"[`$<>(){}\\*?\[\]~#]|(?<!&)&(?!&)")
+SRC_NAME = re.compile(r"^[A-Za-z0-9][A-Za-z0-9._/-]*$")
+MAX_COMMITS = 100
+ALLOW_REASON = "docs/logs のみの fast-forward（CLAUDE.md「ブランチ運用」）"
+
+
+def git_out(cwd, *args):
+    r = subprocess.run(["git", *args], cwd=cwd, capture_output=True, text=True, timeout=10)
+    if r.returncode != 0:
+        raise RuntimeError(args)
+    return r.stdout
+
+
+def push_src(args):
+    """`git push` の引数から push 元を返す。cloudflare 宛の単純な形でなければ None。"""
+    if not set(a for a in args if a.startswith("-")) <= PUSH_OPTS:
+        return None
+    pos = [a for a in args if not a.startswith("-")]
+    if len(pos) > 2 or (pos and pos[0] != "origin"):
+        return None
+    if len(pos) < 2:
+        return "@{push}"  # refspec 省略: 現在ブランチ（HEAD）を @{push} へ
+    src, sep, dst = pos[1].rpartition(":")
+    if not sep:
+        src = dst
+    if dst not in DEST or not SRC_NAME.match(src):
+        return None  # +refspec・:cloudflare（削除）・式を含む src を含む
+    return src
+
+
+def single_push_src(command):
+    """各区切りを読み、push 元を1つ返す。fetch・merge-base・tail・head 以外を含めば None。"""
+    if SHELL_META.search(FD_DUP.sub(" ", command)):
+        return None
+    srcs = []
+    for seg in SEGMENT_SPLIT.split(command):
+        try:
+            words = shlex.split(FD_DUP.sub(" ", seg))
+        except ValueError:
+            return None
+        if not words:
+            continue
+        if words[0] in ("tail", "head") and all(re.fullmatch(r"-n?\d+", w) for w in words[1:]):
+            continue
+        if words[0] != "git" or len(words) < 2:
+            return None
+        sub, args = words[1], words[2:]
+        if sub == "merge-base" or (sub == "fetch" and set(args) <= FETCH_WORDS):
+            continue
+        if sub != "push":
+            return None
+        srcs.append(push_src(args))
+    return srcs[0] if len(srcs) == 1 else None
+
+
+def logs_only_push(command, cwd):
+    """push 元が origin/cloudflare から fast-forward で、途中のどの時点からの差分も docs/logs の追加・変更だけか。
+
+    fetch はしない。origin/cloudflare が古くても、本物の cloudflare は push 元までの ancestry-path 上にあるので全部調べる。"""
+    src = single_push_src(command)
+    if not src:
+        return False
+    try:
+        if src == "@{push}":
+            if git_out(cwd, "config", "--default", "simple", "push.default").strip() not in ("simple", "current"):
+                return False
+            if git_out(cwd, "rev-parse", "--symbolic-full-name", "@{push}").strip() != BASE:
+                return False
+            src = "HEAD"
+        base = git_out(cwd, "rev-parse", "--verify", BASE + "^{commit}").strip()
+        tip = git_out(cwd, "rev-parse", "--verify", src + "^{commit}").strip()
+        git_out(cwd, "merge-base", "--is-ancestor", base, tip)
+        path = git_out(cwd, "rev-list", "--ancestry-path", f"{base}..{tip}").split()
+        if len(path) > MAX_COMMITS:
+            return False
+        for rev in [base, *path[1:]]:  # path[0] は tip 自身
+            fields = git_out(cwd, "diff", "--no-renames", "--name-status", "-z", rev, tip).split("\0")[:-1]
+            for status, name in zip(fields[::2], fields[1::2]):
+                if status not in ("A", "M") or not name.startswith(LOGS_DIR) or name == LOGS_GUIDE:
+                    return False
+    except (RuntimeError, OSError, subprocess.TimeoutExpired):
+        return False
+    return True
+
+
 def main():
-    command = json.load(sys.stdin).get("tool_input", {}).get("command", "")
+    data = json.load(sys.stdin)
+    command = data.get("tool_input", {}).get("command", "")
     verdicts = [judge(git_args(s)) for s in SEGMENT_SPLIT.split(command)]
     verdicts = [v for v in verdicts if v]
     if not verdicts:
         return 0
     decision, reason = next((v for v in verdicts if v[0] == "deny"), verdicts[0])
+    if verdicts == [ASK_PROTECTED] and logs_only_push(command, data.get("cwd")):
+        decision, reason = "allow", ALLOW_REASON
     print(json.dumps({"hookSpecificOutput": {
         "hookEventName": "PreToolUse",
         "permissionDecision": decision,
diff --git a/CLAUDE.md b/CLAUDE.md
index 32eb9c40..f8ef9cd4 100644
--- a/CLAUDE.md
+++ b/CLAUDE.md
@@ -89,7 +89,8 @@ This file provides guidance to Claude Code (claude.ai/code) when working with co
   - `docs/`配下のみの変更では Workers Builds が走らず check-run も出ない（#171）。`docs/`外のドキュメントを含むpushではデプロイが1回走る（表示は変わらない）
 - **マージの手順:** worktree内で`git push origin <作業ブランチ>:cloudflare`とし、cloudflareはチェックアウトしない。
   **push直前に必ず再fetchし、`git merge-base --is-ancestor origin/cloudflare HEAD`で push 先が自分のHEADの祖先であることを確認すること。**
-  他セッションのfetchで`origin/cloudflare`が進むため、取り込み時点を前提にすると他セッションのコミットを巻き戻す
+  他セッションのfetchで`origin/cloudflare`が進むため、取り込み時点を前提にすると他セッションのコミットを巻き戻す。
+  hook（mj-git-guard）はこのpushを、docs/logs のみ（`_template.md`を除く）の fast-forward なら確認なしで通し、それ以外は ask にする（docs/notes/skills.md）
 - **`.github/workflows/`を追加・変更する作業では、着手時にdocs/notes/branch-operations.md「ワークフローを変更したとき」を読む。
   マージの前に作業ブランチで手動実行して結果を確かめ、実行できないときは報告して判断を仰ぐこと**
 - 長期間マージされないブランチは、定期的に`cloudflare`を取り込んで乖離を小さく保つ
@@ -151,7 +152,7 @@ push したログとガイド文書は public の`retroeater/mj-logs`に写る
   `work/`ではこの目印のあるpushだけがmj-logsへ写る。途中の節目のpushには付けない（#298）
 - 構成（ヘッダ・`## 指示`〈貼られた指示文をそのまま〉・`## 経過`〈詳細はすべてここ〉・`## 報告`）と各項目の書き方は`docs/logs/_template.md`（コピーして使う）。
   **`## 報告`はログの末尾に必ず置き、作業の最後に更新してpushする。** チャット側はこの節だけを読んで判断するため、**10項目を省かず、該当が無ければ「なし」と書く**
-- ログは作業ブランチにコミットし、`cloudflare`へのマージ時に成果物と一緒に入れる
+- ログは作業ブランチにだけpushし（ログ先行・節目のpushを含む）、`cloudflare`へは指示の最後のマージ1回で成果物と一緒に入れる
 - 後の指示で使うスクリプト・中間データの置き場所は docs/notes/session-network.md「作業ファイルの置き場所」（scratchpad は再起動で消える）
 - **ターミナルへ返す最終報告は、状態・ログのURL・ブランチ・（あれば）確認用・ログ（公開）・Chat-Ref の行だけにする。**
   形は`docs/logs/_template.md`「ターミナルへ返す最終報告」。**URL の直後に文字を続けない**（続く文字まで URL とみなされ404になる）。
diff --git a/docs/notes/skills.md b/docs/notes/skills.md
index fff8e4b5..669f5142 100644
--- a/docs/notes/skills.md
+++ b/docs/notes/skills.md
@@ -67,16 +67,26 @@ Bash のコマンド文字列を `&&`・`||`・`;`・`|`・改行で区切り、
 | 判定 | 対象 |
 |---|---|
 | deny（実行させない） | `reset --hard`・`clean`・`stash`・`branch -D`（`--delete --force` を含む）・`checkout .`・`gh-pages` への push |
-| ask（人に確認する） | cloudflare への push（`<作業ブランチ>:cloudflare`、cloudflare 上での押し先を省いた `git push` など。マージは正規の操作なので止めない）・`claude/*` への push（cloud-sessions.md「始め方」） |
+| ask（人に確認する） | cloudflare への push（`<作業ブランチ>:cloudflare`、cloudflare 上での押し先を省いた `git push` など。マージは正規の操作なので止めない）のうち allow に当たらないもの・`claude/*` への push（cloud-sessions.md「始め方」） |
+| allow（確認なしで通す） | cloudflare への push のうち、docs/logs のみの fast-forward（下の条件） |
 | 通過 | 上以外（`work/*` への push・ログ先行 push・`branch -d`・`checkout -b` など） |
 
+allow の条件（`logs_only_push`。1つでも外れるか判定できなければ ask。allow はコマンド全体を通すため形を絞る）:
+
+- コマンドは `git push`（1回）と `git fetch [-q] [--prune] [origin]`・`git merge-base`・`tail -N`/`head -N` だけ。`2>&1` 以外のリダイレクト・`$`・`cd`・`-C` などを含まない
+- push は `origin` 宛て、refspec は1つ（`<src>:cloudflare`・`<src>:refs/heads/cloudflare`、src は名前だけ）か省略（`@{push}` が origin/cloudflare）。force 系・`+`・削除は外れる
+- src が手元の origin/cloudflare から fast-forward。origin/cloudflare から src までの途中のどのコミットからの差分も、`docs/logs/` の追加・変更だけ（削除・リネーム・`docs/logs/_template.md` は外れる）。
+  hook は fetch しない。origin/cloudflare が古くても本物の cloudflare はその途中にあるので、巻き戻しは ask になる
+
 試験した入力と結果（2026-09-29）:
 
 | コマンド | 判定 |
 |---|---|
 | `git push -u origin work/SKL` | 通過 |
-| `git push origin work/SKL:cloudflare` | ask |
+| `git push origin work/SKL:cloudflare`（`.claude/` の変更を含む） | ask |
 | `git push origin HEAD:refs/heads/cloudflare` | ask |
+| `git push origin work/x:cloudflare`（docs/logs のみ・ff、2026-09-29） | allow |
+| 同（docs/handover.md・コード・`_template.md`・削除・リネームを含む／非 ff／`--force`） | ask |
 | `git push`（work/SKL 上） | 通過 |
 | `git push origin claude/foo` | ask |
 | `git push origin gh-pages` | deny |
```

### 止まる条件の確認

- push 元と範囲の特定: できた（判定できない形はすべて ask に倒す）
- CLAUDE.md の判定: 警告なし
- 緩めたほうがよさそうな箇所: 見つからなかった。逆に、今の判定が漏らしている箇所を1つ見つけた（下の「判断が必要なこと」。締める側なので実装していない）

## 報告

- 状態: 判断待ち（hook の変更はコードの変更のため、マージは平野さんの判断）
- ブランチ: work/0930-hkg-01
- ログ: https://github.com/retroeater/mj/blob/work/0930-hkg-01/docs/logs/CHAT-0930-HKG-01.md
- 比較URL: https://github.com/retroeater/mj/compare/cloudflare...work/0930-hkg-01
- 確認用URL: なし（表示に影響しない。CLAUDE.md を含むので Workers Builds は1回走る）
- マージ: 未（平野さんの判断待ち）
- issue: なし
- 判断が必要なこと:
  - マージの可否。差分・試験の表・容量判定は `## 経過` の「手順3」「手順4」「差分」
  - allow の対象から `docs/logs/_template.md` を外した（ガイド文書のため）。指示の「docs/logs/ 配下」より狭い。広げるなら `LOGS_GUIDE` の判定を消す
  - 指示の3条件に加え、コマンドの形を絞った（`git fetch`・`git merge-base`・`tail`/`head` 以外との連結、`cd`・`-C`・`$` などを含むと ask）。hook の allow はコマンド全体を確認なしで通すため。ほかの形を通したい場合は判断を
  - 締める側の提案（実装していない）: 今の判定は、cloudflare をチェックアウトした状態の `git push origin HEAD` を ask にしない（押し先を refspec の文字列 `HEAD` で判定し、cloudflare と見なさない）。セッションは cloudflare をチェックアウトしない決まりなので実害は小さい
- 未確認の項目:
  - hook が allow を返したときの実機の挙動（承認の問い合わせが出ずに通るか、auto モードの分類器を越えるか）。試験は hook に JSON を渡す形だけで、このセッションでは cloudflare へ push していない。マージ後、最初のログのみのマージで確かめる必要がある
- エラー: なし

<!-- guide-links -->
---

ガイド文書（この版を写した時点の最新、mj dc536d83）: https://github.com/retroeater/mj-logs/tree/main/guide/dc536d83

- CLAUDE.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/CLAUDE.md
- docs/handover.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/handover.md
- docs/instruction-template.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/instruction-template.md
- docs/notes/chat-side-operations.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/notes/chat-side-operations.md
- docs/notes/cloudflare.md: https://github.com/retroeater/mj-logs/blob/main/guide/dc536d83/docs/notes/cloudflare.md
- 使用済みの Chat-Ref 識別子: https://github.com/retroeater/mj-logs/blob/main/chat-ids/fbb55c8c.md
