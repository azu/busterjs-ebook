==================================
簡単なテストを動かしてみよう
==================================

設定ファイル-buster.js
=====================

Buster.JSではコンフィグファイルを作成し、そこへテストで使用するソースファイルやテストファイルの読み込みの定義や
ブラウザ、Node環境どちらで実行するテストなのかなどを指定する必要があります。

デフォルトでは ``buster.js`` というファイル名のjsファイルがコンフィグファイルとして利用されます。

以下のような ``Buster-Project`` というディレクトリ直下に ``buster.js`` ファイルが有る場合はそれが利用され、
spec または test ディレクトリに ``buster.js`` ファイルが置かれている場合はそちらが利用されます。

::

    Buster-Project
    ├── buster.js
    ├── spec/
    │   └── buster.js
    └── test/
        └── buster.js
        
通常はtestかspec ディレクトリに置いておくのが良いですが、任意のディレクトリにおいて
``buster test`` の ``--config`` オプションで任意の場所にある設定ファイルのパスを指定して利用できます。

.. code-block:: bash

    $ buster test
    // test/buster.js or spec/buster.js
    
    $ buster test --config path/to/buster.js
    // 任意の場所の設定ファイルを指定できる

シンプルなNodeテストを動かしてみよう
=====================================

使用するサンプルは以下のリポジトリにまとめられているので ``git clone`` するなどして取得して下さい

* `azu/busterjs-kumite`_
* ``git clone https://github.com/azu/busterjs-kumite.git``

.. seealso::
     
     `busterjs-kumite/getting-started at master · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/master/getting-started>`_
        ``cd `git rev-parse --show-toplevel`/getting-started``

今回の動かすサンプルは ``getting-started/`` ディレクトリにあります。
まずは動かしてみましょう。

:doc:`/doc/introduction/installation` を参考にBuster.JSがインストール済みならば、
次のように ``getting-started/`` ディレクトリで ``buster test`` コマンドを叩くことで
テストが実行されテスト結果が表示されます。

.. code-block:: bash

    $ cd getting-started/
    $ buster test
    My Test Case: ....
    1 test case, 4 tests, 4 assertions, 0 failures, 0 errors, 0 timeouts

``getting-started/`` ディレクトリ直下には ``buster.js`` 設定ファイルが置かれているので、
今回は ``--config`` オプションで設定ファイルのパスを指定しなくても自動で読み込まれます。

::

    ./getting-started/
    ├── ./buster.js
    └── ./test
        └── ./test/simple-node-test.js

``buster test`` によりテストが実行が確認出来たので、 ``buster.js`` 設定ファイルの中身を見ていきます。

:file: /busterjs-kumite/getting-started/buster.js
.. literalinclude:: /busterjs-kumite/getting-started/buster.js
  :language: js

BusterJSでは `JsTestDriver <http://code.google.com/p/js-test-driver/>`_ の ``jsTestDriver.conf`` のように
設定ファイルを読み込んでテストを実行しますが、 ``buster.js`` は名前の通り設定ファイルもJavaScriptファイルで定義されます。

今回の設定ではnode環境で、testディレクトリにある ``*-test.js`` にマッチするテストファイルを読み込んで実行するという定義がされています。
testディレクトリ以下には ``simple-node-test.js`` しか無いので、そこに書かれているテストが実行されます。

:file: /busterjs-kumite/getting-started/test/simple-node-test.js
.. literalinclude:: /busterjs-kumite/getting-started/test/simple-node-test.js
  :language: js
  :linenos:

1行目は ``env : "node"`` の時に必要なだけなので、ブラウザテストの場合はなくても問題ありません。

``buster.testCase`` 内で それぞれ以下のような形でテストケースを定義して使います。

::
    
    "テスト名" : function(){
        // テストの中身
    }

Buster.JSのアサーション関数の多く [#assertion]_ は ``assert/refute`` 以下になっています。

``assert`` と ``refure`` は対になる関係で、
assertは ``assert(true);`` ならテストがパスされ、
refuteは ``refute(false);`` ならテストがパスされます。

.. important::  **assert <-> refute**

``assert/refute`` はそれぞれ同様のメソッド(equalsやsame等)を持っているので、
この２つのアサーション関数を利用してテストを書いていくことになります。

.. _`azu/busterjs-kumite`: https://github.com/azu/busterjs-kumite
.. [#assertion] BDDのシンタックスやモック関連等では異なる事がある