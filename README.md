# unbound を利用した宅内DNSサーバーの構築
WAN上にあるサーバーにアクセスするのは容易だが、自宅のIPアドレスに対してドメイン名が設定されている場合、自宅内での名前解決ができずにエラーとなるため、自宅内からでもドメイン名で引けるように宅内DNSサーバを立てる。
特に自宅内でスマホからPCのテストサーバーにドメイン付きでアクセスしないとテストできないような環境の時に使う。（本来はやらない方が良いが……）

## 手順
[mvance/unbound](https://hub.docker.com/r/mvance/unbound/) を利用。
設定ファイルをサンプルからコピーし、適宜自分の環境に合わせて修正する。
```
$ cp unbound.sample.conf unbound.conf
$ cp a-records.sample.conf a-records.conf
$ cp root.sample.key root.key
```

### ヒント
* `a-records.conf` に、解決したいドメイン名と、解決先のローカルIPアドレスを記載する。
* `unbound.conf` は `mvance/unbound` では自動生成していたが、一部問題があったのとパフォーマンス最適化のために自前で書いた。
* `docker-compose.yml` 内に `ports` `volumes` を記述している。


## Mac のDNSサーバーを止める
Macの場合、デフォルトのDNSサーバーが邪魔をしてくるので念のために書き換えを行っておく。
`192.168.x.1` の部分は、自分の環境に合わせる。
```
sudo networksetup -listallnetworkservices
sudo networksetup -setdnsservers Ethernet 127.0.0.1 192.168.x.1
```


## 実行方法
`docker-compose` を使っているので、簡単に起動可能。
```
$ cd /path/to/unbound-for-local
$ sudo docker-compose up -d
```

## テスト
以下で `your.local.domain.name` のレスポンスにローカルIPが返却されれば成功。
```
$ dig A google.com
$ dig A your.local.domain.name
```