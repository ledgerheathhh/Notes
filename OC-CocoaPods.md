**静态库，动态库，Framework的区别**

所谓的库就是一段编译好的二进制文件，加上头文件，相关的资源文件就可供别人使用

> 静态库：（静态链接库）（.a）在编译时会将库copy一份到目标程序中，编译完成之后，目标程序不依赖外部的库，也可以运行
> 缺点是会使应用程序变大
>
> 动态库：（.dylib）编译时只存储了指向动态库的引用。
> 可以多个程序指向这个库，在运行时才加载，不会使体积变大，
> 但是运行时加载会损耗部分性能，并且依赖外部的环境，如果库不存在或者版本不正确则无法运行
>
> Framework：实际上是一种打包方式，将库的二进制文件，头文件和有关的资源文件打包到一起，方便管理和分发。
> iOS8 / Xcode 6 之前是无法使用静态库，出现了AppExtension之后可以使用

对于是否使用Framework，CocoaPods 通过use_frameworks来控制

* 不使用use_frameworks! -> static libraries 方式 -> 生成.a文件

> 在Podfile中如不加use_frameworks!，cocoapods会生成相应的 .a文件（静态链接库），
> Link Binary With Libraries: libPods-xxx.a 包含了其他用pod导入有第三库的.a文件

* use_frameworks! -> dynamic frameworks 方式 -> 生成.framework文件

> 使用了use_frameworks!，cocoapods会生成对应的frameworks文件（包含了头文件，二进制文件，资源文件等等）
>
> Link Binary With Libraries：Pods_xxx.framework包含了其它用pod导入的第三方框架的.framework文件

**1.纯oc项目中 通过pod导入纯oc项目, 一般都不使用frameworks**

**2.swift 项目中通过pod导入swift项目，必须要使用use_frameworks！，在需要使用的到地方 import AFNetworking**
