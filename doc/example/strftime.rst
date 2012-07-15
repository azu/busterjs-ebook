============================================
小さなJSのテストを書いてみよう
============================================

strftime
========

指定形式にフォーマットされた日時を取得できる関数 ``strftime`` を例にテストを書いてみます。

.. seealso:: 

  `busterjs-kumite/strftime at develop · azu/busterjs-kumite · GitHub <https://github.com/azu/busterjs-kumite/tree/develop/strftime>`_
    ``cd `git rev-parse --show-toplevel`/strftime/``
    作成したサンプル
  `Test-Driven JavaScript Development <http://tddjs.com/>`_
     Buster.JSの作者が書いた書籍。strftimeをchapter1,2で扱ってる
  `テスト駆動JavaScript <http://www.amazon.co.jp/dp/4048707868/>`_
    上記の書籍の日本語版

-----------------------
|buster.js| を作る
-----------------------

strftimeは特に環境固有の機能に依存しなくても実現できるため、
Node、ブラウザどちらの環境でも実行できるハイブリッドテストとして定義する。

:file:`/busterjs-kumite/strftime/buster.js`

.. literalinclude:: /busterjs-kumite/strftime/buster.js
  :language: js

Nodeとブラウザ向けの設定をそれぞれ書いておくが、両者の違いとしては
Node向けの設定ではsourcesの指定がなく、代わりにテストファイル自体に読み込むソースを定義している点が異なっている。

:file:`/busterjs-kumite/strftime/test/strftime-test.js`

.. literalinclude:: /busterjs-kumite/strftime/test/strftime-test.js
  :language: js
  :linenos:
  :lines: 1-5

Node、ブラウザ環境 どちらで実行するかは :doc:`/doc/config/patterns` で書かれているように、
``buster test`` 実行時に ``-e/--environment`` オプション指定すればよい。
 
-----------------------
テストの実行
-----------------------

手動で実行する場合は、strftimeディレクトリ直下で ``buster test`` でテストを実行します。

.. code-block:: bash

  $ buster test
  
毎回確認する度に、 ``buster test`` を実行するのは手間なので、ファイルの変更を監視して保存される度に
``buster test`` を実行してくれる ``buster autotest`` コマンドがあります。

.. code-block:: bash

  $ buster-autotest

-----------------------
実装
-----------------------

**strftime.jsの実装**

:file:`/busterjs-kumite/strftime/src/strftime.js`

.. literalinclude:: /busterjs-kumite/strftime/src/strftime.js
  :language: js

**strftime-test.js の実装**

:file:`/busterjs-kumite/strftime/test/strftime-test.js`

.. literalinclude:: /busterjs-kumite/strftime/test/strftime-test.js
  :language: js
  :lines: 6-
  
それぞれのテストごとに、new Dateするのは冗長なので、 ``setUp`` で ``this.date = new Date();`` のようにして行なっておけば、
それぞれのテスト実行前にsetUpが実行されて、 ``this.date`` で参照できるため共通部品がまとめられます。

``assert.same`` は ``===`` の厳密比較演算子での比較結果がtrueならテストがPassされます。

つまり、イメージ的には以下のような感じです。

.. code-block:: js

  // assert.same(Date.formats.Y(this.date), "2009");
  // ==>
  if(Date.formats.Y(this.date) === "2009"){
    // pass test
  }else{
    // fail test
  }

やや、おさらい的な内容でしたが、これで短めなstrftimeの実装は終わりです。
一般的なstrftimeにはもっとformatsがあるので、続けてそれを実装してみるのもいいでしょう。

.. seealso:: 
  
  `tokuhirom/strftime-js <https://github.com/tokuhirom/strftime-js>`_
    テストも書かれてるstrftimeの実装

今回、どのような手順で実装したのかは、このアプリのコミットまとめておいたのでそちらで見られるようにしてあります。

* `Commits · azu/busterjs-kumite <https://github.com/azu/busterjs-kumite/compare/c73114fb972233b5a97e6734e5eb1ecff1ea5b97...1e8444bcb07cd62714c98edc94e9830b0c5dde89>`_

簡単に手順を並べると

#. |buster.js| を書く
#. strftime.js と strftime-test.js を作成して配置
#. strftime.js にインターフェイスだけ(中身は実装してない)を実装
   (先にインターフェイスだけ作ってるのは、テストを書くときにエディタから補完が効くので形だけ作っておいてる)
#. strftime-test.js にテストを実装
#. テストを実行しながら、 strftime.js に中身を実装

というような手順で実装しましたが、これが正解だというものは無いと思うのでまずは書いてみるのがいいでしょう。