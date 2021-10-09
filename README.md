# Tutorial: Concurrency in Java

This tutorial cover all the basic and intermediate topics of concurrency with Java programming language.

### Context

* [Introduction to Thread and Multithreading Programming](res/tuOne.md)
* [Fields and methods of Thread class](#res/tuTwo.md)





### Introduction to Thread and Multithreading Programming

In computer science, a thread of execution is the smallest sequence of programmed instructions that can be managed independently by a scheduler, which is typically a part of the operating system. A thread is a component of a process.

In simple words, thread is a unit of execution. When we run a code, the code is executed by the processor step by step. This execution is always linear, we can think of this as a thread.

So every time we run our code, threading happens. There is always at least one thread in a Java program. Think of a Java program -

``` java
class Main{
   public static void main(String[] args) {
       // program starts here
       System.out.println("Hello World");
       // program stops here
   }
}
```

Here, execution starts from the `main(String[] args)` function, prints “Hello World” and terminates. This can be represented like this - 

![L1Fig1](img/L1Fig1.png)

If there were a function in this `main(String[] args)` method - 

``` java
class Main{
   static void printsHelloWorld(){
       System.out.println("Hello World");
   }
   public static void main(String[] args) {
       // program starts here
       printsHelloWorld();
       // program stops here
   }
}
```

This can be represented like this - 

![L1Fig2](img/L1Fig2.png)

Again, If we take a recursive function - 

``` java
class Main{
   static int recursiveFunction(int i){
       if(i == 0) return 0;
       else return recursiveFunction(i-1);
   }
   public static void main(String[] args) {
       // program starts here
       System.out.println(recursiveFunction(3));
       // program stops here
   }
}
```

This can be represented like this - 

![L1Fig3](img/L1Fig3.png)

These are all single threaded programs. They all have one starting point and one ending point. In fact, every program has one starting point. A program cannot have more than one single starting point. But, when a program ends in 2 different points, that is a multithreaded program. For example -

![L1Fig4](img/L1Fig4.png)

We know that we cannot have more than one `main(String[] args)` method in a program. So, how can we create another thread?

There is a class called `Thread` which implements a `Runnable` interface. And that interface has an abstract method called `run()`. This abstract method is one of the most important things in threading so keep this in mind. We’ll discuss it later.

There are mainly 3 ways we can do that.

- Instantiating `Thread` class
- Extending `Thread` class
- Implementing `Runnable` interface

`main(String[] args)` is the starting point indeed, but this `main(String[] args)` will start other threads, and they all will stop their own ways. For example - 3

``` java
class Main{
   public static void main(String[] args) {
       // program starts here
       Thread thread = new Thread();
       // thread starts here
       thread.start();
       // thread will stop independently on its own
       // program stops
   }
}
```

Here, the instantiation method is applied for simplicity. We instantiated the `Thread` class and created a thread object. `Thread` class has a non-static method called `start()`. When we call this method, the thread begins. **So, the `start()` method starts another thread**. Let’s see another example - 

``` java
class Main{
   public static void main(String[] args) {
       // program starts here
       Thread threadOne = new Thread();
       Thread threadTwo = new Thread();
       // threadOne starts here
       threadOne.start();
       System.out.println("Hello World");
       // threadTwo starts here
       threadTwo.start();
       // thread will stop independently
       // program stops
   }
}

```

We have 2 threads here (actually three if we count the main thread). First of all, execution of the main thread will begin, then the main thread will start `threadOne`. After that, the main thread will print “Hello World” and then start `threadTwo`. Diagram -

![L1Fig5](img/L1Fig5.png)

We can add as many threads as we want. Now, the question is, what is the benefit of making threads? Threads will save a huge amount of time in applications because of working different things simultaneously. Suppose we build an app that **takes some user input** and **downloads a random picture** from the internet. So, the app has 2 different tasks to complete. No task is dependent on the other one. If we don’t use thread -

``` java
import java.util.Scanner;

class Main{
   public static void main(String[] args) {
       // Taking user input
       String var = new Scanner(System.in).nextLine();
       // Downloading a random picture
       downloadRandomPicture();
   }
 
   static void downloadRandomPicture(){
       // Code goes here
   }
}
```

The code would look like this. First we take input, then we download. We could also download a picture first and take input. For example - 

``` java
import java.util.Scanner;

class Main{
   public static void main(String[] args) {
       // Downloading a random picture
       downloadRandomPicture();
       // Taking user input
       String var = new Scanner(System.in).nextLine();
   }

   static void downloadRandomPicture(){
       // Code goes here
   }
}
```

In both scenarios, we have to wait either for downloading the picture which results in wastage of time. Because of not using threads, this code cannot download and take input simultaneously. With threads, we can do both tasks simultaneously, so while the user is giving input, the app will download a picture in the background thus saving time.

Remember the `run()` method mentioned earlier? This `run()` method is an abstract method of the `Runnable` interface. We know that if we implement something in a class, we have to override the abstract methods. As `Thread` class implements the `Runnable` interface, and `Runnable` has the abstract method `run()`, we have to implement this `run()` method. Otherwise a thread won’t do anything.

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               // code goes here
           }
       };
       // thread starts here
       thread.start();
   }
}
```

In this code, the `run()` is implemented. Now if we run this code, the thread will execute but not display anything. Let’s write something in this `run()` method.

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               for (int i = 0; i < 10; i++) {
                   System.out.println(i);
               }
           }
       };
       // thread starts here
       thread.start();
   }
}
```

