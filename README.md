<img src="https://yitaoyitao.oss-cn-qingdao.aliyuncs.com/app/img/temp/test/cover.png"/>

### BigImage + ImageView + ViewPager = BigImageViewPager

BigImageViewPager是一个图片浏览器框架，支持超大图、超长图、动图，支持手势，支持查看原图、下载、加载百分比进度显示。采用区块复用加载，优化内存占用，有效避免OOM。
### 注意：本框架支持网络图片、本地图片、支持gif动图、支持Android 13。


# 推荐扫描二维码进行安装体验：
<p align="center">
  <img src="https://www.pgyer.com/app/qrcode/bigimageviewpager" width="200"/>
  <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bfd23ce0042e431bb200d7e5e2fca87d~tplv-k3u1fbpfcp-zoom-1.image" width="200"/>
</p>

<p align="center">
  <img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a045eaa577834b00a345b409b81826f8~tplv-k3u1fbpfcp-watermark.image" width="200"/>
  <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0a1125f397b46619c9beea59691eaf5~tplv-k3u1fbpfcp-watermark.image" width="200"/>
  <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/414c415380f742c4918c4b705ffc2f4f~tplv-k3u1fbpfcp-watermark.image" width="200"/>
  <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ffb4cfcbaecf43488d7ae671c7c02d3d~tplv-k3u1fbpfcp-watermark.image" width="200"/>
</p>


## ⭐️⭐️Star数量曲线⭐️⭐️

