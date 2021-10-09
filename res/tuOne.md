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