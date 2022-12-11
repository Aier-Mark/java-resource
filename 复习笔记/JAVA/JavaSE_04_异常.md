# 1 异常

异常处理原因：

- 对于任何语言的程序设计而言，错误的发生总是不可避免的；
- 为了加强程序的健壮性，程序设计时，必须充分考虑错误发生的可能性，并建立相应的处理机制。

异常（Exception）又称为例外，是指在程序运行过程中发生的非正常事件，这些事件的发生会影响程序的正常执行。如：

- 进行数学中“无意义”的运算，例如除数为零、对负数求对数平方根等；
- 对数组进行操作时，超出了数组的最大下标；
- 程序所需进行的I/O操作不能正常执行，如所需访问的文件不存在；
- 内存耗尽无法进行类的实例化；
- JVM崩溃。

## 1.1 java中的异常类

![1657625703607](.\assets\1657625703607.png)

在Java语言中，任何的异常对象都是Throwable类的直接子类或间接子类的实例。Java的类库已经提供了一些常见的异常类，如果这些异常类不能够满足要求，用户也可以创建自己的异常类。

## 1.2 Exception类

Exception的子类表示了不同类型的异常，例如：

- RuntimeException表示运行时异常；
- IOException表示I/O问题引起的异常。 

这些子类也可以被继承，以对不同类型的异常进行细分，如：

- RuntimeException还可细分为NullPointerException、ArithmeticException等；
- IOException还可细分为FileNotFoundException、EOFException等。 

常见的异常类：

- 算术异常类：ArithmeticExecption
- 空指针异常类：NullPointerException
- 类型强制转换异常：ClassCastException
- 数组下标越界异常：ArrayIndexOutOfBoundsException
- 违背安全原则异常：SecturityException
- 文件已结束异常：EOFException
- 文件未找到异常：FileNotFoundException
- 字符串转换为数字异常：NumberFormatException
- 操作数据库异常：SQLException
- 输入输出异常：IOException
- 方法未找到异常：NoSuchMethodException

## 1.3 Error类

Error类表示Java运行时产生的系统内部错误或资源耗尽等严重错误。

这种错误通常是程序无法控制和解决的，如果发生这种错误，通常的做法是通知用户并中止程序的执行。

常见的错误类：

- NoClassDefFoundError
- OutOfMemoryError
- VirtualMachineError

## 1.4 异常分类

非检查异常：非检查性异常一般是程序代码写的不够严谨而导致的问题，可以通过修改代码来规避。也称为运行时异常、非编译异常。

检查异常：检查性异常必须使用try catch或者throws等关键字进行处理，否则编译器会报错。也被称为非运行时异常、编译异常。

## 1.5 异常处理的一般步骤

1. 异常抛出；
2. 异常捕获；
3. 异常处理。

# 2 抛出异常

## 2.1 抛出异常

方法中需要抛出异常时，可使用throw语句实现，具体步骤应该是：

1. 选择合适的异常类；
2. 创建该类的一个对象；
3. 使用throw语句抛出该对象。

```java
//运行时异常
public void exception1() {
    throw new NullPointerException();
}

//编译时异常,需要向外抛出异常   小异常写面前，大异常写后面，如果多个异常类存在继承关系，那么只需要抛出父类异常即可
public void exception2() throws FileNotFoundException, IOException {
    if (false) {
        throw new IOException();
    } else {
        throw new FileNotFoundException();
    }
}
```

一个方法必须通过throws语句在方法的声明部分说明它可能抛出而并 未捕获的所有的“必检异常”，如果没有这么做，将不能通过编译。

如果在子类中覆盖了的某一方法，那么该子类方法不可以比被其覆盖的父类方法抛出更多检查异常（但可以更少）。所以，如果被覆盖父类的方法没有抛出任何的“检查异常”，那么子类方法绝不可能抛出“必检异常”。

```java
class Father2 {
    public void say() throws IOException {

    }
}

class Son2 extends Father2 {
    @Override
    public void say() throws IOException {
        boolean flag = false;
        if (flag) {
            //运行时异常
            throw new NullPointerException();
        } else {
            throw new IOException();
        }
    }
}
```

子类抛出的异常，不能比父类更多！！

注意：不包含运行时异常。