Now, if we run this code, outputs will be shown in the console. If you are wondering what just happened, then let’s understand how `run()` is called. Like the `main(String[] args)` method, this `run()` acts as the starting point of a thread. Every thread has a `run()` method which is basically the starting point of that thread. When we call `thread.start()`, this method calls the `run()` method, thus starting the thread.

*Actually, starts() calls the private method start0(), which eventually calls run(). Google it.*

Another example -

``` java
class Main {
   public static void main(String[] args) {
       new Thread() {
           @Override
           public void run() {
               for (int i = 0; i < 10; i++) {
                   System.out.println("From other Thread : " + i);
               }
           }
       }.start();

       for (int i = 9; i >= 0; i--) {
           System.out.println("From main thread : " + i);
       }
   }
}
```

Diagram for above code -

![L1Fig6](img/L1Fig6.png)

Both for loops will run parallelly. So here the code competes for the process cycle, whichever gets first prints first. There are no certain things like who will come first as it is unpredictable.

```shell
$ java Main.java
From main thread : 9
From main thread : 8
From other Thread : 0
From main thread : 7
From other Thread : 1
From main thread : 6
From other Thread : 2
From main thread : 5
From other Thread : 3
From main thread : 4
From other Thread : 4
From main thread : 3
From other Thread : 5
From main thread : 2
From other Thread : 6
From other Thread : 7
From main thread : 1
From other Thread : 8
From main thread : 0
From other Thread : 9
```

*[Process cycle: Although this code is working simultaneously, the processor can only execute one instruction at a time, Google* **Fetch-Decode-Execute Cycle.** *So, whichever instruction comes first gets printed in the console.]*

#### Thread Scheduler in Java

Thread scheduler in java is the part of the JVM that decides which thread should run. There is no guarantee that which runnable thread will be chosen to run by the thread scheduler. Only one thread at a time can run in a single process. We will learn about it later.

One last Example:

```java
class Main {
   public static void main(String[] args) {
       // First thread
       Thread t1 = new Thread(){
           @Override
           public void run() {
               for (int i = 0; i < 5; i++) {
                   System.out.println("From t1: " + i);
               }
           }
       };
       t1.start();

       // Second Thread
       Thread t2 = new Thread(){
           @Override
           public void run() {
               for (int i = 6; i < 15; i++) {
                   System.out.println("From t2: " + i);
               }
           }
       };
       t2.start();

       for (int i = 9; i >= 0; i--) {
           System.out.println("From main thread : " + i);
       }
   }
}
```

Here, we have in total 3 threads (with main thread). The 1st thread will loop and print 0 to 4, 2nd thread will loop and print 6 to 14. The main thread has a loop that will print from 9 to 0.

---

### Fields and methods of Thread class

Thread class implements the `Runnable` class which has a `run()` method.

```java
public class Thread implements Runnable{ ... }
```

The `Runnable` interface is -

``` java
public interface Runnable {
     public abstract void run();
}
```

So, whenever we instantiate a Thread class, we need to implement this run method in order to work.

#### Priority in Threads -

