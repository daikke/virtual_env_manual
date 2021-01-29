# 環境構築手順書
## バージョン一覧
|サービス|バージョン|
|--|--|
|PHP|7.3|
|Nginx|1.19.2|
|MySQL|5.7|
|Laravel|6.0|
|OS|CentOS7|
## 構築フロー
1. 作業ディレクトリ作成
    1. ```mkdir virtual_env_lesson && cd virtual_env_lesson```実行
1. VagrantFileの構築
    1. 該当のディレクトリにて`vagrant init centos/7`実行
    1. 生成されたVagrantfileに下記の修正を実行
        1. ```config.vm.network "forwarded_port", guest: 80, host: 8080```のコメントイン。
        1. ```config.vm.network "private_network", ip: "192.168.33.19"```のコメントイン。
        1. ```config.vm.synced_folder "../data", "/vagrant_data"```のコメントイン
        1. ```config.vm.synced_folder "./", "/vagrant", type:"virtualbox"```の編集
    1. ```vagrant up```の実行
    1. ```vagrant ssh```実行
1. PHPの導入
    1. ```sudo yum -y install epel-release wget```実行
    1. ```sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm```実行
    1. ```sudo rpm -Uvh remi-release-7.rpm```実行
    1. ```sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip```実行
1. laravelの導入
    1. ```php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"```実行
    1. ```php composer-setup.php```実行
    1. ```php -r "unlink('composer-setup.php');"```実行
    1. ```sudo mv composer.phar /usr/local/bin/composer```実行
    1. exitし、ローカルにて、```mkdir vagrant_test && cd vagrant_test```実行
    1. ```composer create-project laravel/laravel --prefer-dist laravel_app 6.0```実行
    1. ```cp .env.example .env```実行
    1. .env以下の三点編集
        1. ```DB_DATABASE=laravel_app```
        1. ```DB_USERNAME=root```
        1. ```DB_PASSWORD=任意の文字列１```
    1. ```php artisan key:generate```実行
1. MySQLのインストール
    1. ```sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm```実行
    1. ```sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm```実行
    1. ```sudo yum install -y mysql-community-server```実行
    1. ```sudo systemctl start mysqld```実行
    1. ```sudo cat /var/log/mysqld.log | grep 'temporary password'```実行。出力されるパスワードを次フェーズで入力
    1. ```mysql -u root -p```実行
    1. ```set password = "任意の文字列１";```
    1. ```CREATE DATABESE laravel_app```実行後、exit
1. Nginxのインストール
    1. ```sudo vi /etc/yum.repos.d/nginx.repo```実行
    1. ```sudo yum install -y nginx```実行
    1. ```sudo vi /etc/nginx/conf.d/default.conf```で以下の点修正
        1. ```server_name  192.168.33.19;```変更
        1. ```root /vagrant/laravel_app/public;```追記
        1. ```index  index.html index.htm index.php;```追記
        1. ```#root   /usr/share/nginx/html;```変更
        1. ```#index  index.html index.htm;```変更
        1. ```try_files $uri $uri/ /index.php$is_args$args;``` location / {ないに追記
        1. ```fastcgi_param  SCRIPT_FILENAME  /$document_root/$fastcgi_script_name;```変更
    1. ```sudo vi /etc/php-fpm.d/www.conf```で以下の点修正
        1. ```user = nginx```変更
        1. ```group = nginx```変更
    1. ```sudo systemctl start nginx```実行
    1. ```sudo systemctl start php-fpm```実行
1. laravel、storageへ権限付与
    1. ```sudo chmod -R 777 /vagrant/laravel_app/storage```実行
## 構築所感
正直なところ、かなり想像で書いてます（Vagrant環境ができなかったので・・・）。  
コツさえわかって仕舞えば、カリキュラムの写しでなんとか対応できそう！！。  
Markdown書く方がきつかった・・・
## 参考サイト
- [ServerLesson_section3](https://giztech.gizumo-inc.work/lesson/18/217)
- [ServerLesson_section4](https://giztech.gizumo-inc.work/lesson/18/218)
- [ServerLesson_section7](https://giztech.gizumo-inc.work/lesson/18/221)
- [ServerLesson_section8](https://giztech.gizumo-inc.work/lesson/18/222)