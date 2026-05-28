---
title: "AIエージェントの作業内容をHTMLでリアルタイム出力する"
emoji: "🖨️"
type: "tech"
topics:
  - "ai"
  - "claude"
  - "skills"
published: true
---

## HTML の有用性

Anthropic のAIエージェントの出力フォーマットとして Markdown ではなく HTML を活用するブログでは、メリットを以下のように示している。

https://claude.com/blog/using-claude-code-the-unreasonable-effectiveness-of-html

- 情報密度: HTML は Markdown に比べて格段にリッチな情報を伝えられる
- 視覚的な明瞭さと読みやすさ: Claude が構造を視覚的に整理し、タブ、イラスト、リンクで快適にナビゲートでき、モバイル対応も可能
- 共有のしやすさ: HTMLファイルをアップロードすれば、リンクを簡単に共有でき、Markdown よりもレンダリングが綺麗
- 双方向のインタラクション: デザインを調整するためのスライダーやノブを追加させたり、アルゴリズムの異なるオプションを試して結果を見たりできる
- データの取り込み: ファイルシステム以外にも、Claude Code は MCP（Slack、Linearなど）、Webブラウザ（Claude in Chrome経由）、git履歴を使って追加のコンテキストを得ることができる。記事で紹介されている、HTML を使用するユースケースは以下の通り
  - 仕様、計画、探索 - コードレビューと理解
  - デザインとプロトタイプ
  - レポート、リサーチ、学習
  - カスタム編集インターフェース

[AI時代のリッチテキスト形式（RTF）](https://blog.lai.so/ai-rich-text-format/) でも示しているように、

> 「HTML is the new Markdown」という言い方は、対立構図として読むとどうしても違和感が残ります。 HTML は Markdown の新版ではありませんし、 両者は別の役割を担うようになった、というのが本記事の結論と発見です。

対立構図ではなく、最終成果物として人間が閲覧する用途では HTML、AIや人間が編集・転送して次のコンテキストへ運ぶための中間素材としては Markdown というように、それぞれ使い分けるのが趣旨となる。
本記事では、成果物としての HTML 出力に関して、レポート観点を明らかにしたかったのでスキルを作成することにした。

## implementation-notes スキル

Anthropic の元記事を書いた Thariq さんがXでポストしていたプロンプトをベースにし、リッチ表現が可能なHTMLでAIエージェントの作業結果をトレースする。

https://x.com/trq212/status/2056415973125796184?s=20

ポストされていたプロンプト:

```md
Implement <SPEC>. As you work maintain a running implementation-notes.html file that captures anything I should know about how the implementation diverges from or interprets the spec, including:

- Design decisions: choices you made where the spec was ambiguous
- Deviations: places where you intentionally departed from the spec, and why
- Tradeoffs: alternatives you considered and why you picked what you did
- Open questions: anything you'd want me to confirm or revise
```

作成したスキル:

元のプロンプトに加えて、検証結果の項目を追加したり、CSS による配色の制御、レビュー支援のインタラクションに JavaScript を適宜利用するように追記している。

また、生成物を `.implementation-notes/` のようなディレクトリに日付付きで蓄積するのも考えたが、フィーチャーの開発時には worktree を使うことが多く、1つのworktreeにつき、1つの `implementation-notes.html` というシンプルな構造で充分と判断した。
なので、保存したい場合には、ローカルの適当なディレクトリに手動コピーする。

https://github.com/to4iki/skills/tree/main/implementation-notes

今のところ個人の使い所としては、Codex や Claude Code の Planモード 後や、[suposuperpowers:brainstorming](https://github.com/obra/superpowers/tree/main/skills/brainstorming) スキル や [grill-me](https://www.aihero.dev/skills-grill-me) スキル で詳細を詰めたそれなりの規模の実装する際に、`/implementation-notes XXX機能の実装を開始して` のように指示している。すると、こんな感じで、作業状況や苦労した点を可視化した HTML がリアルタイムで書き起こされる。

![](/images/implementation-notes.png)

特に効果的なのは「途中経過の共有」と「意思決定の振り返り」で、Markdown でログを追う場合は時系列の把握に寄りやすいが、HTML だと「設計判断」「逸脱理由」「未解決事項」を視覚的に分けて見られるため、レビュー時の確認コストを下げやすい。

Markdown と比較しトークンをそれなりに使うし出力の時間もかかるが、視認性が高いので、特にチームに状況を共有するケースにおいて使ってみようと思う。

## 参考

HTMLの出力例:
- https://thariqs.github.io/html-effectiveness
- https://nowokay.hatenablog.com/entry/2026/05/09/164006
