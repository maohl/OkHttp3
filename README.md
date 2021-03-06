## OkHttp3
基于OkHttp3封装的网络请求工具类
## 功能点
* 支持Http/Https等协议
* 支持Cookie持久化
* 支持协议头参数Head设置、二进制参数请求
* 支持Unicode自动转码、服务器响应编码设置
* 支持同步/异步请求、断网请求、缓存响应、缓存等级
* 当Activity/Fragment销毁时自动取消相应的所有网络请求，支持取消指定请求
* 异步请求响应自动切换到UI线程，摒弃runOnUiThread
* Application中自定义全局配置/增加系统默认配置
* 支持文件和图片上传/批量上传，支持同步/异步上传，支持进度提示
* 支持文件下载/批量下载，支持同步/异步下载，支持进度提示
* 支持文件断点下载，独立下载的模块摒弃了数据库记录断点的过时方法
* 完整的日志跟踪与异常处理
* 支持请求结果拦截以及异常处理拦截
* 支持单例客户端，提高网络请求速率
* 后续优化中...
## 引用方式
### Maven
```
<dependency>
  <groupId>com.zhousf.lib</groupId>
  <artifactId>okhttp3</artifactId>
  <version>2.6.9.5</version>
  <type>pom</type>
</dependency>
```
### Gradle
```
compile 'com.zhousf.lib:okhttp3:2.6.9.5'
```
若出现V7版本冲突请采用下面方式进行依赖：
```
compile ('com.zhousf.lib:okhttp3:2.6.9.5'){
    exclude(module: 'appcompat-v7')
}
```
### ProGuard
如果你使用了ProGuard混淆，请添加如下配置:
```
-dontwarn okio.**
```

## 提交记录
* 2016-6-29 项目提交
* 2016-7-4 
    *  项目框架调整
    *  增加Application中全局配置
    *  增加系统默认配置
    *  修复内存释放bug
* 2016-7-19
    *  代码优化、降低耦合
    *  修复已知bug
* 2016-8-9
    *  增加文件上传功能，支持批量上传
* 2016-8-10
    *  增加文件下载功能，支持批量下载
* 2016-8-17
    *  增加文件断点下载功能
* 2016-10-10
    *  增加请求结果拦截以及异常处理拦截
* 2016-10-12
    *  增加Cookie持久化
* 2016-10-25
    *  支持协议头参数Head设置
* 2016-12-7
    *  增加取消指定请求功能
* 2016-12-12
    *  增加单例客户端，提高网络请求速率
* 2017-3-3
    *  修复上传文件入参bug（感谢*Sanqi5401*指正）
* 2017-3-6
    *  在集成过程中出现了okio丢失的情况请添加 compile 'com.android.support:multidex:1.0.1'
（感谢*kevin*提供相关解决方案）
* 2017-3-31
    *  增加单次批量上传文件功能：一次请求上传多个文件
* 2017-4-21
    *  增加二进制流请求功能，DEMO中已添加动态权限申请功能

## 权限
```
    <!-- 添加读写权限 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <!-- 访问互联网权限 -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
```

## 项目演示DEMO
项目中已包含所有支持业务的demo，详情请下载项目参考源码。
## 自定义全局配置
在Application中配置如下：
```
OkHttpUtil.init(this)
                .setConnectTimeout(30)//连接超时时间
                .setWriteTimeout(30)//写超时时间
                .setReadTimeout(30)//读超时时间
                .setMaxCacheSize(10 * 1024 * 1024)//缓存空间大小
                .setCacheLevel(CacheLevel.FIRST_LEVEL)//缓存等级
                .setCacheType(CacheType.FORCE_NETWORK)//缓存类型
                .setShowHttpLog(true)//显示请求日志
                .setHttpLogTAG("HttpLog")//设置请求日志标识
                .setShowLifecycleLog(false)//显示Activity销毁日志
                .setRetryOnConnectionFailure(false)//失败后不自动重连
                .setDownloadFileDir(downloadFileDir)//文件下载保存目录
                .setResponseEncoding(Encoding.UTF_8)//设置全局的服务器响应编码
                .addResultInterceptor(HttpInterceptor.ResultInterceptor)//请求结果拦截器
                .addExceptionInterceptor(HttpInterceptor.ExceptionInterceptor)//请求链路异常拦截器
                .setCookieJar(new PersistentCookieJar(new SetCookieCache(), 
                new SharedPrefsCookiePersistor(this)))//持久化cookie
                .build();
            
```

