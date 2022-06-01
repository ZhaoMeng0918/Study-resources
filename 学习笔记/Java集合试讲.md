# Java集合试讲-20220528

## 一、Java为什么要有集合？集合有什么用呢？

我们知道，在Java中有数组的概念，数组可以用来存放一组数据，但是，数组的长度是固定的，这样在使用的时候就会有很多不方便的地方。

举例：有一个餐厅软件，需要在程序中保存就餐客户的信息，那数组长度该设置多少？

举例：有一个餐厅软件，厨师需要看到做菜的流水信息，菜品类型都不同，该如何设计数组？

这个时候，我们就希望有一种可以动态改变大小的数组，即集合。

**集合和数组的区别：**

|          | 数组                             | 集合             |
| -------- | -------------------------------- | ---------------- |
| 长度区别 | 固定                             | 可变             |
| 内容区别 | 可以是基本类型，也可以是引用类型 | 只能是引用类型   |
| 元素内容 | 只能存储一种类型                 | 可以存储不同类型 |

## 二、集合分类

我们通常所说的集合包括Collection和Map。

Collection：单列集合，每次存储一个元素。

Map：双列集合，是一种键值对的映射关系。

<img src="E:\ProgramData\2_工作空间\2022.6~2022.7_东软代课\20220528_试讲\Collection体系图.png" style="zoom:150%;" />

<img src="E:\ProgramData\2_工作空间\2022.6~2022.7_东软代课\20220528_试讲\Map体系图.png" style="zoom: 150%;" />

## 三、Collection接口和常用方法

**Collection接口实现类的特点**

```java
public interface Collection<E> extends Iterable<E>
```

1. Collection实现子类可以存放多个元素，每个元素可以是Object。
2. 有些Collection的实现类可以存放重复的元素，有些不可以重复。
3. 有些Collection的实现类存放元素是有序的，有些是无序的。
4. Collection接口没有直接的实现子类，是通过它的子接口Set和List来实现的。

**Collection接口常用方法**

<img src="E:\ProgramData\2_工作空间\2022.6~2022.7_东软代课\20220528_试讲\Collection常用方法图.png" style="zoom:150%;" />

*【代码演示】*

## 四、Collection接口遍历方式

**遍历方式1：使用Iterator（迭代器）**

**遍历方式3：使用增强for**：增强for就是简化版的iterator，本质一样。只能用于遍历集合或数组。

*【代码演示】*



**补充：Iterable**

Iterable是Collection的最顶层接口，针对Iterable我们要明白三个问题：

1. 什么是Iterable？
2. 什么是Iterator？
3. Iterable和Iterator有什么关系？

**1. 什么是Iterable？**

JDK描述：实现此接口允许对象成为 "for-each" 语句的目标。

```java
public interface Iterable<T> {
    Iterator<T> iterator();
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

**2. 什么是Iterator？**

Iterator接口是提供了一种**统一的遍历集合元素**的方式。使用Iterator对象可以不用关心具体集合对象的具体类型和内部实现，统一使用Iterator对象的接口方法就可以遍历集合。

Iterator是顺序遍历迭代器，JDK中默认对集合框架中数据结构做了实现。

Iterator在实际应用中有一个比较好的点是：可以边遍历边删除元素。

```java
public interface Iterator<E> {
    boolean hasNext();
    E next();
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
} 
```

**3. Iterable和Iterator有什么关系？**

- Iterator是迭代器接口，实现此接口的实例可以对元素集合进行迭代遍历。而Iterable是为了只要实现该接口就可以使用foreach进行迭代。
- Iterable中封装了Iterator接口，只要实现了Iterable的类，就可以使用Iterator迭代器了。
- 集合Collection、List、Set都是Iterable的实现类，所以他们及其他们的子类都可以使用foreach进行迭代。
- Iterator中和核心的方法next()，hasnext()，remove()，都是依赖当前位置，如果这些集合直接实现Iterator，则必须包括当前迭代位置的指针。当集合在方法间进行传递的时候，由于当前位置不可知，所以next()之后的值，也不可知。而当实现Iterable则不然，每次调用都返回一个从头开始的迭代器，各个迭代器之间互不影响。

## 五、List

### **List接口基本介绍**

List接口是Collection接口的子接口。

1. List集合类中元素有序（即添加顺序和取出顺序一致）、且可重复。
2. List集合中的每个元素都有其对应的顺序索引，即支持索引。
3. List容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素。
4. 常用实现类：ArrayList、LinkedList、Vector

#### **List常用方法**

<img src="E:\ProgramData\2_工作空间\2022.6~2022.7_东软代课\20220528_试讲\List对比Collection方法图.png" style="zoom:200%;" />

*【代码演示】*

### ArrayList

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    ...
}
```

