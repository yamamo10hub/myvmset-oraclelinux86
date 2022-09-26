# oraclelinux masterimage
8.6  
  
## 概要
vmwareでIaCを構成するために、ベースとなるtemplate用のOSimageが必要となった  
作成時の情報を記載する  
imageからVMinstallするとき、そこそこの負荷が発生する  
  
## 形態
手動でVM作成、OSinstall、IP付与  
sshでコマンド投入  
shutdownしvcenterからtemplate化  
  
## 設定内容
[machine.md](machine.md) に記載  
  
## 今後
・コード化の範囲を広げる  
・os/ 以下にansibleでコードにしたい  
  