## 获取网络请求客户端单例示例
```
//获取单例客户端（默认）
 方法一、OkHttpUtil.getDefault(this)//绑定生命周期
            .doGetSync(HttpInfo.Builder().setUrl(url).build());
 方法二、OkHttpUtil.getDefault()//不绑定生命周期
            .doGetSync(HttpInfo.Builder().setUrl(url).build());
            
```

## 取消指定请求
建议在视图中采用OkHttpUtil.getDefault(this)的方式进行请求绑定，该方式会在Activity/Fragment销毁时自动取消当前视图下的所有请求；
请求标识类型支持Object、String、Integer、Float、Double；
**<font color=red>请求标识务必保证唯一</font>**。
```
//*******请求时先绑定请求标识，根据该标识进行取消*******/
//方法一：
OkHttpUtil.Builder()
                .setReadTimeout(120)
                .build("请求标识")//绑定请求标识
                .doDownloadFileAsync(info);
//方法二：
OkHttpUtil.getDefault("请求标识")//绑定请求标识
            .doGetSync(info);
            
//*******取消指定请求*******/ 
OkHttpUtil.getDefault().cancelRequest("请求标识");
 
```

## 在Activity中同步调用示例
```
    /**
     * 同步请求：由于不能在UI线程中进行网络请求操作，所以采用子线程方式
     */
    private void doHttpSync() {
        new Thread(()-> {
                HttpInfo info = HttpInfo.Builder()
                .setUrl(url)
                .addHead("head","test")//协议头参数设置
                .setResponseEncoding(Encoding.UTF_8)//设置服务器响应编码
                .build();
                OkHttpUtil.getDefault(MainActivity.this).doGetSync(info);
                if (info.isSuccessful()) {
                    final String result = info.getRetDetail();
                    runOnUiThread(() -> {
                            resultTV.setText("同步请求：" + result);
                        }
                    );
                }
            }
        ).start();
    }
```

## 在Activity中异步调用示例
```
  /**
     * 异步请求：回调方法可以直接操作UI
     */
    private void doHttpAsync() {
                //回调方式一
                OkHttpUtil.getDefault(this).doGetAsync(
                        HttpInfo.Builder()
                                .setUrl(url)
                                .build(),
                        new CallbackOk() {
                            @Override
                            public void onResponse(HttpInfo info) throws IOException {
                                if (info.isSuccessful()) {
                                    String result = info.getRetDetail();
                                    resultTV.setText("异步请求："+result);
                                }
                            }
                        });
                //回调方式二
                OkHttpUtil.getDefault(this).doGetAsync(
                        HttpInfo.Builder()
                                .setUrl(url)
                                .build(),
                        new Callback() {
                            @Override
                            public void onFailure(HttpInfo info) throws IOException {
                                String result = info.getRetDetail();
                                resultTV.setText("异步请求失败："+result);
                            }

                            @Override
                            public void onSuccess(HttpInfo info) throws IOException {
                                String result = info.getRetDetail();
                                resultTV.setText("异步请求成功："+result);
                                //GSon解析
                                TimeAndDate time = new Gson().fromJson(result, TimeAndDate.class);
                                LogUtil.d("MainActivity",time.getResult().toString());
                            }
                        });
    }



```

## 在Activity中上传图片示例
```
 /**
     * 异步上传图片：显示上传进度
     */
    private void doUploadImg() {
        HttpInfo info = HttpInfo.Builder()
                        .setUrl(url)
                        .addUploadFile("file", filePathOne, new ProgressCallback() {
                            //onProgressMain为UI线程回调，可以直接操作UI
                            @Override
                            public void onProgressMain(int percent, long bytesWritten, long contentLength, boolean done) {
                                uploadProgressOne.setProgress(percent);
                                LogUtil.d(TAG, "上传进度：" + percent);
                            }
                        })
                        .build();
        OkHttpUtil.getDefault(this).doUploadFileAsync(info);
    }
```

