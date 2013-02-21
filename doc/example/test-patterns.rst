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

.. _test-case-contexts:

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

.. _deferred_tests:

// Deferred tests
===============================

Deferred testsとはDeferredを使ったテストという意味ではなくて、そのテストそのものを延期することを示しています。
延期されたテストは、テスト実行時に実行されない用になります。
 
テスト名を//から始める事でそのテストは Deferred test になります。
 
 
:file:`/busterjs-kumite/test-patterns/test/deferred-test.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/deferred-test.js
    :language: js

実行するとDeferred された有無も表示されます。
コンソールの場合は、 `Deferred - Travis CI <http://travis-ci.org/#!/azu/busterjs-kumite/builds/1981095/L412>`_ のような感じで出力されます。

また、 :ref:`test-case-contexts` などで、そのコンテキストにDeferred testsのみしかない場合は、
そのコンテキストに存在するsetUp/tearDownは実行されないようになっています。

そのため、上記の例では ``buster.log("doesn't call");`` は呼ばれない事になります。

.. _async_tests:

非同期テスト
===============================

Buster.JSでは非同期のテストもサポートされている。
非同期テストの手法は大きく分けて3種類用意されている

* Callback done flag - 引数doneの実行を終了フラグとする
* Promise - Promiseオブジェクトをreturnする
* 非同期をMock/Stub/Fakeなどを使い同期的にする

.. _callback-done-flag:

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

その引数のコールバック(下記ではdone)が実行されるまで、テストは終了されないようになるため、非同期の動作を含んだテストを実行することができます。

:file:`/busterjs-kumite/test-patterns/test/async-test.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/async-test.js
  :language: js

Promise
--------------------------------

Promiseとは何かについては下記を参照して下さい。

* `Promises/A - CommonJS Spec Wiki <http://wiki.commonjs.org/wiki/Promises/A>`_
* `"promise" による JavaScript での非同期プログラミング - Internet Explorer ブログ (日本語版) - Site Home - MSDN Blogs <http://blogs.msdn.com/b/ie_jp/archive/2011/10/05/10220180.aspx>`_

`CommonJSのPromiseに沿ったシンプルなオレオレDeferred書いた - Cheese Pie <http://d.hatena.ne.jp/cheesepie/20111112/1321064204>`_ より:: 

    Promiseは"未完"な状態から始まり、"未完"あるいは"完了"あるいは"失敗"の状態へ遷移すること
    Promiseは"then"という名前の関数をもったオブジェクトを返すこと
    "then"メソッドはPromiseを返しチェインできるようにし、またコールバックでエラーが起きた場合は"失敗"の状態へ遷移すること

Buster.JSではPromiseを使ったテストをサポートしていて、テストでpromiseオブジェクトを返すと、
そのpromiseがresolveするまでテストの終了を待ってくれます。

これは :ref:`callback-done-flag` での引数のコールバックdoneが実行されるまでテストが終了されないのと似ています。

実際のPromiseを使った例を見てみましょう。

Promiseは ``then`` メソッドを持ったオブジェクトであればいいので、特別なライブラリを使わなくても手動で実現することもできます。
下記の例では手動で実現するパターンと、Buster.JSに含まれているPromiseの実装である、 `when.js <https://github.com/cujojs/when>`_ を
使った2つの例が入っています。

:file:`/busterjs-kumite/test-patterns/test/promise-test.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/promise-test.js
  :language: js

どちらも、基本的に同じ事をやっていて、テスト関数内でreturnで ``then`` メソッドを持ったPromiseオブジェクトを返してあげる。
そのPromiseオブジェクトのresolveをする前に、テストしたい内容をassertしてあげて、resolveするとテストが終了します。

基本的にはwhen.jsを使った方がシンプルな記述で済みます。

Mock/Stub/Spy/Fake
===============================

Mock/Stub/Fakeを使って非同期のコードを同期的にテストしたり、
依存関係などを緩和してテストを実行したり、テストを楽にしてくれます。

Sinon.JS
-------------------------------

.. todo::

  @ryuoneさん、@kyo_ago さんがもっとくわしく書いてくれる

Buster.JSには `Sinon.JS`_ が統合されています。
`Sinon.JS`_ はをMock/Stub/Spy/Fake Timer/Fake XHRなどのテストを補助する機能が含まれています。

以下に、Sinon.JSの主要な機能を簡単に使ったテストを書いてみます。

:file:`/busterjs-kumite/test-patterns/test/sinon-test.js`

.. literalinclude:: /busterjs-kumite/test-patterns/test/sinon-test.js
    :language: js

コードを見てみると、
Sinon.JSを読みこまなくても ``this.spy,this.mock,this.sandbox`` などで参照できていることが分かります。
また、 ``assert.calledOnce`` のように、いくつかSinon.JSを前提とされているようなassertionが存在してることが分かります。

このように、Buster.JSとSinon.JSでは作者が同じ事もあって、Sinon.JSを使ったテストが多少楽に出来るようになっています。

.. _`Sinon.JS`: http://sinonjs.org/