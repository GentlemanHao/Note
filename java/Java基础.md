### 1. 面向对象的特征

**抽象：**抽象是将一类对象的共同特征总结出来构造类的过程，包括数据抽象和行为抽象两方面。抽象只关注对象有哪些属性和行为，并不关注这些行为的具体实现。

**封装：**隐藏对象的实现细节，仅对外公开接口，是针对一个对象来说的。

**继承：**继承是从已有类得到继承信息创建新类的过程。提供继承信息的类被称为父类；得到继承信息的类被称为子类。

**多态：**多态指允许不同子类的对象对同一方法做出不同实现。就是说同样的对象引用调用同样的方法但做了不同的事情。

### 2. Java是值传递还是引用传递

Java是值传递。可以理解为传入的是一个引用的副本，指向统一地址。当值改变时，原引用和副本指向地址中的值都变了；当副本指向的地址改变，指向新值时，原引用指向的地址没有改变，原值也没有改变。

### 3. Java中的异常

`Error`和`Exception`继承自`Throwable`。

`Error`类对象由Java虚拟机生成并抛出，不可捕捉。

`Exception`分为受检查异常和不受检查异常，受检查异常会在编译时强制要求我们`try/catch`。

### 4. int和Integer的区别

int是基本类型，Integer是int的包装类型，包装类型可以有一些自己的方法，引入包装类型可以使Java更好的面向对象。

**原始类型：**boolean    char    byte    short    int    long    float    double

**包装类型：**Boolean    Character    Byte    Short    Integer    Long    Float    Double

~~~java
				Integer a = new Integer(3);
        Integer b = 3;                  // 将3自动装箱成Integer类型
        int c = 3;
        System.out.println(a == b);     // false 两个引用没有引用同一对象
        System.out.println(a == c);     // true a自动拆箱成int类型再和c比较
~~~

~~~java
				Integer f1 = 100, f2 = 100, f3 = 150, f4 = 150;
        System.out.println(f1 == f2); //true
        System.out.println(f3 == f4); //false
//自动装箱时，使用的时Integer的valueof方法，当int在-128到127之间时，并不会new一个新的对象，而是直接使用常量池中的Integer
~~~

### 5. String、StringBuffer与StringBuilder的区别

`String` 类型和 `StringBuffer` 类型的主要性能区别其实在于 **`String` 是不可变的对象**

`StringBuffer`和`StringBuilder`底层是 char[]数组实现的，`StringBuffer`是线程安全的，而`StringBuilder`是线程不安全的