ArrayList内部是使用动态数组实现的，换句话说，ArrayList封装了对内部数组的操作，比如向数组种添加、删除、插入新的元素或数据的扩展和重定向。

- 继承了AbstractList，此类提供List接口的骨干实现，以最大限度地减少实现“随机访问”数据存储（如数组）支持的该接口所需的工作。对于连续的访问数据（如链表），应优先使用AbstractSequentialList，而不是此类。
- 实现了List接口，意味着ArrayList元素是有序的，可以重复的，可以有null元素的集合。
- 实现了RandomAccess接口，标识着其支持随机快速访问。实际上，我们查看RandomAccess源码，可以看到里面什么都没有定义，因为ArrayList底层是数组，那么随机快速访问是理所应当的。
- 实现了Cloneable接口，标识着它可以被复制。注意，ArrayList里面的clone()其实是浅复制。
- 实现了Serializable接口，标志着集合可以被序列化。



对于ArrayList我们要明白如下问题：

1. **ArrayList扩容机制**
2. **ArrayList迭代器实现**
3. **Fail-Fast机制**
4. **ArrayList序列化与反序列化机制**
5. **ArrayList clone实现**

#### 一、ArrayList扩容机制

初始化：ArrayList提供了三个构造函数来对elementData数组初始化。

```java
// 默认的容量大小（常量）
private static final int DEFAULT_CAPACITY = 10;

// 定义的空数组（final修饰，大小固定为0）
private static final Object[] EMPTY_ELEMENTDATA = {};

// 定义的默认空容量的数组（final修饰，大小固定为0）
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

// 定义的不可被序列化的数组，实际存储元素的数组
transient Object[] elementData; 

// 数组中元素的个数
private int size;

// 1. 无参构造函数: 直接赋值一个空的数组
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    // 可以看出，当我们直接创建ArrayList时，elementData被赋予了默认空容量的数组。
    // 注意，因为默认空容量数组是被final修饰的，此时ArrayList数组是空的、固定长度，
    // 也就是说其容量此时为0，元素个数size为0。
}
// 2. 指定大小的构造函数
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    }
}
// 3. 构造一个包含指定集合的元素的列表
public ArrayList(Collection<? extends E> c) {
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray might (incorrectly) not return Object[] (see 6260652)
        if (elementData.getClass() != Object[].class) // 看程序
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // replace with empty array.
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

补充：总结之EMPTY_ELEMENTDATA和DEFAULTCAPACITY_EMPTY_ELEMENTDATA的区别：EMPTY_ELEMENTDATA是为了优化创建ArrayList空实例时产生不必要的空数组，使得所有ArrayList空实例都指向同一个空数组。DEFAULTCAPACITY_EMPTY_ELEMENTDATA是为了确保无参构成函数创建的实例在添加第一个元素时，\*最小的容量\*是默认大小10。



扩容：添加元素时使用ensureCapacityInternal()方法来保证容量足够，如果不够时，需要使用grow()方法进行扩容，新容量的大小为：oldCapacity + oldCapacity >> 1，也就是旧容量的1.5倍。

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);
    elementData[size++] = e;
    return true;
}

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1); //关键代码
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // 注意：扩容时，需要调用Arrays.copyOf()把原数组复制到新数组中，
    // 这个代价很高，因此最好在创建ArrayList对象时就指定大概的容量大小，减少扩容操作的次数。
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

#### 二、ArrayList迭代器实现

ArrayList通过内部类实现Iterator接口，通过Iterator我们可以实现对elementData中的元素迭代遍历。而ArrayList又实现了一种功能更为强大的ListIterator来实现迭代遍历。ListIterator继承于Iterator接口，对Iterator接口做了扩展，支持向前向后遍历、迭代过程中去修改集合等。

```java
public interface ListIterator<E> extends Iterator<E> {
    boolean hasNext();    // 检查是否有下一个元素
    E next();             // 返回下一个元素
    boolean hasPrevious();// check是否有previous(前一个)element(元素)
    E previous();         // 返回previous element
    int nextIndex();      // 返回下一element的Index
    int previousIndex();  // 返回当前元素的Index
    void remove();        // 移除一个elment
    void set(E e);       // set()方法替换访问过的最后一个元素 注意用set设置的是List列表的原始值
    void add(E e);        // 添加一个element
}
```

【代码演示】

#### 三、Fail-Fast机制

fail-fast机制是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。
例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。



原理：modCount用来记录ArrayList结构发生变化的次数，结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。

再进行序列化或者迭代等操作时，需要比较前后modCount是否改变，如果改变了需要抛出ConcurrentModificationException。

#### 四、ArrayList序列化与反序列化机制

```java
transient Object[] elementData;
```

我们知道，被transient关键字修饰的成员变量不被序列化。

所以，ArrayList被序列化成功的原因是，它重写了readObject和writeObject来自定义的序列化和反序列化策略。

- 在序列化过程中，如果被序列化的类中定义了writeObject和 readObject方法，虚拟机会试图调用对象类里的writeObject和readObject方法，进行用户自定义的序列化和反序列化。
- 如果没有这样的方法，则默认调用是ObjectOutputStream的defaultWriteObject方法以及ObjectInputStream的defaultReadObject方法。
- 用户自定义的writeObject和readObject方法可以允许用户控制序列化的过程，比如可以在序列化的过程中动态改变序列化的数值。

那么为什么要自定义序列化策略呢？

原因是ArrayList实际上是动态数组，每次在放满以后自动增长设定的长度值，如果数组自动增长长度设置为100，而实际只放了一个元素，那么就会序列化99个null元素。为了保证在序列化的时候不会将这么多的null同时进行序列化，ArrayList把元素设置为transient。

#### 五、ArrayList clone实现

ArrayList的clone实现，实际上是通过数组元素拷贝来实现的浅拷贝。

直接看源码：

```java
public Object clone() {
    try {
        ArrayList<?> v = (ArrayList<?>) super.clone();
        v.elementData = Arrays.copyOf(elementData, size);
        v.modCount = 0;
        return v;
    } catch (CloneNotSupportedException e) {
        // this shouldn't happen, since we are Cloneable
        throw new InternalError(e);
    }
}
```

#### ArrayList课后习题：

用一个集合A存入20个随机数字，然后筛选其中的偶数元素，放到集合B当中，要求用自定义的方法来筛选。

### Vector

Vector是线程安全的动态数组，同ArrayList一样继承自AbstractList，并且实现了List、RandomAccess、Cloneable、Serializable接口。

内部实现依然基于数组，Vector与ArrayList基本是一致的，唯一不同的是Vector是线程安全的，会在可能出现线程安全的方法前面加上synchronized关键字。

与ArrayList类似，随机访问速度快，插入和移除性能较差，支持null元素，有顺序，元素可以重复，线程安全。

扩容：

Vector在capacityIncrement大于0时扩容capacityIncrement大小，否则扩容为原始容量的2倍。而ArrayList在默认数组容量不够时默认扩展1.5倍。

**为什么现在都不提倡使用Vector了？**

因为Vector实现并发安全的原理是在每个操作方法上加锁，这些锁并不是必须的，在实际开发中一般都是通过锁一系列的操作来实现线程安全，也就是说将需要同步的资源放在一起加锁来保证线程安全，如果多个Thread并发执行一个已经加锁的方法，但在该方法中又有Vector的存在，双重锁会造成额外的开销。

所以在JDK1.5之后，推荐使用java.util.concurrent包下的并发类。

### LinkedList

对于LinkedList我们要明白如下知识：

1. LinkedList的List特性
2. LinkedList的Queue特性

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    ...
}
```

