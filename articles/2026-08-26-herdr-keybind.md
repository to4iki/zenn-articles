---
title: "herdrのキーバインドをmacOS標準のように簡略化する"
emoji: "👻"
type: "tech"
topics:
  - "herdr"
  - "ghostty"
  - "wezterm"
published: true
---

ターミナルマルチプレクサに関して herdr を利用している。

https://herdr.dev/

tmux を長らく利用していたので、prefix の後に何か入力するというのは慣れているのだが、考えてみると prefix 経由しなくても良いのにと思うケースがある。
例えば、herdrの新規 workspace 作成に関して、`prefix + shift + n` ではなく、macOS のよくあるキーバインドである `cmd + n` で実行ができたら嬉しい。

実は、herdr側で `cmd + n` を直接割り当てることもできるし、公式は prefix 無しなら `ctrl + alt` を勧めている。[^1]

[^1]: https://herdr.dev/docs/keyboard/#going-prefix-free

ただ、寄せたいのは macOS でよく使う `cmd + {任意のキー}` ショートカットであり、かつ herdr 側の設定は極力触らず config を薄くしたかったので、外側の Ghostty で `cmd` を prefix 列に翻訳して送ることにした。

副作用として Ghostty 標準の新規ウィンドウ作成（`cmd + n`）は使えなくなるが、herdrを常に使う想定なので許容している。

## Ghostty Custom Keybindings

Ghostty にはカスタムのキーバインド設定がある。これを使うことで以下のように herdr コマンドをプロキシできる。

https://ghostty.org/docs/config/keybind

```sh
# prefix は ctrl+t
keybind = cmd+n=text:\x14N
```

必要最低限の頻出のコマンドだけ、`cmd + {任意のキー}` へ寄せた。

https://github.com/to4iki/dotfiles/blob/61136ea7e28fda952f270fdcddc04551dcec45d6/dot_config/ghostty/config#L26-L32

特に、パネル移動に関して `cmd + o` を連打で行き来できるのは便利。`last_pane` はデフォルトでは未設定なので、herdr側ではこの1行だけ足している。

```toml:.config/herdr/config.toml
last_pane = "prefix+o"
```

ちなみに WezTerm でも `SendString` で同じことができる。[^2]

[^2]: https://wezterm.org/config/lua/keyassignment/SendString.html

```lua:.config/wezterm/wezterm.lua
{ key = 'n', mods = 'CMD', action = wezterm.action.SendString '\x14N' }
```
