---
layout: post
title:    "Synchronized and singleton"
date:   2016-07-27 20:00:47 +0800
tags: Java 并发 SingleTon
categories: Java
---
本文主要介绍了几种利用Synchronized关键字实现线程安全单例模式的几种情况，比较其利弊。





>- Thread Safety & Singleton 

A : 

```java
public class Singleton {
     private Singleton() {}
     private static Singleton instance = null;
     public static Singleton getInstance() {
      if(instance == null) {
          instance = new Singleton();
        }
    return instance;
    }
}
```

B :

```java
public class Singleton{   
    private static Singleton instance=null;   
    private Singleton(){}   
    public static synchronized Singleton getInstance(){   
        if(instance == null){   
            instance = new Singleton();   
        }   
        return instance;   
    }   
}   
```

C : 

```java 
public class Singleton{   
    private  static Singleton instance=null;   
    private  Singleton(){}   
    public   static  Singleton getInstance(){   
        if(instance == null){   
            synchronized(Singleton.class){  
                instance = new Singleton();   
            }   
        }   
        return instance;   
    }   
}   
```

D :

```java
public class Singleton{   
    private static Singleton instance=null;   
    private Singleton(){} 
public static Singleton getInstance(){     
    if(instance == null){     
        synchronize (Singleton.class) {    
           if(instance == null){     
              instance =  new Singleton();      
            }     
         }     
    }     
    return instance;  
}    
```

