## AFNetworking

> AFNetworking是目前使用人数最多的网络加载第三方开源框架，从最初的NSURLConnection到现在的NSURLSession，它都一直保持着与苹果的步调一致，而由它也衍生出大量的相关第三方网络功能框架。在绝大多数情况下，当我们需要发送网络请求，或者是下载文件，使用AFNetworking都是最便捷的选择。

AFNetworking是一个完全开源的第三方框架，因此我们可以清晰的获取其中每个类的实现方式。根据AFNetworking框架中的内容，主要分为如下四个功能模块：

* 网络通信模块(URLSession)
* 网络状态监听模块(Reachability)
* 网络通信安全策略模块(Security)
* 网络通信信息序列化/反序列化模块(Serialization)

AFNetworking的其核心是网络通信模块，其余的模块均是为了配合网络通信模块工作。

AFNetworking的核心类是AFURLSessionManager类，AFHTTPSessionManager继承自AFURLSessionManager，而AFURLRequestSerialization和AFURLResponseSerialization， AFSecurityPolicy, AFNetworkReachabilityManager则被AFURLSessionManager所使用。

## AFNetworking常用方法

AFNetworking对复杂的网络请求进行了封装，大大降低了有关网络开发的难度，一般来说，只要掌握如下两个核心方法即可。

* 发送POST请求并对返回结果进行处理。我们可以在**success**这个block中获取服务器返回的数据responseObject，并对服务器返回的数据进行后续处理，需要注意的是该block是异步调用的，即当成功获取网络请求后才被调用。

```objectivec
- (nullable NSURLSessionDataTask *)POST:(NSString *)URLString
                    parameters:(nullable id)parameters
                       success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                       failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;
```

* 发送GET请求并对返回结果进行处理。服务器返回的数据同样封装在success这个block中的responseObject参数中。

```objectivec
- (nullable NSURLSessionDataTask *)GET:(NSString *)URLString
                   parameters:(nullable id)parameters
                      success:(nullable void (^)(NSURLSessionDataTask *task, id _Nullable responseObject))success
                      failure:(nullable void (^)(NSURLSessionDataTask * _Nullable task, NSError *error))failure;
```
