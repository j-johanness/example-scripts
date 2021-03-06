# image.classify.md
- Creates a new image by replicating a specified image and adding it to a new band of a specified name whose values are generated by classifying those of the specified image according to a specified classifier. 

## Syntax

#### Javascript
```
newImage = oldImage.classify( classifier, newClassificationBandName )
```

- *newImage* is the new image.
- *oldImage.classify* is the specified image, whose band names must be consistent with those called for by classifier. 
- *classifier* is the specified classifier. 
- *newClassificationBandName* is the specified name of the new band, given as a string. 

Argument names used in documentation:
```
https://developers.google.com/earth-engine/api_docs#eeimageclassify
```

## Example

#### Javascript
```
var OriginalIMAGE       = ee.Image( 'LANDSAT/LC8_L1T_TOA/LC82320672013207LGN00' );
var BandSelectedIMAGE   = OriginalIMAGE.select( ['B2','B3','B4','B5','B6','B7','B10','B11'] );

var OriginalFEATURES    = ee.FeatureCollection( 'ft:10X7SUjDTiFJDyIA58zLcptK8pwBwjj1BV12SQOgJ' );
var ClassCodedFEATURES  = OriginalFEATURES.remap( [1, 2], [0, 1], 'class' );
var BandCodedFEATURES   = BandSelectedIMAGE.sampleRegions( ClassCodedFEATURES, ['class'], 30 );

var UntrainedCLASSIFIER = ee.Classifier.cart();
var TrainedCLASSIFIER   = UntrainedCLASSIFIER.train(BandCodedFEATURES, 'class', ['B2','B3','B4','B5','B6','B7','B10','B11']);

var ClassifiedIMAGE     = BandSelectedIMAGE.classify( TrainedCLASSIFIER );
print( 'Image of All Bands',            OriginalIMAGE );
print( 'Image of Selected Bands',       BandSelectedIMAGE );
print( 'Features by Class',             ClassCodedFEATURES );
print( 'Features with Band Properties', BandCodedFEATURES );
print( 'Untrained Classifier',          UntrainedCLASSIFIER );
print( 'Trained Classifier',            TrainedCLASSIFIER );
Map.centerObject( BandSelectedIMAGE, 8 );
Map.addLayer( OriginalIMAGE,      
    {bands:['B4','B3','B2'], max: 0.4},           'Original Image');
Map.addLayer( ClassifiedIMAGE,    
    {min:0, max:1, palette:['007700', '999900']}, 'Classified Image');
Map.addLayer( ClassCodedFEATURES, 
    {color:'0000ff'},                             'Training Points' );

```
