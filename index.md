---
title: 日本ロボット学会　ロボットの作り方 ～移動ロボットの基本とROSによるナビゲーション実習～
---

# ロボットの作り方 ～移動ロボットの基本とROSによるナビゲーション実習～

[日本ロボット学会 セミナー申し込みページ](https://www.rsj.or.jp/event/seminar/news/2021/s139.html)

- Table of contents
{:toc}

## 事前準備

下記を<span style="color:red">*必ず*</span>ご用意ください。

- 実習に利用するノート PC
  - Ubuntu Linux と ROS を事前にインストールしてください
  - インストール方法は以下を参考にしてください
    - [Ubuntu LinuxとROSのインストール](linux_and_ros_install.html)

- 組み立て済み移動ロボット（各グループで1台以上）
  - Robotis社が提供している[TurtleBot3 burger](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)を利用します
  - <span style="color:red">_注意_</span>：本セミナー申し込み時に購入したTurtleBot以外を使用する場合、セミナーの内容を適宜読み替えて進めて頂く必要があります。
- TurtleBotが走行可能なスペース

不明な点や、事前準備がうまく行かない点については、オーガナイザまでお問い合わせ下さい。

## スケジュール

### １日目（1/29(土) 10:00-17:00）

|10:00-10:30|セミナーの進行につきまして|
|10:30-12:00|[セミナー環境の確認とLinuxの基本操作](linux_basics.html)と[ROSの基本操作](ros_basics.html) |
|12:00-13:00|昼休み |
|13:00-14:00|講義1 「自己位置推定ROSパッケージの作り方」<br>講師：上田　隆一 (千葉工業大学)|
|14:00-17:00|[移動ロボットの動作確認](turtlebot-basics.html) |

<!--|15:00-16:30|[ROSを用いたマップ取得](slam-basics.html)|-->
<!--|15:00-16:30|[ROS Navigationの利用](ros-navigation.html)|-->

### ２日目（1/30(日) 10:00-16:00）

|10:00-11:00|講義2 「Autonomous Navigation: from Small-sized Robots to Personal Mobility Vehicles」<br>講師：モラレス　ルイス (名古屋大学)|
|11:00-12:00|[ROSを用いたマップ取得](slam-basics.html)|
|12:00-13:00|昼休み|
|13:00-14:30|[マップを利用したナビゲージョン操作１](map-navigation.html)|
|14:30-15:30|[マップを利用したナビゲージョン操作２](map-navigation-2.html)|
|15:30-16:00|課題と質疑|

<!--|13:30-15:00|[障害物認識と回避](obstacle-detection.html)|-->

**スケジュールは、演習の進行等に応じて変更する場合がありますのでご了承ください。**

## 会場

オンラインでの実習（zoomを使用予定です）


## セミナーテキスト
順次テキストをアップロードする予定です。

1. [Ubuntu LinuxとROSのインストール](linux_and_ros_install.html)

1. [セミナー環境の確認とLinuxの基本操作](linux_basics.html)

1. [ROSの基本操作](ros_basics.html)

1. [Turtlebot3の基本操作](turtlebot-basics.html)

1. [ROSを用いたマップ取得](slam-basics.html)

1. [マップを利用したナビゲージョン操作１](map-navigation.html)

1. [マップを利用したナビゲージョン操作２](map-navigation-2.html)

1. [障害物認識と回避](obstacle-detection.html)

## 参考情報

- [日本ロボット学会 第139回 ロボットの作り方 申し込みページ](https://www.rsj.or.jp/event/seminar/news/2021/s139.html)
- [ROS Japan UG （日本ユーザ会）](https://rosjp.connpass.com/)
- [ROS メッセージボード](https://discourse.ros.org/)
- [ROS Answers](http://answers.ros.org/)（日本語でも大丈夫です）
- [Programming Robots with ROS](http://shop.oreilly.com/product/0636920024736.do)
- [TurtleBot Manual](https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/)
