# coco2d-iphone(1)
首先我们先看看它有哪些属性：（它继承自NSObject，因此它与Foundation框架的兼容性完全不用担心= =）

float rotation_

节点的旋转角度，如果是图片的话，会看到旋转效果，所以基本上是为屏幕输出服务的。

float scaleX_, scaleY_

节点的缩放比例，X为横向，Y为纵向，当值为1的时候表示原比例，0.5表示缩小一半，2表示放大1倍，如果是图片的话就会看到效果，也是为屏幕输出服务的。

CGPoint position_

节点的坐标，该属性是一个结构体，position_.x是它的横坐标，position_.y是纵坐标，基本上也是要显示到屏幕上才有意义。

float skewX_, skewY_

节点的扭曲角度，类似于Windows画图板中的扭曲功能，X是横向扭曲，Y是纵向扭曲，单位是度数，也是屏显用的。

CGPoint anchorPointInPoints_

节点的锚点坐标，所谓锚点就是当旋转、缩放或是用其它方式操作节点时的参考的基准点，例如图片的锚点在正中心，当放大它的时候，图片就会向四周扩散，当旋转一个锚点在左上角的图片时，图片就会以左上角为轴旋转。

CGPoint anchorPoint_

节点的锚点在节点位置的比例，比如说一个节点的坐标为(0, 0)，宽高各为100，那么当它的锚点坐标为(50, 50)的时候，anchorPoint_.x = 0.5，anchorPoint_.y = 0.5。

CGSize contentSize_

节点的宽高，它也是一个结构体，contentSize_.width表示宽，contentSize_.height表示高，它和position_配合可以用来检测碰撞，但需要注意，contentSize_不会因为节点的旋转和缩放而改变大小。

CCCamera *camera_

节点的镜头，cocos2d虽然是2D游戏引擎，但实际上它是通过3D方式绘制2D效果的，因此有镜头存在就不奇怪了。CCCamera也是cocos2d的一个类，它默认情况下是正对着节点的，距离也是刚好使节点能够以1:1的比例显示到屏幕上，当我们手动修改它的参数后，节点就会呈现出歪曲或缩放等效果。

NSInteger zOrder_

节点的Z轴，当有多个节点在屏幕显示时，引擎会根据它们Z轴的大小决定绘制顺序，Z轴大的会盖住Z轴小的。

CCArray *children_

重头戏来了，前面说过节点可以有N个子节点，children_就是存储这些子节点地址的变量，节点就是通过它们找到自己的子节点的。children_的类型是CCArray，这是cocos2d自己封装的一个数组类，每个元素存储一个子节点地址，它引入了tag机制，当节点有多个子节点时，可以根据它们的tag找到自己想要的那一个。

CCNode *parent_

另一个重点——节点的父节点。因为树中的每个节点都继承自CCNode，所以用CCNode类型定义父节点是最万无一失的，节点可以通过它找到自己的父节点，因为父节点最多只有一个，所以不需要用数组保存。

NSInteger tag_

节点的标签，主要是帮助其父节点找到自己，因为每个节点的子节点并不一定是唯一的，当需要遍历时可以用该参数做区分，所以兄弟节点的标签尽量不要一样。

Bool isRunning_

表示节点是否在更新逻辑，之前说过，未加入树的节点是不会显示到屏幕上的，其实它的逻辑运算也是一样，就是说没有父节点的节点不会显示，也不会处理自己的逻辑，它的一切活动都会停止。那么当节点被加入到树中的时候，isRunning_会被自动置为Yes，此时它会开始执行自己的运算，当节点离开树时，isRunning_会被置为No，如果想在它在树中的时候暂停运算，就需要将它的isRunning_手动置为No。

Bool visible_

和isRunning_对应，用来表示节点是否在绘制，当visible_为No时，节点即使在树中也不会绘制到屏幕上，就相当于隐藏了起来。

以上就是CCNode比较重要的属性，不过我们在开发中直接操作它们的频率并不高，而是通过类中声明的相关方法去改变它们，下面我就来说说CCNode中的方法。

+(id) node

实例化一个CCnode对象，实际上就是封装了alloc、init和autorelease方法。

-(id) init

初始化，将一些数值型的参数归0，缩放比例设为1，指针型的参数设为nil。

-(CCArray*) children

返回对象的children_，即获取全部的子节点。

-(Bool) visible

返回visible_参数，用于检测该节点当前是否被绘制。

-(void) setVisible: (Bool)visible

设置节点的绘制状态，Yes为可见，No为不可见

-(CCNode*) parent

返回节点的parent_，即获取父节点

-(void) setParent: (CCNode*)parent

设置父节点，但实际应用中不要这样修改，因为极易出bug，正确的修改方法会在后面介绍。

-(NSInteger) tag

返回对象的tag_，即获取节点的标签。

-(NSInteger) zOrder

返回对象的zOrder_,即获取Z轴。

-(Bool) isRunning

返回对象的isRunning_，即检测该节点当前是否更新逻辑。

-(float) rotation

返回对象的rotation_，即获取节点的旋转角度。

-(void) setRotation: (float)rotation

设置对象的rotation_值，即旋转节点。

-(CGPoint) position

返回对象的position_，即获取节点的坐标。

