---
layout:     post
title:      "Android开发之RxJava和Retrofit的结合"
subtitle:   ""
date:       2019-03-28 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Android
    - RxJava
    - Retrofit  

---  


为了使Rxjava2与retrofit2结合，我们需要在Retrofit对象建立的时候添加一句代码  
```java  
addCallAdapterFactory(RxJava2CallAdapterFactory.create())  
```
当然你还需要在build.gradle文件中添加如下依赖：

```gradle  
implementation 'io.reactivex.rxjava2:rxjava:2.2.1'  
implementation 'io.reactivex.rxjava2:rxandroid:2.1.0'  
implementation 'com.squareup.retrofit2:retrofit:2.4.0'  
implementation 'com.squareup.retrofit2:converter-gson:2.4.0'  
implementation 'com.squareup.retrofit2:adapter-rxjava2:2.4.0'  

```
这些依赖是Rxjava2和Retrofit2结合需要的依赖。  


完整的代码如下：
```java  
Retrofit retrofit = new Retrofit.Builder().baseUrl("www.xxxxxxx.com")
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .build();

```

然后我们还需要修改RetrofitService 中的代码：  
```java  

public interface RetrofitService {
    @GET("book/search")
    Observable<Book> getSearchBook(@Query("q") String name,
                                    @Query("tag") String tag, @Query("start") int start,
                                    @Query("count") int count);
```

可以看到，在原来的RetrofitService 中我们把getSearchBook方法返回的类型Call改为了Observable，也就是被观察者。其他都没变。然后就是创建RetrofitService 实体类：
```java  
RetrofitService service = retrofit.create(RetrofitService.class);  
```

和上面一样，创建完RetrofitService ，就可以调用里面的方法了：  
```java  
Observable<Book> observable =  service.getSearchBook("Hello", null, 0, 1);  
```

其实这一步，就是创建了一个rxjava中observable，即被观察者,有了被观察者，就需要一个观察者，且订阅它：

```java  
       @Override
    public void getArticles(final int page) {
        Observable<BaseResponse<ArticleListResponse>> observable = dataManager.getArticles(page);
        observable.observeOn(AndroidSchedulers.mainThread())//请求完成后在主线程更显UI
                .subscribeOn(Schedulers.io()) //请求数据的事件发生在io线程
                .map(new Function<BaseResponse<ArticleListResponse>, List<Article>>() {
                    @Override
                    public List<Article> apply(
                            @NonNull BaseResponse<ArticleListResponse> response)
                            throws Exception {
                        return response.getData().getDatas();
                    }
                }).subscribeWith(new Observer<List<Article>>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {
                disposable.add(d);
            }

            @Override
            public void onNext(@NonNull List<Article> articles) {
              //这里的book就是我们请求接口返回的实体类
                getView().showArticles(page, articles);
            }

            @Override
            public void onError(@NonNull Throwable e) {
              //请求过程中发生错误
                getView().showError(e.getMessage());
            }

            @Override
            public void onComplete() {
              //所有事件都完成，可以做些操作。。。
            }
        });
    }  

```

在上面中我们可以看到，事件的消费在Android主线程，这样我们就引入了RxAndroid，RxAndroid其实就是对RxJava的扩展。比如上面这个Android主线程在RxJava中就没有，因此要使用的话就必须得引用RxAndroid。  


## 参考  

1. [MVP+Retrofit+RxJava+Dagger框架](https://blog.csdn.net/jinhuoxingkong/article/details/76260032)  
2. [网络加载框架 - Retrofit](https://www.jianshu.com/p/0fda3132cf98)  
3. [最适合android的MVP模式](https://www.jianshu.com/p/bbb3b77d47eb)  
4. [这可能是最好的RxJava 2.x 教程（完结版）]https://www.jianshu.com/p/0cd258eecf60)  


