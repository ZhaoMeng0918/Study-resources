### 1. finally块中的代码什么时候执行？

在Java语言的异常处理中，finally块的作用就是为了保证无论出现什么情况，finally块里的代码一定会被执行。

由于程序执行return就意味着结束对当前函数的调用并跳出这个函数体，因此任何语句要执行都只能在return前执行（除非遇到exit函数），因此finally块里的代码也是在return之前执行的。

此外，如果try-finally或者catch-finally中都有return，那么finally块中的return将会覆盖别处的return语句，最终返回到调用者那里的是finally中的return值。

### 2. finally是不是一定会被执行到

不一定。例如：

（1）当程序进入try块之前就出现异常时，会直接结束，不会执行finall块中的代码

（2）当程序在try块中强制退出时也不会去执行finally块中的代码，比如在try块中执行exit方法

### 3. try-catch-finally中，如果catch中return了，finally还会执行吗？

会，程序在执行到return时会首先将返回值存储在一个指定的位置，其次去执行finally块，最后再返回。

因此，对基本数据类型，在finally块中改变return的值没有任何影响，直接覆盖掉；而对引用类型是有影响的，返回的是在finally对前面return语句返回对象的修改值。

### 4. try-catch-finally中哪个部分可以省略

catch和finally可以省略其中一个，但必须保留其中一个。

try只适合处理运行时异常，try+catch适合处理运行时异常+普通异常。也就是说，如果只用try去处理普通异常却不加catch处理，编译是通不过的，因为编译器硬性规定，普通异常如果选择捕获，则必须用catch显示声明以便进一步处理，而运行时异常在编译时没有如此规定，所以catch可以省略。

总结：受检异常catch不能省略，非受检异常catch可以省略；finally随时可以省略。

### 5. Error和Exception中那个部分可以省略

Error类和Exception类的父类都是Throwable类，主要区别如下：

Error类：一般是指与虚拟机相关的问题，如：系统崩溃、虚拟机错误、内存空间不足、方法调用栈溢出等。这类错误将导致应用程序中断，仅靠程序本身无法恢复和预防。

Exception类：分为运行时异常和受检查的异常。

### 6. 运行时异常与受检异常有何异同

运行时异常：如：空指针异常、指定的类找不到、数组越界、方法传递参数错误、数据类型转换错误。可以编译通过，但是一运行就停止了，程序不会自己处理。

受检查异常：要么用try,catch捕获，要么用throws声明抛出，交给父类处理。

### 7. throw和throws的区别

（1）throw：在方法体内部，表示抛出异常，由方法体内部的语句处理；throw 是具体向外抛出异常的动作，所以它抛出的是一个异常实例；

（2）throws：在方法声明后面，表示如果抛出异常，由该方法的调用者来进行异常的处理；表示出现异常的可能性，并不一定会发生这种异常。

### 8. 常见的异常类有哪些？

NullPointerException

SQLException

IndexOutOfBoundsException

FileNotFoundException

IOException

ClassCastException

IllegalArgumentException

ArithmeticException

ArrayStoreException：数组存储空间不够引起的异常

ConcurrentModificationException

### 9. 主线程可以捕获子线程的异常吗？

线程设计的理念：线程的问题应该线程自己本身来解决，而不要委托到外部。

正常情况下，如果不做特殊的处理，在主线程中是不能够捕获到子线程中的异常的。如果想要在主线程中捕获子线程的异常，我们可以用如下的方式进行处理，使用Thread的静态方法 

```java
Thread.setDefaultUncaughtExceptionHandler(new MyUncaughtExceptionHandle());
```