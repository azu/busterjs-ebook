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

Error.stackの情報
================================================================

.. todo:: 

	-f/--full-stacks や スタックトレースについて書く

buster staticとWebStormのデバッグ連携
================================================================
