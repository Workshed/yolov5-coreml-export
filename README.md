# yolov5 CoreML export

## TLDR;

If you train or finetune a model with [yolov5](https://github.com/ultralytics/yolov5) and then export to CoreML you'll have a bad time. Dragging and dropping it in to Xcode will leave you with a whole bag of complex stuff that needs doing in order to be able to use that model in your iOS or macOS code.

Instead of exporting a CoreML model using the script included in [yolov5](https://github.com/ultralytics/yolov5) run this export script instead.

## Usage

Use the same parameters you'd use for the original export script, for example:

```
python export-nms.py --weights weights.pt --include "coreml"
```

## The details

While the official [yolov5](https://github.com/ultralytics/yolov5) repository includes an export for CoreML, it's missing some features that many would consider essential for using the resulting model. e.g. 

https://github.com/ultralytics/yolov5/issues/535
https://github.com/ultralytics/yolov5/issues/1575

There's a glaring lack of information about this on the Ultralytics docs site: 

https://docs.ultralytics.com/yolov5/tutorials/model_export/#exported-model-usage-examples

## What's missing?

This [site](https://machinethink.net/blog/bounding-boxes/) (and this [page](https://machinethink.net/blog/mobilenet-ssdlite-coreml/) as well as this [one](https://machinethink.net/blog/object-detection/) from the same author), explains the issue.  

### Non-Maximum Suppression model

One stage detectors, like YOLO, are not only performing classification they're also performing detection. There may be several objects in an image and these must be detected and classified. 

YOLO uses a fixed grid of detectors, each cell of the grid can end up with overlapping predictions. This is where non-maximum suppression (NMS) comes in, it keeps the predictions with the highest confidence scores and removes other boxes that overlap with lower confidence. 

### Output types

The default export script will just spit out a `MultiArray` and you're left to your own devices to sort out whats what. Instead this script sets up the NMS and specifies types including an image, thresholds, confidence, etc. 

### mlpackage

The export from yolov5 will result in a `<model>.mlmodel` file, the newer format is `<model>.mlpackage`.

### Xcode previews

Exporting with the correct types etc specified means we can use Xcode's preview of the model to drag and drop test images and Xcode displays results from the model.

## Where has this script come from?

This script is almost entirely from Julius Hietala's yolov5 fork [here](https://github.com/hietalajulius/yolov5)

### Other interesting links

https://github.com/dbsystel/yolov5-coreml-tools
https://www.neuralception.com/exportcoreml/

