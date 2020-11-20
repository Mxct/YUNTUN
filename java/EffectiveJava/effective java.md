## 第1章

​		引言：

​			组件的用户不该被其行为所迷惑，组件尽可能小，单不能太小，组件指任何可以重用的软件元素（单个方法，或则多个包的复杂框架）

​		代码应该被重用、而不是被拷贝，组件之间的依赖性应该尽可能降到最小

​		

​		Java支持的四种类型：接口、类、数组、基本数据类型（前三种为引用类型）



## 第2章 创建和销毁对象

#### 	第1条：用静态工厂方法代替构造函器

​		静态工厂方法与构造器不同的第一大优势，它们有名字。**调用更加清晰**

​		第二大优势，不必每次调用它们的时候都创建一个新的对象

​		第三大优势，可以灵活的返回原返回类型的任何子类型的对象

​		第四大优势，返回对象的类可以随着每次调用而发生变化，取决于静态工厂方法的参数值。

​		第五大优势，方法返回的对象所属的类，在编写包含该静态工厂方法的类的时候可以不存在。

​		第一个缺点，类如果不包含共有的或者受保护的构造器，就不能被子类化。

​		第二个缺点，程序员很难发现它们



#### 第2条：遇到多个构造器参数时要考虑使用构造器

​		如果类的构造器或者静态工厂中具有多个参数，设计这种类的时候，Builder模式是一个不错的选择



#### 第3条：用私有构造器或者枚举类强化Singleton属性

​		使类成为Singleton会使它的客户端测试变得十分困难

​		1.使Field域公有并且final

​		2.静态工厂，使Field域私有并且final提供获取实例的公共静态方法

​		3.可以编写一个泛型Singleton工厂

​		优缺点：

​		1.公有域的优势在于，API很清楚的表明了这个类是一个Singleton

​		2.静态工厂方法的优势之一在于，它提供了灵活性：在不改变API的前提下，可以改变这个类是否应该是Singleton的想法。

​			可以通过方法引用作为提供者

​		3.单元素的枚举类型经常成为实现Singleton的最佳方法，如果Singleton必须拓展一个超类，而不是拓展Enum的时候就不宜使用这个方法。

​		

#### 第4条：通过私有构造器强化不可实例化的能力

​		有的时候需要编写包含静态方法和静态域的类。这样的类不希望被实例化，在缺省构造器的情况下会自动提供一个公有、无参的缺省构造器

​		企图通过将类做成抽象类来强制该类不可被实例化是**行不通**的。

​		让这个类包含一个私有构造器，它就不能被实例化

~~~java
//Noninstantiable utility class
public class UtilityClass{
    //Suppress default constructor for noninstantiability
    private UtilityClass(){
        throw new AssertionError();
    }
 	....// Remainder omitted
}
~~~



#### 第5条：有限考虑依赖注入来引用资源

​		不要用Singleton和静态工具类实现依赖一个或多个底层资源的类，且该资源的行为会影响到该类的行为，也不要直接用这个类来创建这些资源，而应该将这些资源或工厂传给构造器（或者静态工厂，或者构造器），通过它们来创建类。这个就叫做依赖注入它极大的提升了类的**灵活性、可重用性和可测试性**。



#### 第6条：避免创建不必要的对象

​		要优先使用基本类型而不是装箱基本类型，要当心无意识的自动装箱

​		“保护性拷贝”的情况下，重用对象的代价要比创建新的重复对象要大



#### 第7条：消除过期的对象引用

​		清空对象引用应该是一种例外，而不是一种规范行为

​		只要类是自己管理内存，程序员就应该警惕内存泄露问题

##### 		内存泄露的另一个常见来源是缓存

​		只有当所要缓存项的生命周期是由该键外部引用而不是由值决定时，WeakHashMap才有用

​		

##### 		内存泄露的第三个常见的来源是监听器和其他回调

​		

#### 第8条：避免使用终结方法和清楚方法

​		终结方法（finalizer）通常是不可预测的，也是很危险的，一般情况下是不必要的。

​		清除方法没有终结方法那么危险，但仍然不可预测、运行缓慢，一般情况下也是不必要的。

​		注重实践的任务不应该由终结方法或者清除方法来完成

#### 		永远不应该依赖终结方法或者清除方法来更新重要的持久状态

​		使用终结方法和清楚方法有一个非常严重的性能损失



#### 第9条：try-with-resources 优先于 try-finally

## 第3章 对于所有对象都通用的方法

#### 第10条：覆盖equals时请准守通用约定

​		什么时候应该覆盖equals方法，如果类具有自己特有的“逻辑相等”概念（不同于对象相等），而且超类还没有覆盖equals，这通常属于“值类“的情形。不仅必须覆盖equals方法，而且这样做也使得这个类的实例可以被做映射表（map）的键（key）或者集合（set）的元素。		

​		覆盖equals方法的时候，必须遵循通用的约定，来自Object的规范

​		**自反性，对称性，传递性，一致性，对于任何非null的引用值x，x.equals(null)必须返回null**

​		

​		覆盖 equals 时总要覆盖 hashCode

​		不要企图让 equals 方法过于智能

​		不要将 equals 声明中的 Object 对象替换成其他类型



​		编写和测试 equals（及 hashCode）  方法都是十分繁琐的，得到的代码也很琐碎，代替手工编写和测试这些方法的最佳途径，是使用Google开源的 AutoValue 框架，它会自动替你生成这些方法，通过类的单个注解就能触发。

​		

​		**总而言之不要轻易覆盖 equals 方法，除非迫不得已，覆盖也要准守 equals 合约的五个条款。**



#### 第11条：覆盖equals时总要覆盖hashCode
