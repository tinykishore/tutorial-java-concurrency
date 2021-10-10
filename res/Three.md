# Non-Static fields and method of Thread class

A thread object doesn’t have any public fields. All the fields are private and the getter-setter method is used. So, all we have here is only some important method.

### Thread Name

Every thread has a name which is by default given by the JVM. Thread class has a private field name 

```java
private volatile String name;
```

We can view/change this name with the getter-setter method-

```java
public final synchronized void setName(String name)
public final String getName()
```

For Example:

```java
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

We saw this code in the previous lesson. The output will be:

```shell
$ java Main.java
Thread[main,5,main]
Thread[Thread-0,5,main]
```

[Note that even though we ran the thread first, `Thread.currentThread()`from the main thread executed first followed by the threads’ one. That is why `Thread[main,5,main]` printed first.

Here, we can see the name of the main thread is main and the thread we made has the name Thread-0. Thread-0 is some name given by the JVM. There is a non-static method `getName()` just for getting the name of the thread.

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               System.out.println(Thread.currentThread().getName());
           }
       };
       thread.start();

       // main thread
       System.out.println(Thread.currentThread().getName());
   }
}
```

This will print just the name of the threads in the console. For example:

``` shell
$ java Main.java
main
Thread-0
```

We can change these names with `setName(String name)` method -

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               super.run();
           }
       };
       thread.setName("My_Custom_Thread");
       System.out.println(thread.getName());
      
       // main thread
       Thread.currentThread().setName("Main_Function_Thread");
       System.out.println(Thread.currentThread().getName());
   }
}
```

This will change the name of our threads. For example:

``` shell
$ java Main.java
My_Custom_Thread
Main_Function_Thread
```

[Notice custom thread name prints first]

---

### Thread Priority

Every thread has a priority as we have seen earlier, ranging from 1 to 10 where 0 is the lowest and 10 is the highest. Thread class has a private field name -

```java
private int priority;
```

We can view/change this name with the getter-setter method-

``` java
public final void setPriority(int newPriority)
public final int getPriority()
```

For example:

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               super.run();
           }
       };
       System.out.println(thread.getPriority());
       // main thread
       System.out.println(Thread.currentThread().getPriority());
   }
}
```

The output will be -

``` shell
$ java Main.java
5
5
```

We know that every thread has a default priority value 5. Changing it will give a certain thread more priority than other threads and is likely to execute first. Although we cannot really tell for sure that threads with more priority will execute first.

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               super.run();
           }
       };
       thread.setPriority(10); // Priority set to 10 (highest)
       System.out.println(thread.getPriority());
       // main thread
       Thread.currentThread().setPriority(4); // Priority set to 4 (low)
       System.out.println(Thread.currentThread().getPriority());
   }
}
```

The output will be -

``` shell
$ java Main.java
10
4
```

Now, let’s write a code to check if the priority system is working correctly. Let's make 2 threads one will print 0 to 9 and another will print 9 to 10 without any sleep. If the priority system works, then we will see the thread with higher priority execute more often.

``` java
class Main {
   public static void main(String[] args) {
       Thread thread = new Thread(){
           @Override
           public void run() {
               for (int i = 0; i < 10; i++) {
                   System.out.println("From custom thread: " + i);
               }
           }
       };
       thread.setPriority(Thread.MAX_PRIORITY);
       thread.start();
       // main thread
       Thread.currentThread().setPriority(Thread.MIN_PRIORITY);
       for (int i = 9; i >= 0; i--) {
           System.out.println("From main Thread: " + i);
       }
   }
}
```

And here is the output :

``` shell
$ java Main.java
From custom thread: 0
From main Thread: 9
From custom thread: 1
From main Thread: 8
From main Thread: 7
From custom thread: 2
From main Thread: 6
From main Thread: 5
From custom thread: 3
From main Thread: 4
From main Thread: 3
From custom thread: 4
From main Thread: 2
From main Thread: 1
From custom thread: 5
From main Thread: 0
From custom thread: 6
From custom thread: 7
From custom thread: 8
From custom thread: 9
```

So, it seems like it doesn’t work. It really depends on the OS. OS has a lot going on other than just running a simple code. Again another example:

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread(){
           @Override
           public void run() {
               System.out.println(Thread.currentThread());
           }
       };
       Thread t2 = new Thread(){
           @Override
           public void run() {
               System.out.println(Thread.currentThread());
           }
       };
       Thread t3 = new Thread(){
           @Override
           public void run() {
               System.out.println(Thread.currentThread());
           }
       };

       // t1 priority: 10, t2 priority: 5, t3 priority : 1
       t1.setPriority(Thread.MAX_PRIORITY);
       t3.setPriority(Thread.MIN_PRIORITY);

       t1.start();
       t2.start();
       t3.start();
   }
}
```