- 实现List接口，具有List集合的特性。
- 实现Deque接口，具有队列的特性。
- 实现Cloneable接口，可以通过clone来实现浅拷贝。
- 实现Serializable接口，可以序列化，通过writeObject和readObject自定义序列化。

LinkedList类是List接口的实现类，它是一个集合，可以根据索引来随机访问集合中的元素，还实现了Deque接口，它还是一个队列，可以当成双端队列来使用。

虽然LinkedList是一个List集合，但是它的实现方式和ArrayList完全不同，ArrayList底层是通过一个动态的Object数组实现的，而LinkedList底层是通过双向链表实现，因此他的随机访问速度是比较差的，但它的删除、插入操作很快。

- LinkedList是基于双向循环链表实现的，除了可以当作链表外，它还可以当作栈、队列和双端队列来使用。
- LinkedList同样是非线程安全的，只在单线程下适合使用。
- LinkedList允许插入null值。

#### 链表和双向链表

- 链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针连接次序实现的。
- 每一个链表都包含多个节点，节点又包含两个部分，一个是数据域（储存节点含有的信息），一个是引用域（储存下一个节点或者上一个节点的地址）。

链表的特点：

- 获取数据麻烦，需要遍历查找，比数组慢
- 方便插入、删除

*【代码实现】*

