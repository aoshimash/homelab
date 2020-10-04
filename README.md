# homelab

自宅kubernetesクラスタの設定

## Description

Raspberry Pi 4 で kubernetes クラスタをセットアップする ansible playbook

## Usage

各playbookの役割

- setup_nodes.yml: master, worker共通のセットアップを実行するplaybook
- setup_master_node.yml: masetr固有の設定を実行しるplaybook
- setup_worker_nodes.yml: workre固有の設定を実行するplaybook

kubernetesクラスタを構築する場合は次の手順で作業を行う。

### masetrノードのセットアップ

#### 1. inventoryファイルの作成

inventoryファイル (`hosts/master.yml`) を作成する。`hosts/master.yml.example` をコピーして各変数を自分の環境に合わせて変更するだけで良い。

#### 2. playbookの実行

```
$ ansible-playbook setup_nodes.yml -i hosts/master.yml

$ ansible-playbook setup_master_node.yml -i hosts/master.yml
(...)
TASK [display the result] ********************************************************************************************************************************************************************
ok: [master-1] => {
    "msg": [
        "token: wjkuzc.0dx3khby21k0by1b",
        "ca_cert_hash: 63c4e6a01f6f3ef2dff873cdd926a6b08cb52260d72c4cef9a5d9171801dec79"
    ]
}
PLAY RECAP ***********************************************************************************************************************************************************************************
master-1                   : ok=7    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```

masterノードのセットアップに成功すると、上記のように `token` と`ca_cert_hash` の値を取得することができる。これらの情報はworkerノードをクラスタに追加するときに必要なのでメモしておく。また、トークンの有効期限は24時間以内なのでワーカーの追加は24時間以内に行う必要がある。

### workerノードのセットアップ

#### 1. inventoryファイルの作成

inventoryファイル (`hosts/worker.yml`) を作成する。`hosts/worker.yml.example` をコピーして各変数を自分の環境に合わせて変更するだけで良い。`token` と`ca_cert_hash` の値はmasetrノードのセットアップ時に取得したものを使う。

#### 2. playbookの実行

```
$ ansible-playbook setup_nodes.yml -i hosts/worker.yml
$ ansible-playbook setup_worker_node.yml -i hosts/worker.yml
```
