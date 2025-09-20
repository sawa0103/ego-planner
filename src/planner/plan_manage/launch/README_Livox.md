# Livox MID-360 Integration with EGO-Planner

このディレクトリには、Livox MID-360 LiDARセンサーをEGO-Plannerで使用するためのlaunchファイルが含まれています。

## 作成されたファイル

### 1. run_with_livox.launch
- **用途**: Livox MID-360用の完全なEGO-Planner設定
- **特徴**: 
  - シミュレータを使用しない実環境向け
  - Livox用に最適化されたパラメータ
  - RViz可視化付き
  - 高性能飛行設定

### 2. simple_livox.launch  
- **用途**: 基本的なLivox統合
- **特徴**:
  - シンプルな設定
  - 初回テスト用
  - 保守的なパフォーマンス設定

### 3. livox_param.xml
- **用途**: Livox MID-360に最適化されたパラメータ設定
- **最適化内容**:
  - グリッドマップ解像度: 0.2m
  - 最大レイ長: 120m (MID-360の最大レンジ)
  - より大きなローカルマップ範囲
  - LiDAR用の確率的占有グリッド設定

## 使用方法

### 前提条件
1. Livox ROS Driver 2がインストールされていること
2. Livox MID-360が適切に設定されていること
3. 以下のトピックが利用可能であること:
   - `/livox/points` (PointCloud2)
   - `/Odometry` (nav_msgs/Odometry)

### 起動手順

#### 1. Livox ROS Driverの起動
```bash
# MID-360用のLivoxドライバーを起動
roslaunch livox_ros_driver2 rviz_MID360.launch
```

#### 2. EGO-Plannerの起動

**完全版 (推奨)**:
```bash
roslaunch ego_planner run_with_livox.launch
```

**シンプル版**:
```bash
roslaunch ego_planner simple_livox.launch
```

### 設定の調整

#### マップサイズの変更
```xml
<arg name="map_size_x" value="80.0"/>  <!-- X方向のマップサイズ (m) -->
<arg name="map_size_y" value="80.0"/>  <!-- Y方向のマップサイズ (m) -->
<arg name="map_size_z" value="6.0"/>   <!-- Z方向のマップサイズ (m) -->
```

#### 性能パラメータの調整
```xml
<arg name="max_vel" value="3.0" />      <!-- 最大速度 (m/s) -->
<arg name="max_acc" value="4.0" />      <!-- 最大加速度 (m/s²) -->
<arg name="planning_horizon" value="15.0" />  <!-- プランニング範囲 (m) -->
```

## トピック構成

### 入力トピック
- `/livox/points`: Livox MID-360からのPointCloud2データ
- `/Odometry`: ドローンの位置・姿勢情報

### 出力トピック
- `/planning/pos_cmd`: 生成された軌道コマンド
- `/grid_map/occupancy`: 占有格子マップ
- `/grid_map/occupancy_inflate`: 膨張済み占有格子マップ

## トラブルシューティング

### 1. Point Cloudが表示されない場合
- `/livox/points`トピックが配信されているか確認
- フレーム設定を確認 (livox_frame → world変換)

### 2. プランニングが実行されない場合
- Odometryトピックが適切に配信されているか確認
- マップサイズとプランニング範囲の設定を確認

### 3. 性能問題
- `livox_param.xml`の解像度設定を調整
- プランニング範囲を小さくする
- Point Cloudのデータレートを確認

## パラメータの意味

### Grid Map設定
- `resolution`: グリッドの解像度 (m/cell)
- `max_ray_length`: レイキャストの最大距離 (m)
- `obstacles_inflation`: 障害物の膨張サイズ (m)

### Planning設定  
- `planning_horizon`: プランニングの先読み距離 (m)
- `control_points_distance`: B-spline制御点間隔 (m)

設定に問題がある場合は、各パラメータファイルを確認してください。