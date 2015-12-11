title: iOS软解和硬解播放器截屏
date: 2015-12-03 17:20:21
categories: Player
tags: ffmpeg
---


## 对于播放器的截屏功能实现的整理

### 硬解播放器

#### MPMoviePlayerController 中直接提供关于截屏的接口

```bash
// Returns a thumbnail at the given time.
// Deprecated.  Use -requestThumbnailImagesAtTimes:timeOption: / MPMoviePlayerThumbnailImageRequestDidFinishNotification instead.
- (UIImage *)thumbnailImageAtTime:(NSTimeInterval)playbackTime timeOption:(MPMovieTimeOption)option NS_DEPRECATED_IOS(3_2, 7_0);
```

接受两个参数：

playbackTime：当前的播放时间
MPMovieTimeOption：增加一个时间的选项 

```bash 
// Thumbnails

typedef NS_ENUM(NSInteger, MPMovieTimeOption) {
    MPMovieTimeOptionNearestKeyFrame,       // 按照视频时间附近的关键帧截取
    MPMovieTimeOptionExact					// 按照传入的时间直接截取
} NS_DEPRECATED_IOS(3_2, 9_0) __TVOS_PROHIBITED;
```

MPMoviePlayerController截屏函数调用代码：

``` bash
- (UIImage *)thumbnailImageAtCurrentTime
{
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wdeprecated-declarations"
    return [super thumbnailImageAtTime:self.currentPlaybackTime timeOption:MPMovieTimeOptionExact];
#pragma clang diagnostic pop
}
```

#### AVPlayer 截屏 
```bash 
- (UIImage *)thumbnailImageAtCurrentTime
{
    AVAssetImageGenerator *imageGenerator = [AVAssetImageGenerator assetImageGeneratorWithAsset:_playAsset]; // 初始化AVAssetImageGenerator
    NSError *error = nil;
    CMTime time = CMTimeMakeWithSeconds(self.currentPlaybackTime, 1); // 获取当前的时间戳
    CMTime actualTime;
    CGImageRef cgImage = [imageGenerator copyCGImageAtTime:time actualTime:&actualTime error:&error]; // 从imageGenerator 取出时间戳 
    UIImage *image = [UIImage imageWithCGImage:cgImage];
    return image;
}
```
### 软解播放器的截屏

实质是对于OpenGL ES 渲染层的截取 

- 对于iOS 7 以及以上的版本

```bash
- (UIImage*)snapshotInternalOnIOS7AndLater
{
    UIGraphicsBeginImageContextWithOptions(self.bounds.size, NO, 0.0);
    // Render our snapshot into the image context
    [self drawViewHierarchyInRect:self.bounds afterScreenUpdates:NO]; // 该方法只有在iOS 7之后才可用

    // Grab the image from the context
    UIImage *complexViewImage = UIGraphicsGetImageFromCurrentImageContext();
    // Finish using the context
    UIGraphicsEndImageContext();

    return complexViewImage;
}
```
- 对于低于iOS 7 的版本

```bash

- (UIImage*)snapshotInternalOnIOS6AndBefore
{
    EAGLContext *prevContext = [EAGLContext currentContext];
    [EAGLContext setCurrentContext:_context];

    GLint backingWidth, backingHeight;

    // Bind the color renderbuffer used to render the OpenGL ES view
    // If your application only creates a single color renderbuffer which is already bound at this point,
    // this call is redundant, but it is needed if you're dealing with multiple renderbuffers.
    // Note, replace "viewRenderbuffer" with the actual name of the renderbuffer object defined in your class.
    glBindRenderbuffer(GL_RENDERBUFFER, _renderbuffer);

    // Get the size of the backing CAEAGLLayer
    glGetRenderbufferParameteriv(GL_RENDERBUFFER, GL_RENDERBUFFER_WIDTH, &backingWidth);
    glGetRenderbufferParameteriv(GL_RENDERBUFFER, GL_RENDERBUFFER_HEIGHT, &backingHeight);

    NSInteger x = 0, y = 0, width = backingWidth, height = backingHeight;
    NSInteger dataLength = width * height * 4;
    GLubyte *data = (GLubyte*)malloc(dataLength * sizeof(GLubyte));

    // Read pixel data from the framebuffer
    glPixelStorei(GL_PACK_ALIGNMENT, 4);
    glReadPixels((int)x, (int)y, (int)width, (int)height, GL_RGBA, GL_UNSIGNED_BYTE, data);

    // Create a CGImage with the pixel data
    // If your OpenGL ES content is opaque, use kCGImageAlphaNoneSkipLast to ignore the alpha channel
    // otherwise, use kCGImageAlphaPremultipliedLast
    CGDataProviderRef ref = CGDataProviderCreateWithData(NULL, data, dataLength, NULL);
    CGColorSpaceRef colorspace = CGColorSpaceCreateDeviceRGB();
    CGImageRef iref = CGImageCreate(width, height, 8, 32, width * 4, colorspace, kCGBitmapByteOrder32Big | kCGImageAlphaPremultipliedLast,
                                    ref, NULL, true, kCGRenderingIntentDefault);

    [EAGLContext setCurrentContext:prevContext];

    // OpenGL ES measures data in PIXELS
    // Create a graphics context with the target size measured in POINTS
    UIGraphicsBeginImageContext(CGSizeMake(width, height));

    CGContextRef cgcontext = UIGraphicsGetCurrentContext();
    // UIKit coordinate system is upside down to GL/Quartz coordinate system
    // Flip the CGImage by rendering it to the flipped bitmap context
    // The size of the destination area is measured in POINTS
    CGContextSetBlendMode(cgcontext, kCGBlendModeCopy);
    CGContextDrawImage(cgcontext, CGRectMake(0.0, 0.0, width, height), iref);

    // Retrieve the UIImage from the current context
    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
    UIGraphicsEndImageContext();

    // Clean up
    free(data);
    CFRelease(ref);
    CFRelease(colorspace);
    CGImageRelease(iref);

    return image;
}
```