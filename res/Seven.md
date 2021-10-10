# Synchronization

### Idea of Synchronization

Suppose, we have a class Counter which has a static variable *count* and a method `countStart()`.

``` java
class Counter {
   private static int count;
  
   Counter(){
       count = 0;
   }
  
   public void countStart(){
       while (count <= 10){
           System.out.println(Thread.currentThread().getName() +
                   " -> Count : " + count);
           ++count;
       }
   }
}
```

This small class will print numbers 0 to 10 as well as which thread is executing the method. Now, let’s make a class MyThread that implements Runnable interface and executes the `countStart()` method. This class will have an instance variable -

``` java
Counter counter;
```

And a constructor that will initialize the counter variable -

``` java
public MyThread(Counter counter) {
   this.counter = counter;
}
```

So the class is-

``` java
class MyThread implements Runnable {
   Counter counter;

   public MyThread(Counter counter) {
       this.counter = counter;
   }

   @Override
   public void run() {
       counter.countStart();
   }
}
```

Now in the main class, we will create an instance of Counter class and make 2 threads `t1` and `t2` from MyThread class. We will pass the Counter object to both MyThread parameters.

``` java
class Main {
   public static void main(String[] args) {
       Counter c = new Counter();
       Thread t1 = new Thread(new MyThread(c));
       Thread t2 = new Thread(new MyThread(c));
       t1.start();
       t2.start();
   }
}
```

**So the complete code will be -**

``` java
class Counter {
   private int count;

   Counter() {
       count = 0;
   }

   public void countStart() {
       while (count <= 10) {
           System.out.println(Thread.currentThread().getName() +
                   " -> Count : " + count);
           ++count;
       }
   }
}

class MyThread implements Runnable {
   Counter counter;

   public MyThread(Counter counter) {
       this.counter = counter;
   }

   @Override
   public void run() {
       counter.countStart();
   }
}

class Main {
   public static void main(String[] args) {
       Counter c = new Counter();
       Thread t1 = new Thread(new MyThread(c));
       Thread t2 = new Thread(new MyThread(c));
	 t1.setName("t1");
 t2.setName("t2");
       t1.start();
       t2.start();
   }
}
```

Now, if we run this code, we will see the following output -

``` shell
$ java Main.java
t2 -> Count : 0
t1 -> Count : 0
t1 -> Count : 2
t2 -> Count : 1
t1 -> Count : 3
t2 -> Count : 4
t1 -> Count : 5
t2 -> Count : 6
t1 -> Count : 7
t2 -> Count : 8
t1 -> Count : 9
t2 -> Count : 10
```

This is expected, because in both threads, the same object is passed. So, if one thread increments the count value, the other thread increments the incremented value.

For example, if `t1` thread increments count to 1, `t2` thread will increment count to 2. Again if `t2` increments the count to 3, the `t1` thread will find the count has value 3. Note that the same variable is used here.

**But why is 0 printed twice?**

Since both threads started simultaneously, both of them reached the while loop at the same time. At that moment, the value of count was 0. So, before any thread could increment count, both threads tried to print 0 in the display. 

Process Scheduler is mainly a queue. In Windows, a special algorithm is followed to determine which process will come first. This algorithm is called Round-Robin Algorithm. The processor also execute one statement per cycle, so either it will be a t1 or t2 instruction that the processor will execute. 

The main point is, 2 threads are accessing the same object. Most of the time we don’t want this because imagine 2 threads writing some text into a file. This will be a mess because if we want to print “Hello World” and “Goodbye World” at the same time, the output will be a complete gibberish. To avoid this situation, an idea called “Synchronization” is implemented in java. Synchronization keeps an object locked to a thread so that other threads cannot access it. For synchronization, we use a java keyword called `synchronization`. For example if we use synchronization in the above public void `countStart()` method:

``` java'
public synchronized void countStart() {
   while (count <= 10) {
       System.out.println(Thread.currentThread().getName() +
               " -> Count : " + count);
       ++count;
   }
}
```

Output:

``` shell
$ java Main.java
t1 -> Count : 0
t1 -> Count : 1
t1 -> Count : 2
t1 -> Count : 3
t1 -> Count : 4
t1 -> Count : 5
t1 -> Count : 6
t1 -> Count : 7
t1 -> Count : 8
t1 -> Count : 9
t1 -> Count : 10
```

Notice that all the output came from t1. Why is t2 not executed? Because, count is a static variable. t1 incremented count to 10 and t2 will not be able to increment count any further because of the condition in the while loop. It says the loop will execute if the count variable is below or equals 10. If we rewrite the `countStart()` like this-

``` java
public void countStart() {
   while (count <= 10) {
       System.out.println(Thread.currentThread().getName() +
               " -> Count : " + count);
       ++count;
   }
   count = 0;
}
```

Output:

``` shell
$ java Main.java
t1 -> Count : 0
t1 -> Count : 1
t1 -> Count : 2
t1 -> Count : 3
t1 -> Count : 4
t1 -> Count : 5
t1 -> Count : 6
t1 -> Count : 7
t1 -> Count : 8
t1 -> Count : 9
t1 -> Count : 10
t2 -> Count : 0
t2 -> Count : 1
t2 -> Count : 2
t2 -> Count : 3
t2 -> Count : 4
t2 -> Count : 5
t2 -> Count : 6
t2 -> Count : 7
t2 -> Count : 8
t2 -> Count : 9
t2 -> Count : 10
```

So, we can see that `t1` executes first then `t2` starts execution.

---

### Idea of Locking an Object

In the previous example, we saw that t1 executes first then t2 begins to execute. While t1 is executing, it actually locks the object to itself so no other threads can access that object and remains in BLOCKED state. So in synchronization, when a thread is accessing a synchronized object, the thread actually locks the object. We will see more about synchronization.

