# UIKit框架简介

> UIKitk框架提供了一整套完整的API，用于建立和管理iOS应用程序的用户界面( UI )接口、应用程序对象、事件控制、绘图模型、窗口、视图和用于控制触摸屏等的接口，是一个用于控制界面操作的Class类集合，iOS开发过程中，70%~80%的工作是在围绕UIKit框架中定义的类进行的。

**UIKit中的所有类都是继承自NSObject类的，其中所有的类的命名都是以UI开头，说明这些类都是与界面操作相关的类。UIKit框架中定义了几十个子类。**

![1715844893549](../image/UIKit/1715844893549.png)

* UIResponder类定义了一个接口，响应和处理事件的对象，所有继承自UIResponder的子类都可以响应用户交互，例如，点击、滑动等等。UIResponder类的实例有时被称为作为响应者对象。
* UIView类的对象负责定义在屏幕上一块矩形区域的显示样式，以及在这块矩形区域内发生的用户交互动作。UIView类具有若干子类，这些子类除了继承了UIView类的功能外，在样式以及用途方面进行了功能扩展，例如，UILabel可以显示文字标签、UIImageView可以用来显示图片、UIButton可以用来定义按钮的样式以及行为。
* UIViewController类用于管理iOS应用程序中的数据以及视图对象，在MVC设计模式中，控制器类是模型与视图之间交互通信的桥梁和纽带。另外，像UINavigationController和UITabBarController的这样的子类，可以用于提供管理复杂的视图控制器层级结构以及视图的其他行为。
* UIGestureRecognizer是具体手势识别类的抽象基类，提供了手势类所具有的通用方法和属性。在UIKit框架中，提供了点击(UITapGestureRecognizer)、捏合(UIPinchGestureRecognizer)、旋转(UIRotationGestureRecognizer)、滑动(UISwipeGestureRecognizer)、拖动(UIPanGestureRecognizer)、长按(UILongPressGestureRecognizer)等几种手势，在开发中可以灵活使用。

# UIView

## UIView简介

> UIView是所有界面UI类控件的父类。UIView类的对象负责屏幕上一个矩形区域的显示和行为动作。我们熟知的UIButton，UIImageview等等都继承自UIView，因此，UIView所具备的属性和方法，其子类也都同样具备。

UIView类（视图类）负责管理屏幕上的一块矩形区域，包括这个区域内的显示样式，比如背景颜色，大小，以及行为动作，例如监测用户点击等触碰事件。

视图还可以用于管理一个或者多个子视图。用户看到的某个样式，有可能是多个视图叠加后的显示效果。视图的这种布局方式，也称为视图层次，一个父视图可以包含任意多个子视图。同时，父视图的属性有时也会影响到子视图的样式以及用户交互行为。

总体来讲，视图类的主要作用有如下3个方面：

* 样式显示与动画：负责自身矩形区域内样式的显示，以及某些属性（大小、位置、角度）变化时的动画过渡效果；
* 布局与子视图管理：管理子视图
* 事件处理：接收并响应用户的触摸事件

在iOS开发中，UIView与UIViewController紧密协作，UIViewController负责UIView的加载与卸载。

## UIView的父类与子类

UIView继承自UIResponder，因此UIView可以响应用户交互。另外，我们熟知的一些常用控件都继承自UIView。需要特别说明的是，窗口(UIWindow)也是继承自UIView，窗口可以认为是一个特殊的View。

## 基本样式：背景颜色、透明度以及是否隐藏

* 背景颜色属性BackgroundColor属性是UIView类中最常使用的属性之一，由于UIView是一个矩形区域，所以在实际的开发过程中，常常通过设置视图的背景颜色来检查视图的大小以及位置。
* 透明度alpha属性可以修改视图的透明度，实现一些虚化的效果。在一些游戏App中，游戏的按钮经常是虚化的效果。需要注意的是对于UIView以及其子类，当alpha的值小于等于0.01时，就不能够再响应用户交互了，例如：UIButton就不能够点击了。
* 是否隐藏hidden能够控制视图的显示与隐藏。

```objectivec
@property(nullable, nonatomic,copy)	UIColor	*backgroundColor;
@property(nonatomic)	CGFloat	alpha;
@property(nonatomic,getter=isHidden)	BOOL	 hidden;
```

## 位置与大小：Frame/Bounds/Center

```objectivec
@property(nonatomic) CGRect            frame;  
@property(nonatomic) CGRect            bounds; 
@property(nonatomic) CGPoint           center; 
```

Frame、Bounds以及Center是用来设置视图对象位置以及大小的属性，在对任何视图类对象进行初始化之后，紧接着就要去设置视图对象的Frame属性。：

* 绝对坐标系：屏幕的左上角是坐标原点（0，0），横向为X轴，纵向为Y轴；向左移动X值减小，向右移动X值增加；向下移动Y值增加，向上移动Y值减小；
* 每个视图的起始位置和大小由frame属性来确定，frame是一个CGRect类型的属性，CGRect是一个结构体，其中包含有两个变量origin和size，其中origin是一个CGPoint类型的结构体变量，指的是视图左上角的那个点的位置，决定了视图的位置；size是CGSize类型的结构体变量，定义了矩形的长度和宽度，从而决定了视图的大小；
* Frame：视图在其**父视图坐标系**中的位置和大小，建议大家在控件初始化之后，紧接着就去设置Frame，设置完成后，假如涉及到修改控件的位置、大小等，就不要再去修改Frame了；
* Bounds：视图在其**自身的坐标系**中的位置和大小。Bounds属性中，视图的bounds.origin始终是（0，0），因此bounds属性最核心的作用是设置视图的大小，即bounds.size，当需要去修改视图大小的时候，可以修改bounds.size；
* Center：视图中心点在**父视图坐标系**中的坐标，当需要修改视图对象的位置时，可以修改Center属性。

> 在开发过程中，经常需要对视图对象的样式进行修改，常见的修改操作有位移、放大/缩小、旋转等。当涉及到视图位移的时候，可以修改视图的center以及frame属性；当涉及到视图的缩放以及旋转操作时，推荐修改视图的transform属性。

## 视图的层次关系

> 在视图中可以添加子视图，通过多个视图的叠加显示，最终展示给用户。对于视图层次的管理，既可以通过InterfaceBuilder进行图形化的查看和管理，也可以通过纯代码的方式进行查看和管理，不过从实际开发经验来看，最好二选一，不要同时使用InterfaceBulider以及代码来管理子视图。

* 常用属性

```objectivec
@property(nullable, nonatomic,readonly) UIView       *superview;//父视图
@property(nonatomic,readonly,copy) NSArray *subviews;//所有的子视图
@property(nullable, nonatomic,readonly) UIWindow     *window;//视图所在的Window 
```

* 常用方法

```objectivec
- (void)addSubview:(UIView *)view;//添加子视图
- (void)bringSubviewToFront:(UIView *)view;//把某个子视图移到最前显示
- (void)sendSubviewToBack:(UIView *)view;//把某个子视图移动到最后显示
- (void)removeFromSuperview;//从父视图中移除
```

可以通过调用UIView的addSubview:以及removeFromSuperView方法，来添加/删除子视图。添加的子视图，会被插入到subviews数组的最后，最后添加的视图显示在最前端。可以通过调用bringSubViewToFront:以及sendSubViewToBack:来对子视图的显示位置进行调整。当存在较多的子视图时，UIView类也提供了相对复杂的方法，来实现层级关系的精确调整。
