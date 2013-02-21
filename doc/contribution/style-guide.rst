#################################
ドキュメントスタイルガイド
#################################

基本的なrst文章の書き方については以下が参考になります。

* `Sphinxドキュメント — Sphinx documentation <http://sphinx-users.jp/doc10/contents.html>`_
* `逆引き辞典 :: ドキュメンテーションツール スフィンクス Sphinx-users.jp <http://sphinx-users.jp/reverse-dict/index.html>`_
* `Documentation style guide — Style guide for Sphinx-based documentations 0.1dev documentation <http://documentation-style-guide-sphinx.readthedocs.org/en/latest/style-guide.html>`_
* `Sphinxでドキュメントを書くためreST記法に入門した - kk_Atakaの日記 <http://d.hatena.ne.jp/kk_Ataka/20111202/1322839748>`_
* `Sphinxを使ってプログラムドキュメントを楽しく書こう - maru source <http://blog.h13i32maru.jp/blog/2012/05/29/sphinx-rest/>`_


用語の統一について
=======================

いくつか利用頻度の高い用語については、統一した記法を用います。

.. glossary::

   Buster.JS
   	`Buster.JS`_ のテスティングフレームワーク自体を示す。
   	公式サイトの記法と同じ、 "Buster.JS" という表記に統一する
   	
   	``|Buster.JS|`` という置換記法を利用する事で用語集へのリンクをつけたものに置換される

   buster.js(設定ファイル)
   	Buster.JSで用いる ``buster.js`` というファイル名の設定ファイルの事。
   	"buster.js設定ファイル" 又は" ``buster.js`` 設定ファイル "という表記を使う
   	
   	``|buster.js|`` という置換記法を利用する事で用語集へのリンクをつけたものに置換される。
   	buster.js(設定ファイル)と毎回書くのは冗長なため ``|buster.js|`` と書くのを推奨する。


サンプルのコード表示について
===============================

`azu/busterjs-kumite`_ に置かれているサンプルコードを参照する場合は、
以下のような形で、サブモジュールにおいてあるコードを読み込んで利用する。

.. code-block:: rst

  .. literalinclude:: /busterjs-kumite/getting-started/test/simple-node-test.js
    :language: js
    :linenos:

コードを読み込み表示する際に ``:linenos:`` で行番号を表示させる場合でも、
ファイルがいつ変更されるかわからないため、直接その行番号に対して言及するのはできるだけ避けるようにする。
1行目等、殆ど変わることがない場合はその限りではない。

直接コードを参照する場合、または `azu/busterjs-kumite`_ 以外のコードを参照する場合は
コードディレクティブを用いて表示する。
その際に、シンタックスハイライトが出来る言語であるなら、正しく指定する

.. code-block:: rst

	.. code-block:: js
	
		var code = "サンプルコード";


Buster.JS ドキュメントへのリンクについて
===========================================

`Buster.JS <http://docs.busterjs.org/en/latest/>`_  のドキュメントに対してリンクを張る場合、
intersphinx が利用できる場合(ラベルが設定してある)は

::

    :ref:`buster:buster-test-reporters`

のように、 refを使ったリンクにすることを推奨します。
Buster.JSのドキュメントのdomainには ``buster`` を割り当ててあるので、

::

     :ref:`buster:ラベル名`

という感じで、Buster.JSのドキュメントに対して参照を貼ることができます。

* `sphinx.ext.intersphinx – 他のプロジェクトのドキュメントへのリンク — Sphinx 1.1 (hg) documentation <http://sphinx-users.jp/doc11/ext/intersphinx.html#sphinx.ext.intersphinx>`_
* `2012/12/08 intersphinxを使おう - #sphinxjp アドベントカレンダー2012 - 清水川Web <http://www.freia.jp/taka/blog/how-to-use-intersphinx/index.html#id2>`_

ラベルが見つからない場合は、無理してやるよりかは通常のURLリンクで問題ありません。

.. _`Buster.JS`: http://busterjs.org/
.. _`azu/busterjs-kumite`: https://github.com/azu/busterjs-kumite