The output is: 

``` shell
$ java Main.java
Thread[Thread-0,10,main]
Thread[Thread-2,1,main]
Thread[Thread-1,5,main]
```

Though the highest priority thread is executed first, the lowest priority thread is executed second. But it can vary.

---

### Daemon Thread

Daemon thread is a low priority thread that runs in the background to perform tasks such as garbage collection. It is an utmost low priority thread. If the JVM finds a running daemon thread, it terminates the thread and after that shuts down itself. JVM does not care whether the Daemon thread is running or not. Thread class has a private field name -

```java
private boolean daemon = false;
```

By default, daemon is off. We can turn it on/off using the setter method -

``` java
public final void setDaemon(boolean on)
```

We can check if a threads’ daemon is on by this method -

``` java
public final boolean isDaemon()
```

For example -

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread(){
           @Override
           public void run() {
               super.run();
           }
       };
       System.out.println(t1.isDaemon());
       t1.setDaemon(true);
       System.out.println(t1.isDaemon());
   }
}
```

The output will be:

``` shell
$ java Main.java
false
true
```

---

### Thread ID

The thread ID is a positive long number generated when this thread was created. The thread ID is unique and remains unchanged during its lifetime. When a thread is terminated, this thread ID may be reused. Thread class has a private field name -

```java
private final long tid
```

We cannot change a thread ID. We can view this ID by calling this method -

``` java
public long getId()
```

For example -

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread(){
           @Override
           public void run() {
               super.run();
           }
       };
       System.out.println(t1.getId());
       System.out.println(Thread.currentThread().getId());
   }
}
```

And the output will be -

``` shell
$ java Main.java
16
1
```

Our customized thread has the identifier 16 while the main thread has 1 as the identifier.

---

### Thread State (Life Cycle of a Thread) 

We need some knowledge for understanding this.

A thread can be in one of the six states.

- NEW
- RUNNABLE
- BLOCKED
- WAITING
- TIMED_WAITING
- TERMINAED

We can view these states by calling a getter-method:

``` java
public State getState()
```

State cannot be changed manually. So, there are no setter methods. Also, State is not a class, it is an `enum`

Different States and their Definition:

1. **State: NEW**
   Before calling start()method, an instance of a Thread class is in the New State. This is a thread state for a thread which has not yet started.

   ``` java
   class Main {
      public static void main(String[] args) {
          Thread t1 = new Thread(){
              @Override
              public void run() {
                  super.run();
              }
          };
          System.out.println(t1.getState());
      }
   }
   ```

   Output:

   ``` shell
   $ java Main.java
   NEW
   ```

   ---

2. **State: RUNNABLE**

   A thread in the runnable state is executing in the Java virtual machine.

   ``` java
   class Main {
      public static void main(String[] args) {
          Thread t1 = new Thread(){
              @Override
              public void run() {
                  System.out.println(getState());
              }
          };
          t1.start();
      }
   }
   ```

   Output:

   ``` shell
   $ java Main.java
   RUNNABLE
   ```

   ---

3. **State: BLOCKED/WAITING**
   When a thread is temporarily inactive, then it’s in one of the following states:

   - BLOCKED
   - WAITING

   For example, when a thread is waiting for I/O to complete, it lies in the blocked state. A thread in this state cannot continue its execution any further until it is moved to the RUNNABLE state. Any thread in these states does not consume any CPU cycle. **We will see examples later. **

   ---

