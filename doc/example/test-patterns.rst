================================
テストを補助する機能について
================================

.. seealso:: 

  `busterjs-kumite/test-patterns at develop · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/develop/test-patterns>`_
    ``cd `git rev-parse --show-toplevel`/test-patterns/`` サンプルコード

setup/teardown
===============================

setUp/tearDownはそれぞれのテストの 実行前(setup)/実行後(teardown) に実行する関数を登録できます。
多くのテストフレームワークで同様の機能が提供されています。

:file:`/busterjs-kumite/test-patterns/test/setup-teardown.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/setup-teardown.js
  :language: js

上記の例では、 ``"one"`` と ``"two"`` それぞれの前後でsetUp/tearDownが実行されています。

テスト内で共通のプロパティを使いたい場合は、 ``this`` にプロパティを追加して利用できます。
ここでは、 ``this.i`` を実行前に値を設定して、実行後に削除しています。
そのため、 ``"one"`` の中で ``this.i`` の値を変更しても、 ``"two"`` には影響がでないようになっています。

Test case contexts
===============================

Buster.JSでは一つのtestCase内にコンテキストを複数持つことができます。

:file:`/busterjs-kumite/test-patterns/test/testcase-context.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/testcase-context.js
  :language: js

上記のコードだと、test #1が所属するコンテキスト、test #2が所属するのコンテキスト、test #3が所属するの3つがあることが分かります。
コンテキストにまとめることで、一つのtestCase内でもテストを一定のグループにまとめることができるようになっています。

コンテキストにはそれぞれ、setUp/tearDownを書くことができ、そこで書いたsetUp/tearDownはそのコンテキスト以下のテスト時に使われます。

また、 :doc:`/doc/console/reporters` によってはコンテキストを認識して見やすい形で出力してくれる場合もあります。

非同期テスト
===============================

Buster.JSでは非同期のテストもサポートされている。
非同期テストの手法は大きく分けて3種類用意されている

* Callback done flag - 引数doneの実行を終了フラグとする
* Promiss - Promissオブジェクトをreturnする
* 非同期をMock/Stub/Spy/Fakeなどを使い同期的にする

Callback done flag
--------------------------------

まずは、一番最初のケースについて。

下記のように、通常のテストにsetTimeout(非同期の動作)を混ぜてしまうとがsetTimeoutの中身を実行する前にテストが終了してしまい、
assertが一つもないテストという扱いになってしまいます。

..  code-block:: js

  "test not asynchronous" : function(){
      setTimeout(function(){
          assert(true);
      }, 100);
  }
  // ✖ My thing test not asynchronous
  // No assertions!


これを回避するためには、テストに指定する関数に引数を受け取るようにします。

その引数(下記ではdone)が実行されるまで、テストは終了されないようになるため、非同期の動作を含んだテストを実行することができます。

:file:`/busterjs-kumite/test-patterns/test/async-test.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/async-test.js
  :language: js

Promiss
--------------------------------


// Deferred tests
===============================

Mock/Stub/Spy
===============================

Sinon.JS
-------------------------------

@kyo_ago

.. code-block:: js

    module.exports = sinon;
    module.exports.spy = require("./sinon/spy");
    module.exports.stub = require("./sinon/stub");
    module.exports.mock = require("./sinon/mock");
    module.exports.collection = require("./sinon/collection");
    module.exports.assert = require("./sinon/assert");
    module.exports.sandbox = require("./sinon/sandbox");
    module.exports.test = require("./sinon/test");
    module.exports.testCase = require("./sinon/test_case");
    module.exports.assert = require("./sinon/assert");