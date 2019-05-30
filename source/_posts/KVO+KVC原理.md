---
title: KVO + KVC原理
categories: iOS面试
date: 2019-05-30 19:30:29
---



### KVO原理
KVO其实就是给某个属性添加一个监听者，然后这个属性的值发生变化会触发回调方法

例如：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    self.p = [[Person alloc]init];
    self.p.name = @"aaaa";
    
    NSKeyValueObservingOptions options = NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld;
    [self.p addObserver:self forKeyPath:@"name" options:options context:nil];
}

-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    self.p.name = @"bbbbb";
}

-(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context{
    NSLog(@"keyPath==%@, object==%@, change==%@, context==%@", keyPath, object, change, context);
    /*
     2019-05-17 22:17:10.758745+0800 KVO原理[73525:3459803] keyPath==name, object==<Person: 0x600003b1c890>, change=={
     kind = 1;
     new = bbbbb;
     old = aaaa;
     }, context==(null)
     */
}

-(void)dealloc{
    [self.p removeObserver:self forKeyPath:@"name"];
}
```

#### 原理解析：

<!--more-->


>1、当某个类的属性对象被观察时，系统在运行期动态的给该类派生出一个NSKVONotifyin_XXX的子类，这派生类中重写了基类中被观察的属性setter方法，在重写的方法内部实现通知机制(addMethed:会把消息转发到自定义IMP)

>2、把被观察相关资料存入到相关数组中。

>3、每个类都有一个isa指针当前类，当被观察时，系统将isa动态指向生成的派生类，从而给属性赋值的时候执行的是派生类的setter方法.

>4、重写的内部机制会监听到派生类调用的setter方法，可以根据setter方法转变成getter，获取到旧数据，然后执行willChangeValueForKey:和 didChangevlueForKey，期间调用sendsuper方法修改父类的属性值

>5、从关联数组获取到改对象，执行回调方法。


```
- (void)MM_addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath block:(Block)block{
    
    //动态创建实例
    Class superClass = object_getClass(self);
    //动态生成set方法t
    SEL setterSeletor = NSSelectorFromString(setterFormGetter(keyPath));
    //从实例里面找到set方法 类方法:class_getClassMethod
    
    Method setterMethod = class_getInstanceMethod(superClass, setterSeletor);
    //判断有没有当前方法
    if (!setterMethod) {
        @throw [NSException exceptionWithName:NSInvalidArgumentException reason:[NSString stringWithFormat:@"%@ 没有 %@setter方法",self,keyPath] userInfo:nil];
    }
    NSString *superClassName = NSStringFromClass(superClass);
    Class newClass;
    if (![superClassName hasPrefix:@"NSKVONotifying_"]) {
        newClass = [self createClassFromSuperClass:superClassName];
        object_setClass(self, newClass);
    }
    
    //
    const char *types = method_getTypeEncoding(setterMethod);
    class_addMethod(newClass, setterSeletor, (IMP)MMKVO_Setter, types);
    
    //生成对象
    MM_Info *info = [[MM_Info alloc]initWithObserver:observer keyPath:keyPath block:block];
    //关联数组 存储对象
    NSMutableArray *myArray = objc_getAssociatedObject(self, &AssociatedKey);
    if (!myArray) {
        myArray = [[NSMutableArray alloc]initWithCapacity:1];
        [myArray addObject:info];
        objc_setAssociatedObject(self, &AssociatedKey, myArray, OBJC_ASSOCIATION_COPY_NONATOMIC);
    }
    [myArray addObject:info];
    
}
```


```
void MMKVO_Setter(id self, SEL _cmd ,id newValue){
    //消息转发
    NSString *setterName = NSStringFromSelector(_cmd);
    NSString *getterName = getterFromSetter(setterName);
    if (!getterName) {
        @throw [NSException exceptionWithName:NSInvalidArgumentException reason:[NSString stringWithFormat:@"%@ 没有 %@getter方法",self,getterName] userInfo:nil];
    }
    
    id oldValue = [self valueForKeyPath:getterName];
    
    [self willChangeValueForKey:setterName];
    void(*mm_kvomsgSend)(void *,SEL,id) = (void*)objc_msgSendSuper;
    struct objc_super mm_objcSuper = {
        .receiver = self,
        .super_class = class_getSuperclass(object_getClass(self)),
    };
    mm_kvomsgSend(&mm_objcSuper,_cmd,newValue);
    [self didChangeValueForKey:setterName];
    
    NSMutableArray *myArray = objc_getAssociatedObject(self, &AssociatedKey);
    
    for (MM_Info *info in myArray) {
        if ([info.keyPath isEqualToString:getterName]) {
            info.block(info.observer, info.keyPath, oldValue, newValue);
        }
    }
    
}
```
#### 通过在终端验证
> po p->isa
会得到NSKVONotifyin_Person的类对象
> po (IMP)0x101c7c640
会得到(Foundation`_NSSetObjectValueAndNotify)


