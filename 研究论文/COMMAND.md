icart-miniの地図作成

ハードウェアを準備

 電源ケーブルをバッテリーと接続

 Lidarのethernetケーブル、
 icart-miniのusbケーブル、
 vn-100(加速度センサ)のusbケーブル
 をPCに接続

 icart-miniの電源スイッチON

icart-miniと接続
 >ypspur-coordinator -d /dev/ttyACM0 -p ros/icartmini/icart-mini.param
   (エラー発生時は何度か試すとうまく行きます。)
 エラー時
 >sudo usermod -aG dialout $USER　→再起動

PS4コントローラを接続

 PS4コントローラの share+PSボタンを同時長押し(shareボタンが先のほうがやりやすい)
 >sudo ds4drv
   (パスワードを要求されます。)
   接続待機中はコントローラのライトが点滅し、接続が成功すると点灯します。

PS4コントローラによるicart-mini操作を起動

 >roslaunch ros/launchs/icartmini_controller.launch
 表示される操作モード選択ウィンドウのコントローラモードをクリック

操作方法

 十字キー、左スティック：移動操作
 前進:L2
 後退:R2

モータ、レーザ、加速度センサのデータをpublish

 >roslaunch ros/launchs/icartmini_hw_proxies.launch

環境計測

 地図作成場所に移動

 地図フォルダを ros/locations に新たに作成する

 センサデータのデータが正しくpublishされているか確認
 >rostopic echo /imu_vecnav
   加速度センサ
 >rostopic echo /scan
   Lidar
 >rostopic echo /motor_rate
   icart-miniのタイヤモータ
 (データが正しく出ていることを確認したらそれぞれのechoは終了して良い)
 
 何も出力されていない場合、icartmini_hw_proxy.launch
 を ctrl + c で終了し、ケーブルの接続を確認後
 >roslaunch ros/launchs/icartmini_hw_proxy.launch
 を再び動かす。

 >rosbag record motor_rate scan imu_vecnav
   センサデータの録画
   ctrl + c で終了
   ホームディレクトリにその録画時の日付と時刻で録画データ 西暦-月-日-時-分-秒.bag ファイルが生成される。

 １回目の計測は、停止した状態で１分ほどデータを取る。
 「地図名offset.bag」と何のデータかわかるように名前を変更し、地図フォルダへ移動

 ２回目の計測は、コントローラで動かしながら計測
   動いている間のデータが地図作成に使われる
   できる限り直進のみの走行をする。曲がる回数が多いと、それだけ地図の制度が低くなる。
   直進中は１０秒に１回５秒以上の停止を挟む。方向転換するときは、直進→停止→旋回→停止→直進
 「地図名makemap.bag」と何のデータかわかるように名前を変更し、地図フォルダへ移動

ここまで、環境データの収集作業
-----------------------------------------------------------------------------------------

ここから、環境データから地図を生成する作業
PCのみで作業

環境データ収集作業で動かしていたプログラムはすべて終了(ctrl + c)しておくと安心

地図生成

概略
 icartmini_hw_simulation -> gnd_lssmap_maker -> rosbag play の順に起動
 rosbag play 終了後に gnd_lssmap_maker -> icartmini_hw_simulation の順に停止

