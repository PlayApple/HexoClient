---
title: UIImageView异步加载网络图片
tags:
  - UIImageView
id: 261
categories:
  - IPhone
date: 2012-06-28 08:37:42
---

今天要在UIImageView中显示网络的图片，因为UIImageView自身就可以读取网络图片，所以，最简单的做法是：

[cpp]
- (void)viewDidLoad
{
 [super viewDidLoad];

 self.tv_headPic = [[[UIImageView alloc] initWithFrame:CGRectMake(0, 0, 80, 80)] autorelease];
self.tv_headPic.layer.masksToBounds = YES;
self.tv_headPic.layer.cornerRadius = 5.0f;
 [self.tv_headPic setBackgroundColor:[UIColor grayColor]];
 [self.view addSubview:self.tv_headPic];

 NSURL *imageUrl = [NSURL URLWithString:HEADIMAGE_URL];
 UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:imageUrl]];
self.tv_headPic.image = image;
}
[/cpp]

这是最简单的，但是由于在主线程中加载，会阻塞UI主线程。所以可以试试NSOperationQueue，一个NSOperationQueue 操作队列，就相当于一个线程管理器，而非一个线程。因为你可以设置这个线程管理器内可以并行运行的的线程数量等等。

下面就是使用NSOperationQueue实现子线程加载图片：

[cpp]

- (void)viewDidLoad
{
 [super viewDidLoad];

 operationQueue = [[NSOperationQueue alloc] init];

 self.tv_headPic = [[[UIImageView alloc] initWithFrame:CGRectMake(110, 50, 100, 100)] autorelease];
 self.tv_headPic.layer.masksToBounds = YES;
 self.tv_headPic.layer.cornerRadius = 5.0f;
 [self.tv_headPic setBackgroundColor:[UIColor grayColor]];
 [self.view addSubview:self.tv_headPic];

 NSInvocationOperation *op = [[NSInvocationOperation alloc] initWithTarget:self selector:@selector(downloadImage) object:nil];
 [operationQueue addOperation:op];
}

- (void)downloadImage
{
 NSURL *imageUrl = [NSURL URLWithString:HEADIMAGE_URL];
 UIImage *image = [UIImage imageWithData:[NSData dataWithContentsOfURL:imageUrl]];
 self.tv_headPic.image = image;
}

[/cpp]

不过这也不是最好的设计，因为虽然是异步加载，但是没有缓存图片。重新加载时又要重新从网络读取图片，所以可以考虑保存图片。

1、建立本地缓存目录

[cpp]

NSArray *paths = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
 diskCachePath = [[[paths objectAtIndex:0] stringByAppendingPathComponent:@&quot;ImageCache&quot;] retain];

 if (![[NSFileManager defaultManager] fileExistsAtPath:diskCachePath]) {
 NSError *error = nil;
 [[NSFileManager defaultManager] createDirectoryAtPath:diskCachePath
 withIntermediateDirectories:YES
 attributes:nil
 error:&amp;error];
 }

[/cpp]

2、保存在本地，接着可以用图片名称或者URL或者hash过后的值作为key（本地文件名），写入到本地，我比较喜欢用MD5一下图片url作为文件名保存。

[cpp]

if (![[NSFileManager defaultManager] fileExistsAtPath:localPath]) {
 [[NSFileManager defaultManager] createFileAtPath:localPath contents:localData attributes:nil];
 }

[/cpp]

下载图片前先判断是否已经缓存了，已经缓存了就不要再去下载了。