-(void) setPosition: (CGPoint)position

设置对象的position_，即更改节点的坐标。

-(CGPoint) anchorPoint

返回对象的anchorPoint_，即获取节点的锚点。

-(void) setAnchorPoint: (CGPoint)anchorPoint

设置对象的anchorPoint_，即修改节点的描点。

-(CGSize) contentSize

返回对象的contentSize_，即获取节点的宽高。

-(void) setContentSize: (CGSize)contentSize

设置对象的contentSize_，即改变节点的宽高。

-(float) skewX

返回对象的skewX_，即获取节点的横向扭曲角度。

-(void) setSkewX: (float)skewX

设置对象的skewX_，即设置节点的横向扭曲角度。

-(float) skewY

返回对象的skewY_，即获取节点的纵向扭曲角度。

-(void) setSkewY: (float)skewY

设置对象的skewY_，即设置节点的纵向扭曲角度。

-(float) scaleX

返回对象的scaleX_，即获取节点的横向缩放比例。

-(void) setScaleX: (float)scaleX

设置对象的scaleX_，即横向缩放节点。

-(float) scaleY

返回对象的scaleY_，即获取节点的纵向缩放比例。

-(void) setScaleY: (float)scaleY

设置对象的scaleY_，即纵向缩放节点。

-(CGRect) boundingBox

返回对象的碰撞框（由坐标和宽高组合而成），这个参数会随着节点的缩放或旋转等操作而改变。

-(CCCamera*) camera

返回对象的camera_，即获取节点的镜头。

-(CCNode*) getChildByTag: (NSInteger) aTag

根据tag_返回children_数组中的某个元素，即根据标签获取节点的某个子节点。

-(void) addChild: (CCNode*)child z: (NSInteger)z tag: (NSInteger)aTag

向children_数组中添加一个CCNode对象，即给节点添加一个子节点，z是子节点的Z轴，aTag是子节点的标签，此方法会将该子节点的parent_指针指向自己，因此这才是设置父节点的正解，比setParent要安全的多。

-(void) addChild: (CCNode*)child z: (NSInteger)z

-(void) addChild: (CCNode*)child

缺省了tag或z的添加子节点方法，缺省值默认为0。

-(void) removeFromParentAndCleanup: (Bool)cleanup

将节点从它的父节点的children_数组中删除，即脱离树，cleanup表示是否将其从内存中清除。

-(void) removeChild: (CCNode*)child cleanup: (Bool)cleanup

将某一节点从children_数组中删除，即删除某一子节点。

-(void) removeChildByTag: (NSInteger)aTag cleanup: (Bool)cleanup

根据tag_删除children_数组中的某个元素，即根据标签删除节点的某个子节点。

-(void) removeAllChildrenWithCleanup: (Bool)cleanup

清空对象的children_数组，即删除所有子节点。

-(void) _setZOrder: (NSInteger)z

设置对象的zOrder_，即改变节点的Z轴。

-(void) reorderChild: (CCNode*)child z: (NSInteger)z

将某节点从子节点队列中取出，并重新重新插入队列，目的是改变其Z轴。

-(void) visit

绘制函数，用来将节点内容（多为图片或文字）显示到屏幕。

-(void) onEnter

相当于一个激活过程，它会在节点被添加到树中的时候执行，使其可以更新自己的逻辑。

-(void) onExit

取消激活，作用是暂停某节点的逻辑更新。

-(CCAction*)runAction: (CCAction*)action

一个会经常被用到的类，作用是执行一种行为，行为模式由参数action决定，CCAction就是上一章提到的行为类，runAction就是联系实体类和行为类的桥梁。行为执行完毕后，action会自动释放（用了autorelease），所以使用时不用担心内存问题。但需要注意，当isRunning_为No时，节点执行的行为会暂停，就是说onExit有暂停节点一切行为的功能。

-(void) stopAllActions

停止节点正在执行的所有行为，注意这里是停止，不是暂停，函数执行后节点中的所有行为类实例都将从内存中清除。

-(void) stopAction: (CCAction*)action

停止节点的某一个行为，如果没有执行该行为则什么都不做。

-(void) stopActionByTag: (NSInteger)aTag

根据标签停止节点的某一个行为。

-(CCAction*) getActionByTag: (NSInteger)aTag

根据标签获取某个正在执行的行为。

-(void) schedule: (SEL)selector interval: (ccTime)interval

Schedule是cocos2d中的一个很重要的机制，在作者看来它的实用性仅次于行为类，它的功能和CCAction有相似的地方，都是执行一种行为；不同之处在于，schedule是通过调用方法来执行逻辑的，而且他它内部还有个计时器，可以每隔一定的时间调用一次，反复循环，直到手动将其停止或者节点被删除（暂停）。这里的参数selector就是执行的方法，interval是间隔时间（秒），ccTime其实就是一个浮点数。

-(void) unschedule: (SEL)selector: (SEL)selector

停止一个schedule任务，注意这里是停止不是暂停。

-(void) unscheduleAllSeletors

停止所有schedule任务

-(void) resumeSchedulerAndActions

恢复运行节点的行为和schedule。

-(void) pauseSchedulerAndActions

暂停节点的行为和schedule。

