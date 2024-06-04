# NSURLSession

`NSURLSession`在 `iOS7`中推出，`NSURLSession`的推出旨在替换之前的 `NSURLConnection`，`NSURLSession`的使用相对于之前的 `NSURLConnection`更简单，而且不用处理 `Runloop`相关的东西。

在苹果官方提供的解决方案中，最早出现的是NSURLConnection。作为Core Foundation/CFNetwork 框架的APIs之上的一个抽象，NSURLConnection伴随着2003年Safari浏览器的原始发行版本，诞生于十几年前。NSURLConnection这个名字，实际上指的是一组构成Foundation框架中URL加载系统的相互关联的组件：NSURLRequest，NSURLResponse，NSURLProtocol，NSURLCache，NSHTTPCookieStorage，NSURLCredentialStorage，以及和它同名的NSURLConnection。

在2013年的WWDC上，苹果官方提供了NSURLConnection的继任者--NSURLSession。与NSURLConnection相比，NSURLSession最直接的改善就是提供了配置每个会话的缓存，协议，Cookie和证书政策（Credential Policies），甚至跨应用程序共享它们的能力。这使得框架的网络基础架构和部分应用程序独立工作，而不会互相干扰。每一个NSURLSession对象都是根据一个NSURLSessionConfiguration初始化的，该NSURLSessionConfiguration指定了上面提到的政策，以及一系列为了提高移动设备性能而专门添加的新选项。NSURLSession的另一重要组成部分是会话任务NSURLSessionTask，它负责处理数据的加载，以及客户端与服务器之间的文件和数据的上传下载服务。

`NSURLSession`由三部分构成：

* **`NSURLSession`**

  * 负责请求/响应的关键对象，可以用系统提供的单例对象，也可以自己使用 `NSURLSessionConfiguration` 配置对象进行创建
  * 在请求/响应的执行过程中调用 `NSURLSessionTaskDelegate` 所定义的各种代理方法。
* **`NSURLSessionConfiguration`**

  * 用于对 `NSURLSession` 对象进行初始化，一般都采用 `default`，可以配置  **可用网络** 、 **Cookie** 、 **安全性** 、 **缓存策略** 、 **自定义协议** 、**启动事件** 等选项，以及用于移动设备优化的相关选项。
  * 几乎可以配置任何选项。
* **`NSURLSessionTask`**

  * 负责执行具体请求的 `task`，由 `session`创建
  * 一个抽象类，其子类可以创建不同类型的任务（Task），如：下载、上传、获取数据（如：JSON 或 XML）
  * 在特定 URL Session 中执行

---

我们可以将 `NSURLSession` 中的类分为以下 6 种：

* URL 加载 （URL Loading）
* 配置管理 （Configuration Management）
* 缓存管理 （Cache Policy）
* Cookie 存储 （Cookie Storage）
* 认证和证书 （Authentication and Credentials）
* 协议支持 （Protocol Support）

在一个请求被发送到服务器之前，系统会先查询共享的缓存信息，然后根据 **缓存策略（Cache Policy）** 以及 **可用性（availability）** 的不同，一个已经被缓存的响应可能会被立即返回。如果没有缓存的响应可用，则这个请求将根据我们指定的策略来缓存它的响应，以便将来的请求可以使用。

在一个请求被发送到服务器过程中，服务器可能会发出  **鉴权查询（Authorization Challenge）** ，这可以由共享的 Cookie 或 **证书存储（Credential Storage）** 来自动响应，或者由被委托对象来响应。此外，发送中的请求也可以被注册的 `NSURLProtocol` 对象所拦截，以便在必要时改变其加载行为。

## NSURLSessionTask

`NSURLSessionTask` 是一个抽象类，其包含如下 3 个实体子类。这 3 个子类封装了 3 个最基本的网络任务： **获取数据** （如：JSON 或 XML）、 **上传文件** 、 **下载文件** 。

* `NSURLSessionDataTask`
* `NSURLSessionUploadTask`
* `NSURLSessionDownloadTask`

对于 `NSURLSessionDataTask`，服务器会有响应数据；而对于上传请求，服务器也会有响应数据， `NSURLSessionUploadTask` 继承自 `NSURLSessionDataTask`。`NSURLSessionDownloadTask` 完成时，会带回已下载文件的一个临时的文件路径。

---

关于 `NSURLSessionTask` 的数据返回方式，主要有两种方式：

* **`completionHandler` 回调**
* **`NSURLSessionDelegate` 代理**

通过 `completionHandler` 回调将会创建一个隐式的代理（delegate），从而替代该 Task 原来的代理 —— Session。

对于需要 override 原有 Session Task 的代理的默认行为的情况，我们需要使用不带 `completionHandler` 版本。