## 2.2 Throw和Throws的区别

Throw：

- 作用在方法内，表示抛出具体异常，由方法体内的语句处理。具体向外抛出的动作，所以它抛出的是一个异常实体类。若执行了Throw一定是抛出了某种异常。

Throws：

- 作用在方法的声明上，表示如果抛出异常，则由该方法的调用者来进行异常处理。
- 主要的声明这个方法会抛出会抛出某种类型的异常，让它的使用者知道捕获异常的类型。
- 出现异常是一种可能性，但不一定会发生异常。

# 3 捕捉异常

要捕获一个异常，只需要在程序中设置一个try/catch块，其格式如下： 

```java
try {
    //抛出异常的代码
} catch (某Exception类型 e) {
    //处理该异常类型的代码
} catch (某Exception类型 e) {
    //处理该异常类型的代码
}
```

当try块中的某条代码抛出异常时：

- 首先，自该语句的下一条语句起的所有try块中的剩余语句将被跳过不予执行；
- 其次，程序执行catch子句进行异常捕获，异常捕获的目的是进行异常类型的匹配，并执行与所抛出的异常类型相对应的catch子句中的异常处理代码。

如果try块中没有任何的异常抛出，则所有的catch子句将会被跳过；

如果try块中所抛出的异常对象类型与所有的catch子句中的所声明的异常类型都不匹配，则方法会立即中止，并将该异常对象继续抛出，沿调用堆栈传递。

注意：最大的异常要在catch的最后一个，如果写在第一个，后面子异常将不会在进行捕捉。

# 4 finally

当一个方法的某条语句抛出异常后，该方法剩余的语句将无法继续执行。这种情况下，方法往往无法将其占用的资源进行释放。

Java语言的异常处理机制中提供finally子句来统一进行资源释放之类的工作。

```java
try {
    //抛出异常的代码
} catch (某Exception类型 e) {
    //处理该异常类型的代码
} catch (某Exception类型 e) {
    //处理该异常类型的代码
} finally {
    //最后一定会被执行的代码
}
```

不论try块中的代码是否抛出异常及异常是否被捕获，finally子句中的代码一定会被执行：

- 如果try块中没有抛出任何异常，当try块中的代码执行结束后，finally中的代码将会被执行；
- 如果try块中抛出了一个异常且该异常被catch正常捕获，那么try块中自抛出异常的代码之后的所有代码将会被跳过，程序接着执行与抛出异常类型匹配的catch子句中的代码，最后执行finally子句中的代码。
- 如果try块中抛出了一个不能被任何catch子句捕获（匹配）的异常，try块中剩下的代码将会被跳过，程序接着执行finally子句中的代码，未被捕获的异常对象继续抛出，沿调用堆栈顺序传递。

## 4.1 finally、final、finalize

- final用于声明属性，方法和类，分别表示属性不可变，方法不可覆盖，类不可继承。
- finally是异常处理语句结构的一部分，表示总是执行。
- finalize是Object类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，供垃圾收集时的其他资源回收，例如关闭文件等。

## 4.2 声明异常

一个方法不处理它产生的异常，而是沿着调用堆栈向上传递，由调用它的方法来处理这些异常，则需要声明异常。

```java
public static void main(String[] args) throws ParseException {
    Date date = new SimpleDateFormat("yyyy-MM-dd").parse("1999-9-9");
    System.out.println(date); //Thu Sep 09 00:00:00 GMT+08:00 1999
}
```

# 5 自定义异常

Java语言中允许用户定义自己的异常类，这些用户自定义异常类必须是Throwable的直接子类或间接子类。

根据Java异常类的继承关系，用户最好将自己的异常类定义为Exception的子类，而不要将其定义为RuntimeException的子类。

因为对于RuntimeException的子类而言，即使调用者不进行处理，编译程序也不会报错。将自定义异常类定义为Exception的子类，可以确保调用者对其进行处理。

```java
/* main */
try
{
    throw  new MyException("性别输入错误");
} catch (MyException e)
{
    e.printStackTrace();
}
```

```java
/**
 * 要想让一个类具备某项特殊的功能，要么去继承一个类，要么实现一个接口
 */
class MyException extends Exception
{
    public MyException(String msg)
    {
        super(msg);
    }
}
```



