### Qt串口通信
##### 1. Qt串口通信类QSerialPort
在Qt5的的更新中，新增了串口通信的相关接口类QSerialPort，这使得在开发者在使用Qt进行UI开发时，可以更加简单有效地实现串口通信的相关功能。  
开发者可以简单地通过`#include<QSerialPort>`，来使用系统包含的库函数。然而这里有一个非常容易忽略的问题，会导致编译时的报错。在使用QSerialPort类之前，需要在Qt的配置文件即.pro文件中添加`Qt += serialport`。
##### 2. 代码示例
a. 串口的初始化：
```
QSerialPort *serial = new QSerialPort();
QObject::connect(serial, SIGNAL(readyRead()), this, SLOT(recSerialData())); // 连接串口收到数据事件与读取数据函数
serial->setPortName("COM3");
serial->setBaudRate(QSerialPort::Baud9600);
serial->setParity(QSerialPort::NoParity);
serial->setDataBits(QSerialPort::Data8);
serial->setStopBits(QSerialPort::OneStop);
serial->setFlowControl(QSerialPort::NoFlowControl);
if (serial->open(QIODevice::ReadWrite)) {
    qDebug()<<"open success";
} else {
    qDebug()<<"open failed";
}
```
b. 向串口发送数据
```
serial->write("test");
```
c. 接收串口返回的数据
```
void MySerial::recSerialData()
{
    QByteArray ba;
    ba = serial->readAll();
    display(ba);
}
```
##### 3. 总结
总的来说，使用Qt5进行串口通信是较为简单的。大致步骤为：配置串口参数->打开串口->收发数据。

##### 4. 附录（Mac下的串口使用Tip）
在Mac下使用串口，基本流程和步骤同Windows差异不是很大，但是有一些细节，还是想逼叨两句，让受到困扰的同志们少走弯路。  
首先是串口驱动程序的下载和安装[CH341SER for Mac](http://0xcf.com/2015/03/13/chinese-arduinos-with-ch340-ch341-serial-usb-chip-on-os-x-yosemite/)。在安装完成后，由于OS X Yosemite10以后的版本，默认开机不加载第三方驱动程序，所以在驱动安装完成后，需要在terminal中输入`sudo nvram boot-args=”kext-dev-mode=1`来默认加载驱动程序。如果重启开机后，在网络设置中看到下图中的“USB2.0”，那么证明你的Mac安装串口驱动成功。  
![pic_usb](https://raw.githubusercontent.com/onlytjt/MarkdownSource/master/pic/QtSerial_1.png)

之后插入相应的设备，在terminal中输入`ls /dev/ | grep ch`则可以看到设备的名称，在串口初始化的步骤中进行更改`serial->setPortName("your-port-name");`，现在你就可以顺利地使用Mac进行串口的相关操作了！  
Mac串口通信参考资料：http://0xcf.com/2015/03/13/chinese-arduinos-with-ch340-ch341-serial-usb-chip-on-os-x-yosemite/  
***
By tjt  
2015.09.02



