# はじめに
https://twitter.com/i/status/1500369489845964801
動画の通り部室のRPLIDARを借りてSLAMをしました。
借りたからには記事にしなかんと思ったので書いていきます。
(Twitterのアカウントは気にしないで下さい。)

既存パッケージごり押し、lidarのみのなんちゃってSLAMです。
参考記事はTwitterに貼ってあります。
主にハード面の話とそれらをパッケージに当てはめていく部分をクリアできれば簡単に実装できるはずです。
# ハード
<img width="584" alt="image.png (811.8 kB)" src="https://img.esa.io/uploads/production/attachments/12504/2022/03/14/59643/3c4090f7-910b-4989-a5a9-c071fcc74bfc.png">

写真の通り、
- raspi 4b
- おもちゃモーター
- rplidar
- arduino
の構成です
## raspi
[#170:  ROS/Ubuntu関連/RaspiにUbuntuServerを設定する](/posts/170) 
ここからUbuntuServerを設定し、ssh接続します。
rosbridgeを使用してwslにtopicを飛ばし、重い処理や、GUIがいる部分はノートパソコンでやります。

モバイルバッテリーで給電します。
数10分なら余裕です。
## おもちゃモーター
lego mindstormのモーターで一応7Vくらいいけるやつです。
エンコーダーの出力もありましたが今回は使っていません。

MDはこいつ
https://www.switch-science.com/catalog/3586/

給電はパナの充電池×４です。
## RPLIDAR
[#159: [WIP] ROS/Raspiでrplidarを起動し、wslで確認する](/posts/159) 
この通りに起動して、/scanトピックが飛ばせればOkです。

ただし、あとからrosbridgeのマスターとローカルで同じトピックを飛ばすとコンフリクトすることが分かったので、一方通行に設定する必要があります。
## Arduino
mdを制御しています。
正直この規模ならraspiのGPIOで事足りるのですが、rosserial-arduinoを使ってみたかったので使いました。

https://qiita.com/srs/items/53b1d0a9d574d9ca7752
基本このサイトの通りにやれば出来ました。

ただし、100Hzなど高速でのトピックのやり取りはできなかったのでマイコンの限界を感じました。
STMやESP、Teensyやraspi picoなら余裕だったかもしれません。
# 内容
https://t.co/1uSlE9EmvQ
基本はこの記事の通りです。

別途容易する必要があるのは、マイコンに依存する部分ぐらいです。

まずここら辺はrosの慣例的な話になるっぽいですが、/cmd_velというトピックがSLAMで使われるmove_baseなどのパッケージでは配信されます。

そのため機体に合わせて、/cmd_velを変換してやる必要があります。

あとは/scanメッセージさえ配信できていればほとんどlaunchファイルそのままで動くと思います。
