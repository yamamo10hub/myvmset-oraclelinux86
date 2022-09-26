# OS install parameter
コード化も可能にみえるが後手に回したので、設定手順のメモを設置する  
  
## OSインストール時のパラメーター

VMスペック  
```
cpu 2core , mem 2G , hdd 32G , networkアダプタ defaultNetworkのアダプタ  
仮想マシンの作成  
名前の指定  
クラスタ、リソース、ストレージの指定  
linux , Oraclelinux8  
リソースの指定でコンテンツライブラリのisoを指定して接続にチェック  
```
  
---
インストール
```
起動  
　メディアテスト抜きでInstallを選択
言語  
　日本語を選択  
  
インストール概要  
　インストール先  
　　　作成したHDDを指定  
　ソフトウェアの選択  
　　　最小限(minimal)でアドオンを足す  
　　　　　GuestAgents, development tools, network servers
　　　　　securitytools, systemtools
インストールの開始
install中にCPU使用率が上昇したので注意
```
  
## セットアップコマンド
◆OS設定
networkIFの確認
```
nmcli d
-> ens192
```
IP設定
```
nmcli con mod ens192 ipv4.addresses "192.168.1.222/24"
nmcli con mod ens192 ipv4.gateway "192.168.1.10"
nmcli con mod ens192 ipv4.method manual
nmcli con mod ens192 ipv4.dns 8.8.8.8
nmcli con mod ens192 ipv4.ignore-auto-routes yes
nmcli con mod ens192 ipv4.ignore-auto-dns yes
nmcli con mod ens192 ipv4.may-fail no
nmcli con mod ens192 ipv6.method disabled
nmcli connection modify ens192 connection.autoconnect yes
nmcli connection up ens192
```
systemctl network restartは8からは存在しない  
  
adminの追加
```
useradd sysadmin
passwd sysadmin
```
visudo 
```
:101
sysadmin   ALL=(ALL)    ALL
```
上記のvisudoをコマンドにするとこうなるが、パーミッションの問題で失敗するかもしれない
```
cp /etc/sudoers /etc/sudoers.org
rm /etc/sudoers
cat /etc/sudoers.org | sed 101i'sysadmin   ALL=(ALL)      ALL \n' > /etc/sudoers
chmod 440 /etc/sudoers
```
dnfで追加
```
dnf update
dnf install open-vm-tools
dnf install telnet
dnf install net-tools
dnf install bind-utils
dnf install openssl-devel
dnf install vim
dnf install python39 
dnf install cloud-utils-growpart
```
systemdの設定変更
```
systemctl enable vmtoolsd
systemctl disable firewalld
```
OS全体の設定パラメーター変更
```
sed -i -e s/SELINUX=enforcing/SELINUX=disabled/g /etc/selinux/config
hostnamectl set-hostname oracle-86
```
  
pythonの設定  
```
alternatives --set python /usr/bin/python3
```
  
nmcliの停止
```
nmcli connection modify ens192 connection.autoconnect no
```
  
OSの停止
```
shutdown -h now  
```
  
vCenterで設定
```
annotationの付与 -> (作成日、作成者等)
「サマリ」->「メモの編集」
VMのtemplate化  
「テンプレート」->「テンプレートに変換」
```
  
