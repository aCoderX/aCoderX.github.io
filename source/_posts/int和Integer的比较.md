---
title: int和Integer的比较
date: 2017-03-03 14:26:47
tags: java
---
### int和Integer相等比较
 <!--more-->
```java
public class Test {
    public static void main(String[] args) {
        Integer a = 1;  //jdk5之后，会自动装箱成Integer（自动调用valueOf(1)）
        Integer b = 1;
        System.out.println(a==b); //true 自动装箱的对象在-127到128之间是从常量池拿出来的
        Integer c = 129;
        Integer d = 129;
        System.out.println(c==d);//false  由于超过了128 相当于new Integer(129)
        int f = 1000;
        int g = 1000;
        System.out.println(f==g);//true  不用解释..
        int h = 50;
        Integer i = 50;
        System.out.println(h==i);  //true i会自动拆箱成int型进行比较
        int j = 500;
        Integer k = 500;
        System.out.println(j==k);//true  同上
        Integer l = new Integer(1);
        Integer m = new Integer(1);
        System.out.println(l==m);//false  new出来的的对象引用是不同的，是指向堆上的
    }
}
```

### jdk 自动装箱的源码
```java
    public static Integer valueOf(int i) {
        //low为-127，high为128
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