## 在Activity中单次批量上传文件示例
* 若服务器为php，接口文件参数名称后面追加"[]"表示数组，
示例：builder.addUploadFile("<font color=red>uploadFile[]</font>",path);
```
/**
     * 单次批量上传：一次请求上传多个文件
     */
     private void doUploadBatch(){
        imgList.clear();
        imgList.add("/storage/emulated/0/okHttp_download/test.apk");
        imgList.add("/storage/emulated/0/okHttp_download/test.rar");
        HttpInfo.Builder builder = HttpInfo.Builder()
                .setUrl(url);
        //循环添加上传文件
        for (String path: imgList  ) {
            //若服务器为php，接口文件参数名称后面追加"[]"表示数组，示例：builder.addUploadFile("uploadFile[]",path);
            builder.addUploadFile("uploadFile",path);
        }
        HttpInfo info = builder.build();
        OkHttpUtil.getDefault(UploadFileActivity.this).doUploadFileAsync(info,new ProgressCallback(){
            @Override
            public void onProgressMain(int percent, long bytesWritten, long contentLength, boolean done) {
                uploadProgress.setProgress(percent);
            }

            @Override
            public void onResponseMain(String filePath, HttpInfo info) {
                LogUtil.d(TAG, "上传结果：" + info.getRetDetail());
                tvResult.setText(info.getRetDetail());
            }
        });
    }
```

## 在Activity中断点下载文件示例
```
 @OnClick({R.id.downloadBtn, R.id.pauseBtn, R.id.continueBtn})
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.downloadBtn://下载
                download();
                break;
            case R.id.pauseBtn://暂停下载
                if(null != fileInfo)
                    fileInfo.setDownloadStatus(DownloadStatus.PAUSE);
                break;
            case R.id.continueBtn://继续下载
                download();
                break;
        }
    }

    private void download(){
        if(null == fileInfo)
            fileInfo = new DownloadFileInfo(url,"fileName",new ProgressCallback(){
                @Override
                public void onProgressMain(int percent, long bytesWritten, long contentLength, boolean done) {
                    downloadProgress.setProgress(percent);
                    tvResult.setText(percent+"%");
                    LogUtil.d(TAG, "下载进度：" + percent);
                }
                @Override
                public void onResponseMain(String filePath, HttpInfo info) {
                    if(info.isSuccessful()){
                        tvResult.setText(info.getRetDetail()+"\n下载状态："+fileInfo.getDownloadStatus());
                    }else{
                        Toast.makeText(DownloadBreakpointsActivity.this,info.getRetDetail(),Toast.LENGTH_SHORT).show();
                    }
                }
            });
        HttpInfo info = HttpInfo.Builder().addDownloadFile(fileInfo).build();
        OkHttpUtil.Builder().setReadTimeout(120).build(this).doDownloadFileAsync(info);
    }
```

## 二进制流方式请求
```
HttpInfo info = new HttpInfo.Builder()
        .setUrl("http://192.168.120.154:8082/StanClaimProd-app/surveySubmit/getFileLen")
        .addParamBytes(byte)//添加二进制流
        .build();
OkHttpUtil.getDefault().doPostAsync(info, new Callback() {
    @Override
    public void onSuccess(HttpInfo info) throws IOException {
        String result = info.getRetDetail();
        resultTV.setText("请求失败："+result);
    }

    @Override
    public void onFailure(HttpInfo info) throws IOException {
        resultTV.setText("请求成功："+info.getRetDetail());
    }
});
```

