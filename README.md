# 期中考實戰題(成績: 5/52)
---
### 如何讀取檔案並調整成適當的閱讀方式
- 使用glob模組抓取各股csv路徑

```
path_list = glob.glob('stock_data_final/stock_data/*.csv')
```

- 將各股dataframe組合成大dataframe以便檢視資料全貌
- 將大dataframe以日期作排序

### 進行資料觀察(Explore the Data)並以你的觀點闡述觀察到的現象
- 首先畫出個股收盤價的序列圖後，發現有許多的資料斷點，因此改用df.isnull做heatmap，如下圖。由圖中可以發現，資料中除了隨機遺失值，還有4個個股在數天均沒有資料的情況。
![](https://i.imgur.com/NhYQZaR.png)
- 雖然由corr matrix可以看出各feature的與0056收盤價的相關係數，但由pair plot的散佈圖中可看到關係不明顯，應該是資料屬於時間序列資料的關係。
- 針對資料遺失的特性，採去不同的處理方法
-- 1.針對隨機遺失，採用線性插值補值
-- 2.針對連續遺失，先確認其股價與0056的關係後，考慮"刪除整個"個股特徵。

- 由下圖，可以發現有些單一個股的走勢未反映在0056趨勢，可能是該段期間單一個股的操作。
![](https://i.imgur.com/iaBYCRs.png)
- 最後決定:不採用9945,2915,2327,3532

### 資料前處理(Data preprocess)
- Data Cleaning : 針對隨機遺失，採用線性插值補值
```
df.interpolate(method='linear')
```
- Feature Engineering : 新增下列特徵
-- 當日最大振福 (high - low)
-- 5, 10, 20日移動平均
-- 周日別 (周一至周五)
-- 刪除個股收盤價

- Feature Normalization
-- min-max scaling

### 模型的建置
- 嘗試以下模型:
-- random forest
-- kernel ridge regression
-- xgboost
-- LightGBM
-- SVR
-- stacking above
### 預測的結果與輸出
- 一開始使用stacking，效果一直很差，便意識到有overfitting的可能，因此畫出單一mdoel的預測結果，由圖中可發現SVR與其它結果容易overfitting的演算法差異頗大，因此最終採用SVR，並使用grid search找尋最佳參數。
![](https://i.imgur.com/UrPnzaY.png)