There are priorities in threads. Each thread has its own priority which is denoted by an integer where 1 is the lowest value and 10 being the highest. When we create a thread it always gets a default priority which is 5. Even the main thread gets default priority.

- **Lowest priority is defined as `MIN_PRIORITY`  in Thread class which has value 1.**

- **Default priority is defined as `NORM_PRIORITY` in Thread class which has value 5.**

- **Highest priority is defined as `MAX_PRIORITY`in Thread class which has value 10.**

Thread Class has 3 **`public static`** instance variables -

| `public static` instance variable       | Value |
| --------------------------------------- | ----- |
| `public static final int MIN_PRIORITY`  | 1     |
| `public static final int NORM_PRIORITY` | 5     |
| `public static final int MAX_PRIORITY`  | 10    |

Again, Thread Class has various **`public static`** methods -

| **`public static`** method                                   | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ```public static native void sleep(long millis) throws InterruptedException``` | Causes the currently running thread to block for at least the specified number of milliseconds. |
| `public static native Thread currentThread()`                | Returns a reference to the currently running thread, which is the thread that invokes this method. |
| `public static void dumpStack()`                             | Prints the stack trace for the currently running thread, which is useful when debugging a multithreaded application. |
| `public static int activeCount()`                            | Returns an estimate of the number of active threads in the current thread's thread group and its subgroups. |
| `public static boolean interrupted()`                        | Tests whether the current thread has been interrupted. The interrupted status of the thread is cleared by this method |

Let’s see the usage of the above mention static methods - 

- **`public static int activeCount()`**
  This method simply returns an integer, which is an estimate of the number of active threads in the current program at the calling point. For example -

  ``` java
  class Main {
     public static void main(String[] args) {
         System.out.println(Thread.activeCount());
         // new thread created
         new Thread(){
             @Override
             public void run() {
                 super.run();
             }
         }.start();
        
         System.out.println(Thread.activeCount());
     }
  }
  ```

  In this program, before creating the thread, there was only one thread in the program which is the main thread. After creating the thread, we will have in total 2 threads. So the output would look like this:

  ```shell
  $ java Main.java
  1
  2
  ```

  Now, let’s see the output of the above program when this tutorial was written-

  Here we can see, the first call of `activeCount()`, it returned 2. Then in the second call, it returned 3. What just happened?

  ![](img/L2Fig1.png)

  Java has a garbage collector, we know that. That garbage collector runs another thread behind the scenes. GC (Garbage Collector) doesn't usually run always. In fact, we really don’t know when it will run. It runs as it needs to. So, there are actually 2 threads even though we don’t create a new thread, one is the main itself and the other is GC. However, if the code is run in debug mode, GC won’t be run. At that time it will return 1 and 2, as we previously expected.

- **`public static native Thread currentThread()`**

  Returns a reference to the currently executing thread object. We can store that thread object. 

  ```java
  class Main {
     public static void main(String[] args) {
         Thread t = Thread.currentThread();
         System.out.println(t);
     }
  }
  ```

  Here, we are storing the current running thread object into t .Now this t can be used as needed. Printing this `Thread.currentThread()` will print some description of the current thread. The output of the above code :

  ```shell
  $ java Main.java
  Thread[main,5,main]
  ```

  Here the syntax is -`Thread[thread_name, priority, namespace]`

  Here the `thread_name` is main, which is given by the JVM. We can change this. The `priority` is 5 because every thread has priority 5 by default. Also, we can change this. The `namespace` is main. Again :

  ``` java
  class Main {
     public static void main(String[] args) {
         Thread thread = new Thread(){
             @Override
             public void run() {
                 System.out.println(Thread.currentThread());
             }
         };
         thread.start();
         // main thread
         System.out.println(Thread.currentThread());
     }
  }
  ```

  Output :

  ```shell
  $ java Main.java
  Thread[main,5,main]
  Thread[Thread-0,5,main]
  ```

  So, calling `Thread.currentThread()` and printing will always print the current threads’ description.

- **`public static boolean interrupted()`**

  A thread can be interrupted for various reasons. This method just returns a `boolean` to indicate if a thread is being interrupted or not. **More on this later. Skip this part at the moment.**

  ``` java
  class Main {
     public static void main(String[] args) {
         Thread t = Thread.currentThread();
         System.out.println(Thread.interrupted());
         t.interrupt();
         System.out.println(Thread.interrupted());
     }
  }
  ```

  In this code, before calling `interrupt()`, it prints `false`. Then after being interrupted, it will display `true`.

  ```shell
  $ java Main.java
  false
  true
  ```

  

