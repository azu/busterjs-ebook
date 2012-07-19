============================
エディタ対応
============================

Buster.JSのエディタ対応について

WebStorm
==================

WebStorm では自分が公開してる、Buster.JSについてのJSDocファイルを利用できます。

Webstormの　:menuselection:`Settings --> JavaScript --> Libraries` に下記のjsファイルを追加する事で、
BusterJSのメソッドを補完候補に加えたり、IDE上から簡単なドキュメント(Quick Lookup)を参照できます。

* `azu/BusterJSDoc <https://github.com/azu/BusterJSDoc>`_
* `JavaScript Testing FrameworkのBusterJSを使う <http://azu.github.com/slide/Kamakura/busterJS.html#slide27>`_

.. todo:: 

  ConfigurationやExternal Toolsとの連携方法についても書く

Emacs
==================

Emacsは作者の一人が作られてるbuster-mode.elが公開されています。

* `buster-mode in Buster - Gitorious <https://gitorious.org/buster/buster-mode>`_
* `An introduction to Emacs Lisp <http://cjohansen.no/an-introduction-to-elisp>`_

また別の方が、Buster.JSのYasnippetsを公開しています。

* `magnars/buster-snippets.el <https://github.com/magnars/buster-snippets.el>`_

Vim
==================

Vimはvim-busterというものが公開されています。
Vim上からテストの実行やキーワードのハイライトなどに対応しています。

* `malclocke/vim-buster <https://github.com/malclocke/vim-buster>`_

TextMate
==================

TextMateのbundleが公開されています。

* `magnars/buster.tmbundle <https://github.com/magnars/buster.tmbundle>`_