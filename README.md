# Darknet with support for DREML functions
This repository forks from darknet-nnpack and adds support for DREML functions. These functions are enabled by macros in [darknet.h](https://github.com/kammirzazad/darknet-nnpack-dreml/blob/alexeyab/include/darknet.h). The macros and their usecases are explained below:
* **CUSTOM\_BACKPROP**: Enables custom loss functions in backpropogation of region\_layer, yolo\_layer and softmax\_layer
* **DYNAMIC\_FMAP\_PRUNING**: Prevents backpropogation of convolutional layers from calculating weight updates
* **IMG\_SEG**: Adds support for image segmentation as proposed [here](https://github.com/ArtyZe/yolo_segmentation), should be undefined for other applications
* **DET\_THRESH**: Detection threshold used by custom loss functions of region\_layer and yolo\_layer

If you have a question about this repository or a problem using modified darknet, you can contact Kamyar at kammirzazad@utexas.edu. The remainder of this README is same as the original repo.

![build](https://github.com/kammirzazad/darknet-nnpack-dreml/actions/workflows/build.yml/badge.svg)

# Darknet with NNPACK
NNPACK was used to optimize [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet) without using a GPU. It is useful for embedded devices using ARM CPUs.

## Build from Raspberry Pi 4
Log in to Raspberry Pi using SSH.<br/>
Install [PeachPy](https://github.com/Maratyszcza/PeachPy) and [confu](https://github.com/Maratyszcza/confu)
```
sudo pip install --upgrade git+https://github.com/Maratyszcza/PeachPy
sudo pip install --upgrade git+https://github.com/Maratyszcza/confu
```
Install [Ninja](https://ninja-build.org/)
```
git clone https://github.com/ninja-build/ninja.git
cd ninja
git checkout release
./configure.py --bootstrap
export NINJA_PATH=$PWD
```
Install clang
```
sudo apt-get install clang
```
Install [NNPACK-darknet](https://github.com/digitalbrain79/NNPACK-darknet.git)
```
git clone https://github.com/digitalbrain79/NNPACK-darknet.git
cd NNPACK-darknet
confu setup
python ./configure.py --backend auto
$NINJA_PATH/ninja
sudo cp -a lib/* /usr/lib/
sudo cp include/nnpack.h /usr/include/
sudo cp deps/pthreadpool/include/pthreadpool.h /usr/include/
```
Build darknet-nnpack
```
git clone https://github.com/digitalbrain79/darknet-nnpack.git
cd darknet-nnpack
make
```

## Test
COCO trained weights files can be downloaded from the [AlexeyAB/darknet](https://github.com/AlexeyAB/darknet).
```
COCO
./darknet detector test cfg/coco.data [cfg file] [weights file] [image path]
```
```
Pascal VOC
./darknet detector test cfg/voc.data [cfg file] [weights file] [image path]
```
## Results
#### COCO
cfg | Build Options | mAP | Prediction Time (seconds)
:-:|:-:|:-:|:-:
yolov3-tiny.cfg | NNPACK=1 | 33.1 | 1.1
yolov3-tiny.cfg | NNPACK=0 | | 14.5
yolov3-tiny-prn.cfg | NNPACK=1 | 33.1 | 0.86
yolov3-tiny-prn.cfg | NNPACK=0 | | 9.3

#### Pascal VOC
cfg | Build Options | mAP | Prediction Time (seconds) | Weights file
:-:|:-:|:-:|:-:|:-:
yolov3-tiny-voc.cfg | NNPACK=1 | 65.9 | 1.0 | [yolov3-tiny-voc.weights](https://drive.google.com/open?id=1gP531RumQnuGlMUUcQgymktatWajF4mH)
yolov3-tiny-voc.cfg | NNPACK=0 | | 14.0 | 
yolov3-tiny-prn-voc.cfg | NNPACK=1 | 65.2 | 0.77 | [yolov3-tiny-prn-voc.weights](https://drive.google.com/open?id=1NljMzqeFxu0Kr04iftjc-zSL0Nxkns1n)
yolov3-tiny-prn-voc.cfg | NNPACK=0 | | 8.9 | 
Gaussian_yolov3-tiny-voc.cfg | NNPACK=1 | 65.7 | 1.0 | [Gaussian_yolov3-tiny-voc.weights](https://drive.google.com/open?id=1qHdCsYsyvPX37pNoYpoug-FUUtu_1HxM)

## Raspberry Pi OS Image
Download OS image from [here](https://drive.google.com/open?id=1D9XRKn8eYiGokf_uN1Pwkqtnt_ae5SAQ)
```
sudo dd bs=4M if=darknet-nnpack.img of=/dev/sdX conv=fsyn
```
