# NYC Traffic & Air Quality Dataset

## 项目概述
本数据集整合了2024年1月纽约市的出租车行程数据、空气质量监测数据、道路网络数据和气象数据，用于分析交通活动与PM2.5浓度的关系。

## 数据文件
### 文件结构
- `data/raw/` - 原始数据
- `data/processed/` - 清洗后数据  
- `scripts/src` - 处理脚本
- `scripts/tests` - 单元测试脚本
- `outputs/` - 最终结果和图表

### 运行顺序
`clean_taxi_data.py` -> `merge_taxi_air_station.py` —> `clean_air_data.py` -> `merge_osm_and_climate.py` -> `vis_and_ans.py` -> `dash_app.py`

### 主要数据集
- `final_complete_dataset.csv` - 完整整合数据集 (10,143行 × 57列)

### 原始数据文件
- `cleaned_taxi_data_2024_01.csv` - 清洗后的出租车数据
- `station-info.csv` - 空气质量站点信息  
- `weather_nyc_2024_01.json` - 气象数据
- `taxi_zones.shp` - 出租车区域边界文件
- `gis_osm_roads_free_1.shp` - OSM道路网络数据
- `AirQuality2401.csv` - 原始空气质量监测数据

## 数据维度

- **时间范围**: 2024-01-01 00:00:00 至 2024-01-31 23:00:00
- **空间范围**: 纽约市5个行政区
- **空气质量站点**: 16个监测站
- **时间粒度**: 小时级别
- **总记录数**: 10,143条

## 特征类别

### 1. 标识特征
- `timestamp` - 时间戳 (小时粒度)
- `site_id` - 空气质量站点ID
- `site_name` - 站点名称
- `borough` - 所属行政区

### 2. 目标变量
- `PM2_5` - PM2.5浓度 (μg/m³)

### 3. 交通特征
- `trip_count` - 行程数量
- `total_distance` - 总行驶距离
- `avg_speed` - 平均车速
- `total_passengers` - 乘客总数
- `trip_density` - 行程密度
- `passenger_density` - 乘客密度

### 4. 道路特征
- `road_density_[300/500/1000]m` - 道路密度
- `total_road_length_[300/500/1000]m` - 道路总长度
- `major_road_ratio_[300/500/1000]m` - 主要道路比例
- `intersection_density_[300/500/1000]m` - 交叉口密度

### 5. 气象特征
- `temperature` - 温度 (°C)
- `humidity` - 湿度 (%)
- `pressure` - 气压 (hPa)
- `wind_speed` - 风速 (m/s)
- `wind_direction` - 风向 (°)
- `precipitation` - 降水量 (mm)
- `wind_direction_cat` - 风向分类
- `wind_speed_cat` - 风速分类
- `temperature_cat` - 温度分类

### 6. 时间特征
- `hour_of_day` - 小时 (0-23)
- `day_of_week` - 星期几 (0-6)
- `is_weekend` - 是否周末
- `is_rush_hour` - 是否高峰时段
- `sin_hour`, `cos_hour` - 小时周期特征
- `sin_day`, `cos_day` - 星期周期特征

### 7. 时序特征
- `*_lag_[1/3/6]` - 滞后特征 (1/3/6小时)
- `*_ma_[3/6/12]` - 移动平均特征 (3/6/12小时)

## 数据质量说明

### 数据来源

