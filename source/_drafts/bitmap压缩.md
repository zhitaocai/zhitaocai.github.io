title: bitmap压缩
date: 2015-07-04 21:04:12
tags: hexo
categories: 搭建个人博客
---
关于搭建个人博客，网上已经有很多教程了，重复别人的概述显得有点浪费时间。而我在自己在搭建这个博客的时候，了解到目前大概有[3种比较常用个人博客搭建方法](http://www.jintongyao.com/2014/build-a-blog-by-hexo/)的方法，而我所用的是**GitHub&&Hexo**，因此，下面我主要总结概述一下在我搭建这个博客的时候，所用到的关于**GitHub&&Hexo**方面参考内容，大家可以参考参考，省去从海量资料中寻找有用内容的过程。

<!--more-->


# 压缩Bitmap

## 质量压缩
针对jpg图片下面代码不会真的进行压缩

```java

	public static void test() {
		Bitmap bm = xxx;
		Log.i("test", getBitmapSizeWithspecifiedImageFormat(bm, 50, Bitmap.CompressFormat.PNG, false));
		Log.i("test", getBitmapSizeWithspecifiedImageFormat(bm, 30, Bitmap.CompressFormat.PNG, false));
	}

	/**
	 * 将图片压缩一定比率和指定转换格式之后获取其二进制数据
	 *
	 * @param bm          原始图片
	 * @param comRadio    压缩比 100为无压缩 0为压缩到极致
	 * @param imageFormat 转换后图片格式
	 *                    <ul>
	 *                    <li> {@link Bitmap.CompressFormat#JPEG}</li>
	 *                    <li> {@link Bitmap.CompressFormat#PNG}</li>
	 *                    <li> {@link Bitmap.CompressFormat#WEBP}</li>
	 *                    </ul>
	 * @param flag        处理完毕之后是否回收图片
	 *
	 * @return
	 */
	public static byte[] getBitmapSizeWithspecifiedImageFormat(Bitmap bm, int comRadio, Bitmap.CompressFormat imageFormat, boolean flag) {
		ByteArrayOutputStream baos = null;
		try {
			baos = new ByteArrayOutputStream();
			bm.compress(imageFormat, comRadio, baos);
			return baos.toByteArray();
		} catch (Exception e) {
			if (Debug_SDK.isWxLog) {
				Debug_SDK.te(Debug_SDK.mWxTag, WxUtil.class, e);
			}
		} finally {
			try {
				if (flag) {
					if (bm != null && !bm.isRecycled()) {
						bm.recycle();
					}
				}
				if (baos != null) {
					baos.close();
				}
			} catch (Exception e2) {
			}
		}
		return null;
	}
```

``test()``方法中无论输入什么压缩比，结果都是一样的，因为如果压缩的时候，选择png格式的是无法压缩到的，要实现可以根据不同的压缩比来进行真正的质量压缩，那么需要选择 ``Bitmap.CompressFormat.JPEG``
