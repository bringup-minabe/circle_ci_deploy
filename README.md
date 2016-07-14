# circle_ci_deploy

##事前設定
<https://github.com/CircleAround/git_web_hook>

##動作流れ
1. GitHubへPUSH
2. CircleCiがPUSHを検知し、ビルド開始
3. ビルド後CircleCiのWebhookよりデプロイをおこなうサーバーのhook.phpへhook
4. hook.phpでデプロイ処理

##Git
デプロイをおこなうサーバーからのgit接続時のportは**443**に変更

  <https://help.github.com/articles/using-ssh-over-the-https-port/>

##.htaccess
    SetEnv CI_SECRET hogehoge #ymlで設定したsecret
    SetEnv CI_MODE normal #debugでdebugモード
    SetEnv CI_BRANCH_NAME master #対象ブランチ
    SetEnv CI_LOG_PATH logs/ #ログ保存先パス

##apacheユーザでPHPからsudo権限でシェルを叩く
git pull 専用ユーザー deployer(名前は任意)を作成している前提で書きます。

<http://blog.ousaan.com/index.cgi/links/20120504.html>

    $ sudo visudo

    apache ALL=(deployer) NOPASSWD: /bin/sh # deployer の権限でshコマンドを実行する時
    Defaults:apache !requiretty # apacheだけ端末デバイス(Terminal)以外からのアクセスを許す

##pull.sh
    #!/bin/sh
    cd /path/to/file
    git pull origin master

##circle.yml
Webhooksを複数設定する場合は以下

    notify:
      webhooks:
        - url: http://hogehoge.jp/ci/hook.php?secret=hogehoge
        - url: http://hogehoge2.jp/ci/hook.php?secret=hogehoge

##php.ini
環境変数を使えるようにphp.iniの設定変更

    ; $_ENV が使えない設定の例
    variables_order = "GPCS"

    ; $_ENV が使える設定の例
    variables_order = "EGPCS"