需要注意的是，`NSURLSessionTask` 及其子类都有着各自的代理协议，它们之间也存在着继承关系。

* `NSURLSessionDelegate`：定义了网络请求最基础的代理方法。作为所有代理的基类。
* `NSURLSessionTaskDelegate`：定义了网络请求任务相关的代理方法。
* `NSURLSessionDownloadDelegate`：定义了下载任务相关的代理方法，如：下载进度等
* `NSURLSessionDataDelegate`：定义了普通数据任务和上传任务相关的代理方法。

---

### NSURLSessionDataTask

`NSURLSessionDataTask` 主要用于  **读取服务端的简单数据** ，如：JSON、XML 数据。

 **创建方法** （基于 `NSURLSession` 对象）

```objectivec
// 使用 NSURLRequest 对象创建
- (NSURLSessionDataTask *)dataTaskWithRequest:(NSURLRequest *)request;

// 使用 NSURL 对象创建
- (NSURLSessionDataTask *)dataTaskWithURL:(NSURL *)url;
```

**CompletionHandler**

```objectivec
- (NSURLSessionDataTask *)dataTaskWithRequest:(NSURLRequest *)request completionHandler:(void (^)(NSData *data, NSURLResponse *response, NSError *error))completionHandler;  

- (NSURLSessionDataTask *)dataTaskWithURL:(NSURL *)url completionHandler:(void (^)(NSData *data, NSURLResponse *response, NSError *error))completionHandler;
```

### NSURLSessionUploadTask

`NSURLSessionUploadTask` 主要用于  **向服务器发送文件类型的数据** 。

 **创建方法** （基于 `NSURLSession` 对象）

```objectivec
// 使用 NSURLRequest 对象创建，上传时指定文件源
- (NSURLSessionUploadTask *)uploadTaskWithRequest:(NSURLRequest *)request fromFile:(NSURL *)fileURL;  

// 使用 NSURLRequest 对象创建，上传时指定数据源   
- (NSURLSessionUploadTask *)uploadTaskWithRequest:(NSURLRequest *)request fromData:(NSData *)bodyData;  

- (NSURLSessionUploadTask *)uploadTaskWithStreamedRequest:(NSURLRequest *)request;
```

**CompletionHandler**

```objectivec
- (NSURLSessionUploadTask *)uploadTaskWithRequest:(NSURLRequest *)request fromFile:(NSURL *)fileURL completionHandler:(void (^)(NSData *data, NSURLResponse *response, NSError *error))completionHandler;  

- (NSURLSessionUploadTask *)uploadTaskWithRequest:(NSURLRequest *)request fromData:(NSData *)bodyData completionHandler:(void (^)(NSData *data, NSURLResponse *response, NSError *error))completionHandler;
```

### NSURLSessionDownloadTask

`NSURLSessionDownloadTask` 主要用于  **文件下载** ，它针对大文件的网络请求做了更多的处理，如：下载进度、断点续传等。

 **创建方法** （基于 `NSURLSession` 对象）

```objectivec
// 使用 NSURLRequest 对象创建
- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request;  

// 使用 NSURL 对象创建
- (NSURLSessionDownloadTask *)downloadTaskWithURL:(NSURL *)url;  

// 使用之前已经下载的数据来创建
- (NSURLSessionDownloadTask *)downloadTaskWithResumeData:(NSData *)resumeData;
```

**CompletionHandler**

```objectivec
- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request completionHandler:(void (^)(NSURL *location, NSURLResponse *response, NSError *error))completionHandler;  

- (NSURLSessionDownloadTask *)downloadTaskWithURL:(NSURL *)url completionHandler:(void (^)(NSURL *location, NSURLResponse *response, NSError *error))completionHandler;  

- (NSURLSessionDownloadTask *)downloadTaskWithResumeData:(NSData *)resumeData completionHandler:(void (^)(NSURL *location, NSURLResponse *response, NSError *error))completionHandler;
```



## NSURLSession

`NSURLSession` 是负责请求/响应的关键对象，使用 `NSURLSessionConfiguration` 配置对象进行创建。

`NSURLSession` 本身并不会进行请求，而是通过创建 Task 的形式来进行网络请求。同一个 `NSURLSession` 可以创建多个 Task，并且这些 Task 之间的 Cache 和 Cookie 是共享的。

`NSURLSession` 在管理请求/响应的过程中会调用相关的代理方法。这些代理方法主要分两类：

* **Session 的委托对象实现的代理方法（`NSURLSessionDelegate` 定义的方法）**
  * 主要用于处理连接层问题，如：服务器信任、客户端证书认证、NTLM 和 Kerberos 协议等问题
