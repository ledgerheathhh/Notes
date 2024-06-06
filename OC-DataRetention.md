## 沙盒

> iOS中的沙盒机制（SandBox）是一种安全体系，它规定了应用程序只能在该应用的文件夹内读取文件，不可以访问其他区域的内容，此区域被成为沙盒。所有的非代码文件都保存在这个地方，比如图片、声音、属性列表和文本文件等。

* 每个应用程序都有自己的存储空间
* 应用程序不能随意跨越自己的沙盒去访问别的应用程序沙盒的内容
* 应用程序向外请求或接收数据都需要经过权限认证

正是因为沙盒机制的存在，iOS以及MacOS的软件安全性要比Android以及Windows应用的安全性高。

### 沙盒目录

默认情况下，每个App应用的沙盒含有3个文件夹：Documents, Library 和 tmp。因为应用的沙盒机制，应用只能在这几个目录下读写文件。iTunes在与iPhone同步时，会备份所有的Documents和Library文件。iPhone在重启时，会丢弃所有的tmp文件。

* Documents：苹果建议将程序中建立的或在程序中浏览到的文件数据保存在该目录下，iTunes备份和恢复的时候会包括此目录
* Library：存储程序的默认设置或其它状态信息，其中又包含了Caches文件夹和Preferences文件夹
* Library/Caches：存放缓存文件，iTunes不会备份此目录，此目录下文件不会在应用退出时删除
* Library/Preferences：存放偏好设置的plist文件
* tmp：提供一个即时创建临时文件的地方

程序的沙盒文件在Mac上是被隐藏的，所以如果想要查看程序的沙盒路径，首先需要显示Mac上隐藏的文件夹。

我们可以在Terminal中，执行如下命令，来实现文件夹的显示与隐藏。

* 显示隐藏的文件夹

```shell
defaults write com.apple.finder AppleShowAllFiles -bool true
```

在应用程序中，使用 **NSHomeDirectory()** 函数，获取App的沙盒路径

# NSUserDefaults

> 偏好设置在iOS开发中的使用是比较普遍的，主要原因在于其简单易用。偏好设置本质上就是一个Plist文件，不过该Plist文件是由系统自动创建的，并且在Foundation框架中提供了一些专用的访问方法。

绝大多数iOS应用开发过程中，都会使用到偏好设置，比如保存用户名、密码、字体大小等设置。 Foundation框架提供了一套标准的解决方案为应用提供了偏好设置功能。每个应用都有一个NSUserDefaults实例，通过它来存取偏好设置。NSUserDefaults 基本上支持所有的原生数据类型NSString、 NSNumber、NSDate、 NSArray、NSDictionary、BOOL、NSInteger等等。关于偏好设置需要了解如下两个要点。

* 偏好设置也是保存在应用的沙盒中的，保存的路径在**Library/Preferences**路径下
* 偏好设置可以理解为是一个特殊的Plist文件，但由于其本质上还是Plist文件，因此，存储形式还是使用键值对的方式。

在Foundation框架中的NSUserDefaults.h文件中，提供了NSUserDefaults类的方法和属性，常用的方法和属性有如下几个。

* standardUserDefaults：获取系统默认的偏好设置对象 , `NSUserDefaults`是一个用于在iOS和macOS应用程序中存储和检索用户偏好和其他设置信息的类。在应用程序的整个生命周期中，`NSUserDefaults`的单例实例都是唯一的，并且会自动将更新后的数据缓存在内存中，以提高读取和写入的性能。

```objectivec
@property (class, readonly, strong) NSUserDefaults *standardUserDefaults;
```

* 偏好设置写入方法

```objectivec
- (void)setObject:(nullable id)value forKey:(NSString *)defaultName;
- (void)setInteger:(NSInteger)value forKey:(NSString *)defaultName;
- (void)setFloat:(float)value forKey:(NSString *)defaultName;
- (void)setDouble:(double)value forKey:(NSString *)defaultName;
- (void)setBool:(BOOL)value forKey:(NSString *)defaultName;
- (void)setURL:(nullable NSURL *)url forKey:(NSString *)defaultName;
```

* 偏好设置读取方法

