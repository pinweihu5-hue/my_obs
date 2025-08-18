[[跨區 tickets]]


---



# How to create new Zone, import KML

  

ref: [https://note.wemoscooter.com/bnx4ok_5SDeDq0yy2eJ7ng?both](https://note.wemoscooter.com/bnx4ok_5SDeDq0yy2eJ7ng?both)

  
  
  

## Create Map

create one map 

note: code uses Identity column to recognize

![[IMG-import zone kml-20241003104933933.png]]

  
  

and sumbit

![[IMG-import zone kml-20241003104948853.png]]

  
  
  
  
  

click view and you can see layer

![[100_attachements/7118f43cddface7298b4b02ca3b335d3_MD5.png]]

  
  
  
  
  

## Create Layer

click new layer

![[IMG-import zone kml-20241003105005616.png]]

  
  

click sumbit

  

![[IMG-import zone kml-20241003105027383.png]]

  
  

then edit the layer, you shall see

![[IMG-import zone kml-20241003105052129.png]]

  

## Create Zone / Import KML

for each region, import kml for its boundary

- [ref](https://gis.stackexchange.com/questions/183248/getting-polygon-boundaries-of-city-in-json-from-google-maps-api)
- [Nominatim](https://nominatim.openstreetmap.org/ui/search.html)
- [polygons](http://polygons.openstreetmap.fr/index.py)
- [MyGeodata Converter](https://mygeodata.cloud/converter/geojson-to-kml)
    

### Steps

1. search city or place on [Nominatim](https://nominatim.openstreetmap.org/ui/search.html)
2. click “Detail” and find OSM Id
    

  

![[IMG-import zone kml-20241003105120406.png]]

  
  

3. go to [polygons](http://polygons.openstreetmap.fr/index.py) and search with OSM Id
    

  

![[IMG-import zone kml-20241003105120587.png]]

X > 0 會超出預定的區域,  
  = 0 盡量符合  
  < 0 小於預定的區域

  
  

為了降低進線，目前選擇略微超出預定區域

可把 X 設定約 0.001

  

Y, Z 的設定越高, 點數就越少, say 全部都是零, 如下圖，最多情況 12310個點，但需考量效能

![[IMG-import zone kml-20241003105133021.png]]

  

4. download GeoJson file (save file as json)
    

![[IMG-import zone kml-20241003105141924.png]]

  
  
  

5. use [MyGeodata Converter](https://mygeodata.cloud/converter/geojson-to-kml) to convert geo json to kml
    

  

有每月個位數 quote 限制，但目前可透過換瀏覽器刷新使用次數

covert it

![[IMG-import zone kml-20241003105142065.png]]

  

解壓縮

![[IMG-import zone kml-20241003105149598.png]]

  

6. edit kml file 




remove 
```
<Folder> </Folder> 
```

  

因為 apollo 的 KML 只能接受一個 polygon

因此如果你看到 MultiGeometry, 需要拆成一個 polygon一個 kml

(下圖是剛好就是一個 Polygon)
![[IMG-import zone kml-20241003105155506.png]]

  
  

7. import kml with Apollo (如果上面沒有處理好，這邊會跳錯誤)
![[IMG-import zone kml-20241003105155623.png]]

  
  

匯入後，會看到 zone 出來了

右下角 confirm zone 按下就進入 atlas DB 了!
![[IMG-import zone kml-20241003105201057.png]]

  
  

如果要砍 zone, 點remove即可

(這個畫面要點左上拉出才會看到 remove button)

![[IMG-import zone kml-20241003105201180.png]]

  
  

## 其他

目前 apollo 的 KML 匯入有限制
- 無法在同一個 layer 匯入 overlappingk的 zone -> 因此如果兩個 zone 會 overlapping -> 需要用兩個 layer 來處理
- 無法處理挖空的 polygon
- 無法處理 multiple-polygon -> 因此需要開兩個 zone
    
  
