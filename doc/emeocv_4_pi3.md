树莓派移植emeocv
====
[TOC]
- 下载交叉编译器
- 编译依赖库
	- 编译Log4CPP
	- 编译OpenCV
- 编译emeocv

# 下载交叉编译器
- TODO 
- 确认下载路径（/opt/Work/pi/kernel/RpiTools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin）



# 编译依赖库
## 编译Log4CPP

- Download
 > 
wget https://ncu.dl.sourceforge.net/project/log4cpp/log4cpp-1.1.x%20%28new%29/log4cpp-1.1/log4cpp-1.1.3.tar.gz
- tar -xvzf log4cpp-1.1.3.tar.gz
- cd log4cpp
- export PATH=$PATH:/opt/Work/pi/kernel/RpiTools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
- ./configure CC=arm-linux-gnueabihf-gcc CXX=arm-linux-gnueabihf-g++ --build=x86_64 --host=arm-linux 
- make -j8
- cp src/.libs/liblog4cpp.so /usr/local/arm/opencv-depend/lib/

## 交叉编译OpenCV 

- git clone https://github.com/opencv/opencv.git -b 2.4
- mkdir build
- cd build
- vim toolchain.cmake
``` bash
###########user defined#############
set( CMAKE_SYSTEM_NAME Linux )
set( CMAKE_SYSTEM_PROCESSOR arm )
set( CMAKE_C_COMPILER arm-linux-gnueabihf-gcc )
set( CMAKE_CXX_COMPILER arm-linux-gnueabihf-g++ )
###########user defined#############
set( CMAKE_FIND_ROOT_PATH "/usr/local/arm/opencv-depend" )
set( CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER )
set( CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set( CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
######################################
```

- cmake -DCMAKE_TOOLCHAIN_FILE=toolchain.cmake ../

- cmake-gui

``` 
Commandline options:
-DCMAKE_INSTALL_PREFIX:PATH="/usr/local/arm/opencv-depend" -DWITH_OPENCLAMDBLAS:BOOL="0" -DWITH_OPENEXR:BOOL="0" -DWITH_1394:BOOL="0" -DWITH_GTK:BOOL="0" -DWITH_CUDA:BOOL="0" -DWITH_TIFF:BOOL="0" -DWITH_OPENCL:BOOL="0" -DBUILD_opencv_ocl:BOOL="0" -DWITH_LIBV4L:BOOL="0" -DWITH_GSTREAMER:BOOL="0" -DWITH_OPENCLAMDFFT:BOOL="0" 


Cache file:
CMAKE_INSTALL_PREFIX:PATH=/usr/local/arm/opencv-depend
WITH_OPENCLAMDBLAS:BOOL=0
WITH_OPENEXR:BOOL=0
WITH_1394:BOOL=0
WITH_GTK:BOOL=0
WITH_CUDA:BOOL=0
WITH_TIFF:BOOL=0
WITH_OPENCL:BOOL=0
BUILD_opencv_ocl:BOOL=0
WITH_LIBV4L:BOOL=0
WITH_GSTREAMER:BOOL=0
WITH_OPENCLAMDFFT:BOOL=0
```

- vim build/version_string.tmp  to check your configuration

- sudo mkdir /usr/local/arm/opencv-depend -p
- sudo chmod zhou:zhou /usr/local/arm/opencv-depend
- make -j8
- make install

- copy the folder **opencv-depend** to your board, run 
>  ./opencv-depend/bin/opencv_version

# 编译emeocv
- git clone https://github.com/zydz/emeocv
- cd emeocv
- vim Makefile

``` patch 
diff --git a/Makefile b/Makefile
index d6d32c5..c8e1db6 100644
--- a/Makefile
+++ b/Makefile
@@ -9,13 +9,23 @@ OBJS = $(addprefix $(OUTDIR)/,\
   ImageInput.o \
   KNearestOcr.o \
   Plausi.o \
-  RRDatabase.o \
   main.o \
   )
 
-CC = g++
+CC = arm-linux-gnueabihf-g++
 CFLAGS = -Wno-write-strings -I .
 
+CFLAGS +=-I/usr/local/arm/opencv-depend/include/ -I /media/zhou/SG2_ext4/ML/rrdtools/log4cpp/include
 # DEBUG
 ifneq ($(RELEASE),true)
 CFLAGS += -g -D _DEBUG
@@ -26,7 +36,10 @@ endif
 
 BIN := $(OUTDIR)/$(PROJECT)
 
-LDLIBS = -lopencv_core -lopencv_imgproc -lopencv_highgui -lopencv_ml -lrrd -llog4cpp
+LDLIBS = -lopencv_core -lopencv_imgproc -lopencv_highgui -lopencv_ml -llog4cpp \
+-L/usr/local/arm/opencv-depend/lib/
 
 SUFFIXES= .cpp .o
 .SUFFIXES: $(SUFFIXES) .
diff --git a/RRDatabase.cpp b/RRDatabase.cpp
index 7d6dba8..fd1e788 100644
--- a/RRDatabase.cpp
+++ b/RRDatabase.cpp
@@ -3,7 +3,7 @@
  *
  */
 
-#include <rrd.h>
+//#include <rrd.h>
 #include <iostream>
 
 #include <log4cpp/Category.hh>
diff --git a/main.cpp b/main.cpp
index 64a797d..495c0a6 100644
--- a/main.cpp
+++ b/main.cpp
@@ -30,7 +30,7 @@
 #include "ImageProcessor.h"
 #include "KNearestOcr.h"
 #include "Plausi.h"
-#include "RRDatabase.h"
+//#include "RRDatabase.h"
 
 static int delay = 1000;
 
@@ -171,7 +171,7 @@ static void writeData(ImageInput* pImageInput) {
 
     Plausi plausi;
 
-    RRDatabase rrd("emeter.rrd");
+//    RRDatabase rrd("emeter.rrd");
 
     struct stat st;
 
@@ -190,7 +190,7 @@ static void writeData(ImageInput* pImageInput) {
         if (proc.getOutput().size() == 7) {
             std::string result = ocr.recognize(proc.getOutput());
             if (plausi.check(result, pImageInput->getTime())) {
-                rrd.update(plausi.getCheckedTime(), plausi.getCheckedValue());
+//                rrd.update(plausi.getCheckedTime(), plausi.getCheckedValue());
             }
         }
         if (0 == stat("imgdebug", &st) && S_ISDIR(st.st_mode)) {

```

- make
- copy Debug/emeocv to your target board, and run it. 