4. **State: TIMED_WAITING**
   Thread state for a waiting thread with a specified waiting time. A thread is in the timed waiting state due to calling sleep()methods with a specified positive waiting time.

   ``` java
   class Main {
      public static void main(String[] args) {
          Thread t1 = new Thread() {
              @Override
              public void run() {
                  try {
                      sleep(1000);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
   
              }
          };
          // Before calling t1.start() - NEW
          System.out.println(t1.getState());
          t1.start();
          // After calling t1.start() - RUNNABLE
          System.out.println(t1.getState());
          try {
              // Pause for a moment to enter t1 into TIMED_WAITING state
              Thread.sleep(100);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          // After entering TIMED_WAITING state
          System.out.println(t1.getState());
      }
   }
   ```

   Output:

   ``` shell
   $ java Main.java
   NEW
   RUNNABLE
   TIMED_WAITING
   ```

   ---

5. **State: TERMINATED**

   Thread state for a terminated thread. The thread has completed execution.

   ```java
   class Main {
      public static void main(String[] args) {
          Thread t1 = new Thread() {
              @Override
              public void run() {
                  System.out.println("t1 Thread Ended.");
              }
          };
          t1.start();
          try {
              // Wait until t1 Thread terminates
              Thread.sleep(100);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          System.out.println(t1.getState());
      }
   }
   ```

   Output:

   ``` shell
   $ java Main.java
   t1 Thread Ended.
   TERMINATED
   ```

   We will discuss more about states later.

   ---

### Joining a Thread

Sometimes we need to keep a thread waiting until another thread ends. Suppose we have two threads t1 and t2. If t1 depends on t2, then we might need to keep t1 waiting until t2 finishes. For this we need to call a method - 

``` java
public final void join() throws InterruptedException
```

