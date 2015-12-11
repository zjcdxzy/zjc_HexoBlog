title: build-ffmpeg.sh 脚本分析
date: 2015-12-01 18:53:50
categories: Shell
tags:
---

##build-ffmpeg.sh 脚本分析

### 脚本参数的判定

```bash
if [ "$*" ]
then
# 如果脚本的参数是lipo, 对于变量COMPILE 设置为空
	if [ "$*" = "lipo" ]
	then
		# skip compile
		COMPILE=
	else
# 将ARCHS 赋值为 脚本对应的参数
		ARCHS="$*"
# 如果是参数的数量是一个，将变量 LIPO 置为空
		if [ $# -eq 1 ]
		then
			# skip lipo
			LIPO=
		fi
	fi
fi
```

### 编译流程

> 待补充。。。


### 合并流程

#### lipo 流程拆出，单独放在一个独立的脚本中进行测试 


```bash
#!/bin/sh

FAT="FFmpeg"
THIN=`pwd`/"thin"
ARCHS="arm64 armv7 x86_64 i386"
LIPO="YES"

# LIPO 默认为yes， FAT 默认为 FFmpeg-iOS
if [ "$LIPO" ]
then
    echo "building fat binaries..."
# 生成FFMpeg lib最终的存放地址， 使用 mkdir -p 递归的创建
    mkdir -p $FAT/lib
# 重新指定下，初始化参数 ARCHS="arm64 armv7 x86_64 i386"
    set - $ARCHS
# 打印初始化参数 结果为 arm64 armv7 x86_64 i386
    echo $*
    CWD=`pwd`
    cd $THIN/$1/lib
	# 打印位置  /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib
    echo $THIN/$1/lib
    for LIB in *.a
        do
        cd $CWD
        echo lipo -create `find $THIN -name $LIB` -output $FAT/lib/$LIB 1>&2
        lipo -create `find $THIN -name $LIB` -output $FAT/lib/$LIB || exit 1
    done
# -rf 强制复制文件夹 将include文件 复制到 FFmpeg 的文件夹之下
    cd $CWD
    cp -rf $THIN/$1/include $FAT
fi
```

#### 输出的日志 
``` bash 
localhost:FFmpeg-iOS-build-script-master zjc$ sh lipo.sh 
building fat binaries...
arm64 armv7 x86_64 i386
/zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libavcodec.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libavcodec.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libavcodec.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libavcodec.a -output FFmpeg/lib/libavcodec.a
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libavformat.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libavformat.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libavformat.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libavformat.a -output FFmpeg/lib/libavformat.a
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libavutil.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libavutil.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libavutil.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libavutil.a -output FFmpeg/lib/libavutil.a
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libpostproc.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libpostproc.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libpostproc.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libpostproc.a -output FFmpeg/lib/libpostproc.a
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libswresample.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libswresample.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libswresample.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libswresample.a -output FFmpeg/lib/libswresample.a
lipo -create /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/arm64/lib/libswscale.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/armv7/lib/libswscale.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/i386/lib/libswscale.a /zjcletv/360云盘/FFmpeg-iOS-build-script-master/thin/x86_64/lib/libswscale.a -output FFmpeg/lib/libswscale.a
```

*对 `1>&2`重定向解析：
http://www.cnblogs.com/zhaobin/archive/2009/06/25/1511163.html *