```objectivec
- (nullable id)objectForKey:(NSString *)defaultName;
- (nullable NSString *)stringForKey:(NSString *)defaultName;
- (nullable NSArray *)arrayForKey:(NSString *)defaultName;
- (nullable NSDictionary<NSString *, id> *)dictionaryForKey:(NSString *)defaultName;
- (nullable NSData *)dataForKey:(NSString *)defaultName;
- (NSInteger)integerForKey:(NSString *)defaultName;
- (float)floatForKey:(NSString *)defaultName;
- (double)doubleForKey:(NSString *)defaultName;
- (BOOL)boolForKey:(NSString *)defaultName;
- - (nullable NSURL *)URLForKey:(NSString *)defaultName;
```

* 移除某个键值对

```objectivec
- (void)removeObjectForKey:(NSString *)defaultName;
```

* synchronize: 立即写入偏好设置Plist文件中

```objectivec
- (BOOL)synchronize;
```

## 存储数据(新增/更新)

在偏好设置中存储数据，需要根据存储数据的对象类型调用不同的写入方法，同时还需要为每个数据提供一个标识键值。

* 获取NSUserDefaults对象单例实例

```objectivec
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
```

* 保存数据

```objectivec
[defaults setObject:[NSDate date] forKey:@"LastLoginTime"];
[defaults setBool:NO forKey:@"IsFirstLogin"];
[defaults setValue:@"ledger" forKey:@"UserName"];
```

    在iOS和macOS应用程序中，`NSUserDefaults`会自动将更新后的数据缓存在内存中，以提高读取和写入的性能。但是，为了确保数据的完整性和持久性，我们需要在适当的时机将缓存在内存中的数据都写入到磁盘上的plist文件中。

`NSUserDefaults`提供了两种策略来更新磁盘上的plist文件：

* 同步更新：调用 `[defaults synchronize]`方法，将缓存在内存中的所有更新都立即写入到磁盘上的plist文件中。这是一个同步操作，会阻塞当前线程，直到所有更新都写入到磁盘上的plist文件中为止。我们应该谨慎地使用这个方法，避免在主线程中频繁调用，以免影响应用程序的性能和用户体验。
* 异步更新：在iOS 8和macOS 10.10之后，`NSUserDefaults`会自动在后台异步地将缓存在内存中的更新写入到磁盘上的plist文件中。这是一个异步操作，不会阻塞当前线程，但是也无法保证数据的实时性和一致性。因此，我们在使用异步更新策略时，需要注意数据的一致性和安全性问题。

## 读取数据

由于偏好设置中的每个存储内容在写入的时候都需要提供对应的标识键值，因此在读取操作时，我们就可以根据标识键值来读取其中存储的内容。

* 获取NSUserDefaults对象

```objectivec
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
```

* 读取数据

```objectivec
NSDate *lastLoginTime = [defaults objectForKey:@"LastLoginTime"];
BOOL isFirstLogin = [defaults boolForKey:@"IsFirstLogin"];
NSString *userName = [defaults valueForKey:@"UserName"];
```

* 打印数据

```objectivec
NSLog(@"%@--%d--%@", lastLoginTime, isFirstLogin, userName);
```

## 删除数据

当我们需要删除偏好设置中的内容时，也是根据其标识键值进行删除的。

* 获取NSUserDefaults对象

```objectivec
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
```

* 根据键值名称，删除数据

```objectivec
[defaults removeObjectForKey:@"LastLoginTime"];
```

# SQLite

SQLite 是一个轻量级的、嵌入式的关系型数据库管理系统（RDBMS），它的核心是一个 C 语言库，可以在许多不同的平台和操作系统上使用。SQLite 使用一种简单的文件格式来存储数据库，因此可以方便地在不同的设备和应用程序之间进行交换和共享。

### SQLite 的常用函数

SQLite 提供了许多函数和接口，用于进行数据库的操作和管理。以下是一些常用的函数和接口：

