var roi = 
    /* color: #d63000 */
    /* shown: false */
    /* displayProperties: [
      {
        "type": "rectangle"
      }
    ] */
    ee.Geometry.Polygon(
        [[[91.7536717002394, 22.53419674164168],
          [91.7536717002394, 22.459021145015203],
          [91.90679364848158, 22.459021145015203],
          [91.90679364848158, 22.53419674164168]]], null, false);

var s2 = ee.ImageCollection("LANDSAT/LC09/C02/T1_TOA")
        .filterBounds(roi)
      .filterDate("2023-01-01", "2023-12-31") 
      .filterMetadata('CLOUD_COVER', 'less_than', 10)
.median()
.clip(roi);
print(s2);

var ndvi = s2.normalizedDifference(["B8", "B4"]).rename("NDVI")
var ndmi = s2.normalizedDifference(["B6", "B5"]).rename("NDMI")
var ndvimap = ndvi.getThumbURL({
    'region': roi,
    'min': -1,
    'max': 1,
})
print("NDVI Map:")

var ndmimap = ndmi.getThumbURL({
    'region': roi,
    'min': -1,
    'max': 1,
})
print("NDMI Map:")

Map.addLayer(s2)
Map.addLayer(ndvi)
Map.addLayer(ndmi)
