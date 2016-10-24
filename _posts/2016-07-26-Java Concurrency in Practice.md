---
layout: post
title:    "Synchronized and singleton"
date:   2016-07-27 20:00:47 +0800
tags: Java 并发 SingleTon
categories: Java
---


###  Synchronized 
```java  
public class UnsafeSequence {
    private int value;
    public int getNext() {
       return value++;
    }
}
```
![AB](https://jeffreyxqf.github.io/images/i%20Plus.png)


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

