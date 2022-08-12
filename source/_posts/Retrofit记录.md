---
title: Retrofit记录
date: 2022-08-12 12:28:40
tags: Java
categories: 
    - Java
    - Android
---

发送网络请求（异步）：

```java
//发送网络请求(异步)
call.enqueue(new Callback<Translation>() {
    //请求成功时回调
    @Override
    public void onResponse(Call<Translation> call, Response<Translation> response) {
        //对返回数据进行处理
        response.body().show();
    }

    //请求失败时候的回调
    @Override
    public void onFailure(Call<Translation> call, Throwable throwable) {
        System.out.println("连接失败");
    }
});
```

发送网络请求（同步）：

```java
//发送网络请求（同步）
Response<Reception> response = call.execute();
//对返回数据进行处理
response.body().show();
```



注解实现网络请求方法

以 **IGearsLocatorApi** 中下面这段代码为例：

```java
//V3网络定位请求(明文)
@POST("locate/v3/sdk/loc?")
@Headers({"X-Request-Encrypt:0"
        , "X-Request-Platform:1"
        , "Content-Type:application/json"
        , "X-Request-Type:0"
})
Call<ResponseBody> sendWithPlain(@Body RequestBody content);
```

请求体作为参数传入，这里请求的类型为 POST，Headers 注解内的部分是请求头

这里返回了 Call 对象为执行发起网络请求的实际对象



创建 Retrofit 实例对象：

```java
 Retrofit retrofit = new Retrofit.Builder()
     .baseUrl("http://fanyi.youdao.com/") // 设置网络请求的Url地址
     .addConverterFactory(GsonConverterFactory.create()) // 设置数据解析器
     .addCallAdapterFactory(RxJavaCallAdapterFactory.create()) // 支持RxJava平台
     .build();
```

过程是用 Retorfit 的 Builder 对象进行 build ，得到 **Retrofit** 对象

具体在项目里的构造在 **MtRetrofitFactory** 下面完成



创建网络请求接口实例：

```java
// 创建 网络请求接口 的实例
GetRequest_Interface request = retrofit.create(GetRequest_Interface.class);

//对 发送请求 进行封装
Call<Reception> call = request.getCall();
```

在项目里的网络接口实例是 **IGearsLocatorApi** ，具体使用起来有一点差别



总结一下执行的过程：

先用 Retorfit 的 Builder 对象进行 build ，得到 **Retrofit** 对象，然后用 Retrofit 对象创建网络接口请求对象 Request(service)（**IGearsLocatorApi**），之后通过网络接口的方法获取到执行网络请求的实体 **Call** 对象，调用 call.execute() 会返回响应结果

1. 对于 Retrofit 的获取是通过 **MtRetrofitFactory** 的 getRetrofitByURL(String url) 方法实现的，在这个方法下添加了拦截器以在 execute 的时候向请求头添加用户信息
2. 后在 GearsLocator 下调用 this.**mGearsLocatorApi** = **retrofit**.create(IGearsLocatorApi.class) 来获取网络请求接口对象
3. **mGearsLocatorApi** 下有相关的请求方法
4. 在 GearsController 类下会通过传进请求体调用 **mGearsLocatorApi** 的方法返回 Call 对象执行 execute ，返回 Response 响应
