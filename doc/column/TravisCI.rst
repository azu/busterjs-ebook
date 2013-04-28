=================================
Travis CIでブラウザテスト
=================================

`Travis CI`_ はGithubアカウントを使ってログインして利用するCIサービスで、CIしたいプロジェクトを選択すればGithubへpushにhookしてテストが実行されます。
実行するテストの設定ファイルを `.travis.yml <http://about.travis-ci.org/docs/user/build-configuration/>`_ に書いて置くことでどのようなテストを実行するかを設定できます。

テストが失敗したり、失敗してたテストが直った場合はメールで通知などを飛ばすこともできます。
また、テストの成否はコマンドの終了ステータスで行われていて、 ``0`` なら成功、それ以外だと失敗というステータスになります。
大抵のテストフレームワーク(or 実行環境)などはちゃんと終了ステータスを返してくれるのでテストの成否は正しく判定できます。

こういうウェブサービスの場合、DOMやXHRなどがないJavaScriptのロジックテストのみしか動かせないように思われてしまいますが、
`Travis CI`_ はFirefoxやPhantomJS(Webkit)を使って実際のブラウザを動かしてテストを行う事ができます。

|Buster.JS| ではブラウザをキャプチャページにアクセスさせればブラウザ上でテストが実行できるため、
``.travis.yml`` にかかれたコマンドのみでテストを走らせられるので `Travis CI`_ でもBuster.JSのブラウザテストが行えます。

実際に `Travis CI`_ で動くサンプルプロジェクトを作ってみましょう。

* .. raw:: html

      <a class="first reference external image-reference" href="http://travis-ci.org/azu/BusterJS_TravisCI"><img alt="https://secure.travis-ci.org/azu/BusterJS_TravisCI.png?branch=master" src="https://secure.travis-ci.org/azu/BusterJS_TravisCI.png?branch=master" /></a>
*  `azu/BusterJS-TravisCI`_

npm package.json
================

まず、Travis CI にはBuster.JSは入ってないので、テストを走らせる前にそれらをTravis CIにインストールさせるようにします。

Buster.JSはnpmで配布されているので、テストの依存関係の設定をnpmの `package.json <http://npmjs.org/doc/json.html>`_ にまとめて置くと、
``npm install`` するだけで準備が出来るので、package.jsonを書いていきます。

また、 package.jsonを作っておくと、githubを見た人も ``npm install``  でテスト環境を作れるようになるので、
このように設定をまとめておくのは有用だと思います。

実際に `azu/BusterJS-TravisCI`_ に書かれている `BusterJS_TravisCI/package.json <https://github.com/azu/BusterJS_TravisCI/blob/master/package.json>`_ を見てみます。

.. code-block:: json

    {
        "author" : "azu",
        "name" : "BusterJS_TravisCI",
        "description" : "Buster.JS with Travis CI example",
        "version" : "0.0.1",
        "scripts" : {
            "test" : "node_modules/.bin/buster-test"
        },
        "dependencies" : {
            "buster" : "~0.5.0"
        },
        "engines" : {
            "node" : "~0.6"
        }
    }

``dependencies`` を見るとbusterの"~0.5.0"に依存していると書かれています。
"~0.5.0"とは0.5.0以上で0.6.0未満で一番バージョン番号が新しいものがインストールされるという意味になります。
同じように ``engines`` でnodeのバージョン番号を指定しています。

``scripts`` では ``npm test`` とコマンドを叩いた時に実行される動作を指定できます。
ここでは ``npm install`` でインストールしたnode_modules/のbuster-testコマンドを実行するようにしています。

``npm test`` については以下が参考になります。

* `npmでtestを実行する - 四角革命前夜 <http://d.hatena.ne.jp/sasaplus1/20120326/1332688106>`_
* `Travis CIでNodeのテスト - hokaccha.hamalog v2 <http://d.hatena.ne.jp/hokaccha/20111110/1320910718>`_

この状態で、 ``npm install`` してから ``npm test`` すると、同じディレクトリにある `buster.js <https://github.com/azu/BusterJS_TravisCI/blob/master/buster.js>`_ の設定ファイルを元に
Buster.JSのテストが走ることが確認できます。

