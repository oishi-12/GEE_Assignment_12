var roi = 
    /* color: #d63000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[91.78062194968781, 22.524967739891324],
          [91.78062194968781, 22.485637829595415],
          [91.8513464369925, 22.485637829595415],
          [91.8513464369925, 22.524967739891324]]], null, false);


var s2 = ee.ImageCollection("LANDSAT/LC09/C02/T1_TOA")
        .filterBounds(roi)
      .filterDate("2022-01-01", "2022-12-31") 
      .filterMetadata('CLOUD_COVER', 'less_than', 10)
.median()
.clip(roi);
print(s2);

var ndvi = s2.normalizedDifference(["B4", "B5"]).rename("NDVI")
var class1 = ndvi.gte(-1).and(ndvi.lte(0)).rename("class1").selfMask()
var class2 = ndvi.gt(0).and(ndvi.lte(0.1)).rename("class2").selfMask()
var class3 = ndvi.gt(0.1).and(ndvi.lte(0.2)).rename("class3").selfMask()
var class4 = ndvi.gt(0.2).rename("class4").selfMask()
var img = ee.Image.pixelArea()
Map.addLayer(img)
var ar1 = class1.multiply(img).reduceRegion({
  geometry: roi,
  reducer: ee.Reducer.sum(),
  scale: 10
})
print(ar1)

var ar2 = class2.multiply(img).reduceRegion({
  geometry: roi,
  reducer: ee.Reducer.sum(),
  scale: 10
})
print(ar2)

var ar3 = class3.multiply(img).reduceRegion({
  geometry: roi,
  reducer: ee.Reducer.sum(),
  scale: 10
})
print(ar3)

var ar4 = class4.multiply(img).reduceRegion({
  geometry: roi,
  reducer: ee.Reducer.sum(),
  scale: 10
})
print(ar4)
Map.addLayer(class1,{},"masked ndvi")
Map.addLayer(class2,{},"masked ndvi2")
Map.addLayer(class3,{},"masked ndvi3")
Map.addLayer(class4,{},"masked ndvi4")

Map.addLayer(s2)
Map.addLayer(ndvi)
