编译emeocv
====


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