* `sqlite3_open()`：打开一个 SQLite 数据库文件，并返回一个 `sqlite3` 对象。
* `sqlite3_close()`：关闭一个 `sqlite3` 对象，并释放数据库文件和其他资源。
* `sqlite3_prepare_v2()`：将 SQL 语句编译成字节码，并返回一个 `sqlite3_stmt` 对象。
* `sqlite3_step()`：逐步执行 `sqlite3_stmt` 对象中的 SQL 语句，并获取查询结果或者执行结果。
* `sqlite3_finalize()`：释放 `sqlite3_stmt` 对象所占用的内存。
* `sqlite3_bind_xxx()`：将参数的值绑定到 `sqlite3_stmt` 对象中。
* `sqlite3_column_xxx()`：从查询结果中获取列数据。
* `sqlite3_exec()`：执行一个 SQL 语句，并使用回调函数来处理查询结果或者执行结果。

### SQLite 的数据库文件

SQLite 数据库文件是一种特殊的二进制文件，用于存储数据库中的表、索引、数据等信息。SQLite 数据库文件的后缀名通常是 `.sqlite` 或者 `.db`，但是实际上并没有严格的限制，可以是任意的字符串。在 iOS 中，你需要将数据库文件放置在沙盒的可访问目录中，例如文档目录、缓存目录等，以便于应用程序具有读取和写入数据库文件的权限。

### SQLite 的基本使用

以下是 SQLite 的基本使用步骤：

1. 打开数据库

使用 `sqlite3_open()` 函数打开一个 SQLite 数据库。如果数据库文件不存在，`sqlite3_open()` 函数会自动创建一个新的数据库文件。

```objc
sqlite3 *db;
int rc = sqlite3_open("test.db", &db);
if (rc) {
    NSLog(@"Can't open database: %s", sqlite3_errmsg(db));
    return;
}
```

2. 准备 SQL 语句

使用 `sqlite3_prepare_v2()` 函数将 SQL 语句编译成字节码，并返回一个 `sqlite3_stmt` 对象，用于后续的执行和绑定。

```objc
const char *sql = "SELECT * FROM users WHERE age > ?";
sqlite3_stmt *stmt;
rc = sqlite3_prepare_v2(db, sql, -1, &stmt, NULL);
if (rc != SQLITE_OK) {
    NSLog(@"Prepare statement failed: %s", sqlite3_errmsg(db));
    sqlite3_close(db);
    return;
}
```

3. 绑定参数

如果 SQL 语句中包含参数，可以使用 `sqlite3_bind_xxx()` 函数将参数的值绑定到 `sqlite3_stmt` 对象中，以便于在执行时使用。

```objc
sqlite3_bind_int(stmt, 1, 25);
```

4. 执行 SQL 语句

使用 `sqlite3_step()` 函数逐步执行 `sqlite3_stmt` 对象中的 SQL 语句，并获取查询结果或者执行结果。

```objc
while (sqlite3_step(stmt) == SQLITE_ROW) {
    int id = sqlite3_column_int(stmt, 0);
    const char *name = (const char *)sqlite3_column_text(stmt, 1);
    int age = sqlite3_column_int(stmt, 2);
    NSLog(@"id: %d, name: %s, age: %d", id, name, age);
}
```

5. 处理查询结果

如果 SQL 语句是一个查询语句，可以使用 `sqlite3_column_xxx()` 函数从查询结果中获取列数据，并进行后续的处理和展示。

```objc
const char *name = (const char *)sqlite3_column_text(stmt, 1);
```

6. 清理资源

使用 `sqlite3_finalize()` 函数释放 `sqlite3_stmt` 对象所占用的内存，并使用 `sqlite3_close()` 函数关闭 `sqlite3` 对象，以便于释放数据库文件和其他资源。

```objc
sqlite3_finalize(stmt);
sqlite3_close(db);
```

除了以上的基本使用步骤，SQLite 还提供了一些其他的函数和接口，用于进行数据库的操作和管理。以下是一些常用的函数和接口：