### KVC详解   
KVC(Key-value coding)键值编码，通过key直接访问对象属性，可以给属性赋值，可以动态的访问和修改对象的属性。

KVC的定义都是对NSObject的扩展来实现的,Objective-C中有个显式的NSKeyValueCoding类别名，所以对于所有继承了NSObject的类型，都能使用KVC.

主要方法：

```
- (nullable id)valueForKey:(NSString *)key;                          //直接通过Key来取值

- (void)setValue:(nullable id)value forKey:(NSString *)key;          //通过Key来设值

- (nullable id)valueForKeyPath:(NSString *)keyPath;                  //通过KeyPath来取值

- (void)setValue:(nullable id)value forKeyPath:(NSString *)keyPath;  //通过KeyPath来设值

```

#### 原理

1、赋值
>  -(void)setValue:(nullable id)value forKey:(NSString *)key;          //通过Key来设值
过程：
1. 有限调用set属性，通过setter方法完成设置
2. 如果没有找到setter方法，搜索该类有没有_的成员变量，对成员变量赋值
3. 如果没有_成员变量，会搜索_is的成员变量
4. 如果以上都不存在，会抛出异常
总结: 如果没有找到Set<Key>方法的话，会按照_key，_iskey，key，iskey的顺序搜索成员并进行赋值操作。

注意:
重写accessInstanceVariablesDirectly方法KVC的时候只会查找setter和getter方法，无setter方法会调用setValue: forUndefinedKey方法。

2、取值
- (id)valueForKey:(NSString *)key;//通过Key来取值
过程和赋值一致

3.KVC处理集合
简单集合运算符共有@avg， @count ， @max ， @min ，@sum5种
例：

```
Book *book1 = [Book new];
        book1.name = @"The Great Gastby";
        book1.price = 10;
        Book *book2 = [Book new];
        book2.name = @"Time History";
        book2.price = 20;
        Book *book3 = [Book new];
        book3.name = @"Wrong Hole";
        book3.price = 30;
        
        Book *book4 = [Book new];
        book4.name = @"Wrong Hole";
        book4.price = 40;
        
        NSArray* arrBooks = @[book1,book2,book3,book4];
        NSNumber* sum = [arrBooks valueForKeyPath:@"@sum.price"];//price的和
        NSLog(@"sum:%f",sum.floatValue);
        NSNumber* avg = [arrBooks valueForKeyPath:@"@avg.price"];//平均值
        NSLog(@"avg:%f",avg.floatValue);
        NSNumber* count = [arrBooks valueForKeyPath:@"@count"];//数量
        NSLog(@"count:%f",count.floatValue);
        NSNumber* min = [arrBooks valueForKeyPath:@"@min.price"];//最小值
        NSLog(@"min:%f",min.floatValue);
        NSNumber* max = [arrBooks valueForKeyPath:@"@max.price"];//最大值
        NSLog(@"max:%f",max.floatValue);

```
@distinctUnionOfObjects  //返回操作对象内部的不同对象，返回值为数组
@unionOfObjects //返回操作对象内部的所有对象，返回值为数组

4.KVC处理字典
可以实现字典与对象的转换
>- (NSDictionary<NSString *, id> *)dictionaryWithValuesForKeys:(NSArray<NSString *> *)keys;
>- (void)setValuesForKeysWithDictionary:(NSDictionary<NSString *, id> *)keyedValues;

```
        Address* add = [Address new];
        add.country = @"China";
        add.province = @"Guang Dong";
        add.city = @"Shen Zhen";
        add.district = @"Nan Shan";
        NSArray* arr = @[@"country",@"province",@"city",@"district"];
        NSDictionary* dict = [add dictionaryWithValuesForKeys:arr]; //把对应key所有的属性全部取出来
        NSLog(@"%@",dict);
        
        //字典转模型
        NSDictionary* modifyDict = @{@"country":@"USA",@"province":@"california",@"city":@"Los angle"};
        [add setValuesForKeysWithDictionary:modifyDict];            //用key Value来修改Model的属性
        NSLog(@"country:%@  province:%@ city:%@",add.country,add.province,add.city);

```