## 请求结果统一预处理拦截器/请求链路异常信息拦截器示例
请求结果拦截器与链路异常拦截器方便项目进行网络请求业务时对信息返回的统一管理与设置
```
/**
 * Http拦截器
 * 1、请求结果统一预处理拦截器
 * 2、请求链路异常信息拦截器
 * @author zhousf
 */
public class HttpInterceptor {

    /**
     * 请求结果统一预处理拦截器
     * 该拦截器会对所有网络请求返回结果进行预处理并修改
     */
    public static ResultInterceptor ResultInterceptor = new ResultInterceptor() {
        @Override
        public HttpInfo intercept(HttpInfo info) throws Exception {
            //请求结果预处理：可以进行GSon过滤与解析
            return info;
        }
    };

    /**
     * 请求链路异常信息拦截器
     * 该拦截器会发送网络请求时链路异常信息进行拦截处理
     */
    public static ExceptionInterceptor ExceptionInterceptor = new ExceptionInterceptor() {
        @Override
        public HttpInfo intercept(HttpInfo info) throws Exception {
            switch (info.getRetCode()){
                case HttpInfo.NonNetwork:
                    info.setRetDetail("网络中断");
                    break;
                case HttpInfo.CheckURL:
                    info.setRetDetail("网络地址错误["+info.getNetCode()+"]");
                    break;
                case HttpInfo.ProtocolException:
                    info.setRetDetail("协议类型错误["+info.getNetCode()+"]");
                    break;
                case HttpInfo.CheckNet:
                    info.setRetDetail("请检查网络连接是否正常["+info.getNetCode()+"]");
                    break;
                case HttpInfo.ConnectionTimeOut:
                    info.setRetDetail("连接超时");
                    break;
                case HttpInfo.WriteAndReadTimeOut:
                    info.setRetDetail("读写超时");
                    break;
                case HttpInfo.ConnectionInterruption:
                    info.setRetDetail("连接中断");
                    break;
            }
            return info;
        }
    };
}

```

## Cookie持久化示例
没有在Application中进行全局Cookie持久化配置时可以采用以下方式：
```
OkHttpUtilInterface okHttpUtil = OkHttpUtil.Builder()
            .setCacheLevel(FIRST_LEVEL)
            .setConnectTimeout(25).build(this);
//一个okHttpUtil即为一个网络连接
okHttpUtil.doGetAsync(
                HttpInfo.Builder().setUrl(url).build(),
                new CallbackOk() {
                    @Override
                    public void onResponse(HttpInfo info) throws IOException {
                        if (info.isSuccessful()) {
                            String result = info.getRetDetail();
                            resultTV.setText("异步请求："+result);
                        }
                    }
                });
```


