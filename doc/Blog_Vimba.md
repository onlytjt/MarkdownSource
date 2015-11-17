### AVT Vimba与OpenCV环境配置
近来，由于项目需求，需要使用AVT的一款相机采集图像并进行相应的算法处理。环境的配置过程较为复杂，特此记录，以做备忘。也给有需要的小伙伴们一些key point的分享。

**搭建环境：Windows7 + Python2.7 + OpenCV3.0**

##### 1. Python2.7
由于平时的工作基本都是Mac或Linux平台，很少用到Windows，这次也把在Windows上面安装Python的过程做简单记录。

(1) 这里选择软件支持效果较好的Python2.7，[Python官网](https://www.python.org/downloads/)直接下载安装。

(2) 重量级的包管理器pip。[Pip官网](https://pypi.python.org/pypi/pip)下载二进制文件，解压到某个文件夹后，使用cmd进行安装。步骤是，cd到相应目录，输入`python setup.py install`。

![安装pip](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Vimba_1.png)

(3) 安装完毕后使用pip工具安装一些相当有用的lib，比如numpy，scipy，matplotlib等等。
```
pip install numpy
pip install scipy
pip install matplotlib
```
##### 2. OpenCV3.0
(1) 安装包在[OpenCV官网](http://opencv.org/)下载，网速原因，博主下载了好几次才成功。安装过程其实只是简单的解压缩。

(2) 由于使用Python API，进入OpenCV安装目录下`/build/python/2.7/cv2.pyd`。将cv2.pyd复制到Python安装目录下的`/Lib/site-packages/`。

至此就可以使用Python进行OpenCV的相关操作了。

##### 3. AVT Vimba
(1) [AVT官网](https://www.alliedvision.com/en/products/software.html)下载并安装。由于官方仅提供了C/C++的API，因此想使用Python的同学有几个选择：使用ctypes，或者使用pymba。

(2) 这里要感谢伟大的Github，给我们节约了无数的时间，也让我们能够学习到最先进的代码。pymba的Github地址，https://github.com/morefigs/pymba。这里安装过程同pip，下载解压到某个文件夹后，使用cmd shell，cd到相应目录，输入`python setup.py install`。

![安装pymba](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/Vimba_2.png)

(3) 这里要注意两点。一、pymba目前仅支持Vimba1.3的版本，因此下载更高版本的同学在后续使用过程中，可能会遇到各种Bug。二、在安装pymba之后，需要更改pymba默认索引的VimbaC.dll的路径。具体更改方法为找到`/pymba/vimbadll.py`将其中的Line13和Line16更改为自己VimbaC.dll的路径。我的如下：
```
vimbaC_path = r'D:\Program Files\Allied Vision\Vimba_1.4\VimbaC\Bin\VimbaC.dll'
```
重新编译后，就可以尽情地在python中import了！哎~人生苦短，我用Python啊！

关于Vimba相关API的使用，建议查阅官方的Manual。关于cv2的使用，我后续还会持续更新，迫不及待的小伙伴，可以先自行Google。

最后，把我初步的代码附上，我尽可能地将注释写详细~
```
# 实现功能：驱动AVT相机，实时显示图像
# 时间：2015.11.17
import cv2
import numpy as np
import time
from pymba import *

class CameraInterface:
    def __init__(self):
        self.vimba = 0
        self.camera = 0
        self.cameraIds = 0
        self.frame0 = 0
        self.frame1 = 0
        self.moreUsefulImgData = 0

    def openCamera(self):
        self.vimba = Vimba()
        self.vimba.startup()
        print "The Vimba lib version is:", self.vimba.getVersion(), ", and vimba run successfully!"
        system = self.vimba.getSystem()

        # list available cameras (after enabling discovery for GigE cameras)
        system.runFeatureCommand("GeVDiscoveryAllOnce")
        time.sleep(0.2)
        self.cameraIds = self.vimba.getCameraIds()
        print "Available cameras", self.cameraIds
        self.camera = self.vimba.getCamera(self.cameraIds[0])
        self.camera.openCamera()

    def setCameraFeature(self):
        # show camera default features and set some features
        #cameraFeatureNames = camera.getFeatureNames()
        #print cameraFeatureName
        print self.camera.AcquisitionMode
        self.camera.AcquisitionMode = 'SingleFrame'

        # announce memory for frame
        self.frame0 = self.camera.getFrame()
        self.frame1 = self.camera.getFrame()
        self.frame0.announceFrame()

    def captureFrame(self):
        self.camera.startCapture()
        self.frame0.queueFrameCapture()
        self.camera.runFeatureCommand('AcquisitionStart')
        self.camera.runFeatureCommand('AcquisitionStop')
        self.frame0.waitFrameCapture()

        imgData = self.frame0.getBufferByteData()
        self.moreUsefulImgData = np.ndarray(buffer = self.frame0.getBufferByteData(), dtype = np.uint8, shape = (self.frame0.height, self.frame0.width, 1))
        self.camera.endCapture()

    def showImage(self):
        cv2.imshow("Show Image", self.moreUsefulImgData)

    def endShow(self):
        cv2.destroyAllWindows()
        self.vimba.shutdown()

def main():
    ins = CameraInterface()
    ins.openCamera()
    ins.setCameraFeature()
    while True:
        ins.captureFrame()
        ins.showImage()
        if cv2.waitKey(1) == 27: # press esc to quit
            break
    ins.endShow()

if __name__ == "__main__":
    main()
```

***
**By tjt
2015.11.17**