[![Star History Chart](https://api.star-history.com/svg?repos=SherlockGougou/BigImageViewPager&type=Date)](https://star-history.com/#SherlockGougou/BigImageViewPager&Date)


# 用法
### 一、添加依赖
#### Step 1. 在你project层级的build.gradle中，添加仓库地址:
```
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```
#### Step 2. 在你主module的build.gradle中添加依赖：

```
dependencies {
  // 添加本框架 BigImageViewPager https://github.com/SherlockGougou/BigImageViewPager
  // 此处展示的是最新版本
  implementation 'com.github.SherlockGougou:BigImageViewPager:androidx-7.3.0'
  
  // 另外还需要依赖 glide
  implementation 'com.github.bumptech.glide:glide:4.11.0'
  implementation 'com.github.bumptech.glide:okhttp3-integration:4.11.0'
  
  // 下面两个根据自己的语言二选一：
  // 纯java用户:
  annotationProcessor 'com.github.bumptech.glide:compiler:4.11.0'
  // 纯kotlin、或者kotlin、java混编用户:
  kapt 'com.github.bumptech.glide:compiler:4.11.0'
}
```

#### Step 3. 在你的主module里，添加自定义AppGlideModule。你需要继承AppGlideModule并添加以下代码到对应的重载方法中，例如：
```
@GlideModule
public class MyAppGlideModule extends AppGlideModule {
  @Override
  public void registerComponents(@NonNull Context context, @NonNull Glide glide, @NonNull Registry registry) {
    super.registerComponents(context, glide, registry);

    // 替换底层网络框架为okhttp3，这步很重要！如果不添加会无法正常显示原图的加载百分比，或者卡在1%
    // 如果你的app中已经存在了自定义的GlideModule，你只需要把这一行代码，添加到对应的重载方法中即可。
    registry.replace(GlideUrl.class, InputStream.class, new OkHttpUrlLoader.Factory(ProgressManager.getOkHttpClient()));
  }
}
```

#### Step 4. 以上操作完成后，请点击顶部按钮：Build->Rebuild Project，等待重建完成，至此，框架添加完成。如遇到任何问题，请附带截图提issues，我会及时回复，或添加底部QQ群，进行交流。

## 二、调用方式

#### 1：生成图片源：（如果你有缩略图和原图两种路径，请使用下面的方式，进行图片List的生成；如果你是本地图片或者没有原图缩略图之分，可以跳过这一步）
```
		ImageInfo imageInfo;
		final List<ImageInfo> imageInfoList = new ArrayList<>();
		for (String image : images) {
			imageInfo = new ImageInfo();
			imageInfo.setOriginUrl(url);// 原图url
			imageInfo.setThumbnailUrl(thumbUrl);// 缩略图url
			imageInfoList.add(imageInfo);
		}
```

#### 2：最简单的调用方式：
```
        // 最简单的调用，即可实现大部分需求，如需定制，可参考下一步的自定义代码：

        ImagePreview
            .getInstance()
            // 上下文，必须是activity，不需要担心内存泄漏，本框架已经处理好；
            .setContext(MainActivity.this)

            // 设置从第几张开始看（索引从0开始）
            .setIndex(0)

            //=================================================================================================
            // 有三种设置数据集合的方式，根据自己的需求进行三选一：
            // 1：第一步生成的imageInfo List
            .setImageInfoList(imageInfoList)

            // 2：直接传url List
            //.setImageList(List<String> imageList)

            // 3：只有一张图片的情况，可以直接传入这张图片的url
            //.setImage(String image)
            //=================================================================================================

            // 开启预览
            .start();

            // 默认配置为：
            //      显示顶部进度指示器、
            //      显示右侧下载按钮、
            //      隐藏关闭按钮、
            //      开启点击图片关闭、
            //      开启下拉图片关闭、
            //      加载策略为全自动
```

##### 接口说明：

方法名 | 功能 |  说明
-|-|-
|setBigImageClickListener|设置图片点击事件|默认null|
|setBigImageLongClickListener|设置图片长按事件|默认null|
|setBigImagePageChangeListener|设置页面切换监听|默认null|
|setDownloadClickListener|设置点击下载监听|默认null，可选是否拦截下载行为|
|setDownloadListener|设置下载过程toast|不设置时使用默认toast|
|setCloseIconResId|设置关闭按钮的Drawable资源id|默认内置R.drawable.ic_action_close|
|setContext|设置上下文|不允许为空|
|setDownIconResId|设置下载按钮的Drawable资源id|R.drawable.icon_download_new|
|setEnableClickClose|设置是否开启点击图片退出|默认true|
|setEnableDragClose|设置是否开启下拉图片退出|默认true|
|setEnableUpDragClose|设置是否开启上拉图片退出|默认false|
|setEnableDragCloseIgnoreScale|是否忽略缩放启用拉动关闭|默认true|
|setErrorPlaceHolder|设置加载失败的占位图资源id|默认内置R.drawable.load_failed|
|setFolderName|设置下载到的文件夹名称|默认保存Picture文件夹中|
|setImage|设置单张图片地址|三选一|
|setImageInfoList|设置图片Bean集合|三选一|
|setImageList|设置图片地址集合|三选一|
|setIndex|设置开始的索引|从0开始|
|setLoadStrategy|设置加载策略|详见加载策略说明|
|setLongPicDisplayMode|设置长图的展示模式|默认缩小展示，可选宽度撑满|
|setOnOriginProgressListener|设置原图加载进度回调|加载原图的百分比进度|
|setProgressLayoutId|自定义百分比布局|详细见demo|
|setShowCloseButton|设置是否显示关闭按钮|默认false，不显示|
|setShowDownButton|设置是否显示下载按钮|默认true，显示|
|setShowIndicator|设置是否显示顶部的进度指示器|默认true，显示|
|setIndicatorShapeResId|设置顶部指示器背景shape|默认自带灰色圆角shape，设置为0时不显示背景|
|setShowErrorToast|设置是否显示加载失败的Toast|默认false，不显示|
|setZoomTransitionDuration|设置图片缩放动画时长|默认200ms|
|setPreviewLayoutResId|设置自定义的预览页面布局id|默认R.layout.sh_layout_preview|
|setOnPageFinishListener|页面关闭回调|默认null|
|finish|关闭页面|自主控制关闭页面|
|start|开启看图|最后调用|

##### 3：自定义多种配置：
请参考Demo：https://github.com/SherlockGougou/BigImageViewPager/blob/master/sample/src/main/java/cc/shinichi/bigimageviewpager/MainActivity.java#L299

##### 4：加载策略介绍
```
  public enum LoadStrategy {
    /**
     * 仅加载原图；会强制隐藏查看原图按钮
     */
    AlwaysOrigin,

    /**
     * 仅加载普清；会强制隐藏查看原图按钮
     */
    AlwaysThumb,

    /**
     * 根据网络自适应加载，WiFi原图，流量普清；会强制隐藏查看原图按钮
     */
    NetworkAuto,

    /**
     * 手动模式：默认普清，点击按钮再加载原图；会根据原图、缩略图url是否一样来判断是否显示查看原图按钮
     */
    Default

    /**
     * 全自动模式：WiFi原图，流量下默认普清，可点击按钮查看原图
     */
    Auto
  }

  注：以上所有方式，如果原图缓存存在的情况，会默认加载原图缓存保证清晰度；且原图缓存只要存在，就不会显示查看原图按钮。
```

##### 5：完全自定义预览界面布局：
详细操作请参考Demo：https://github.com/SherlockGougou/BigImageViewPager/blob/master/sample/src/main/java/cc/shinichi/bigimageviewpager/MainActivity.java#L470

##### 6：Q&A
1.查看原图卡在1%？
答：请仔细查看以上第三步的操作。

# GitHub源码
https://github.com/SherlockGougou/BigImageViewPager

# 致谢
- 本框架核心是开源作者 [davemorrissey](https://github.com/davemorrissey) 的 [subsampling-scale-image-view](https://github.com/davemorrissey/subsampling-scale-image-view)，在此感谢他的付出！
对原作感兴趣的，可以去研究学习 ---> [传送门点我](https://github.com/davemorrissey/subsampling-scale-image-view)
- okhttp 进度监听部分代码，借鉴使用了[GlideImageView](https://github.com/sunfusheng/GlideImageView)，在此对其表示感谢，喜欢其作品的可以移步去查看学习

# Bug反馈、增加需求，加 QQ 交流群
<p align="center">
  <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/17de72a7d15445f3b9a9591647af3c9b~tplv-k3u1fbpfcp-watermark.image" width="200"/>
</p>

# 如果有帮助到你，欢迎请我喝杯☕️：
<p align="center">
  <img src="https://github.com/SherlockGougou/BigImageViewPager/assets/17920617/ad358723-6c5c-4878-81b9-ec77b17cda09" width="400"/>
  <img src="https://github.com/SherlockGougou/BigImageViewPager/assets/17920617/507f28d2-c5d0-449c-bbe6-a5e022484130" width="400"/>
</p>

# LICENSE
```
Copyright (C) 2018 SherlockGougou qinglingou@gmail.com

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
