# Some ways of creating a thread

From this lesson, we'll be learning a couple of more convenient ways of creating a thread object.

### The Thread class constructor

Thread class has many constructors. For example, we can set a thread name at the time of instantiation. Here are some constructors -

| Constructor                  | Description                                |
| ---------------------------- | ------------------------------------------ |
| `public Thread()`            | Default Constructor                        |
| `public Thread(String name)` | Set a name of a thread while instantiating |

**`public Thread()`**

We have been using this constructor. This is the default constructor.

**`public Thread(String name)`**

This constructor will set the name of a thread at instantiation. Example-

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread("MyThread");
       System.out.println(t1.getName());
   }
}
```

Output:

``` shell
$ java Main.java
MyThread
```

We will see some more constructor later.

---

### Extending the Thread class

Developers often extend the thread class for creating a thread. This is a more convenient way of using thread. For example:

``` java
class Main extends Thread{ ... }
```

This way, we can have much freedom and flexibility in our code. For example:

``` java
class Main extends Thread{
   @Override
   public void run() {
       System.out.println("This is a Thread");
   }

   public static void main(String[] args) {
       Main t1 = new Main();
       // Thread starts here
       t1.start();
   }
}
```

Another Example:

``` java
class Main extends Thread{
   // Constructor
   public Main(String name) {
       super(name);
   }
   // run method
   @Override
   public void run() {
       System.out.println("This is a Thread");
   }
   // main method
   public static void main(String[] args) {
       Main t1 = new Main("MyThread");
       // Thread starts here
       t1.start();
       System.out.println(t1.getName());   // Output : MyThread
   }
}
```

A bit more complex Example:

``` java
class MyThread extends Thread{

   public MyThread() {
       super();
   }

   public MyThread(String name) {
       super(name);
   }

   @Override
   public void run() {
       for (int i = 0; i < 10; i++) {
           System.out.println(i);
       }
   }
}

class Main{
   public static void main(String[] args) {
       MyThread t1 = new MyThread();
       MyThread t2 = new MyThread("New_Name");
       // displaying names
       System.out.println(t1.getName());
       System.out.println(t2.getName());
   }
}
```

Output:

``` shell
$ java Main.java
Thread-0
New_Name
```

---

### Implementing the Runnable interface

We can also implement the runnable interface and implement the run() method. Implementing a runnable interface is more flexible than inheriting a Thread class. We will see that in a bit.

##### *The Runnable interface*

``` java
public interface Runnable {
   public abstract void run();
}
```

If we implement this in a class, we also have to implement the `run()` method.

``` java
class MyThread implements Runnable{
   @Override
   public void run() {
      // Code goes here
   }
}
```

So, Let’s make a class that implements Runnable interface. It also overrides the run method to print 0 to 9.

``` java
class MyThread implements Runnable{
   @Override
   public void run() {
       for (int i = 0; i < 10; i++) {
           System.out.println(i);
       }
   }
}

class Main{
   public static void main(String[] args) {
       MyThread t1 = new MyThread();
       t1.run();
       // t1.start() will not work, because t1 object is not an
       // instance of Thread class.
   }
}
```

This code will print from 0 to 9. But is it a thread? The answer is no. We have seen earlier, if we call the `run()` method, it is not a thread. For starting a thread, we need to call the start method, which is absent in the Runnable interface. There are two constructor in the Thread class which receive a Runnable object.

``` java
public Thread(Runnable target)
public Thread(Runnable target, String name)
```

So if we pass the MyThread t1 object into a Thread class during instantiation, it will return us a thread object which we can use for running threads. We can also set the name of a thread.

``` java
class Main{
   public static void main(String[] args) {
       MyThread t1 = new MyThread();
       Thread thread = new Thread(t1);
       // Now we can call thread start()
       thread.start();
   }
}
```

This is how we create a thread when implementing a Runnable interface. Example -

``` java
class MyThread implements Runnable{
   @Override
   public void run() {
       for (int i = 0; i < 10; i++) {
           System.out.println(i);
       }
   }
}

class Main{
   public static void main(String[] args) {
       MyThread t1 = new MyThread();
       MyThread t2 = new MyThread();
      
       Thread thread1 = new Thread(t1);
       Thread thread2 = new Thread(t2, "SecondThread");
       // Now we can call thread start()
       thread1.start();
       thread2.start();
   }
}
```

In this code, both of the constructors are used. So, now we know a Thread class has 4 constructors. There are more though, we’ll see that later.

It is said, implementing the Runnable class is the most convenient way of making a thread because in this way we can run a thread task more than once. Also, it is easy to organize and makes the code more readable. Let’s see an example -

``` java
class Task1 implements Runnable {
   // Override the run method to
   // play a music
}

class Task2 implements Runnable {
   // Override the run method to
   // run a compiler
}

class Task3 implements Runnable {
   // Override the run method to
   // process a large calculation
}

class Main {
   public static void main(String[] args) {
       	Thread t1 = new Thread(new Task1(), "Task1");
 		Thread t2 = new Thread(new Task1(), "Task2");
 		Thread t3 = new Thread(new Task1(), "Task3");

       	t1.start();
       	t2.start();
       	t3.start();
   }
}
```

In this thread, tasks/processes are organized so that the code is more readable and manageable. If we want to run a thread more than once, we can just create a new Thread object and pass the Runnable object then call `start()`. Here is an example of starting a thread twice.

``` java
class Task1 implements Runnable {
   @Override
   public void run() {
      // code goes here
   }
}

class Main {
   public static void main(String[] args) {
       // 1st time running
       Thread t1 = new Thread(new Task1());
       t1.start();
       // 2nd time running
       Thread t2 = new Thread(new Task1());
       t2.start();
   }
}
```

---

So, the discussed constructors are -
| Constructor                                   | Description                                |
| --------------------------------------------- | ------------------------------------------ |
| `public Thread()`                             | Default Constructor                        |
| `public Thread(String name)`                  | Set a name of a thread while instantiating |
| `public Thread(Runnable target)`              | Create a thread with Runnable object       |
| `public Thread(Runnable target, String name)` | Create a thread with a name                |