1. **出租车数据**: NYC TLC Trip Record Data [https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page]
2. **空气质量数据**: NYC Air Quality Monitoring Network[https://a816-dohbesp.nyc.gov/IndicatorPublic/data-features/realtime-air-quality/]
3. **气象数据**: Open-Meteo Historical Weather API [https://archive-api.open-meteo.com/v1/archive?latitude=40.7128&longitude=-74.0060&start_date=2024-01-01&end_date=2024-01-31&hourly=temperature_2m,relative_humidity_2m,pressure_msl,wind_speed_10m,wind_direction_10m,precipitation&timezone=America%2FNew_York]
4. **道路数据**: OpenStreetMap [https://download.geofabrik.de/north-america/us/new-york.html] 
5. **出租车行政区划**: NYC Taxi Zones [https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page]
6. **Dash的行政区划**：[https://raw.githubusercontent.com/dwillis/nyc-maps/master/boroughs.geojson]

### 完整数据集列统计报告
#### 🎯 特征概览
| 特征类别 | 特征数量 | 数据类型分布 | 总记录数 | 完整记录数 | 数据完整性 |
|---------|----------|--------------|----------|------------|------------|
| 空气质量特征 | 7 | float64: 7 | 37,111 | 31,996 | 86.2% |
| 交通特征 | 29 | float64: 26, object: 3 | 37,111 | 31,996 | 86.2% |
| 道路特征 | 12 | float64: 12 | 37,111 | 31,996 | 86.2% |
| 气象特征 | 15 | float64: 12, int64: 2, object: 1 | 37,111 | 31,996 | 86.2% |
| 时间特征 | 8 | int64: 2, bool: 2, float64: 4 | 37,111 | 31,996 | 86.2% |
| 标识特征 | 3 | datetime64[ns]: 1, object: 2 | 37,111 | 31,996 | 86.2% |
| **总计** | **74** | **数值: 68, 分类: 5, 时间: 1** | **37,111** | **31,996** | **86.2%** |

#### 🎯 空气质量特征 (7个特征)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| PM2_5 | float64 | 8.394 | 0.1 | 71.0 | 7.642 |
| PM2_5_lag_1 | float64 | 8.394 | 0.1 | 71.0 | 7.642 |
| PM2_5_lag_3 | float64 | 8.394 | 0.1 | 71.0 | 7.642 |
| PM2_5_lag_6 | float64 | 8.480 | 0.1 | 71.0 | 7.644 |
| PM2_5_ma_3 | float64 | 7.518 | 0.1 | 51.267 | 7.632 |
| PM2_5_ma_6 | float64 | 7.097 | 0.3 | 45.883 | 7.638 |
| PM2_5_ma_12 | float64 | 6.800 | 0.5 | 38.533 | 7.653 |

#### 🎯 交通特征 - 核心指标 (9个)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| trip_count | float64 | 0.000 | 0.0 | 1490.286 | 264.754 |
| total_distance | float64 | 0.000 | 0.0 | 4768.943 | 867.685 |
| avg_trip_distance | float64 | 0.000 | 0.0 | 29.580 | 5.176 |
| avg_speed | float64 | 0.000 | 0.0 | 58.696 | 13.363 |
| total_passengers | float64 | 0.000 | 0.0 | 1844.857 | 349.856 |
| total_revenue | float64 | 0.000 | 0.0 | 36650.399 | 7069.489 |
| avg_duration | float64 | 0.000 | 0.0 | 2.535 | 0.318 |
| trip_density | float64 | 0.000 | 0.0 | 25.216 | 4.144 |
| passenger_density | float64 | 0.000 | 0.0 | 31.216 | 5.466 |

#### 🎯 交通特征 - 滞后特征 (12个)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| trip_count_lag_1 | float64 | 0.000 | 0.0 | 1490.286 | 264.692 |
| avg_speed_lag_1 | float64 | 0.000 | 0.0 | 58.696 | 13.355 |
| passenger_density_lag_1 | float64 | 0.000 | 0.0 | 31.216 | 5.465 |
| trip_count_lag_3 | float64 | 0.000 | 0.0 | 1490.286 | 264.445 |
| avg_speed_lag_3 | float64 | 0.000 | 0.0 | 58.696 | 13.342 |
| passenger_density_lag_3 | float64 | 0.000 | 0.0 | 31.216 | 5.460 |
| trip_count_lag_6 | float64 | 0.000 | 0.0 | 1490.286 | 263.982 |
| avg_speed_lag_6 | float64 | 0.000 | 0.0 | 58.696 | 13.321 |
| passenger_density_lag_6 | float64 | 0.000 | 0.0 | 31.216 | 5.451 |
| trip_count_ma_3 | float64 | 0.000 | 0.0 | 1398.619 | 264.858 |
| avg_speed_ma_3 | float64 | 0.000 | 0.0 | 37.770 | 13.363 |
| trip_count_ma_6 | float64 | 0.000 | 0.0 | 1261.548 | 264.921 |

#### 🎯 交通特征 - 移动平均与风速 (8个)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| avg_speed_ma_6 | float64 | 0.000 | 0.0 | 34.849 | 13.364 |
| trip_count_ma_12 | float64 | 0.000 | 0.0 | 1073.083 | 264.869 |
| avg_speed_ma_12 | float64 | 0.000 | 0.0 | 34.849 | 13.367 |
| wind_speed | float64 | 0.000 | 0.0 | 15.917 | 3.894 |
| wind_speed_cat | object | 0.092 | NaN | NaN | NaN |
| wind_speed_lag_1 | float64 | 0.046 | 0.0 | 15.917 | 3.894 |
| wind_speed_lag_3 | float64 | 0.137 | 0.0 | 15.917 | 3.894 |
| wind_speed_lag_6 | float64 | 0.275 | 0.0 | 15.917 | 3.894 |

#### 🎯 道路特征 (12个特征)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| road_density_300m | float64 | 62.5 | 51216.435 | 111878.314 | 81140.917 |
| total_road_length_300m | float64 | 62.5 | 14457.855 | 31582.058 | 22905.218 |
| major_road_ratio_300m | float64 | 62.5 | 0.039 | 0.226 | 0.151 |
| intersection_density_300m | float64 | 62.5 | 605.761 | 1934.186 | 1210.342 |
| road_density_500m | float64 | 62.5 | 64154.867 | 104039.964 | 78632.939 |
| total_road_length_500m | float64 | 62.5 | 50306.213 | 81581.598 | 61659.007 |
| major_road_ratio_500m | float64 | 62.5 | 0.112 | 0.260 | 0.161 |
| intersection_density_500m | float64 | 62.5 | 661.874 | 2013.678 | 1202.171 |
| road_density_1000m | float64 | 62.5 | 58133.704 | 83177.009 | 67192.406 |
| total_road_length_1000m | float64 | 62.5 | 182339.181 | 260888.723 | 210752.240 |
| major_road_ratio_1000m | float64 | 62.5 | 0.092 | 0.221 | 0.150 |
| intersection_density_1000m | float64 | 62.5 | 665.700 | 1629.817 | 1024.215 |

#### 🎯 气象特征 - 基础指标 (6个)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| temperature | float64 | 0.000 | -9.1 | 21.5 | 3.335 |
| humidity | int64 | 0.000 | 23 | 100 | 68.724 |
| pressure | float64 | 0.000 | 982.6 | 1038.0 | 1016.222 |
| wind_speed | float64 | 0.000 | 0.0 | 15.917 | 3.894 |
| wind_direction | int64 | 0.000 | 1 | 360 | 212.117 |
| precipitation | float64 | 0.000 | 0.0 | 14.8 | 0.204 |

#### 🎯 气象特征 - 分类与滞后 (9个)
| 特征名称 | 数据类型 | 缺失比例(%) | 最小值 | 最大值 | 平均值 |
|----------|----------|-------------|--------|--------|--------|
| wind_direction_cat | object | 0.000 | NaN | NaN | NaN |
| wind_speed_cat | object | 0.092 | NaN | NaN | NaN |
| temperature_cat | object | 0.000 | NaN | NaN | NaN |
| temperature_lag_1 | float64 | 0.046 | -9.1 | 21.5 | 3.333 |
| wind_speed_lag_1 | float64 | 0.046 | 0.0 | 15.917 | 3.894 |
| temperature_lag_3 | float64 | 0.137 | -9.1 | 21.5 | 3.329 |
| wind_speed_lag_3 | float64 | 0.137 | 0.0 | 15.917 | 3.894 |
| temperature_lag_6 | float64 | 0.275 | -9.1 | 21.5 | 3.317 |
| wind_speed_lag_6 | float64 | 0.275 | 0.0 | 15.917 | 3.894 |

#### 🎯 时间特征 (8个特征)
| 特征名称 | 数据类型 | 缺失比例(%) |
|----------|----------|-------------|
| hour_of_day | int64 | 0.0 |
| day_of_week | int64 | 0.0 |
| is_weekend | bool | 0.0 |
| is_rush_hour | bool | 0.0 |
| sin_hour | float64 | 0.0 |
| cos_hour | float64 | 0.0 |
| sin_day | float64 | 0.0 |
| cos_day | float64 | 0.0 |

#### 🎯 标识特征 (3个特征)
| 特征名称 | 数据类型 | 缺失比例(%) | 唯一值数量 | 最常见值 |
|----------|----------|-------------|------------|----------|
| timestamp | datetime64[ns] | 0.0 | 2183.0 | 2024-01-01 00:00:00 |
| site_id | object | 0.0 | 17.0 | 360050080 |
| borough | object | 0.0 | 5.0 | Manhattan |

### 🔍 关键数据发现总结

#### 🌫️ PM2.5浓度统计
| 指标 | 数值 |
|------|------|
| 平均值 | 7.64 μg/m³ |
| 中位数 | 6.30 μg/m³ |
| 标准差 | 4.95 μg/m³ |
| 超过WHO标准 | 7.3% 的记录 |
| 超过EPA标准 | 0.1% 的记录 |

#### 🚗 交通活动统计
| 指标 | 数值 |
|------|------|
| 平均每小时行程数 | 264.8 |
| 总乘客数 | 12,219,773 |

#### 🗺️ 空间分布
| 区域 | 站点数量 | 行程数量 | PM2.5平均值 |
|------|----------|----------|-------------|
| Bronx | 3 | 20,815 | 7.44 μg/m³ |
| Brooklyn | 1 | 86,787 | 6.74 μg/m³ |
| Manhattan | 8 | 8,348,548 | 8.07 μg/m³ |
| Queens | 3 | 791,011 | 7.42 μg/m³ |
| Staten Island | 2 | 156 | 7.45 μg/m³ |

#### 📊 数据质量评估
| 评估指标 | 数值 |
|----------|------|
| 总体缺失率 | 10.34% |
| 可用建模记录 | 31,996 行 |
| 完整特征覆盖率 | 86.2% |
