PC命令
====

emeocv使用的一般步骤

- 摄像头安装
	- 摄像头安装在表盘的前方10-20cm，拍摄效果尽量清晰完整, 表盘占图片的比例应该在50%——90%
	- 样张 TODO
    
- 取样
	- 从/dev/video0 每隔1秒拍摄一张图片，存放在images/目录下
	> emeocv -c0 -o images/ -s1000
- 监督学习
	- ./Debug/emeocv -i learn/ -l
- 测试效果
	- ./Debug/emeocv -i test/ -t
- 使用
	- 从camera实时读取
		> ./Debug/emeocv -c0 -a -s10 -vINFO 
	- 从文件夹读取
		> ./Debug/emeocv -i images/ -a -s0 -vINFO


- 调试

``` patch

diff --git a/main.cpp b/main.cpp
index 64a797d..903c2f5 100644
--- a/main.cpp
+++ b/main.cpp
@@ -118,8 +118,8 @@ static void adjustCamera(ImageInput* pImageInput) {
     ImageProcessor proc(config);
     proc.debugWindow();
     proc.debugDigits();
-    //proc.debugEdges();
-    //proc.debugSkew();
+    proc.debugEdges();
+    proc.debugSkew();
```


