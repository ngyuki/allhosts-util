## はじめに ##

複数ホストの設定や管理を助けるための簡単なシェルスクリプトです。


## 対象ホストの指定 ##

対象となるホストは環境変数`$HOSTS`で設定します。

    export HOSTS="192.168.0.1 192.168.0.2 192.168.0.3 192.168.0.4"

または・・・

    HOSTS="192.168.0.1 192.168.0.2 192.168.0.3 192.168.0.4" ALLSYNC ～～～


## ALLHOSTS ##

単に `$HOSTS`を出力します。

他の`ALL*`コマンドがホストのリストを得るために使用しています。

そのため、このスクリプトにホストのリストをベタ書きすれば、`$HOSTS`は不要です。

例えば、`ALLHOSTS`を次の通りに書き換えれば`$HOSTS`を毎度毎度設定しなくても自動的にこれらのホストが対象になります。

    #!/bin/bash
    echo "192.168.0.1 192.168.0.2 192.168.0.3 192.168.0.4"


## ALLEVAL ##

環境変数`$H`にホストを設定しつつ、引数で与えられたコマンドを`eval`で繰り返し実行します。

### 使用例

    $ export HOSTS="192.168.0.1 192.168.0.2 192.168.0.3 192.168.0.4"
    $ ALLHASH echo \$H

※`$H` の前の `\` は **必要** です。

### 実行結果

    # H=192.168.0.1 echo $H
    192.168.0.1
    # H=192.168.0.2 echo $H
    192.168.0.2
    # H=192.168.0.3 echo $H
    192.168.0.3
    # H=192.168.0.4 echo $H
    192.168.0.4


## ALLHASH ##

全てのホストで指定されたファイルの md5 ハッシュ値を表示します。

例えば、次のように設定ファイルの内容が全ホストで同じであるかの確認に使用出来ます。

ディレクトリを指定した場合はディレクトリ内の全てのファイルが対象となります。


### 使用例

    $ ALLHASH /etc/my.cnf /etc/httpd/conf/

### 実行結果

    a19a1c79c1e02177dbde07d6f4c81f28  /etc/httpd/conf/httpd.conf    192.168.0.1
    a19a1c79c1e02177dbde07d6f4c81f28  /etc/httpd/conf/httpd.conf    192.168.0.2
    a19a1c79c1e02177dbde07d6f4c81f28  /etc/httpd/conf/httpd.conf    192.168.0.3
    a19a1c79c1e02177dbde07d6f4c81f28  /etc/httpd/conf/httpd.conf    192.168.0.4
    9934de63d4f4321a375de42464c24c87  /etc/httpd/conf/magic 192.168.0.1
    9934de63d4f4321a375de42464c24c87  /etc/httpd/conf/magic 192.168.0.2
    9934de63d4f4321a375de42464c24c87  /etc/httpd/conf/magic 192.168.0.3
    9934de63d4f4321a375de42464c24c87  /etc/httpd/conf/magic 192.168.0.4
    4465e16b938a4cd91d12eff269b4e856  /etc/my.cnf   192.168.0.1
    4465e16b938a4cd91d12eff269b4e856  /etc/my.cnf   192.168.0.2
    4465e16b938a4cd91d12eff269b4e856  /etc/my.cnf   192.168.0.3
    4465e16b938a4cd91d12eff269b4e856  /etc/my.cnf   192.168.0.4


## ALLSSH ##

指定されたコマンドを ssh 経由で全てのホストで実行します。

### 使用例

    $ ALLSSH uname -n

### 実行結果

    ### ssh 192.168.0.1 uname -n
    host01.example.net
    ### ssh 192.168.0.2 uname -n
    host02.example.net
    ### ssh 192.168.0.3 uname -n
    host03.example.net
    ### ssh 192.168.0.4 uname -n
    host04.example.net


## ALLSYNC ##

指定されたファイル・ディレクトリを rsync で全てのホストに同期します。

自分自身がホストのリストに含まれる場合は自分自身へは rsync しません。

追加で以下のオプションが指定可能です。

    -n    rsync に -n オプションを付けて実行します。
    -d    実際に実行する rsync コマンドを表示します（表示するだけで実行はしません）

### 使用例

    $ ALLSYNC /tmp/ -d

### 実行結果

    ### 192.168.0.2 (debug)
    rsync -Rai --out-format %n --no-implied-dirs --delete /tmp/ 192.168.0.2:/
    ### 192.168.0.3 (debug)
    rsync -Rai --out-format %n --no-implied-dirs --delete /tmp/ 192.168.0.3:/
    ### 192.168.0.4 (debug)
    rsync -Rai --out-format %n --no-implied-dirs --delete /tmp/ 192.168.0.4:/

※オプションの`-d`を無くすと、これらの rsync コマンドが実行されます。

※自ホストが 192.168.0.1 であれば除外されます（192.168.0.1 から他ホストに同期するということ）。
