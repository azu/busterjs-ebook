==============================================
Buster.JS のインストール
==============================================

Mac OS X/Linux
=================

MacとLinuxでは `Node`_ のパッケージマネージャー npm を使ってインストールできます。

.. code-block:: bash

    $ npm install -g buster

.. _`Node`: http://nodejs.org/

Windows
============

.. todo:: Windowsはまだ未サポート
.. todo:: buster-test等一部のみ

VMにインストール
==================

VirtualBoxを使って |Buster.JS| をインストールする。
この方法ならWindowsであっても同様の環境を構築できる

`try-busterjs <https://github.com/mroderick/try-busterjs>`_
------------------------------------------------------------

`VirtualBox <https://www.virtualbox.org/>`_ を使うためホストOSに関係なく、
同じ方法を使ってVirtualBox上で動く |Buster.JS| 環境を構築できます。
あくまで、用意されたお試しの環境をVirtualBoxに作るものなので、ちゃんとやりたい場合は
自分で同じように作ったほうがいいかもしれません。

.. note:: 
    今回はホストOSがWindowsを例にする


まずは、`VirtualBox - Download <https://www.virtualbox.org/wiki/Downloads>`_ と `Vagrant - Downloads <http://downloads.vagrantup.com/>`_ から
それぞれのインストーラーをダウンロードしてインストールします。

``Vagrant`` は任意のディレクトリにインストールし、コマンドラインから利用するためにPATHを通す必要があります。
今回は ``D:\ruby\vagrant\`` にインストールし、 ``D:\ruby\vagrant\bin`` へPATHを通すようにしました。

Ruby + VirtualBox + Vagrant が揃ったら、まずはvagrantで利用できるVMイメージをbaseという名前で取得する

.. code-block:: bash

    $ vagrant box add base http://files.vagrantup.com/lucid32.box

次に `try-busterjs`_ のリポジトリをcloneしてVagrantのセットアップを行います。

.. code-block:: bash

    $ git clone https://github.com/mroderick/try-busterjs.git
    $ cd try-busterjs
    $ vagrant up

`vagrant up` するとVirtualBox上でUbuntuが立ち上がるので、
このゲストOSに対してSSHで接続して |Buster.JS| を動かす事ができます。

MacやLinuxではsshがコマンドラインから利用できるので、 ``vagrant ssh`` を行うだけでSSH接続できますが、
Windowsでは別途 `PuTTY <http://www.chiark.greenend.org.uk/~sgtatham/putty/>`_ や `RLogin <http://nanno.dip.jp/softlib/man/rlogin/>`_ といったSSHクライアントとして
動作するソフトウェアを使用してSSH接続を行います。 [#sshclient]_

.. note:: 

    今回は `RLogin <http://nanno.dip.jp/softlib/man/rlogin/>`_ を使用
    
SSHクライアントでの接続方法は ``vagrant ssh`` を行うと表示されますが、

::

    HOST     : 127.0.0.1
    Username : vagrant
    Password : vagrant
    Private key : ~/.vagrant.d/insecure_private_key
    Port     : 2222


上記のような設定で、SSHの鍵はユーザーのホームディレクトリ以下に ``/.vagrant.d/insecure_private_key`` と
いうものができているため、それを指定します。

.. image:: /_static/2012-05-31-ss1.png

SSH接続ができたら、後は通常のLinuxでの |Buster.JS| 環境と同様になります
``buster`` コマンドがインストールされているかを確認して、もしインストールされてないならnpmでインストールしておきます。

.. code-block:: bash
    
    $ sudo npm install -g buster
    # パスワードは vagrant

これで、 `try-busterjs`_ を使ってVirtualbox上に |Buster.JS| 環境を構築できましたが、
もっと詳細に設定等をしたい場合はSSHで利用するLinux on VM [#ssh]_ などを作成するのがよいと思われます。

ホストOS:Windows、ゲストOS:Ubuntuとして、`VBoxHeadlessTray <http://www.toptensoftware.com/VBoxHeadlessTray/>`_ などを利用して、
ヘッドレスでVM上にLinuxを動作させて、SSH接続して利用すれば、普通の利用の範囲なら速度やメモリ消費量的にも問題無い程度で動作させることができると思われます。


.. `try-busterjs`_: https://github.com/mroderick/try-busterjs
.. [#sshclient] `Get Started With Vagrant On Windows — zamboni 0.8 documentation <http://mozilla.github.com/zamboni/topics/install-zamboni/vagrant-on-windows.html>`_
.. [#ssh] `WindowsからVM上のLinuxをSSH経由で利用する開発環境の構築 | Web scratch <http://efcl.info/2011/0420/res2588/>`_