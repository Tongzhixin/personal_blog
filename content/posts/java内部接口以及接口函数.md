---
title: "java 接口学习（较早）"
date: 2021-02-09T15:24:27+08:00
description: "计算机病毒大作业中部分的工作"
tags: 
    - java
    - 自我学习
categories:
    - Record
draft: false
---

### java内部接口以及接口函数

此处的内部接口是指以下代码
<!--more-->

#### 接口作用

- 类似于C++中的多态虚函数的概念，提供统一标准，继承的类都要实现

  - 实现接口的类要么实现接口里面定义的方法，要么成为抽象类

  - 不可以被实例化，但是可以被实现；另外可以做成指针，绑定一个对象

  - ```java
    interface People{
    	void peopleList();
    }
    class Student implements People{
    	public void peopleList(){
    		System.out.println("I am a 	student");
    	}
    }
    class Teacher implements People{
    	public void peopleList(){
    		System.out.println("I am a teacher");
    	}
    }
    public class TestMain(){
    	public static void main(String args[]){
    		People a; //声明接口变量
    		a=new Student(); //实例化 接口变量中存放对象的引用
    		a.peopleList(); //接口回调
    		a=new Teacher();
    		a.peopleList();
    	}
    }
    //接口回调和向上转型
    ```

    

- 与lambda进行集成，作为接口函数使用

-  

#### 接口特性

1. 接口可以用来定义指针，但是不能用来实例化（new）。
2. 检测一个对象是否实现了某个接口可用instanceOf。
3. 接口可以被扩展（继承）。
4. 接口只有public方法和public static final域。
5. 接口没有实例域。
6. Java SE8之前，接口没有静态方法；Java SE8及其之后，接口可以提供静态方法。详细描述在后面。
7. 一个类只能继承一个类，然而可以实现多个接口。例如一个类可以同时实现Comparable接口、Cloneable接口。
8. Java SE8之前，接口不能实现方法；Java SE8及其之后，接口可以提供方法的默认实现，需要用**default**修饰。详细描述在后面。

#### 函数式接口

##### 接口特性

- 只有一个抽象方法

  1. 函数式接口有且仅有一个抽象方法（非dufault）。
  2. 函数式接口可以有多个default方法。
  3. 函数式接口常在声明时加上注解@functional interface，但不是必须的。

  
##### 方法引用，双冒号语法
- 如果lambda表达式的代码块已经存在于某个方法中，那么可以通过方法引用进行引用，进而推导出lambda表达式。

  一般有以下几种引用：

  1. 类名::静态方法

     当通过函数式接口调用方法时，实际上是ClassName.staticMethod()这样调用的。

  2. 类名::实例方法

     对于这种情况，比较特殊，返回来的方法引用参数会增加一个（第一个）。增加的参数是this指针，需要认为指定相应的this（调用者）。

  3. 对象::实例方法

     实际调用是obj.method()。

  4. 类名::new

     类似于类名::静态。参数为构造器对应的参数（会自动寻找合适的构造器）。

  5. 类型[]::new

     同上。不过参数为int。

```java
package com.ame;

import java.util.Arrays;

interface InterfaceA {
    void fnuc();
}

interface InterfaceB {
    void func(ClassA classA);
}

interface InterfaceC {
    ClassA func();
}

interface InterfaceD {
    int[] func(int t);
}

class ClassA {
    private int i = 0;

    public static void g() {
        System.out.println("g");
    }

    public void f() {
        System.out.println("f:" + i++);
    }

}

public class Main {
    public static void main(String[] args) throws CloneNotSupportedException {
        int i = 1;
        System.out.println("test:" + i++);
        test1();
        System.out.println("test:" + i++);
        test2();
        System.out.println("test:" + i++);
        test3();
        System.out.println("test:" + i++);
        test4();
        System.out.println("test:" + i++);
        test5();
    }

    //类名::静态方法
    public static void test1() {
        ClassA classA = new ClassA();
        InterfaceA interfaceA = null;
        interfaceA = ClassA::g;
        interfaceA.fnuc();
    }

    //类名::实例方法
    public static void test2() {
        ClassA classA = new ClassA();
        InterfaceB interfaceB = null;
        interfaceB = ClassA::f;
        interfaceB.func(classA);
        interfaceB.func(classA);
        interfaceB.func(classA);
    }

    //对象::实例方法
    public static void test3() {
        ClassA classA = new ClassA();
        InterfaceA interfaceA = null;
        interfaceA = classA::f;
        interfaceA.fnuc();
        interfaceA.fnuc();
        interfaceA.fnuc();
    }

    //类名::new
    public static void test4() {
        ClassA classA = new ClassA();
        InterfaceC interfaceC = null;
        interfaceC = ClassA::new;
        classA = interfaceC.func();
        classA.f();
        classA.f();
        classA.f();
    }

    //类型[]:new
    public static void test5() {
        InterfaceD interfaceD = null;
        interfaceD = int[]::new;
        int[] arr = interfaceD.func(3);
        System.out.println(Arrays.toString(arr));
    }
}

```

部分内容截取自https://www.cnblogs.com/black-watch/p/13415890.html。