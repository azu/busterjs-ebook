============================
デバッグとログ
============================

Buster.JSでテストを行いつつデバッグする方法について

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

|buster.js| のconfigグループの"Failure tests" を実行する事で見られます。

> ``buster test -g "Failure tests"``

大きく分けてテストの失敗には3つぐらいあるので、それを見ていきます。

:file:`/busterjs-kumite/debug/test/failure-test.js`

.. literalinclude:: /busterjs-kumite/debug/test/failure-test.js
  :language: js

実行結果　: 

.. figure:: /_static/failure-test.png
	:alt: 実行結果

Assertion failure
--------------------------------------------------------

これはが一番多いと思いますが、 `buster-assertions`_ のassertメソッドでテストを行い、
その結果がfailとなる場合の事です。

テストに失敗すると言った場合は大体これを示す事になると思います。

上記の例ではassertの結果がfailの場合と、assertの使いかたが間違ってる場合の2種類載せています。
どちらも、何故failになったのかのメッセージが表示されています。

Async failure
--------------------------------------------------------

Async failure としましたが、実行してみるとわかるように ``No assertions!`` のため、
一つもassertが実行されなかったためにfailとなっていることが分かります。

多くの場合は非同期なメソッドのコールバックでassertを行う場合に発生したりすると思います。

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

ブラウザでJavaScriptのスタックトレースを見た方は分かると思いますが、
スタックトレースのJavaScriptエンジンによって異なります。

* `occ/TraceKit · GitHub <https://github.com/occ/TraceKit>`_
* `strawman:error_stack [ES Wiki] <http://wiki.ecmascript.org/doku.php?id=strawman:error_stack>`_
* `Latest topics > JavaScriptのスタックトレース - outsider reflex <http://piro.sakura.ne.jp/latest/blosxom/webtech/javascript/2010-07-26_stacktrace.htm>`_

|Buster.JS| では

.. todo:: 

	-f/--full-stacks や スタックトレースについて書く

buster staticとWebStormのデバッグ連携
================================================================


.. _`buster-assertions`: http://docs.busterjs.org/en/latest/modules/buster-assertions/