* `sqlite3_open()`：打开一个 SQLite 数据库文件，并返回一个 `sqlite3` 对象。
* `sqlite3_close()`：关闭一个 `sqlite3` 对象，并释放数据库文件和其他资源。
* `sqlite3_prepare_v2()`：将 SQL 语句编译成字节码，并返回一个 `sqlite3_stmt` 对象。
* `sqlite3_step()`：逐步执行 `sqlite3_stmt` 对象中的 SQL 语句，并获取查询结果或者执行结果。
* `sqlite3_finalize()`：释放 `sqlite3_stmt` 对象所占用的内存。
* `sqlite3_bind_xxx()`：将参数的值绑定到 `sqlite3_stmt` 对象中。
* `sqlite3_column_xxx()`：从查询结果中获取列数据。
* `sqlite3_exec()`：执行一个 SQL 语句，并使用回调函数来处理查询结果或者执行结果。

### SQLite 的数据库文件

SQLite 数据库文件是一种特殊的二进制文件，用于存储数据库中的表、索引、数据等信息。SQLite 数据库文件的后缀名通常是 `.sqlite` 或者 `.db`，但是实际上并没有严格的限制，可以是任意的字符串。在 iOS 中，你需要将数据库文件放置在沙盒的可访问目录中，例如文档目录、缓存目录等，以便于应用程序具有读取和写入数据库文件的权限。

### SQLite 的高级特性

SQLite 不仅提供了基本的数据库操作和管理功能，还提供了许多高级的特性，例如：

* 事务：SQLite 支持事务的机制，可以将多个 SQL 语句组合成一个事务，并且在事务中的 SQL 语句要么全部执行成功，要么全部执行失败。
* 索引：SQLite 支持对表中的列创建索引，以提高查询的效率。
* 触发器：SQLite 支持在表中创建触发器，在特定的条件下自动执行一些 SQL 语句。
* 视图：SQLite 支持创建视图，将多个表中的数据进行关联和筛选，并且可以对视图进行查询和更新。
* 全文检索：SQLite 支持对表中的文本数据进行全文检索，以提高查询的效率和准确性。
* 加密：SQLite 支持对数据库文件进行加密，以保护数据的安全性。

### 小结

SQLite 是一个非常有用的嵌入式数据库，可以在 iOS 中进行数据的存储和管理。使用 SQLite 的基本步骤包括：打开数据库、准备 SQL 语句、绑定参数、执行 SQL 语句、处理查询结果、清理资源。SQLite 提供了许多常用的函数和接口，可以帮助我们更好地使用和管理数据库。SQLite 数据库文件是一种特殊的二进制文件，需要放置在沙盒的可访问目录中。SQLite 还提供了许多高级的特性，可以满足更复杂的数据库操作和管理需求。

# MMKV

MMKV 是基于 mmap 内存映射的 key-value 组件，底层序列化/反序列化使用 protobuf 实现，性能高，稳定性强。

### 安装引入

* **通过 CocoaPods：**

1. `pod repo update` 让 CocoaPods 感知最新的 MMKV 版本；
2. 打开 Podfile, 添加 `pod 'MMKV'`到你的 App Target 里面；或 `pod 'MMKVAppExtension'` 到你的 AppExtension Target 里，或 `pod 'MMKVWatchExtension'` 到你的 WatchExtension Target 里；
3. 在命令行输入 `pod install`；
4. 添加头文件 `#import <MMKV/MMKV.h>`，相应地，如果是其他 Extension Target，头文件也要相应地变更为 `<MMKVAppExtension/MMKV.h>` 或 `<MMKVWatchExtension/MMKV.h>` 。

## 使用

MMKV 可以直接使用，所有变更立即生效，无需调用 `synchronize` 之类的函数。

### 初始化

* 在 App 启动时初始化 MMKV（设定 MMKV 的根目录），例如在 `-[MyApp application: didFinishLaunchingWithOptions:]`里：

  ```objc
  - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
      // init MMKV in the main thread
      [MMKV initializeMMKV:nil];

      //...
      return YES;
  }
  ```
* 如果你需要 **多进程访问** (在主 App 与 Extension 之间)，那么需要在初始化时设置 [**group directory**](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_security_application-groups?language=objc):

  ```objc
  NSString *myGroupID = @"group.company.mmkv";
  // the group dir that can be accessed by App & extensions
  NSString *groupDir = [[NSFileManager defaultManager] containerURLForSecurityApplicationGroupIdentifier:myGroupID].path;
  [MMKV initializeMMKV:nil groupDir:groupDir logLevel:MMKVLogInfo];
  ```

