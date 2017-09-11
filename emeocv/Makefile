.PHONY: clean, mrproper

PROJECT = emeocv

OBJS = $(addprefix $(OUTDIR)/,\
  Directory.o \
  Config.o \
  ImageProcessor.o \
  ImageInput.o \
  KNearestOcr.o \
  Plausi.o \
  main.o \
  )

#  RRDatabase.o \


#export PATH=$PATH:/opt/Work/pi/kernel/RpiTools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
#KERNEL=kernel7
#make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
#make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs

#CC = g++
CC = arm-linux-gnueabihf-g++
CFLAGS = -Wno-write-strings -I .

CFLAGS +=-I/usr/local/arm/opencv-depend/include/ -I /media/zhou/SG2_ext4/ML/rrdtools/log4cpp/include

# DEBUG
ifneq ($(RELEASE),true)
CFLAGS += -g -D _DEBUG
OUTDIR = Debug
else
OUTDIR = Release
endif

BIN := $(OUTDIR)/$(PROJECT)

LDLIBS = -lopencv_core -lopencv_imgproc -lopencv_highgui -lopencv_ml -llog4cpp \
-L/usr/local/arm/opencv-depend/lib/

#LDLIBS += -lrrd

SUFFIXES= .cpp .o
.SUFFIXES: $(SUFFIXES) .


all: $(BIN)

$(OUTDIR):
	mkdir $(OUTDIR)
	
$(OBJS): $(OUTDIR)/%.o: %.cpp
	$(CC) $(CFLAGS) -c $< -o $@

$(BIN) : $(OUTDIR) $(OBJS)
	$(CC) $(CFLAGS) $(LDFALGS) $(OBJS) $(LDLIBS) -o $(BIN)

.cpp.o:
	$(CC) $(CFLAGS) -c $*.cpp

clean:
	rm -rf $(OUTDIR)/*.o

mrproper: clean
	rm -rf $(BIN)
