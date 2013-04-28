==========================================================
テストとデバッグ
==========================================================

Buster.JSでテストを行いつつデバッグする方法

コンソールログと失敗したテストの見方について

コンソールにログを出力する
===========================================

コンソールへのログ出力には ``buster.log(message)`` を利用できます。
これは ``buster.console.log()`` と同等で、buster.consoleにはよく見かけるコンソールAPIが存在しています。

.. code-block:: js

    buster.log(buster.console);
    // => コンソールにフォーマットされて出力される
    [LOG] {
          contexts: { log: [undefined] },
          debug: function () {},
          error: function () {},
          format: function () {},
          level: "debug",
          levels: ["error", "warn", "log", "debug"],
          listeners: { log: [function () {}] },
          log: function () {},
          logFunctions: true,
          warn: function () {}
        }

``buster.log(message)``  による出力は `formatio <https://github.com/busterjs/formatio>`_ により整形されて出力されます。
そのため、オブジェクトならオブジェクトをある程度展開した状態で出力されるようになっています。

DOMオブジェクトも同様に、HTMLタグの状態に展開されて出力されるようになっています。

.. code-block:: js

    var p = document.createElement("p");
    p.id = "sample";
    p.className = "notice";
    p.setAttribute("data-custom", "42");
    p.innerHTML = "Hey there, here's some text for ya there buddy";
    
    buster.log(p);
    // => [LOG] <p id="sample" class="notice" data-custom="42">Hey there, here's so[...]</p>
    // 20文字で短縮される

.. seealso:: 

	`busterjs/formatio <https://github.com/busterjs/formatio>`_
		Pretty formatting モジュール

また、デフォルトではウェブで一般に使われるコンソールAPIの ``window.console`` は ``buster.console`` にバインディングされています。
つまり、 ``console.log`` などの基本的なコンソールAPIはそのまま使うことが可能です。

.. code-block:: js

	assert.same(console.log, buster.console.log);// pass

このバインディングを無効にするには、 ``buster test`` コマンドのオプションに ``-o, --release-console`` オプションを指定することで行えます。
(ブラウザ側のコンソールに出力されるようになります)

.. seealso:: 

	`Test options — Buster.JS 0.6.2 documentation <http://docs.busterjs.org/en/latest/modules/buster-test/options/>`_
		buster-test option documents

失敗したテストを眺める
================================================================

.. seealso:: 

	`busterjs-kumite/debug at develop · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/develop/debug>`_
		``cd `git rev-parse --show-toplevel`/debug`` 

上記のサンプルで |buster.js| のconfigグループの"Failure tests" を指定して実行する事で見られます。
`-g/--config-group <http://docs.busterjs.org/en/develop/modules/buster-test/options/>`_ オプションを指定することで特定のconfigグループを実行できます

.. code-block:: sh

	buster test -g "Failure tests"

大きく分けてテストの失敗には3つぐらいあるので、それを見ていきます。

:file:`/busterjs-kumite/debug/test/failure-test.js`

.. literalinclude:: /busterjs-kumite/debug/test/failure-test.js
  :language: js

実行結果　: 

.. figure:: /_static/failure-test.png
	:alt: 実行結果

Assertion failure
--------------------------------------------------------

これが一番遭遇しやすいと思いますが、 `buster-assertions`_ のassertメソッドでテストを行い、
その結果がfailとなる場合の事です。

テストに失敗すると言った場合は大体これを示す事になると思います。

上記の例ではassertの結果がfailの場合と、assertの使いかたが間違ってる場合の2種類載せています。
どちらも、何故failになったのかのメッセージが表示されています。

Async failure
--------------------------------------------------------

Async failure としましたが、実行してみるとわかるように ``No assertions!`` となり、
一つもassertが実行されなかったためにfailとなっていることが分かります。

多くの場合は非同期なメソッドのコールバックでassertを行う場合に発生すると思います。

解決方法としては、 :ref:`async_tests` で紹介されている、非同期なメソッドをテストする仕組みを利用するか、
そもそもまだテストケースとして完成していないなら、 :ref:`deferred_tests` としておくべきです。

Exception failure
--------------------------------------------------------

これは名前の通り、テスト内でJavaScriptの例外が発生してしまったため、Errorとなっています。
表示も ``Failure`` ではなく ``Error`` となっています。

もし、例外が発生したかをテストしたいならば、 ``assert.exception()`` 等の
例外をキャッチしてテストするassertionを利用しましょう。

Error.stackの情報
================================================================

