# 1. Vagrantを使ったSage jupyter環境構築方法

Sageでもjupyterのnotebookがスタンダードになり、Sageだけでなく、
Pandasやtheano等のpythonパッケージを使ったnotebookが増えてくると思います。

自分の紹介するnotebookを実行するのに必要な環境も含めて公開し、
誰でも同じ結果を得ることができるようにVagrantを使ってVirtualBoxのVM環境を構築する方法を紹介します。

# 2. 必要なアプリケーションのインストール

環境構築に必要なアプリケーションは、以下の２つです。
- VirtualBox: 無料で使用できるx86仮想化ソフトウェアです。Ubuntu14.04の仮想環境を実行するために使用します。<sup>[1](#note1)</sup>
- Vagrant: 仮想環境を構築し、実行するためのツールです。<sup>[2](#note2)</sup>

上記アプリケーションのインストール方法については、以下のリンクを参照してください。
- [Vagrantを使ったSage jupyter環境の紹介](../ReadMe.md)

# 3. SageMathのインストール
準備ができましたので、SageMathをVirtualBoxの仮想マシンで動くようにします。

## 3.1. vagrantファイルのダウンロード
Githubの以下のURLからjupyter用のnotebookとvagrant環境構築用ファイルをダウンロードします。

ターミナルソフトを起動し、以下のGitコマンドを使って必要なファイルをダウンロードします。
```
$ git clone https://github.com/take-pwave/jupyter.git
```

gitコマンドが使えない場合には、以下のURLの「Clone or download」をクリックし、Download ZIPを選択してください。
- https://github.com/take-pwave/jupyter

## 3.2. boxファイルのダウンロード
次に、SageをインストールするUbuntu14.04のboxファイルをダウンロードします。ダウンロードされたboxは、~/.vagrant.d/boxes/に保存されます。

以下の$ の後のコマンドを入力してください（コピーして、ペーストすると簡単です）。

```bash
$ vagrant box add SageMath7.2 https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box
```

## 3.3. 仮想マシンの起動
jupyterをダウンロードしたディレクトリをDOWNLOAD_DIRとします。
ターミナルソフトを起動して、以下のコマンドを入力してください（$ の後のコマンドを入力）。

```bash
$ cd DOWNLOAD_DIR/vagrant
$ vagrant up
```

仮想マシンが起動して、SageMath用のパッケージが自動的にダウンロードされ、セットされます。
1.5GB程度のファイルをダウンロードするため、完了すまでにはかなりの時間が掛かります。

インストール直後は、コンフィグ情報が正しくセットされないため、再度起動します（$ の後のコマンドを入力）。
再起動時の画面は、以下の様になります。

```bash
$ vagrant reload
==> default: Attempting graceful shutdown of VM...
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 8888 (guest) => 8888 (host) (adapter 1)
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /Users/take/proj/jupyter/vagrant
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run.
```

## 3.4. 仮想マシンの停止
仮想マシンを終了するには、以下のコマンドを入力してください（$ の後のコマンドを入力）。

```bash
$ vagrant halt
```

## 4. SageMathを使ってみる
ブラウザーで以下のURLを開きます。
```
http://localhost:8888/
```

jupyterの初期画面が表示されます。

![jupyter screen](images/jupyter_screen.png)

新しくノートブックを作成する場合には、ここで表示されているnotebookディレクトリ中で作成してください。

### 4.1. ノートブックを見る
ブラウザーに表示された「notebook」をクリックし、その中の「GettingStart.ipynb」をクリックしてください。

以下の様に「GettingStart」ノートブックの内容が表示されます。

![jupyter screen](images/GettingStart_nb.png)

File, Edit等のメニューが並んでいる右端には、現在実行しているカーネル（ここではSageMath 7.2）の種類が表示されます。
もしも現在稼働中のカーネルが異なっている場合には、「Kernel」→「Change kernel」メニューから「SageMath 7.2」を選択してください。


### 4.2. ノートブックファイルの場所
jupyterのnotebook内に作成されたファイルは、vagrantのファイル共有機能を使って仮想マシンを実行しているマシン（ホストマシン）のDOWNLOAD_DIR/vagrant/notebookに保存されます。

作成したノートブックのバックアップやファイルの追加・削除もホストマシンで簡単にできます。

![jupyter screen](images/notebook_dir.png)

## 5. パッケージの追加
パッケージを追加したり、処理が途中で終わった場合には、以下のコマンドで仮想マシンにSSHでログインして操作します（$ の後のコマンドを入力）。
```bash
$ vagrant ssh
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-86-generic x86_64)
途中省略
vagrant@vagrant-ubuntu-trusty-64:~$ 
```

次に仮想マシンでのコマンドは、vagrant@で始まります。$ の後のsudo sage -shを入力してください。
最初にワーニングが表示されますが、パッケージのインストール後にexitコマンドで終了することを忘れなければ問題ありません。

これで、Sageの環境にパッケージを追加することができます。pythonの場合には、pipコマンドを使って必要なパッケージをインストールしてください。

SSHを終了するには、(sage-sh) のプロンプトとvagrant@のプロンプトの2カ所でexitすることを忘れないでください。


```bash
vagrant@vagrant-ubuntu-trusty-64:~$ sudo sage -sh
sys:1: RuntimeWarning: not adding directory '' to sys.path since it's not owned by a trusted user.
途中省略
Note: SAGE_ROOT=/usr/lib/sagemath
(sage-sh) root@vagrant-ubuntu-trusty-64:~$ 
```

## 脚注
- <small id="note1">1: 私のMacOSは10.7.5と古いため、VirtualBoxのバージョンは4.3.12を使用。</small>
- <small id="note2">2: 今回使用したバージョンは1.8.4です。</small>
