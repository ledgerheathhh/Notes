# 高级控件

## UIScrollView

> 由于移动设备的屏幕大小有限，所以不能像PC一样显示很多内容，因此，必须通过手指滚动来查看更多的内容。UIScrollView是一个能够在上下左右四个方向滚动的控件，我们常见的UITableView，UICollectionView也是继承自UIScrollView。

### UIScrollView简介

UIScrollView也是UIView的子类，因此也具有UIView的所有属性和方法，例如，可以设置UIScrollView的背景颜色，并且可以在UIScrollView对象上添加子视图。

UIScrollView用于显示超出屏幕大小的内容，一般需要配合其他控件来使用，如添加一个UIImageView子控件，可以用来显示更大的图片。UITableView、UICollectionView以及UITextView这些可以滑动显示更多内容的控件都是UIScrollView的子类。例如，在实际应用开发中，常见的图片轮播期就是使用UIScrollView来实现的。

### UIScrollView的常用属性

UIScrollView继承自UIView, 所以UIView拥有的属性UIScrollView都有,此外它还有一些自己的特定属性。

UIScrollView在使用过程中有如下3个核心属性：

* **contentSize** ：表示UIScrollView内容的尺寸（即可滚动区域），一般会大于屏幕大小；

```objectivec
@property(nonatomic) CGSize contentSize;  // 默认大小为0
```

* **contentOffset** ： 当前屏幕显示区域的原点（即左上角原点），在UIScrollView的位置；

```objectivec
@property(nonatomic) CGPoint contentOffset;  // 默认从原点开始
```

* **contentInset** ： 可以在UIScrollView内容的四周增加额外的滚动区域（设置的值为：上、左、下、右）。

```objectivec
@property(nonatomic) UIEdgeInsets contentInset;  //默认为0
```

除此之外，UIScrollView还有如下几个常用的属性。

* **bounces** ：当UIScrollView滚动到边界时，再继续滚动会有个反弹的效果（通常设置为YES）。注意：如果不设置contentSize，bounces的效果是显现不出来的，除非将alwaysBounceVertical和alwaysBounceHorizontal属性设置为YES；
* **showsHorizontalScrollIndicator** ： 显示水平指示器 （YES为显示）；
* **showsVerticalScrollIndicator** ：显示垂直指示器；
* **pagingEnabled** ：分页效果（是否整页翻动）；
* **scrollEnabled** ：UIScrollView是否可以滚动；

```objectivec
@property(nonatomic) BOOL bounces; //默认为YES
@property(nonatomic) BOOL showsHorizontalScrollIndicator; //默认为YES
@property(nonatomic) BOOL showsVerticalScrollIndicator;//默认为YES
@property(nonatomic,getter=isPagingEnabled) BOOL pagingEnabled ; //默认为NO
@property(nonatomic,getter=isScrollEnabled) BOOL scrollEnabled; 默认为YES
```

## UITableView

> 表视图UITableView是iOS开发中最重要的控件（不是之一），99.99%的App都会用到表视图。它之所以使用广泛，也是因为其强大的定制能力，当然学习起来也需要花费一些时间和精力。表视图主要用于呈现一个滚动的选择列表，在使用过程中主要有三个步骤：初始化、数据源的设置以及委托代理方法的实现。

在实际开发中，我们会看到许多地方都用到了UITableView，如苹果系统中的设置、QQ、微信等。在UITableView中数据只有行的概念，并没有列的概念，因为在手机中显示多列是不利于操作的。

#### UITableView样式

UITableView有两种样式：平铺(UITableViewStylePlain)和分组(UITableViewStyleGrouped)。这两者本质区别不大，在没有特别设置的情况下，UITableViewStyleGrouped样式会默认留出header和footer的位置

#### UITableView的属性

UITableView类中定义了非常多的属性和方法，也正因为如此，UITableView的功能非常强大。

* 获取表视图的样式

```objectivec
@property (nonatomic, readonly) UITableViewStyle style;
```

