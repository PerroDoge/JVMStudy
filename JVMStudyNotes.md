# JVM Study Notes

[toc]

## Class文件结构

magic_number: 魔数，只要开头是“CAFE BABY” 那就是class文件

minor_version: 次版本号

major_version: 主版本号

constant_pool_count: 常量池的长度，常量池实际长度是该长度减一，因为默认开头位置存null

constant_pool: 常量池

access_flags: 本类的修饰符和类的类型（接口or抽象类or枚举类...

this_class: 本类名字，指向常量池

super_class: 超类名字，指向常量池

interfaces_count: 实现了多少个接口

interfaces: 实现的接口

fields_count: 声明了多少个成员变量

fields: 成员变量

methods_count: 声明了多少个方法

methods: 方法

attributes_count: 具有多少个额外的属性

attributes: 额外的属性

## 类的加载到初始化过程

### Loading

#### ClassLoader

类加载器加载类的时候使用了**双亲委派**的机制

#### 双亲委派

​	类加载器在加载一个类时会检查自己缓存中有没有这个类，如果有说明已经加载过了，如果没有则会找自己的父加载器（父加载器指的不是自己继承自的那个加载器，而是自己内部属性"parent"指明的那个加载器），父加载器会检查自己的缓存...以此类推。到达最顶端的“bootstrapClassloader”如果还没有缓存命中，则会检查自己可不可以加载这个类，如果不可以则会交给自己的子加载器加载...如果直到最底端的加载器都不可以加载这个类，那么就会抛出ClassNotFoundException异常

#### 加载器父子关系

顶   	BootstrapClassLoader  负责加载java的核心类

 ↑        ExtClassLoader   负责加载jdk里ext包里的类

↑		AppClassLoader  负责加载classpath路径下的类

底       CustomClassLoader 自定义类加载器 自己写的，看你想让他加载哪儿的

#### 为什么要有双亲委派的机制

​	主要是为了安全考虑，例如不可能随便让一个加载器都可以加载java的核心类（String、Object......

#### 如何自定义类加载器

extends Classloader

重写findClass()方法

如果重写了loadClass()方法那么就可以打破双亲委派机制

### Linking

static int i = 8；	

类从加载到初始化中间的Linking阶段又分为三个小阶段

- Verification 检查class文件是否符合JVM标准
- Preparation 给静态变量赋默认值 （此时 i = 0；
- Resolution 将各种符号引用转变为直接引用

### Initializing

给静态变量赋初始值 （此时 i = 8；