#### LinkedList基本方法

**增加**

- add(E e)：在链表后添加一个元素；   通用方法
- addFirst(E e)：在链表头部插入一个元素；  特有方法
- addLast(E e)：在链表尾部添加一个元素；  特有方法
- push(E e)：与addFirst方法一致 
- offer(E e)：在链表尾部插入一个元素                                                                                                                                                
- add(int index, E element)：在指定位置插入一个元素
- offerFirst(E e)：JDK1.6版本之后，在头部添加； 特有方法                                                                                                         
- offerLast(E e)：JDK1.6版本之后，在尾部添加； 特有方法

**删除**

- remove() ：移除链表中第一个元素;    通用方法 
- remove(E e)：移除指定元素；   通用方法
- removeFirst(E e)：删除头，获取元素并删除；  特有方法
- removeLast(E e)：删除尾；  特有方法
- pollFirst()：删除头；  特有方法
- pollLast()：删除尾；  特有方法
- pop()：和removeFirst方法一致，删除头。 
- poll()：查询并移除第一个元素     特有方法 

**查找**

- get(int index)：按照下标获取元素；  通用方法
- getFirst()：获取第一个元素；  特有方法
- getLast()：获取最后一个元素； 特有方法
- peek()：获取第一个元素，但是不移除；  特有方法
- peekFirst()：获取第一个元素，但是不移除； 
- peekLast()：获取最后一个元素，但是不移除；
- pollFirst()：查询并删除头；  特有方法
- pollLast()：删除尾；  特有方法
- poll()：查询并移除第一个元素     特有方法



#### **总结：**

- LinkedList是基于双向链表实现的，不论是增删改查还是队列和栈的实现，都可通过操作节点实现。
- LinkedList无需提前指定容量，因为基于链表操作，集合的容器随着元素的加入自动增加。
- LinkedList删除元素后，集合占用的内存自动缩小，无需像ArrayList一样调用trimToSize()方法。
- LinkedList的所有方法没有进行同步，因此它也不是线程安全的，应该避免在多线程环境下使用。

#### LinkedList课后习题

定义一个LinkedList存储一批人对象，然后根据人的年龄进行排序。

## 六、Set

### HashSet

```java
public class HashSet<E> extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{
    ...
}
```

- HashSet继承于AbstractSet，该类提供了Set接口的骨架实现，以最大限度地减少实现此接口所需的工作量。
- 实现Set接口，标志着内部元素是无序的，元素是不可以重复的。
- 实现Cloneable接口，标识着它可以被复制。
- 实现Serializable接口，标识着可被序列化。