* UITableView的数据源对象与代理对象，需要遵守UITableViewDataSource协议与UITableViewDelegate协议

```objectivec
@property (nonatomic, weak, nullable) id <UITableViewDataSource> dataSource;
@property (nonatomic, weak, nullable) id <UITableViewDelegate> delegate;
```

* 单元格的行高

```objectivec
@property (nonatomic) CGFloat rowHeight;
```

* 段section的header高度与footer高度

```objectivec
@property (nonatomic) CGFloat sectionHeaderHeight;
@property (nonatomic) CGFloat sectionFooterHeight;
```

* 整体表视图的顶部视图与底部视图

```objectivec
@property (nonatomic, strong, nullable) UIView *tableHeaderView;
@property (nonatomic, strong, nullable) UIView *tableFooterView;
```

* 表索引的样式设置

```objectivec
//索引表的字母的颜色
@property (nonatomic, strong, nullable) UIColor *sectionIndexColor;
//索引栏的背景颜色
@property (nonatomic, strong, nullable) UIColor *sectionIndexBackgroundColor; 
```

* 分割线的样式设置

```objectivec
//设置分割线样式
@property (nonatomic) UITableViewCellSeparatorStyle separatorStyle;
//定义分割线颜色
@property (nonatomic, strong, nullable) UIColor *separatorColor;
```

#### 数据源

> UITableView类的对象，除了可以设置代理属性之外，还需要设置其数据源属性，因为UITableView中展示的数据是由其数据源对象提供的。在设置完UITableView的dataSource属性后，通过实现UITableViewDataSource协议中定义的数据源方法来为UITableView对象提供需要展示的数据。

在UITableView中，引入了NSIndexPath类来定位每一个单元格。在NSIndexPath类中，包含两个关键属性:section与row，分别对应每个单元格在UITableView中段号Section与行号Row。根据索引路径indexPath即可定位到唯一的一个单元格。

```objectivec
@interface NSIndexPath (UITableView)
@property (nonatomic, readonly) NSInteger section;
@property (nonatomic, readonly) NSInteger row;
@end
```

#### UITableViewDataSource中的常用方法

UITableViewDataSource协议中，有3个方法是最常使用的，一般情况下在初始化UITableView时都需要实现。这3个方法分别用于返回表视图的段数、每段的行数以及每个单元格的样式与展示内容。

* （必选）返回每个段（seciton）中有多少个单元格；

```objectivec
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
```

* （必选）返回每个单元格的具体样式和显示内容；

```objectivec
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
```

* （可选）返回整个表视图有多少个段（section），如果不实现该方法，默认为1个段。

```objectivec
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;  
```

##### UITableViewDataSource中的其它方法

* 设置某section上header的标题，当tableview的style是平铺时有悬浮效果，为分组时是没有悬浮效果的。

```objectivec
- (nullable NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section;
```

* 设置每个段section上的footer的标题；

```objectivec
- (nullable NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section;
```

* 设置表视图的索引；

```objectivec
- (nullable NSArray<NSString *> *)sectionIndexTitlesForTableView:(UITableView *)tableView;
```

* 点击右侧索引栏时调用；

```objectivec
- (NSInteger)tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)index;
```

* 对单元格进行编辑(删除、添加)时调用；

```objectivec
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath;
```

* 设置单元格是否可移动；

```objectivec
- (BOOL)tableView:(UITableView *)tableView canMoveRowAtIndexPath:(NSIndexPath *)indexPath;
```

* 对单元格的移动。

```objectivec
- (void)tableView:(UITableView *)tableView moveRowAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath *)destinationIndexPath;
```

#### 单元格

单元格（UITableViewCell）是UITableView的组成单元，每一个单元格都是一个UITableViewCell对象，默认情况下，一个单元格具有一个icon图片、一个title、一个detail title，以及一个accessory。在实际开发中，我们也可以对cell进行定制，完全使用自定义cell，并且这种情况更加普遍。

系统自带的单元格（即UITableViewCell类型的）主要有如下4个常用属性：