テストが失敗した結果にはメッセージ以外にもJavaScriptのスタックトレースが表示されます。
ブラウザでJavaScriptのスタックトレースを見た事がある方は分かると思いますが、
スタックトレースはJavaScriptエンジンによって形式が異なります。

* `occ/TraceKit · GitHub <https://github.com/occ/TraceKit>`_
* `strawman:error_stack [ES Wiki] <http://wiki.ecmascript.org/doku.php?id=strawman:error_stack>`_
* `Latest topics > JavaScriptのスタックトレース - outsider reflex <http://piro.sakura.ne.jp/latest/blosxom/webtech/javascript/2010-07-26_stacktrace.htm>`_

.. seealso:: 

	`testacular <https://github.com/vojtajina/testacular/>`_
		testacular は `WebStormからtestacularでテストとデバッグをする方法 <http://efcl.info/2012/1028/res3154/>`_ のような連携をするために、
		スタックトレースの内容を整形する `ErrorFormatter <https://github.com/vojtajina/karma/blob/8638ce16bb142d2f7978c59ee2351a8cf20fde4d/lib/reporter.js#L7>`_ の仕組みが入っています

本来なら、スタックトレースにはテストファイル以外にも |Buster.JS| 自体に関するトレース等も出るはずですが、
デフォルトでは ``(./test/failure-test.js:15:20)`` といったようなテストファイルに関係する部分しか出ていないことが分かります。

::

    Failure: Chrome Failure Test Assertion failure assert.same should fail
    [assert.same] Expected to receive at least 2 arguments
    at Object.buster.testCase.Assertion failure.assert.same should fail (./test/failure-test.js:15:20)

|Buster.JS| には `Stack filter <http://docs.busterjs.org/en/latest/modules/buster-test/stack-filter/>`_ というモジュールがあり、
デフォルトで入っている ``dots`` や ``specification`` といった :doc:`/doc/console/reporters` ではそれによりフィルターされるようになっています。
(testacularのような整形までの仕組みは今のところ入っていませんが、 :doc:`/doc/console/reporters` 等でも実現できるとは思います)

このフィルターを外すオプションもあり、 ``f/--full-stacks`` オプションを指定することでフィルターなしの結果が出力されるようになります。
(通常はあんまり出す必要は無い気もしますが)

.. code-block:: sh

    buster test -e browser -g "Failure tests" -f
    // =>
    Failure: Chrome My Failure Test Assertion failure assert.same should fail
        [assert.same] Expected to receive at least 2 arguments
        at Object.ba.fail (./buster/bundle-0.6.js:1483:25)
        at assertEnoughArguments (./buster/bundle-0.6.js:1252:16)
        at Function.ba.(anonymous function).(anonymous function) [as same] (./buster/bundle-0.6.js:1264:18)
        at Object.buster.testCase.Assertion failure.assert.same should fail (./test/failure-test.js:15:20)
        at asyncFunction (./buster/bundle-0.6.js:6640:19)
        at callTestFn (./buster/bundle-0.6.js:6746:27)
        at ./buster/bundle-0.6.js:790:27
        at ./buster/bundle-0.6.js:790:27
        at p.then (./buster/bundle-0.6.js:71:31)
        at Object.then (./buster/bundle-0.6.js:177:11)

.. todo:: 

	-f/--full-stacks や スタックトレースのドキュメントリンクを追加する(まだない)

以上で、 ``buster.log`` でのログ出力やテストの失敗するケースやエラースタックの内容について簡単に紹介しました。

WebStormのデバッガー連携
===============================

Buster.JS はテストの実行環境として ``buster server`` と ``buster static`` のコマンドがあります。
``buster server`` とは違い ``buster static`` はウェブページとして静的なテストページを用意され、
ブラウザでそのURLにアクセスするとQUnitなどのようにテストが実行されます。

つまり、 ``buster static`` を使った場合は通常のウェブサイトをデバッグする方法が利用できるため、
ブラウザのデバッガーを使ってテストケースのデバッグなども行いやすいです。

それを利用して、 `WebStorm <http://www.jetbrains.com/webstorm/>`_  のJavaScriptデバッガーを使って、
`WebStormからtestacularでテストとデバッグをする方法 <http://efcl.info/2012/1028/res3154/>`_  のようにBuster.JSのデバッグを行うことも可能です。

.. seealso::

    `WebStormのデバッガでBuster.JSのテストをデバッグをする方法 | Web scratch <http://efcl.info/2013/0220/res3198/>`_
        WebStormのJavaScriptデバッガーを使ってデバッグする方法について


.. _`buster-assertions`: http://docs.busterjs.org/en/latest/modules/buster-assertions/