HashSet内部是以HashMap的Key来保存元素的。

总结：

- HashSet就是限制了功能的HashMap，所以，了解HashMap的实现原理，HashSet自然就明白了。
- 对于HashSet中保存的对象，主要要正确重写equals和hashCode方法，以保证放入Set对象的唯一性。
- 虽说Set是对于重复元素不放入，倒不如说是底层的Map把原值替代了。

```java
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

- HashSet没有提供get()方法，原因是同HashMap一样，Set内部是无序的，只能通过迭代的方式获得。

### LinkedHashSet

LinkedHashSet是HashSet的一个扩展版本，HashSet并不管什么顺序，不同的是，LinkedHashSet会维护插入顺序。

HashSet内部使用HashMap对象来存储它的元素，而LinkedHashSet内部使用LinkedHashMap对象来存储和处理它的元素。

### TreeSet

我们知道TreeMap是一个有序的二叉树，那么同理TreeSet同样也是一个有序的，它的作用是提供有序的Set集合。

TreeSet中的元素支持2种排序方式：自然排序 或者 根据创建TreeSet时提供的Comparator进行排序。这取决于使用的构造方法。

通过源码我们知道TreeSet基础AbstractSet，实现NavigableSet、Cloneable、Serializable接口。

其中AbstractSet提供Set接口的骨干实现，从而最大限度地减少了实现此接口所需的工作。

NavigableSet是扩展的SortedSet，具有了为给定搜索目标报告最接近匹配项的导航方法，这就意味着它支持一系列的导航方法。比如查找与指定目标最匹配项。Cloneable支持克隆，Serializable支持序列化。

TreeSet实际上是TreeMap实现的。当我们构造TreeSet时；若使用不带参数的构造函数，则TreeSet的使用自然比较器；若用户需要使用自定义的比较器，则需要使用带比较器的参数。

### HashTable

HashMap和HashTable实现大致相同，都是基于哈希表来实现的，数组+链表的形式（HashMap后续加入了红黑树），它存储的内容是键值对映射。

HashTable方法加了synchronized关键字，所以是线程安全的。

扩容机制：2

HashMap和HashTable的区别：

- HashMap线程不安全，HashTable是线程安全的。HashMap内部实现没有任何线程同步相关的代码，所以相对而言性能要好一点。如果在多线程中使用HashMap需要自己管理线程同步。HashTable大部分对外接口都使用synchronized包裹，所以是线程安全的，但是性能会相对差一些。
- 二者的基类不一样。HashMap派生于AbstractMap，HashTable派生于Dictionary。它们都实现Map, Cloneable, Serializable这些接口。AbstractMap中提供的基础方法更多，并且实现了多个通用的方法，而在Dictionary中只有少量的接口，并且都是abstract类型。
- key和value的取值范围不同。HashMap的key和value都可以为null，但是HashTable key和value都不能为null。对于HashMap如果get返回null，并不能表明HashMap不存在这个key，如果需要判断HashMap中是否包含某个key，就需要使用containsKey这个方法来判断。
- 算法不一样。HashMap的initialCapacity为16，而HashTable的initialCapacity为11。HashMap中初始容量必须是2的幂,如果初始化传入的initialCapacity不是2的幂，将会自动调整为大于出入的initialCapacity最小的2的幂。HashMap使用自己的计算hash的方法(会依赖key的hashCode方法)，HashTable则使用key的hashCode方法得到。

## 七、List和Set的区别

- List特点：元素有放入顺序，元素可重复 ，Set特点：元素无放入顺序，元素不可重复，重复元素会覆盖掉，（元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的，加入Set 的Object必须定义equals()方法 ，另外list支持for循环，也就是通过下标来遍历，也可以用迭代器，但是set只能用迭代，因为他无序，无法用下标来取得想要的值。） 

- Set和List对比： 
- Set：检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。 
- List：和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。

## 八、课后练习

暂无