* **imageView** ：显示在左边的一张图片logo
* **textLabel** ：主要文字，黑色字体显示，textLabel和detailTextLabel会在上图中的Text处显示
* **detailTextLabel** ：详细说明文字，字体较小
* **accessaryView** ：附件视图，可以使用自定义View，也可以使用系统自带的一些标准按钮

**系统自带单元格的样式**

默认情况下，系统自带了4种样式的单元格，区别在于显示的属性内容以及位置不同。

* **UITableViewCellStyleDefault** ：默认样式
* **UITableViewCellStyleValue1**
* **UITableViewCellStyleValue2**
* **UITableViewCellStyleSubtitle** ：带子标题的样式

Accessory View是显示在单元格最右边的图标，主要可以用来显示一些提示信息，默认情况下是不显示的。当需要显示Accessory View时，可以有两种方式来设置。

* 通过cell的**accessoryType**属性设置。此时可以使用系统提供的样式，常见的样式如下所示:

```objectivec
@property (nonatomic) UITableViewCellAccessoryType accessoryType; 
```

* 通过cell的**accessoryView**属性来自定义附件的样式

```objectivec
@property (nonatomic, strong, nullable) UIView *accessoryView; 
```

### UITableView 和 UICollectionView 的重用机制

#### 1. 目的

- **提高性能**：减少内存使用和提高滚动性能。
- **减少开销**：避免为每个数据项都创建新的单元格实例。

#### 2. 关键概念

- **重用标识符（Reuse Identifier）**：用于标识单元格类型的唯一字符串。
- **重用池（Reuse Pool）**：系统维护的存储已离开屏幕的单元格实例的池子。

#### 3. 实现

##### 在 UITableView 中

```objectivec
-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
    //初始化cell
    static NSString *cellID = @"cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellID];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:cellID];
    }
    //获取随机数据
    NSString *text = [NSString stringWithFormat:@"%d", arc4random_uniform(1000000)];
    //设值
    cell.textLabel.text = text;
    return cell;
}
```

##### 在 UICollectionView 中

```objectivec
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
    UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"cellID" forIndexPath:indexPath];
    UILabel *label = (UILabel *)[cell viewWithTag:100];
    if (label == nil) {
        label = [[UILabel alloc] initWithFrame:cell.contentView.bounds];
        label.tag = 100;
        [cell.contentView addSubview:label];
    }
    label.text = [NSString stringWithFormat:@"Item %ld", (long)indexPath.item];
    return cell;
}
```

#### 4. 内部机制

1. **重用池的维护**

   - 当单元格离开屏幕时，系统将其放入重用池。
   - 当需要显示新的单元格时，首先尝试从重用池中获取可用单元格，如果没有可用单元格，则创建一个新的实例。
2. **实例数量管理**

   - 实例数量与屏幕上可见单元格数量以及合理的缓冲量相关。
   - 当用户滚动时，离开屏幕的单元格实例被放入重用池，新进入屏幕的单元格从重用池中取出并重新配置。

#### 5. 使用注意事项

- **确保正确配置单元格内容**：每次重用单元格时，必须根据当前的 `indexPath` 重新配置内容。
- **区分不同类型的单元格**：对于不同类型的单元格，使用不同的重用标识符。

#### 6. 相关类

- `NSIndexPath`：用于表示单元格的位置，包括 `section` 和 `row`（用于 UITableView）或 `section` 和 `item`（用于 UICollectionView）。

**NSIndexPath 示例**：

```objective-c
NSIndexPath *indexPath = [NSIndexPath indexPathForRow:5 inSection:3]; // UITableView
NSIndexPath *indexPath = [NSIndexPath indexPathForItem:4 inSection:2]; // UICollectionView
```

### 总结

UITableView 和 UICollectionView 的重用机制通过维护一个重用池来高效地管理单元格实例。开发者需要使用重用标识符注册和获取单元格，并在重用时重新配置单元格内容，从而实现高性能和低内存使用的表视图和集合视图。