他にもpackage.jsonでは色々設定できますが、package.jsonで設定できることを詳しく知りたい場合は以下を見るのがいいでしょう。

* `Specifics of npm's package.json <http://npmjs.org/doc/json.html>`_

.. "."で始まるとディレクティブと誤認されるためエスケープ

\.travis.yml
================

``.travis.yml`` では `Travis CI`_ がテストを実行する前に行うことや通知の設定や実行するテストコマンドなどを指定できます。

.. code-block:: yaml

    before_script:
      - export DISPLAY=:99.0
      - sh -e /etc/init.d/xvfb start
      - sleep 5
      - node_modules/.bin/buster-server &
      - sleep 5
      - firefox http://localhost:1111/capture &
      - sleep 5
      - phantomjs node_modules/buster/script/phantom.js http://localhost:1111/capture &
      - sleep 5
    
    script:
      - "npm test"
    
    language: node_js
    
    node_js:
      - 0.6

テストの実行環境はnodeを使うため、languageにnode_jsとし、node_jsのバージョンも指定します。
scriptではテスト実行時に行うコマンドを指定できるので ``npm test`` とします。(無指定でもこれが使われる)

Buster.JSではテスト実行前にブラウザをキャプチャしておかないと行けないので、 ``before_script`` でscriptの行われる前に
Buster.JSでテストを行う準備の設定を記述します。

Travis CIでのブラウザを使ったテストについては `Travis CI: GUI & Headless browser testing on travis-ci.org <http://about.travis-ci.org/docs/user/gui-and-headless-browsers/>`_ にも
書かれていますが、GUIが必要な場合はxvfbを動かすようにします。

次に、 ``buster-server`` コマンドでBuster.JSのサーバをたちあげたら、
キャプチャするURLに対して、Travis CIに入ってるFirefoxとphantomJSを使ってそこへアクセスするようにします。

Buster.JSではPhantom.js用のスクリプトが ``buster/script/phantom.js`` に用意されてるので、それを利用してキャプチャURLにアクセスさせます。
最近のPhantom.jsではXvfbに依存しなくなったので、Phantom.jsだけを使う場合はxvfbはstartしなくてもいいかもしれません。

* `Pure headless PhantomJS (no X11 or Xvfb) - don't code today what you can't debug tomorrow <http://ariya.ofilabs.com/2012/03/pure-headless-phantomjs-no-x11-or-xvfb.html>`_

これで、Travis CI上で次のようにテストが走って失敗なら通知をおくってくれるようになるので、
Githubで公開してるJavaScriptプロジェクトのCIが簡単に行うことができるようになります。

* `azu/BusterJS_TravisCI <http://travis-ci.org/#!/azu/BusterJS_TravisCI>`_

.. image:: /_static/TravisCI.png

サンプルプロジェクト

* .. raw:: html

    <a class="first reference external image-reference" href="http://travis-ci.org/azu/BusterJS_TravisCI"><img alt="https://secure.travis-ci.org/azu/BusterJS_TravisCI.png?branch=master" src="https://secure.travis-ci.org/azu/BusterJS_TravisCI.png?branch=master" /></a>
*  `azu/BusterJS-TravisCI`_

`Travis CI`_ ではビルドステータスの画像を取得するURLもあるので、Githubにreadmeなどに貼り付けておくと分かりやすい。

.. image:: /_static/TravisCI_status.png

また、このドキュメントで使用されているコードも同様にBuster.JSとTravisCIを使いテストされています

* .. raw:: html

    <a class="reference external image-reference" href="http://travis-ci.org/azu/busterjs-kumite"><img alt="https://secure.travis-ci.org/azu/busterjs-kumite.png?branch=master" src="https://secure.travis-ci.org/azu/busterjs-kumite.png?branch=master" /></a>
* `azu/busterjs-kumite <https://github.com/azu/BusterJS-Kumite>`_

.. _`Travis CI`: http://travis-ci.org/
.. _`azu/BusterJS-TravisCI`: https://github.com/azu/BusterJS_TravisCI