This method keeps another method in a WAITING state. For example -

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread() {
           @Override
           public void run() {
               System.out.println("Thread t1: Running t1...");
               try {
                   sleep(10000);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       };
       t1.start();

       Thread t2 = new Thread(){
           @Override
           public void run() {
               try {
                   System.out.println("Thread t2: Waiting for t1”);
                   // t1 will keep t2 in WAITING state until t1 terminates
                   t1.join();
                   System.out.println("Thread t2: t1 Thread terminated.");
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       };
       t2.start();
   }
}
```

In this code, `t1.join()` will change the `t2` state to WAITING. `t2` will wait for `t1` to terminate. After termination of`t1`, `t2` will resume.

Output:

``` shell
$ java Main.java
Thread t1: Running t1...
Thread t2: Waiting for t1
Thread t2: t1 Thread terminated.
```

##### Checking States

The below code is for checking States of different thread object.

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread() {
           @Override
           public void run() {
               System.out.println("Thread t1: Running t1...");
               try {
                   sleep(10000);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       };

       // t1 State before calling start()
       System.out.println("t1 State: " +t1.getState());
       t1.start();

       // t1 State after calling start()
       System.out.println("t1 State: " +t1.getState());

       Thread t2 = new Thread(){
           @Override
           public void run() {
               try {
                   System.out.println("Thread t2: Waiting for t1 to terminate...");
                   // t1 will keep t2 in WAITING state until t1 terminates
                   t1.join();
                   System.out.println("Thread t2: t1 Thread terminated.");
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       };

       // t2 State before calling start()
       System.out.println("t1 State: " +t2.getState());
       t2.start();

       // t2 State after calling start()
       System.out.println("t1 State: " +t2.getState());

       try {
           Thread.sleep(100);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }

       // Check t2 state while Waiting
       System.out.println("t1 State: " +t2.getState());
   }
}

```

Output:

``` shell
$ java Main.java
t1 State: NEW
t1 State: RUNNABLE
Thread t1: Running t1...
t1 State: NEW
t1 State: RUNNABLE
Thread t2: Waiting for t1 to terminate...
t1 State: WAITING
Thread t2: t1 Thread terminated.
```

---

### Deadlocks

If 2 processes are waiting for each other and if they are stuck forever, then it is called deadlock. For example, if we have 2 threads named t1 and t2, and both of them are in WAITING state depending on each other, then they will be stuck forever.

``` java
class Main {
   static Thread t1 = new Thread(){
       @Override
       public void run() {
           try {
               t2.join();
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
       }
   };
   static Thread t2 = new Thread(){
       @Override
       public void run() {
           try {
               t1.join();
           } catch (InterruptedException e) {
               e.printStackTrace();
           }
       }
   };
   public static void main(String[] args) {
       t1.start();
       t2.start();
   }
}
```

The above code will hang forever. So, when using `join()`, watch out for the deadlocks. One single thread can cause a deadlock. For example -

``` java
class Main {
   public static void main(String[] args) {
       Thread t = Thread.currentThread();
       try {
           t.join();
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
   }
}
```

This code will also hang forever.

---

So, we have seen some methods so far. Let’s list them -

| Method name                                            | Description                                             |
| ------------------------------------------------------ | ------------------------------------------------------- |
| `public final synchronized void setName(String name)`  | Set a name for a thread                                 |
| `public final String getName()`                        | Get the current name of thread                          |
| `public final void setPriority(int newPriority)`       | Set a priority for a thread (range: 1-10)               |
| `public final int getPriority()`                       | Get the current priority of thread                      |
| `public final void setDaemon(boolean on)`              | Set daemon for a thread                                 |
| `public final boolean isDaemon()`                      | Check daemon status for a thread                        |
| `public long getId()`                                  | Get the current ID of a thread                          |
| `public State getState()`                              | Get the current State of a thread                       |
| `public final void join() throws InterruptedException` | Keep another thread in WAITING State for current thread |

Other than these there are some methods we use often in thread:

- `public synchronized void start()`
- `public void run()`
- `public void interrupt()`



**`public synchronized void start()`**

Causes this thread to begin execution; the Java Virtual Machine calls the run() method of this thread. This is the starting point of any thread. It is never legal to start a thread more than once. In particular, a thread may not be restarted once it has completed execution. This method calls another private method private native void `start0()` which later calls `run()` method to start thread execution.

**`public void run()`**

This method is called by `start()`. We could directly call run, but that will result in not running code in a thread. The code will run of course but not in a thread mechanism. It will run normally.

``` java
class Main {
   public static void main(String[] args) {
       Thread t = new Thread(){
           @Override
           public void run() {
               for (int i = 0; i < 5; i++) {
                   System.out.println("From custom thread");
               }
           }
       };
       t.run();
       for (int i = 0; i < 5; i++) {
           System.out.println("Main thread");
       }
   }
}
```

The output of this code will be -

``` shell
$ java Main.java
From custom thread
From custom thread
From custom thread
From custom thread
From custom thread
Main thread
Main thread
Main thread
Main thread
Main thread
```

Again, if we do the opposite (Main first, custom second), the output will be also opposite. So, we can see that even though we could call run method, we should not as it results in not running a code in thread.

**`public void interrupt()`**

If any thread is in a sleeping or waiting state for example: `sleep()`, calling the `interrupt()` method on the thread, breaks out the sleeping or waiting state and throws an `InterruptedException`. If the thread is not in the sleeping or WAITING state, calling the `interrupt()` method performs normal behavior and doesn't interrupt the thread but sets the interrupt flag to true. This interrupt flag is -

``` java
private volatile boolean interrupted;
```

As we can see, the flag is private. So we need a getter method to get the flag. A flag cannot be set manually.

``` java
public boolean isInterrupted()
```

Let’s see an example -

``` java
class Main {
   public static void main(String[] args) {
       Thread t1 = new Thread() {
           @Override
           public void run() {
               try {
                   sleep(10000);
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       };
       t1.start();
       Thread t2 = new Thread() {
           @Override
           public void run() {
               System.out.println("Waiting for t1...");

               try {
                   t1.join();
                   System.out.println("This line will never be executed");
               } catch (InterruptedException e) {
                   System.err.println("t2 is interrupted");
                   System.out.println("State of t2: " + getState());

               }
           }
       };
       t2.start();
       try {
           Thread.sleep(3000);
       } catch (InterruptedException e) {
           e.printStackTrace();
       }
       System.out.println("return of isInterrupted: " + t2.isInterrupted());
       t2.interrupt();
       System.out.println("return of isInterrupted: " + t2.isInterrupted());
   }
}

```

The output of the above code is -

``` shell
$ java Main.java
Waiting for t1...
return of isInterrupted: false
return of isInterrupted: true
State of t2: RUNNABLE
t2 is interrupted
```

There is also an static method for checking interruption of a current thread -

```java
public static boolean interrupted()
```

---

These are the basic methods that we use often. There are even more. We will discuss them as needed.