### CRUD 操作

* MMKV 提供一个 **全局的实例** ，可以直接使用：
  ```objc
      // 初始化 MMKV
      [MMKV initializeMMKV:nil];

      // 获取默认的 MMKV 实例
      MMKV *mmkv = [MMKV defaultMMKV];

      // 存储数据
      [mmkv setObject:@"Hello MMKV" forKey:@"myString"];
      [mmkv setInt32:12345 forKey:@"myInt"];
      [mmkv setBool:YES forKey:@"myBool"];

      // 读取数据
      NSString *myString = [mmkv getObjectOfClass:[NSString class] forKey:@"myString"];
      int myInt = [mmkv getInt32ForKey:@"myInt"];
      BOOL myBool = [mmkv getBoolForKey:@"myBool"];

      NSLog(@"String: %@", myString);
      NSLog(@"Int: %d", myInt);
      NSLog(@"Bool: %d", myBool);

      // 更新数据
      [mmkv setObject:@"Updated MMKV" forKey:@"myString"];

      myString = [mmkv getObjectOfClass:[NSString class] forKey:@"myString"];
      NSLog(@"String: %@", myString);

      // 删除数据
      [mmkv removeValueForKey:@"myInt"];

      myInt = [mmkv getInt32ForKey:@"myInt"];
      NSLog(@"Int: %d", myInt);
  ```

```objc
NSDictionary *dic = @{@"key1" : @"value1",
                      @"key2" : @(2)};
[mmkv setObject:dic forKey:@"dictionary"];
dic = [mmkv getObjectOfClass:[NSDictionary class] forKey:@"dictionary"];
NSLog(@"dictionary:%@", dic);

```

* **删除、枚举** ：

```objc
MMKV *mmkv = [MMKV defaultMMKV];

[mmkv removeValueForKey:@"bool"];
[mmkv removeValuesForKeys:@[@"int32", @"int64"]];

BOOL hasBool = [mmkv containsKey:@"bool"];
  
[mmkv enumerateKeys:^(NSString *key, BOOL *stop) {
    if ([key isEqualToString:@"string"]) {
        NSString *value = [mmkv getStringForKey:key];
        NSLog(@"%@ = %@", key, value);
        *stop = YES;
    }
}];

// delete everything
[mmkv clearAll];
```

* 如果不同业务需要 **区别存储** ，也可以单独创建自己的实例：
  ```objc
  MMKV* mmkv = [MMKV mmkvWithID:@"MyID"];
  [mmkv setBool:YES forKey:@"bool"];
  ```
* 如果你需要 **多进程访问** (在主 App 与 Extension 之间)，那么如前文所述需要在初始化时设置  **group directory** 。然后传入 `MMKVMultiProcess` 参数获取多进程实例:
  ```objc
  MMKV *mmkv = [MMKV mmkvWithID:@"MyMultiID" mode:MMKVMultiProcess];
  [mmkv setBool:YES forKey:@"bool"];
  ```

### 支持的数据类型

* 支持以下 C/C++ 语语言基础类型：
  * `bool, int32, int64, uint32, uint64, float, double`
* 支持以下 Objective-C 类型：
  * `NSString, NSData, NSDate`
* 支持实现了 `<NSCoding>`协议的任何类型。

## MMKV 原理

### 内存准备

通过 mmap 内存映射文件，提供一段可供随时写入的内存块，App 只管往里面写数据，由操作系统负责将内存回写到文件，不必担心 crash 导致数据丢失。

### 数据组织

数据序列化方面我们选用 protobuf 协议，pb 在性能和空间占用上都有不错的表现。考虑到我们要提供的是通用 kv 组件，key 可以限定是 string 字符串类型，value 则多种多样（int/bool/double 等）。要做到通用的话，考虑将 value 通过 protobuf 协议序列化成统一的内存块（buffer），然后就可以将这些 KV 对象序列化到内存中。