## 相关截图
### 网络请求界面
![](https://github.com/MrZhousf/OkHttp3/blob/master/pic/1.jpg?raw=true)
### 上传图片界面
![](https://github.com/MrZhousf/OkHttp3/blob/master/pic/3.jpg?raw=true)
### 断点下载文件界面
![](https://github.com/MrZhousf/OkHttp3/blob/master/pic/4.jpg?raw=true)
### 日志
![](https://github.com/MrZhousf/OkHttp3/blob/master/pic/2.jpg?raw=true)
* GET-URL/POST-URL：请求地址
* CostTime：请求耗时（单位：秒）
* Response：响应串


## 有问题反馈
在使用中有任何问题，欢迎反馈给我，可以用以下联系方式跟我交流

* QQ: 424427633


## 感激
感谢以下的项目,排名不分先后

* [OkHttp](https://github.com/square/okhttp/) 
* [PersistentCookieJar](https://github.com/franmontiel/PersistentCookieJar)


## 相关示例

### OkHttpUtil接口
```java
/**
 * 网络请求工具接口
 * @author zhousf
 */
public interface OkHttpUtilInterface {

    /**
     * 同步Post请求
     * @param info 请求信息体
     * @return HttpInfo
     */
    HttpInfo doPostSync(HttpInfo info);

    /**
     * 异步Post请求
     * @param info 请求信息体
     * @param callback 回调接口
     */
    void doPostAsync(HttpInfo info, CallbackOk callback);

    /**
     * 同步Get请求
     * @param info 请求信息体
     * @return HttpInfo
     */
    HttpInfo doGetSync(HttpInfo info);

    /**
     * 异步Get请求
     * @param info 请求信息体
     * @param callback 回调接口
     */
    void doGetAsync(HttpInfo info, CallbackOk callback);

    /**
     * 异步上传文件
     * @param info 请求信息体
     */
    void doUploadFileAsync(final HttpInfo info);

    /**
     * 同步上传文件
     * @param info 请求信息体
     */
    void doUploadFileSync(final HttpInfo info);

    /**
     * 异步下载文件
     * @param info 请求信息体
     */
    void doDownloadFileAsync(final HttpInfo info);

    /**
     * 同步下载文件
     * @param info 请求信息体
     */
    void doDownloadFileSync(final HttpInfo info);
    
    /**
     * 取消请求
     * @param requestTag 请求标识
     */
    void cancelRequest(Object requestTag);
    
}
```

### MainActivity
```java
   package com.okhttp3;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

import com.okhttplib.annotation.CacheLevel;
import com.okhttplib.annotation.CacheType;
import com.okhttplib.HttpInfo;
import com.okhttplib.OkHttpUtil;

import butterknife.Bind;
import butterknife.ButterKnife;
import butterknife.OnClick;
import util.NetWorkUtil;

public class MainActivity extends AppCompatActivity {

    @Bind(R.id.syncBtn)
    Button syncBtn;
    @Bind(R.id.asyncBtn)
    Button asyncBtn;
    @Bind(R.id.cacheBtn)
    Button cacheBtn;
    @Bind(R.id.resultTV)
    TextView resultTV;
    @Bind(R.id.offlineBtn)
    Button offlineBtn;

    /**
     * 注意：测试时请更换该地址
     */
    private String url = "http://api.k780.com:88/?app=life.time&appkey=10003&sign=b59bc3ef6191eb9f747dd4e83c99f2a4&format=json";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
    }

    @OnClick({R.id.syncBtn, R.id.asyncBtn, R.id.cacheBtn, R.id.offlineBtn})
    public void onClick(View view) {
        switch (view.getId()) {
            case R.id.syncBtn:
                doHttpSync();
                break;
            case R.id.asyncBtn:
                doHttpAsync();
                break;
            case R.id.cacheBtn:
                doHttpCache();
                break;
            case R.id.offlineBtn:
                doHttpOffline();
                break;
        }
    }

    /**
     * 同步请求：由于不能在UI线程中进行网络请求操作，所以采用子线程方式
     */
    private void doHttpSync() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                HttpInfo info = HttpInfo.Builder().setUrl(url).build(MainActivity.this);
                OkHttpUtil.Builder().build().doGetSync(info);
                if (info.isSuccessful()) {
                    String result = info.getRetDetail();
                    runOnUiThread(() -> {
                        resultTV.setText("同步请求："+result);
                    });
                }
            }
        }).start();
    }

    /**
     * 异步请求：回调方法可以直接操作UI
     */
    private void doHttpAsync() {
        OkHttpUtil.Builder().setCacheLevel(CacheLevel.FIRST_LEVEL).setConnectTimeout(25).build().doGetAsync(
                HttpInfo.Builder().setUrl(url).build(this),
                info -> {
                    if (info.isSuccessful()) {
                        String result = info.getRetDetail();
                        resultTV.setText("异步请求："+result);
                    }
                });

    }

    /**
     * 缓存请求：请连续点击缓存请求，会发现在缓存有效期内，从第一次请求后的每一次请求花费为0秒，说明该次请求为缓存响应
     */
    private void doHttpCache() {
        OkHttpUtil.Builder()
                .setCacheLevel(CacheLevel.SECOND_LEVEL)
                .build()
                .doGetAsync(
                        HttpInfo.Builder().setUrl(url).build(this),
                        info -> {
                            if (info.isSuccessful()) {
                                String result = info.getRetDetail();
                                resultTV.setText("缓存请求："+result);
                            }
                        });
    }

    /**
     * 断网请求：请先点击其他请求再测试断网请求
     */
    private void doHttpOffline(){
        if(!NetWorkUtil.isNetworkAvailable(this)){
            OkHttpUtil.Builder()
                    .setCacheType(CacheType.CACHE_THEN_NETWORK)//缓存类型可以不设置
                    .build()
                    .doGetAsync(
                            HttpInfo.Builder().setUrl(url).build(this),
                            info -> {
                                if (info.isSuccessful()) {
                                    String result = info.getRetDetail();
                                    resultTV.setText("断网请求："+result);
                                }
                            });
        }else{
            resultTV.setText("请先断网！");
        }
    }


}


```
