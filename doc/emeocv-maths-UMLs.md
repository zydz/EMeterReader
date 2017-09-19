原理和实现的理论基础
======

### OpenCV简介

OpenCV的全称是Open Source Computer Vision Library，是一个跨平台的计算机视觉库。

OpenCV用C++语言编写，它的主要接口也是C++语言，但是依然保留了大量的C语言接口。该库也有大量的Python, Java and MATLAB/OCTAVE (版本2.5)的接口。这些语言的API接口函数可以通过在线文档获得。现在也提供对于C#, Ruby的支持。

它有以下特点：

- 1) 开放的C/C++源码
- 2) 基于Intel处理器指令集开发的优化代码
- 3) 统一的结构和功能定义
- 4) 强大的图像和矩阵运算能力
- 5) 方便灵活的用户接口
- 6）同时支持MS-WINDOWS、LINUX平台

作为一个基本的计算机视觉、图像处理和模式识别的开源项目，OPENCV可以直接应用于很多领域，作为第二次开发的理想工具。

### OCR简介

OCR （Optical Character Recognition，光学字符识别）是指电子设备（例如扫描仪或数码相机）检查纸上打印的字符，通过检测暗、亮的模式确定其形状，然后用字符识别方法将形状翻译成计算机文字的过程；即，对文本资料进行扫描，然后对图像文件进行分析处理，获取文字及版面信息的过程。如何除错或利用辅助信息提高识别正确率，是OCR最重要的课题，ICR（Intelligent Character Recognition）的名词也因此而产生。衡量一个OCR系统性能好坏的主要指标有：拒识率、误识率、识别速度、用户界面的友好性，产品的稳定性，易用性及可行性等。

### 数学原理
- 边沿检测的算法（Canny）
	- 请参考维基百科的解释
[wiki](https://en.wikipedia.org/wiki/Canny_edge_detector)

#### 流程图
![ Program flow](https://github.com/zydz/EMeterReader/blob/master/doc/pictures/emworkflow.png)
### 流程分析
- 抓图保存
- 图像处理
	- Canny算法，边沿识别
	- 设置阀值，选取两条直线之间的区域
- 分割数字区域
	- 在上一步选取的区域中，用矩形来识别数字区域 
- 处理数字区域的数字图形
	- 监督学习，并把训练数据存储在training.yml里
	- 测试识别率，验证学习效果
	- 正常运行 


#### 实现-UML

- 类图
![class](https://github.com/zydz/EMeterReader/blob/master/doc/pictures/class.png )

- 时序图一 调整摄像头
![adjust mode](https://github.com/zydz/EMeterReader/blob/master/doc/pictures/adjust-mode.jpg )

- 时序图二 工作模式
![normal mode](https://github.com/zydz/EMeterReader/blob/master/doc/pictures/normal-mode.jpg )
