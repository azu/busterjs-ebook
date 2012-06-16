========================================================
テストの実行環境について
========================================================

|Buster.JS| では大きく分けて、２つの実行環境でテストを実行できます。

#. Node.js環境
#. ブラウザ環境

:doc:`/doc/introduction/getting-started` ではNode.js環境でのテストを実行しました。

今回はそれぞれで実行する |buster.js| とテストの書き方と、両方で実行できるハイブリッドなテストファイルを見ていきます。

.. seealso:: 

	`busterjs-kumite/config-patterns at develop · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/develop/config-patterns>`_
		``cd `git rev-parse --show-toplevel`/config-patterns``

.. _config-patterns-config-label:

:file:`/busterjs-kumite/config-patterns/buster.js`

.. literalinclude:: /busterjs-kumite/config-patterns/buster.js
  :language: js

Node環境の場合
======================

上記の |buster.js| には、Nodeとブラウザの二種類のテスト設定が書かれています。
Buster.JSでは一つの |buster.js| に複数の設定ファイルを書くことができるようになっています。

.. todo:: 詳細はconfigの項に書く

``env : "node"`` と設定されているNode環境向けのテストを見ていきます。

テストを実行するには ``config-patterns`` ディレクトリにいる状態で
 ``buster test`` の ``-e/--environment`` オプションをnodeに指定して実行します。

.. code-block:: bash
  
  $ cd config-patterns
  $ buster-test -e node
  type: .
  Multi-environment: .
  Skipping Multi-environment dependent on dom context, unsupported requirement: DOM
  
  2 test cases, 2 tests, 2 assertions, 0 failures, 0 errors, 0 timeouts

``-e/--environment`` オプションを指定すると、 |buster.js| で envが該当してるコンフィグのテストを実施します。
つまりこの場合は、　``-e node`` と指定したので :ref:`config["node tests"]<config-patterns-config-label>` のテストが実行されます。

hybrid-test.js はとりあえず置いておいて、以下のテストファイルが読みこまれて実行されます。

:file:`/busterjs-kumite/config-patterns/test/node-test.js`

.. literalinclude:: /busterjs-kumite/config-patterns/test/node-test.js
  :language: js

テスト自体は :doc:`/doc/introduction/getting-started` と代わり映えしないので、次はブラウザ環境のテストを見ていきます。

ブラウザ環境の場合
====================

Buster.JSでブラウザを使ったテストを行う場合は、Node環境のテストと違って少し準備が必要です。

大まかな手順を書くと以下のようになります。

#. ``buster server`` を立ち上げる
#. テストしたいブラウザで http://localhost:1111/capture へアクセスする
#. ``buster test`` でテストを実行する

.. code-block:: bash

  $ buster server
  buster-server running on http://localhost:1111
  $ open http://localhost:1111/capture # もしくは手動で開く
  $ buster test -e browser # configがbrowserしかない場合は-eは指定しなくてもいい

実際に ``config-patterns`` ディレクトリのテストを動かしてみましょう。

.. code-block:: bash

  $ buster server
  $ open http://localhost:1111/capture
  $ buster test -e browser
  Chrome 19.0.1084.56, OS X 10.7 (Lion): 
  Firefox 13.0.1, OS X 10.7 (Lion):Ï
  4 test cases, 6 tests, 8 assertions, 0 failures, 0 errors, 0 timeouts

ブラウザテストを実行すると、それぞれのブラウザで実行したテストの合計が表示されているので、
実際に書かれている テストケースの数 * ブラウザ数 の結果が表示されています。

:ref:`config["browser tests"]<config-patterns-config-label>` の中身を見ていきます。

:file:`/busterjs-kumite/config-patterns/test/browser-test.js`

.. literalinclude:: /busterjs-kumite/config-patterns/test/browser-test.js
  :language: js

Node環境とは違い、最初に手動で ``var buster = require("buster");`` する必要はありません。
また、実際にブラウザで動作するテストのため、DOM APIを使ったテストを実行する事ができます。

Buster.JSではassert.tagNameのようにDOMに関するassertionメソッドも用意されています。

ハイブリッドなテスト
===================

最後にどちらの :ref:`config<config-patterns-config-label>` でも読み込まれている、 ``hybrid-test.js`` について見ていきます。

このテストファイルでは、Node、ブラウザ どちらから読み込んでも動作し、また特定の環境でのみ評価されるようなテストも書かれています。

:file:`/busterjs-kumite/config-patterns/test/hybrid-test.js`

.. literalinclude:: /busterjs-kumite/config-patterns/test/hybrid-test.js
  :language: js

``requiresSupportFor`` に判定するためのオブジェクトを設定して、その中身がtrueなら
そのコンテキスト(この場合は"dependent on dom context"の中)に書かれているテストが実行されます。

つまり、"dependent on dom context" の中に書かれているテストはNodeの場合documentオブジェクトがないため、評価されません。
実際に実行すると

.. code-block:: bash

  $ buster test -e browser && buster test -e node
  Firefox 13.0.1, OS X 10.7 (Lion):                
  2 test cases, 3 tests, 4 assertions, 0 failures, 0 errors, 0 timeouts
  type: .
  Multi-environment: .
  Skipping Multi-environment dependent on dom context, unsupported requirement: DOM
  
  2 test cases, 2 tests, 2 assertions, 0 failures, 0 errors, 0 timeouts
  
Nodeの方ではスキップされているため、結果に表示されるテスト数も異なる事が分かります。

このように、Buster.JSでは一つの |buster.js| に複数の設定を書いたり、
一つのテストファイルに複数の実行環境を場合分けして行うのを簡単にできるような仕組みが備わっています。

どの |buster.js| を使うかは ``buster-test`` の ``-c/--config`` オプションで指定できるので、
|buster.js| 自体を分割してやる方法でもいいと思います。

ブラウザ内でも使える機能が異なる事は多いため、 ``requiresSupportFor`` で実行されるブラウザのfeature detectをして、
実行されるテストを分けることでテストが複雑化し過ぎないようにする事もできます。