詳細
 >roslaunch ros/launchs/icartmini_hw_simulation.launch
   スキャンデータの座標変換や、デッドレコニングを行う
 
 >cd 地図フォルダ
   (地図フォルダへ移動)
 
 >rosrun gnd_lssmap_maker gnd_lssmap_maker test
   エラーが出力されて、test.tmp が生成される。
 test.tmp の中身を編集後 lssmap_maker.conf と名前を変更
  topic-pose=gyrodometry
  topic-laserscan-point=coordinate/scan
  period-cui-status-display=1.0000
  text-log-pointcloud=point_cloud.txt  (#は外しておく)
  text-log-trajectory=trajectory.txt  (#は外しておく)

 >rosrun gnd_lssmap_maker gnd_lssmap_maker lssmap_maker.conf
   地図生成プログラム

 >rosbag play 地図名offset.bag
   録画データの再生
   icartmini_hw_simulation の offsetの値の変動を見ておく
   終了後、lssmap, simulation の順に止める(ctrl + c)

 ros/icartmini/gyrodometer.conf の中身を編集
   offset の default の値を先程の変動値にする
     (変更前の数値は重要でないので上書きで変更して良い)

 >roslaunch ros/launchs/icartmini_hw_simulation.launch
 >rosrun gnd_lssmap_maker gnd_lssmap_maker lssmap_maker.conf
 >rosbag play 地図名makemap.bag
   終了後、 lssmap の終了を確認してから simulation を止める

  Gnuplot で地図の生成過程を見ることができる
  >gnuplot
  >cd "ros/locations/地図フォルダ"
  >plot "trajectory.txt" every 20 using 2:3 w l
  >set size ratio -1
  >replot "point_cloud.txt" every 20 using 2:3 w p pointsize 0.3

 できた地図の確認をする

----------------------------------------------------------------------------
ここから、地図内のルート設定方法

すでにある環境地図フォルダから各種 conf ファイルをコピー
(特にbldg_RandP_5Fが一番ちゃんとしてる)

 lssmap_particle_evaluator.conf (gnd_lssmap_particle_evaluator から作成可)
 navigation_sbtp_icartmini.conf (sbtp_icartmini から作成可)
 particle_localizer.conf  (gnd_particle_localizer から作成可)
 route_editor.conf  (gnd_visualizer gnd_route_editor から作成可)
 visualizer.conf   (gnd_visualizer gnd_visualizer から作成可)

 中身の該当箇所を作成した地図フォルダ名に変えておく (ここのpathが間違っていると動作しない)
 particle_localizer.conf は修正不要(地図データに依存しない)
 テキストエディタの場合 ctrl + f の検索や ctrl + h の置換を利用すると楽

ルートの設定
作成した地図上にロボットが通る経路を設定する
waypoint と呼ばれる目的地兼中継点を設定し、
2つの waypoint 間には直線の経路が引ける
waypoint は目的地と曲がり角や交差点などの中継点に打つ

 >cd /home/kobayashilab/

 >roscore

 >rosrun gnd_visualizer gnd_route_editor ros/locations/地図フォルダ/route_editor.conf
 
   ctrl + クリックで waypoint を打つ。打った順に waypoint の名前がつく(000, 001, 002等)
   shift + クリックで選択。何もないスペースをクリックして選択解除。
   ２つの waypoint を順に選択すると経路を引ける。
   (別のwaypointの移動操作などのときに間違った経路を引かないように注意！)
   ドラックで waypoint の位置、経路の幅を調整可。
   経路を囲っている長方形がそれぞれの経路の幅。
   waypoint や経路を選択後、 delete で削除できる。
   その後 waypoint を打つと削除された waypoint の名前になる。
   ctrl + s で保存 → 端末に戻って ctrl + c で終了
   保存した path ファイルはホームディレクトリに new.path の名前で作成される。

 new.path は 地図フォルダ名.path に名前を変更し、地図フォルダに移動。
   path ファイルには waypoint の名前、座標、パスの幅が記述される。座標を編集するとパスを整えられる。

launch の書き換え

 ros/launchs/各種 launch に作成した地図フォルダを追加しておく。(記述例のように)
   使うものだけ、使うときに変更でも大丈夫
   自律移動システムを動かすときは、icartmini_demo_marker_L.launch
   完成地図確認は、icartmini_localization.launch
 >roslaunch ros/launchs/icartmini_localization.launch を起動するとルート設定後の地図を確認できる。

-----------------------------------------------------------------------------------------------------
その他必要な設定ファイルの作成

すでにある環境地図フォルダから各種 conf ファイルをコピー。
中身の該当箇所を地図フォルダ名に変更する。
navigation_point.csv は修正不要
  (システムの起動時にファイルの存在が必要。ただし、現在は使われておらず中身に意味がないものもある。)

 hdk_waypoint_finder.conf (hdk_waypoint_finder から作成可)
 hdk_pose_resetter_L.conf (hdk_pose_resetter_L から作成可)
 sy_rollsign.conf   (sy_rollsign から作成可)
 navigation_point.csv

 hdk_pose_evaluator.conf  (hdk_pose_evaluator  から作成可)
   自己位置の妥当性を判別するパッケージの設定ファイル
   白黒の2値画像(map-ogm-image8.bmp)が必要
 gimpでmapをレイヤーとして開き、その上にレイヤーを重ねて壁を黒く、レーザが透過する部分を白く塗る。
 元のmapの可視化を解除し、ファイル→名前をつけてエクスポートで、map-ogm-image8.bmpとして地図フォルダに保存する。
