---
title: "herdrのペイン上のURLをterminal-browserで開く"
emoji: "🐏"
type: "tech"
topics:
  - "herdr"
  - "fzf"
  - "terminal-browser"
published: true
---

AIエージェントの出力には以下のようにURLがよく出てくる。

- 参考情報のWebのリンク
- 説明用のHTMLファイル
- 検証用のローカルサーバーのURL

これらのURLを都度コピーして、ブラウザにペーストして確認するのが手間なので、tmux を利用した際には、画面上の URL を fzf で選んで外部ブラウザ表示する tmux-fzf-url[^1] をよく利用していた。コンセプトを参考に herdr 用プラグインを作成することにした。

選択したURLを外部ブラウザで開くのでも良かったが、一時的な用途で雑に表示したいケースが多かったので、terminal-browser を右側のペインに表示するような作りとする。

https://terminal-browser.com/

[^1]: https://github.com/wfxr/tmux-fzf-url

## herdr-fzf-terminal-browser

https://github.com/to4iki/herdr-fzf-terminal-browser

![](/images/herdr-fzf-terminal-browser-demo-1.png)

![](/images/herdr-fzf-terminal-browser-demo-2.gif)

やっていることはシンプルで、今見えているペインの画面から URL を fzf で選び、同じ herdr タブに terminal-browser がなければ右に split し 、あればそのブラウザの新しいタブで開く。

特徴として、ポップアップでURLを選択後に、termina-browser 用のペインにフォーカスを当てている[^2]。また、termina-browser を `ctrl-c` or `ctrl-q` で閉じると、そのままペイン毎閉じるような一時確認用途に最適化している。

[^2]: プラグインが開いたブラウザに限る。自分で起動しておいた terminal-browser にタブを足した場合、フォーカスは元のまま。

注意点は、現在表示中の画面内のURLが対象であって、画面外の例えば過去のやり取りで表示しているURLは検知していない。そのため、必要に応じてユーザーがスクロールして検索対象を調整すること。
あとは、ポップアップ内で `ctrl-y` でクリップボードにコピーもできるようにしているので、外部ブラウザで表示したい場合にはこちらを利用すること。

### インストール

```sh
herdr plugin install to4iki/herdr-fzf-terminal-browser
```

`~/.config/herdr/config.toml` にキーを割り当てて、`herdr server reload-config` する。自分は `prefix+f` にしている。

```toml:.config/herdr/config.toml
[[keys.command]]
key = "prefix+f"
type = "plugin_action"
command = "to4iki.fzf-terminal-browser.pick"
description = "open url in terminal-browser"
```


### 検知するURL

URLの対象に関しては tmux-fzf-url の移植ではなく、コーディングエージェントの出力向けに調整している。
`https://...` だけでなく `localhost:5173` や `127.0.0.1:8080` も `http://` 付きで取得したり、Markdown の `[docs](url)` や JSON に埋まった URL は、引用符の外側と末尾の句読点、対になっていない括弧を落として取り出す。同じ URL が何度も出ていれば、新しいほうだけ残すようにしている。

## 最後に

設定項目はまだないので、split の方向やサイズを変えられるよう今後調整してみようと思う。
herdr で表示画面中のURLを簡単に開きたい方はぜひご利用ください。

## 参考情報

- https://herdr.dev/ja/docs/plugins/
