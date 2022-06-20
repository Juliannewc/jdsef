[![](https://jitpack.io/v/wildma/PictureSelector.svg)](https://jitpack.io/#wildma/PictureSelector)

# PictureSelector
Android 图片选择器（仿 IOS 图片选择控件）

## 效果图

![效果图.jpg](https://upload-images.jianshu.io/upload_images/5382223-9d82fb9c0f22bfb2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 功能特点
- 支持通过拍照获取图片
- 支持通过相册获取图片
- 支持图片是否裁剪两种场景
- 支持仿 IOS 底部弹出选择菜单 ActionSheet 效果
- 适配 Android Q 版本

## 使用
### Step 1. 添加 JitPack 仓库
在项目的 build.gradle 添加 JitPack 仓库
```java
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
    }
}
```
### Step 2. 添加依赖
在需要使用的 module 中添加依赖  
注意：从 2.0.0 版本开始，项目迁移到 AndroidX。如果你的项目还未迁移到 AndroidX，可以使用 1.2.0 版本。
```java
dependencies {
	implementation 'com.github.wildma:PictureSelector:2.1.0'
}
```
### Step 3. 拍照或者从相册选择图片
**使用场景：**
- 不裁剪
```java
PictureSelector
        .create(MainActivity.this, PictureSelector.SELECT_REQUEST_CODE)
        .selectPicture(false);
```

- 自由裁剪
```java
PictureSelector
        .create(MainActivity.this, PictureSelector.SELECT_REQUEST_CODE)
        .selectPicture(true);
```

- 指定宽高及宽高比例裁剪
```java
PictureSelector
        .create(MainActivity.this, PictureSelector.SELECT_REQUEST_CODE)
        .selectPicture(true, 200, 200, 1, 1);
```

**参数解释：**
- create()：参数一是上下文，在 activity 中传 activity.this，在 fragment 中传 fragment.this。参数二是请求码，用于结果回调 onActivityResult() 中判断。
- selectPicture()：参数分别为是否裁剪、裁剪后图片的宽(单位 px)、裁剪后图片的高、宽比例、高比例。

### Step 4. 获取图片地址进行显示
```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        /*结果回调*/
        if (requestCode == PictureSelector.SELECT_REQUEST_CODE) {
            if (data != null) {
                PictureBean pictureBean = data.getParcelableExtra(PictureSelector.PICTURE_RESULT);
                if (pictureBean.isCut()) {
                    mIvImage.setImageBitmap(BitmapFactory.decodeFile(pictureBean.getPath()));
                } else {
                    mIvImage.setImageURI(pictureBean.getUri());
                }

                //使用 Glide 加载图片
                /*Glide.with(this)
                        .load(pictureBean.isCut() ? pictureBean.getPath() : pictureBean.getUri())
                        .apply(RequestOptions.centerCropTransform()).into(mIvImage);*/
            }
        }
    }
```

### 清理缓存
实际开发中将图片上传到服务器成功后需要删除全部缓存图片（即裁剪后的无用图片），调用如下方法即可：
```java
    FileUtils.deleteAllCacheImage(this);
```

## 注意
如果你没有使用依赖的方式，而是直接拷贝源码到你的项目中使用。那么需要自己适配 Android 7.0 导致的 FileUriExposedException 异常，具体方式如下：

将 PictureSelectUtils 中的 authority 与你项目中 AndroidManifest.xml 下的 authority 保持一致。
例如 AndroidManifest.xml 下的 authority 为：
```java
android:authorities="myAuthority"
```
则需要修改 PictureSelectUtils 中的 authority（ [这一行](https://github.com/wildma/PictureSelector/blob/master/pictureselector/src/main/java/com/wildma/pictureselector/PictureSelectUtils.java#L85)） 为：
```java
String authority = "myAuthority";
```


详细介绍请看文章：[一个非常好用的 Android 图片选择框架](https://www.jianshu.com/p/6ac6b681c413)
