========================================================
Test reporters
========================================================

Buster.js のテスト結果を出力する方法を決めるreportersという機能がある

reportersの種類
========================

現在利用できるreportersの種類は下記に書かれているものと、独自に定義したreporterを利用できます。

* `Test reporters`_
* `buster-test/lib/buster-test/reporters at master · busterjs/buster-test · GitHub <https://github.com/busterjs/buster-test/tree/master/lib/buster-test/reporters>`_

デフォルトでは、 ``dots`` が使用され、``buster-test`` の ``-r/--reporter`` オプションで指定することができます。
``-r/--reporter`` オプションの詳細は、コマンドライン上で下記のようにして見ることができます。

.. code-block:: bash

   $ buster test -h reporters
   

独自に定義したreporterを使う
===============================

reporter機能は自分で作成したものを利用する事ができます。

.. seealso:: 

  `busterjs-kumite/reporters at develop · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/develop/reporters>`_
    ``cd `git rev-parse --show-toplevel`/reporters``

まずは、custom reporterを作成します。

:file:`/busterjs-kumite/reporters/reporter/myReporter.js`

.. literalinclude:: /busterjs-kumite/reporters/reporter/myReporter.js
  :language: js

とりあえず、このcustom reporter(myReporter)を使ってテストを実行してみます。

``buster server`` を行なって、ブラウザをキャプチャさせた状態で、
``busterjs-kumite/reporters`` に、test.sh というファイルが用意されているのでそれを実行します。

.. note:: 

  Windowsはshの方法は実行できないかも

.. code-block:: bash

  $ sh test.sh                        
  Browser : Firefox 13.0.1, OS X 10.7 (Lion)
    <My Test Case>
      <Context>
        <Context NEST>
          ✎ 1 TEST Three
        ✔ 2 TEST TWO
      ✔ 3 TEST ONE
  Browser : Chrome 20.0.1132.57, OS X 10.7 (Lion)
    <My Test Case>
      <Context>
        <Context NEST>
          ✎ 1 TEST Three
        ✔ 2 TEST TWO
      ✔ 3 TEST ONE

これを、myReporterを使ったテスト結果を実行するには、 ``-r/--reporter`` オプションでmyReporterと指定する必要がありますが、
適当な場所に作っただけではnodeの ``require()`` からは読み込めないため、 ``NODE_PATH`` に myReporter.jsがあるディレクトリを追加して、
``require("myReporter")`` で読み込めるような状態で ``buster test -r myReporter`` して実行します。

つまり、以下のように ``NODE_PATH`` を設定してテストを実行させています。

.. code-block:: bash

  NODE_PATH=reporter/ buster test -r myReporter

Buster.JSの仕組み的に、 `buster-test / reporters.js <https://github.com/busterjs/buster-test/blob/master/lib/buster-test/reporters.js>`_ にて、
``-r/--reporter`` オプションで指定したモジュール(又は ``BUSTER_REPORTER`` の環境変数でも指定可能) を読み込む処理が行われています。

この読み込む処理は、単純に ``require("reporter名")`` しているため、NODE_PATHを使ったモジュールへのパスの通し方以外でも、
``buster test`` 実行前にrequireで読み込めるように準備しておけば良いことになります。

custom reporterの書き方の詳細はドキュメントや既存のreporterが参考になります。

* `Test reporters`_
* `buster.testRunner <http://busterjs.org/docs/test/runner/>`_
* `buster-test/lib/buster-test/reporters at master · busterjs/buster-test · GitHub <https://github.com/busterjs/buster-test/tree/master/lib/buster-test/reporters>`_

.. _`Test reporters` http://busterjs.org/docs/test/reporters/