- **`public static void dumpStack()`**

  This will print the stack trace of the current thread, just like in exception stack trace `e.printStackTrace()`.

  ``` java
  class Main {
     public static void main(String[] args) {
         Thread.dumpStack();
     }
  }
  ```

  Output:

  ```shell
  $ java Main.java
  java.lang.Exception: Stack trace
  	at java.base/java.lang.Thread.dumpStack(Thread.java:1377)
  	at Main.main(Person.java:3)
  ```

  
  
  
  
- **`public static native void sleep(long millis) throws InterruptedException`**
  This is an important method in threading. With this method, we can slow down a thread process/pause a thread process for some time.

  This method takes a parameter which is of `long` type. This is the time in milliseconds. If we pass 1000 in this method, the thread will pause for 1 second. Again if we pass 5000, it will pause for 5 seconds. As this method throws an `InterruptedException`, we need to catch that Exception. For that we need a try-catch block.

  ```java
  class Main {
     public static void main(String[] args) {
         for (int i = 0; i < 10; i++) {
             try {
                 Thread.sleep(1000);
             } catch (InterruptedException e) {
                 e.printStackTrace();
             }
             System.out.println(i);
         }
     }
  }
  ```

  This code will print 0 to 9 but not at once. After starting execution, it will pause for a second then print 0. Again it will pause for a second and print 1 and so on. So, if we run this code, we can see that each instruction is happening step-by-step. 

  Another Example :

  ``` java
  class Main {
     public static void main(String[] args) {
         // First Thread
         new Thread() {
             @Override
             public void run() {
                 for (int i = 0; i < 5; i++) {
                     try {
                         Thread.sleep(5000);
                     } catch (InterruptedException e) {
                         e.printStackTrace();
                     }
                     System.out.println("From Thread 1:\t" + i);
                 }
             }
         }.start();
        
         // Second Thread
         new Thread() {
             @Override
             public void run() {
                 for (int i = 4; i >=0; i--) {
                     try {
                         Thread.sleep(3000);
                     } catch (InterruptedException e) {
                         e.printStackTrace();
                     }
                     System.out.println("From Thread 2:\t" + i);
                 }
             }
         }.start();
     }
  }
  ```

  In this code there are 2 threads. One will print from 0 to 4 another will print from 4 to 0. First thread will sleep 5 second in each iteration and Second one will sleep 3 second. 
  So, even though we started the first thread first, we will see output from the second thread first. Then we will see output from the first thread, immediately followed by the second thread output.

  Output:

  ```shell
  $ java Main.java
  From Thread 2:    4
  From Thread 1:    0
  From Thread 2:    3
  ...
  ...
  ```

  Here we can actually identify which output will show first and which is second. This is because one thread is taking 5 second sleep and the other one is taking 3 seconds. Let’s calculate the output and match with the right side compiled output.

  Compiled Output:

  ```Shell
  $ java Main.java
  1. From Thread 2: 4
  2. From Thread 1: 0
  3. From Thread 2: 3
  4. From Thread 2: 2
  5. From Thread 1: 1
  6. From Thread 2: 1
  7. From Thread 1: 2
  8. From Thread 2: 0
  9. From Thread 1: 3
  10. From Thread 1: 4
  ```

  Calculated Output:

  | First Thread        | Second Thread |
  | ------------------- | ------------- |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | sleep               | 4             |
  | sleep               | sleep         |
  | 0                   | sleep         |
  | sleep               | sleep         |
  | sleep               | 3             |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | ==1==               | ==2==         |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | sleep               | sleep         |
  | sleep               | 1             |
  | sleep               | sleep         |
  | 2                   | sleep         |
  | sleep               | sleep         |
  | sleep               | 0             |
  | Continues Execution | End of thread |

  Here, in the highlighted box, the first and the second thread will execute at the same time, we cannot be able to tell who will come first in this case. It will depend on the scheduler and the OS to decide. Even if we can calculate what will be the next output, it might not match with the compiled output. So, the calculation of thread is not always accurate. Try with 0-10.

  

  

  