* **Task 的委托对象实现的代理方法（`NSURLSessionTaskDelegate` 及其子协议定义的方法）**
  * 主要用于处理以网络请求为基础的问题，如：Basic，Digest，**代理身份验证（Proxy Authentication）** 等。

`NSURLSession`有三种方式创建：

```objectivec
sharedSession
```

系统维护的一个单例对象，可以和其他使用这个 `session`的 `task`共享连接和请求信息。

```objectivec
sessionWithConfiguration:
```

在NSURLSession初始化时传入一个NSURLSessionConfiguration，这样可以自定义请求头、cookie等信息。

```objectivec
sessionWithConfiguration:delegate:delegateQueue:
```

如果想更好的控制请求过程以及回调线程，需要上面的方法进行初始化操作，并传入 `delegate`来设置回调对象和回调的线程。


## NSURLSessionConfiguration

`NSURLSessionConfiguration` 对象用于对 `NSURLSession` 进行初始化。

`NSURLSessionConfiguration` 对以前 `NSMutableURLRequest` 所提供的网络请求层的设置选项进行了扩充，提供给开发者相当大的灵活性和控制权。从指定可用网络，到 cookie，安全性，缓存策略，再到使用自定义协议，启动事件的设置，以及用于移动设备优化的几个新属性，可以发现使用 `NSURLSessionConfiguration` 可以找到几乎任何想要进行配置的选项。

`NSURLSession` 在初始化时会把配置它的 `NSURLSessionConfiguration` 对象进行一次深拷贝，并保存到自己的 `configuration` 属性中，而且这个属性是只读的。也就是说，`configuration` 只在初始化时被读取一次，之后都是不会变化的。


# AFNetworking

> AFNetworking是目前使用人数最多的网络加载第三方开源框架，从最初的NSURLConnection到现在的NSURLSession，它都一直保持着与苹果的步调一致，而由它也衍生出大量的相关第三方网络功能框架。在绝大多数情况下，当我们需要发送网络请求，或者是下载文件，使用AFNetworking都是最便捷的选择。

AFNetworking是一个完全开源的第三方框架，因此我们可以清晰的获取其中每个类的实现方式。根据AFNetworking框架中的内容，主要分为如下四个功能模块：

* 网络通信模块(URLSession)
* 网络状态监听模块(Reachability)
* 网络通信安全策略模块(Security)
* 网络通信信息序列化/反序列化模块(Serialization)

AFNetworking的其核心是网络通信模块，其余的模块均是为了配合网络通信模块工作。

AFNetworking的核心类是AFURLSessionManager类，AFHTTPSessionManager继承自AFURLSessionManager，而AFURLRequestSerialization和AFURLResponseSerialization， AFSecurityPolicy, AFNetworkReachabilityManager则被AFURLSessionManager所使用。

AFNetworking 主要包含了 6 个类：

* **`AFURLSessionManager`** ：AFNetworking 的核心类。
* **`AFHTTPSessionManager`** ：`AFURLSessionManager` 的子类，主要用于 HTTP 请求。
* **`AFURLRequestSerialization`** ：请求序列化器，用于将参数编码为查询字符串、HTTP 正文，并根据需要设置合适的 HTTP 头部字段。
* **`AFURLResponseSerialization`** ：响应序列化器，用于将数据解码为对象，还可以对传入的响应和数据进行验证。
* **`AFSecurityPolicy`** ：通过安全连接评估服务器对固定的 X.509 证书和公钥的信任。
* **`AFNetworkReachabilityManager`** ：监视网络可达性。

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

# YTKNetwork

YTKNetwork 是猿题库技术团队开源的一个网络请求框架，内部封装了 AFNetworking。YTKNetwork 实现了一套高层级的 API，提供更高层次的网络访问抽象。

## YTKNetwork 架构

YTKNetwork 开源框架主要包含 3 个部分：

* YTKNetwork 核心功能
* YTKNetwork 链式请求
* YTKNetwork 批量请求

其中，链式请求和批量请求都是基于 YTKNetwork 的核心功能实现的。

### YTKNetwork 核心功能

YTKNetwork 核心功能的基本思想是：

* **把每一个网络请求封装成一个对象，每个请求对象继承自 `YTKBaseRequest` 类** 。
* **使用 `YTKNetworkAgent` 单例对象持有一个 `AFHTTPSessionManager` 对象来管理所有请求对象** 。

YTKNetwork 核心功能主要涉及到 3 个类：

* `YTKBaseRequest`
* `YTKNetworkConfig`
* `YTKNetworkAgent`

#### YTKBaseRequest

`YTKBaseRequest` 类用于表示一个请求对象，它提供了一系列属性来充分表示一个网络请求。
