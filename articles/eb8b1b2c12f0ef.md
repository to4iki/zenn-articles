---
title: "git reset コマンドの設定"
emoji: "💻"
type: "tech"
topics:
  - "git"
published: true
published_at: "2022-10-29 18:49"
---

作業途中に一度ローカルの差分をリセットしたい時はよくあると思う。
毎度 `git reset --soft HEAD~` と入力するのが手間なのでaliasを設定している。

```sh:.gitconfig
[alias]
    undo = reset --soft HEAD~
```

ただ、一度リセットしたけどやっぱりあの時の差分が必要でもう一度見たいとなった時、コミットしていないと `git reflog` の出力でも履歴が残っていなくて詰む。

なので、一度 Temporary commit しておいて、その上でリセットをかけるようする。

```sh:.gitconfig
[alias]
    cancel = !git commit -a -m 'Temporary commit for cancel' && git reset --hard HEAD~
```

これで、`git reflog` に履歴が残るので、再度参照したい時には `git reset --hard 844dad8` みたいにして遡ることを可能としている。

```
7cdde45 HEAD@{1}: reset: moving to HEAD~
844dad8 HEAD@{2}: commit: Temporary commit for cancel
```