```objc
message KV {
	string key = 1;
	buffer value = 2;
}

-(BOOL)setInt32:(int32_t)value forKey:(NSString*)key {
	auto data = PBEncode(value);
	return [self setData:data forKey:key];
}

-(BOOL)setData:(NSData*)data forKey:(NSString*)key {
	auto kv = KV { key, data };
	auto buf = PBEncode(kv);
	return [self write:buf];
}

```

### 写入优化

标准 protobuf 不提供增量更新的能力，每次写入都必须全量写入。考虑到主要使用场景是频繁地进行写入更新，我们需要有增量更新的能力：将增量 kv 对象序列化后，直接 append 到内存末尾；这样同一个 key 会有新旧若干份数据，最新的数据在最后；那么只需在程序启动第一次打开 mmkv 时，不断用后读入的 value 替换之前的值，就可以保证数据是最新有效的。

### 空间增长

使用 append 实现增量更新带来了一个新的问题，就是不断 append 的话，文件大小会增长得不可控。例如同一个 key 不断更新的话，是可能耗尽几百 M 甚至上 G 空间，而事实上整个 kv 文件就这一个 key，不到 1k 空间就存得下。这明显是不可取的。我们需要在性能和空间上做个折中：以内存 pagesize 为单位申请空间，在空间用尽之前都是 append 模式；当 append 到文件末尾时，进行文件重整、key 排重，尝试序列化保存排重结果；排重后空间还是不够用的话，将文件扩大一倍，直到空间足够。

```objc
-(BOOL)append:(NSData*)data {
	if (space >= data.length) {
		append(fd, data);
	} else {
		newData = unique(m_allKV);
		if (total_space >= newData.length) {
			write(fd, newData);
		} else {
			while (total_space < newData.length) {
				total_space *= 2;
			}
			ftruncate(fd, total_space);
			write(fd, newData);
		}
	}
}
```

### 数据有效性

考虑到文件系统、操作系统都有一定的不稳定性，另外增加了 crc 校验，对无效数据进行甄别。

### Android 多进程访问

将 MMKV 迁移到 Android 平台之后，需要支持多进程访问（ iOS 不支持多进程）

# SQLite vs MMKV

#### 一、数据存储模型

- **MMKV**：

  - **数据模型**：键值对存储，类似 `SharedPreferences`（Android）或 `NSUserDefaults`（iOS）。
  - **适用场景**：存储配置项、状态信息、小型数据集。
- **SQLite**：

  - **数据模型**：关系型数据库，使用表、行和列存储数据。
  - **适用场景**：存储结构化数据、大型数据集、需要复杂查询的数据。

#### 二、性能

- **MMKV**：

  - **优势**：使用内存映射文件技术，读写速度快，适合频繁读写小数据。
  - **缺点**：不适合存储和处理非常大的数据集或复杂的数据结构。
- **SQLite**：

  - **优势**：处理大数据集和复杂查询时表现出色，支持索引和优化查询执行计划。
  - **缺点**：频繁的单次读写操作性能可能不如MMKV。

#### 三、数据操作

- **MMKV**：

  - **操作简便**：提供简单的键值对存储接口，易于使用。
  - **线程安全**：内置线程安全机制，多线程环境下安全使用。
- **SQLite**：

  - **操作复杂**：需要编写SQL语句，适合需要复杂查询和数据操作的场景。
  - **事务支持**：支持事务，确保数据操作的原子性和一致性。

#### 四、数据持久化

- **MMKV**：

  - **数据持久化**：使用内存映射文件，数据持久化到文件系统，应用崩溃或重启后数据仍然存在。
  - **同步机制**：操作系统自动管理内存和磁盘之间的同步，保证数据持久性。
- **SQLite**：

  - **数据持久化**：数据存储在磁盘文件中，支持持久化存储。
  - **同步机制**：通过SQL事务和写入同步机制，确保数据一致性和持久性。

#### 五、适用场景

- **MMKV**：

  - 适用于存储应用配置、用户设置、状态信息等小型数据。
  - 适合需要快速读写小数据的场景，如应用启动配置加载。
- **SQLite**：

  - 适用于存储复杂结构化数据、需要复杂查询的数据。
  - 适合数据量较大、关系复杂的场景，如应用数据存储、离线数据缓存。
