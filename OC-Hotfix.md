
# 热修复

### 本地js热修复

```objectivec
    [JPEngine startEngine];
  
    NSString *sourcePath = [[NSBundle mainBundle] pathForResource:@"hot_fix" ofType:@"js"];
    NSString *script = [NSString stringWithContentsOfFile:sourcePath encoding:NSUTF8StringEncoding error:nil];
  
    [JPEngine evaluateScript:script];
```



### 在使用Objective-C类之前需要调用 `require('className’)` :

```js
require('UIView')
var view = UIView.alloc().init()
```

### 可以用逗号 `,` 分隔，一次性导入多个类:

```js
require('UIView, UIColor')
var view = UIView.alloc().init()
var red = UIColor.redColor()
```

### 或者直接在使用时才调用 `require()` :

```js
require('UIView').alloc().init()
```

### 覆盖方法：

```javascript
defineClass("JPTableViewController", {
  //实例方法
}, {
  //类方法
})
```

### block 传递

当要把 JS 函数作为 block 参数给 OC时，需要先使用 `block(paramTypes, function)` 接口包装:

```objc
// Obj-C
@implementation JPObject
+ (void)request:(void(^)(NSString *content, BOOL success))callback
{
  callback(@"I'm content", YES);
}
@end
```

```js
// JS
require('JPObject').request(block("NSString *, BOOL", function(ctn, succ) {
  if (succ) log(ctn)  //output: I'm content
}))
```

### JS断点调试

#### 启动调试工具

首先需要开启 Safari 调试菜单：Safari -> 偏好设置 -> 高级 -> 勾选[在菜单栏中显示“开发”菜单]

接着启动APP -> Safari -> 开发 -> 选择你的机器 -> JSContext

即可开始调试。

连接真机调试时，需要打开真机的web检查器：设置 -> Safari -> 高级 -> Web检查器

#### 资源列表

资源列表列出了 JSPatch 所有执行中的脚本文件，点开文件后可以对其进行断点调试。

通过 `[JPEngine evaluateScript:script]` 接口执行的脚本，在资源列表里都表示为`main.js`。

通过`[JPEngine evaluateScriptWithPath:filePath]` 接口执行的脚本，在资源列表里会以原文件名表示。
