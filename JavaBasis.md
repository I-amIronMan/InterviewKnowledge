#### 接口和抽象类的区别和共同点
共同点：  
+ 都不能被实例化。
+ 都可以包含抽象方法。
+ 都可以有默认实现的方法（Java 8 可以用 default 关键在接口中定义默认方法）。

区别：  
+ 接口主要用于对类的行为进行约束，你实现了某个接口就具有了对应的行为。抽象类主要用于代码复用，强调的是所属关系（比如说我们抽象了一个发送短信的抽象类，）。
+ 一个类只能继承一个类，但是可以实现多个接口。
+ 接口中的成员变量只能是 public static final 类型的，不能被修改且必须有初始值，而抽象类的成员变量默认 default，可在子类中被重新定义，也可被重新赋值。
#### 深拷贝和浅拷贝
+ 浅拷贝：浅拷贝会在堆上创建一个新的对象（区别于引用拷贝的一点），不过，如果原对象内部的属性是引用类型的话，浅拷贝会直接复制内部对象的引用地址，也就是说拷贝对象和原对象共用同一个内部对象。
+ 深拷贝 ：深拷贝会完全复制整个对象，包括这个对象所包含的内部对象。
####Object类
##### ==和equais()
+ 对于基本数据类型来说，== 比较的是值。
+ 对于引用数据类型来说，== 比较的是对象的内存地址。  

equals() 不能用于判断基本数据类型的变量，只能用来判断两个对象是否相等。  
equals() 方法存在两种使用情况：
+ 类没有重写 equals()方法 ：通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象，使用的默认是 Object类equals()方法。
+ 类重写了 equals()方法 ：一般我们都重写 equals()方法来比较两个对象中的属性是否相等；若它们的属性相等，则返回 true(即，认为这两个对象相等)。
##### hashCode()
hashCode() 的作用是获取哈希码（int 整数），也称为散列码。这个哈希码的作用是确定该对象在哈希表中的索引位置。
+ 如果两个对象的hashCode 值相等，那这两个对象不一定相等（哈希碰撞）。
+ 如果两个对象的hashCode 值相等并且equals()方法也返回 true，我们才认为这两个对象相等。
+ 如果两个对象的hashCode 值不相等，我们就可以直接认为这两个对象不相等。  

#### java并发
##### 线程
线程私有资源：程序计数器、虚拟机栈和本地方法栈。
线程共享资源：堆和方法区 (JDK1.8 之后的元空间)。
##### 程序计数器为什么是私有的？
程序计数器主要有下面两个作用：
+ 字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制，如：顺序执行、选择、循环、异常处理。
+ 在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时候能够知道该线程上次运行到哪儿了。

程序计数器私有主要是为了线程切换后能恢复到正确的执行位置。
##### sleep() 方法和 wait() 方法区别和共同点?
+ 两者最主要的区别在于：sleep()方法没有释放锁，而wait()方法释放了锁 。
+ 两者都可以暂停线程的执行。
+ wait()通常被用于线程间交互/通信，sleep()通常被用于暂停执行。
+ wait()方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的notify()或者notifyAll()方法。
sleep()方法执行完成后，线程会自动苏醒。或者可以使用wait(long timeout)超时后线程会自动苏醒。
##### 为什么我们调用start()方法时会执行run()方法，为什么我们不能直接调用run()方法?
new一个Thread，线程进入了新建状态。
调用start()方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。
start()会执行线程的相应准备工作，然后自动执行run()方法的内容，这是真正的多线程工作。
但是，直接执行run()方法，会把run()方法当成一个main线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。  
**总结**： 调用 start() 方法方可启动线程并使线程进入就绪状态，直接执行 run() 方法的话不会以多线程的方式执行。
##### synchronized关键字
synchronized 关键字解决的是多个线程之间访问资源的同步性，
synchronized关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。

### Java中ArrayList的扩容机制

+ 使用无参构造方法初始化时，复制的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为 10。

1. 加入第一个元素时

```java
/*jdk7*/
public boolean add(E e) {
    //添加元素之前，先调用ensureCapacityInternal方法
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //这里看到ArrayList添加元素的实质就相当于为数组赋值
    elementData[size++] = e;
    return true;
}
//得到最小扩容量
private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        // 获取默认的容量和传入参数的较大值
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}
//判断是否需要扩容
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        //调用grow方法进行扩容，调用此方法代表已经开始扩容了
        grow(minCapacity);
}
```

- 当我们要 add 进第 1 个元素到 ArrayList 时，elementData.length 为 0 （因为还是一个空的 list），因为执行了 `ensureCapacityInternal()` 方法 ，所以 minCapacity 此时为 10。此时，`minCapacity - elementData.length > 0`成立，所以会进入 `grow(minCapacity)` 方法。
- 当 add 第 2 个元素时，minCapacity 为 2，此时 e lementData.length(容量)在添加第一个元素后扩容成 10 了。此时，`minCapacity - elementData.length > 0` 不成立，所以不会进入 （执行）`grow(minCapacity)` 方法。
- 添加第 3、4···到第 10 个元素时，依然不会执行 grow 方法，数组容量都为 10。

2. grow()方法

```java
/**
  * 要分配的最大数组大小
  */
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

/**
  * ArrayList扩容的核心方法。
  */
private void grow(int minCapacity) {
    // oldCapacity为旧容量，newCapacity为新容量
    int oldCapacity = elementData.length;
    //将oldCapacity 右移一位，其效果相当于oldCapacity /2，
    //我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    // 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
    //如果minCapacity大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

**ArrayList在扩容之后，容量会变成原来的1.5倍左右。**

- 当 add 第 1 个元素时，oldCapacity 为 0，经比较后第一个 if 判断成立，newCapacity = minCapacity(为 10)。但是第二个 if 判断不会成立，即 newCapacity 不比 MAX_ARRAY_SIZE 大，则不会进入 `hugeCapacity` 方法。数组容量为 10，add 方法中 return true,size 增为 1。
- 当 add 第 11 个元素进入 grow 方法时，newCapacity 为 15，比 minCapacity（为 11）大，第一个 if 判断不成立。新容量没有大于数组最大 size，不会进入 hugeCapacity 方法。数组容量扩为 15，add 方法中 return true,size 增为 11。
- 以此类推······

3. hugeCapacity()方法

```java
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    //对minCapacity和MAX_ARRAY_SIZE进行比较
    //若minCapacity大，将Integer.MAX_VALUE作为新数组的大小
    //若MAX_ARRAY_SIZE大，将MAX_ARRAY_SIZE作为新数组的大小
    //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，如果 minCapacity 大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。