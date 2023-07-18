# Java-Multithreading

Process: The process is the execution of a program and performs the relevant actions specified in a program, or it is an execution unit where a program runs. The operating system creates, schedules and terminates the processes for the use of the CPU. Creation of each process includes system calls for each process separately. A process is an isolated execution entity and does not share data and information. Processes use IPC (Inter-process communication) mechanism for communication which significantly increases the number of system calls. Process management consumes more system calls. Each process has its own stack and heap memory, instruction, data and memory map.
1) System calls involved in process.  
2) Context switching required.
3) Different process have different copies of code and data. 
4) Operating system treats different process differently. 
5) If a process got blocked, remaining process continue their work.
6) Processes are independent.
7) Process run in separate memory space.
8) Processes have their own program counter (PC), register set, and stack space.
9) Communication between processes requires some time.
10) Processes don’t share the memory with any other process
11) Process have overhead.

======
Thread: A thread is a lightweight process that can be managed independently by a scheduler. It improves the application performance using parallelism. A thread shares information like data segment, code segment, files etc. with its peer threads while it contains its own registers, stack, counter etc. Threads reside inside the process. Each thread belongs to exactly one process. No thread exists outside the process. A thread, in the context of Java, is the path followed when executing a program. All Java programs have at least one thread, known as the main thread, which is created by the Java Virtual Machine (JVM) at the program's start, when the main() method is invoked with the main thread. Threads die instantly once the job is finished. JVM switches between the new thread and the main thread until both threads complete. Thread scheduler makes all the decisions about who moves from runnable to running and who will leave the running state. No one controls the scheduler.
1) No system calls involved.
2) No context switching required.
3) Sharing same copy of code and data can be possible among different threads..
4) All user level threads treated as single task for operating system.
5) If a user level thread got blocked, all other threads get blocked since they are treated as single task to OS. (Noted: This is can be avoided in kernel level threads).
6) Threads exist as subsets of a process. They are dependent.
7) Threads run in shared memory space. And use memory of process which it belong to.
8) Threads share Code section, data section, Address space with other threads.
9) Communication between threads requires less time than processes.
10) Threads share the memory with other threads of the same process
11) Threads have no overhead.

======
Threads vs Processes: A process runs independently and isolated of other processes. It cannot directly access shared data in other processes. The resources of the process, e.g. memory and CPU time, are allocated to it via the operating system. A thread is a so called lightweight process. It has its own call stack, but can access shared data of other threads in the same process. Every thread has its own memory cache. Like processes, threads are independent, concurrent paths of execution through a program, and each thread has its own stack, its own program counter, and its own local variables. However, threads within a process are less insulated from each other than separate processes are. They share memory, file handles, and other per-process state. A process can support multiple threads, which appear to execute simultaneously and asynchronously to each other. Multiple threads within a process share the same memory address space, which means they have access to the same variables and objects, and they allocate objects from the same heap. While this makes it easy for threads to share information with each other, you must take care to ensure that they do not interfere with other threads in the same process.

======
Asynchronous or background processing: Server applications get their input from remote sources, such as sockets. When you read from a socket, if there is no data currently available, the call to SocketInputStream.read() will block until data is available. If a single-threaded program were to read from the socket, and the entity on the other end of the socket were never to send any data, the program would simply wait forever, and no other processing would get done. On the other hand, the program could poll the socket to see if data was available, but this is often undesirable for performance reasons. If, instead, you created a thread to read from the socket, the main thread could perform other tasks while the other thread waited for input from the socket. You can even create multiple threads so you can read from multiple sockets at once. In this way, you are notified quickly when data is available (because the waiting thread is awakened) without having to poll frequently to check if data is available. The code to wait on a socket using threads is also much simpler and less error-prone than polling would be.

======
Thread priority can be set to make one thread have more priority than other, it makes higher priority thread runnable and low priority thread stop, but this also doesn't guarantee.

======
Joining two threads: The Thread API contains a method for waiting for another thread to complete: the join() method. When you want one thread to wait for another before starts processing. It's like a relay race where the second runner waits until the first runner comes and hand over the flag to him. Remember, unlike sleep(), join() is not a static method so you need an object of java.lang.Thread class to call this method. The current thread, which calls join method will wait until the thread on which join has called die or wait at most specified millisecond for this thread to die.
/**
 * Sample Java class to illustrate How to join two threads in Java.
 * join() method allows you to serialize processing of two threads.
 */
public class Join {
  
    public static void main(String args[]) throws InterruptedException{   
        System.out.println(Thread.currentThread().getName() + " is Started");
        Thread exampleThread = new Thread(){
            public void run(){
                try {
                    System.out.println(Thread.currentThread().getName() + " is Started");
                    Thread.sleep(2000);
                    System.out.println(Thread.currentThread().getName() + " is Completed");
                } catch (InterruptedException ex) {
                    Logger.getLogger(Join.class.getName()).log(Level.SEVERE, null, ex);
                }
            }
        };
        exampleThread.start();
        exampleThread.join();
        System.out.println(Thread.currentThread().getName() + " is Completed");
    }
}
Output:
main is Started
Thread-0 is Started
Thread-0 is Completed
main is Completed

======
Join is a final method in java.lang.Thread class and you cannot override it.

======
Sleep: The Thread API includes a sleep() method, which will cause the current thread to go into a wait state until the specified amount of time has elapsed or until the thread is interrupted by another thread calling Thread.interrupt() on the current thread's Thread object. Thread.sleep() method can be used to pause the execution of current thread for specified time in milliseconds. The argument value for milliseconds can’t be negative, else it throws IllegalArgumentException. There is another overloaded method sleep(long millis, int nanos) that can be used to pause the execution of current thread for specified milliseconds and nanoseconds. The allowed nano second value is between 0 and 999999. Thread sleep doesn’t lose any monitors or locks current thread has acquired. Any other thread can interrupt the current thread in sleep, in that case InterruptedException is thrown. Interrupt is to indicate a thread that it should stop doing what it is currently doing and exit. Sleep honors interrupt and since it is an exception condition, it indicates it by throwing an exception i.e. InterruptedException. InterruptedException is a checked exception. Its design tells you that if you want to pause for a few milliseconds, make your code interruption-ready. Thread.sleep() is designed (taking a very primitive approach):
public static void sleep(long millis) throws InterruptedException {
  while (/* You still need to wait */) {
    if (Thread.interrupted()) {
      throw new InterruptedException();
    }
    // Keep waiting
  }
}

======
How to stop/interrupt a thread?
Thread loop = new Thread(
  new Runnable() {
    @Override
    public void run() {
      while (true) {
        if (Thread.interrupted()) {
          break;
        }
        // Continue to do nothing
      }
    }
  }
);
loop.start();
loop.interrupt();
- You can also stop the thread using the volatile boolean variable. Checking in every loop the value of the volatile variable will help to get out of the thread

======
Starting the same thread again will give you java.lang.IllegalThreadStateException
th1.start();  
th1.start(); 
Exception in thread "main" th1 is executing. java.lang.IllegalThreadStateException

======
When we create a Thread instance in a java program, then its state is new. Then we start the Thread, then it's state changes to Runnable(ready to run but not running yet). Execution of Threads depends upon ThreadScheduler. ThreadScheduler is responsible to allocate CPUs to threads in Runnable thread pool and change their state to Running.Waiting,Blocked and Dead are the remaining states of the Thread.

======
Difference between user thread and daemon thread: When we create a thread in java program, then it is called as user thread. We can not make a user thread to daemon thread if a thread is started. The daemon threads are the low priority threads that provide the background support to the user threads. It provides services to the user threads. A child thread created from a daemon thread is also a daemon thread. By setting the setDaemon(true), we can create a daemon thread in java.

======
The java.lang.Object.wait() causes current thread to wait until another thread invokes the notify() method or the notifyAll() method for this object. The current thread must own this object's monitor. The thread releases ownership of this monitor and waits until another thread notifies threads waiting on this object's monitor to wake up either through a call to the notify method or the notifyAll method. The thread then waits until it can re-obtain ownership of the monitor and resumes execution.

======
notify() - Wakes up the single thread that is waiting on this object's monitor. notifyAll() - It wakes up all the threads that called wait() on the same object. The Object class defines the methods wait(), notify(), and notifyAll(). To execute any of these methods, you must be holding the lock for the associated object.

======
wait(), notify(), notifyAll(): As per the definition these functions have to acquire the lock of the object in which the current thread is running. Moreover, locks are available as per the object basis. Hence, all these three functions are put in the Object class in place of Thread class.

======
Difference between wait() sleep(): The key difference between wait() and sleep() is that former is used for inter-thread communication while later is used to introduced to pause the current thread for a short duration. This difference is more obvious from the fact that, when a thread calls the wait() method, it releases the monitor or lock it was holding on that object, but when a thread calls the sleep() method, it never releases the monitor even if it is holding. wait for method in Java should be called from synchronized method or block while there is no such requirement for sleep() method. Another difference is Thread.sleep() method is a static method and applies on current thread, while wait() is an instance specific method and only got wake up if some other thread calls notify method on same object. In the case of sleep, sleeping thread immediately goes to Runnable state after waking up while in the case of wait, waiting for a thread first acquires the lock and then goes into Runnable state. Waiting thread can be awake by calling notify and notifyAll while sleeping thread can not be awakened by calling notify method. 

======
sleep() method throws Interrupted Exception if another thread interrupts a sleeping thread in java. wait(), notify(), and notifyAll() methods are implemented as final methods in Object, so all classes have them. As sleep is static method, it can't be overridden, so no need to make it as final.

======
yield(): It just releases the CPU hold by Thread to give another thread an opportunity to run though it's not guaranteed who will get the CPU. It totally depends upon thread scheduler and it's even possible that the thread which calls the yield() method gets the CPU again. Hence, it's not reliable to depend upon yield() method, it's just on best effort basis. If you call the yield or sleep method, it applies to whichever thread is currently executing, rather than any specific thread - you don't have to specify which thread is currently running to free up the processor

======
The major difference between yield and sleep in Java is that yield() method pauses the currently executing thread temporarily for giving a chance to the remaining waiting threads of the same priority to execute. If there is no waiting thread or all the waiting threads have a lower priority then the same thread will continue its execution. The yielded thread when it will get the chance for execution is decided by the thread scheduler whose behavior is vendor dependent. Yield method doesn’t guarantee that current thread will pause or stop but it guarantee that CPU will be relinquished by current Thread as a result of a call to Thread.yield() method in java. 

======
Race condition in java and how we can solve it: When more than one thread try to access same resource without synchronization causes race condition. We can solve race condition by using a synchronized block or synchronized method.

======
You have thread T1, T2, and T3, how will you ensure that thread T2 run after T1 and thread T3 run after T2: It can be achieved by using the join method of Thread class.

=================
Synchronization
=================
It is the capability to control the access of multiple threads to any shared resource. The simplest way for threads to share their results is to use shared variables. They should also use synchronization to ensure that values are propagated correctly from one thread to another and to prevent threads from seeing inconsistent intermediate results while another thread is updating several related data items. The Java language provides two keywords for ensuring that data can be shared between threads in a controlled manner: synchronized and volatile. Synchronized has two important meanings: it ensures that only one thread executes a protected section of code at one time (mutual exclusion or mutex), and it ensures that data changed by one thread is visible to other threads (visibility of changes). Synchronization uses the concepts of monitors, or locks, to coordinate access to particular blocks of code. Every Java object has an associated lock. Java locks can be held by no more than one thread at a time. When a thread enters a synchronized block of code, the thread blocks and waits until the lock is available, acquires the lock when it becomes available, and then executes the block of code. It releases the lock when control exits the protected block of code, either by reaching the end of the block or when an exception is thrown that is not caught within the synchronized block.
public class SyncExample {
   private static lockObject = new Object();
   private static class Thread1 extends Thread {
      public void run() {
         synchronized (lockObject) {
            x = y = 0;
            System.out.println(x);
         }
      }
   }
   private static class Thread2 extends Thread {
      public void run() {
         synchronized (lockObject) {
            x = y = 1;
            System.out.println(y);
         }
      }
   }
   public static void main(String[] args) {
      new Thread1().run();
      new Thread2().run();
   }
}
The synchronized keyword in Java ensures:
1) that only a single thread can execute a block of code at the same time
2) that each thread entering a synchronized block of code sees the effects of all previous modifications that were guarded by the same lock

=======
The synchronized keyword in Java provides locking, which ensures mutually exclusive access to the shared resource and prevents data race. Before entering into synchronized method or block thread needs to acquire the lock, at this point it reads data from main memory than cache and when it release the lock, it flushes write operation into main memory which eliminates memory inconsistency errors.

=======
We can have both static synchronized method and non-static synchronized method and synchronized blocks in Java but we can not have synchronized variable in java. Using synchronized keyword with a variable is illegal and will result in compilation error. 

=======
Limitation of java synchronized keyword is that it can only be used to control access to a shared object within the same JVM. If you have more than one JVM and need to synchronize access to a shared file system or database, the Java synchronized keyword is not at all sufficient. You need to implement a kind of global lock for that. Global lock can be like creating a static variable initialized with simple Object class. Now if synchronized using this object then, it's kind of global lock.

=========
If your shared object is either read-only or immutable object, then you don't need synchronization, despite running multiple threads. I the objects are immutable or read only different threads can't change the state of that object so, even if two threads tries and access the same object, it really doesn't matter.

=========
Java synchronized code could result in deadlock or starvation while accessing by multiple threads if synchronization is not implemented correctly.

=========
Do not synchronize on the non-final field in synchronized block because the reference of the non-final field may change anytime and then different thread might synchronizing on different objects i.e. no synchronization at all. an example of synchronizing on the non-final field:
private String lock = new String("lock");
synchronized(lock){
    System.out.println("locking on :"  + lock);
}
any if you write synchronized code like above in java you may get a warning "Synchronization on the non-final field"

======
It's not recommended to use String object as a lock in java synchronized block because a string is an immutable object and literal string and internally string gets stored in String pool. So by any chance if any other part of the code or any third party library used same String as there lock then they both will be locked on the same object despite being completely unrelated which could result in unexpected behavior and bad performance. Instead of String object it is advised to use new Object() for Synchronization in Java on synchronized block.
private static final String LOCK = "lock";   //not recommended
private static final Object OBJ_LOCK = new Object(); //better

======
According to the Java language specification you can not use Java synchronized keyword with constructor it’s illegal and result in compilation error. So you can not synchronize constructor in Java which seems logical because other threads cannot see the object being created until the thread creating it has finished it. You cannot apply java synchronized keyword with variables and can not use java volatile keyword with the method.

======
public class Counter{
  private static int count = 0;
  public static synchronized int getCount(){
    return count;
  }
  public synchronized setCount(int count){
     this.count = count;
  }
}
In this example of Java, the synchronization code is not properly synchronized because both getCount() and setCount() are not getting locked on the same object and can run in parallel which may result in the incorrect count. Here getCount() will lock in Counter.class object while setCount() will lock on current object (this). To make this code properly synchronized in Java you need to either make both method static or non-static or use java synchronized block instead of java synchronized method. 

======
Java locking incorporates a form of mutual exclusion. Only one thread may hold a lock at one time. Locks are used to protect blocks of code or entire methods, but it is important to remember that it is the identity of the lock that protects a block of code, not the block itself. One lock may protect many blocks of code or methods. Conversely, just because a block of code is protected by a lock does not mean that two threads cannot execute that block at once. It only means that two threads cannot execute that block at once if they are waiting on the same lock. Locks are not per method, but per object.

======
For ordinary synchronized methods, this lock will be the object on which the method is being invoked. For static synchronized methods, this lock will be the monitor associated with the Class object in which the method is declared. Just because setXY() is declared as synchronized doesn't mean that two different threads can't still execute setXY() at the same time, as long as they are invoking setXY() on different Point instances. Only one thread can execute setXY(), or any other synchronized method of Point, on a single Point instance at one time. Because synchronization prevents multiple threads from executing a block at once, it has performance implications. It is a good practice to use synchronization around the smallest possible block of code that needs to be protected.

======
The class has also its own lock. For ex. if class has 3 objects then there are 4 locks available. 3 for the objects and 1 for the class. So if you synchronize two static methods in a single class, a thread will need the class lock to enter either of the methods.

======
Synchronization bad effects:
1) Hitting Synchronized method is a performance hit. 
2) Synchronized method can slow your program down because synchronization restricts concurrency. 
3) Synchronized methods can lead to deadlock.

======
Because synchronization carries a small performance penalty, most general-purpose classes, like the Collection classes in java.util, do not use synchronization internally. This means that classes like HashMap cannot be used from multiple threads without additional synchronization. You can use the Collections classes in a multi-threaded application by using synchronization every time you access a method in a shared collection. For any given collection, you must synchronize on the same lock each time. A common choice of lock would be the collection object itself. The example class SimpleCache shows how you can use a HashMap to provide caching in a thread-safe way. Generally, however, proper synchronization doesn't just mean synchronizing every method. The Collections class provides us with a set of convenience wrappers for the List, Map, and Set interfaces. You can wrap a Map with Collections.synchronizedMap and it will ensure that all access to that map is properly synchronized.
public class SimpleCache {
   private final Map cache = new HashMap();
   
   public Object load(String objectName) {
      // load the object somehow
   }
   
   public void clearCache() {
      synchronized (cache) {
         cache.clear();
      }
   }
   public Object getObject(String objectName) {
      synchronized (cache) {
         Object o = cache.get(objectName);
         if (o == null) {
            o = load(objectName);
            cache.put(objectName, o);
         }
      }
      return o;
   }
}

======
//deadlock
public void transferMoney(Account fromAccount, Account toAccount, DollarAmount amountToTransfer) { 
  synchronized (fromAccount) {
    synchronized (toAccount) { 
      if (fromAccount.hasSufficientBalance(amountToTransfer) { 
        fromAccount.debit(amountToTransfer); 
        toAccount.credit(amountToTransfer);
      }
    }
  }
}
transferMoney(accountOne, accountTwo, amount);
transferMoney(accountTwo, accountOne, anotherAmount);

======
Resolving this, Avoid Deadlock: One of the best ways to prevent the potential for deadlock is to avoid acquiring more than one lock at a time, which is often practical. However, if that is not possible, you need a strategy that ensures you acquire multiple locks in a consistent, defined order.

======
Volatile: Volatile is simpler than synchronization and is suitable only for controlling access to single instances of primitive variables -- integers, booleans, and so on. When a variable is declared volatile, any write to that variable will go directly to main memory, bypassing the cache. This means that all threads see the same value for a volatile variable at all times. It provides the happens-before guarantee which ensures that a write to a volatile variable will happen before any reading. This ensures that any modification to volatile object or primitive type will be visible to all threads i.e. it provides the visibility guarantee. When you make a variable volatile, running thread will not cache its value in its local stack and always refer main memory. The volatile variable also provides happens before guarantee, which can be used to synchronize values.

======
Java program to demonstrate where to use Volatile keyword in Java. In this example Singleton Instance is declared as volatile variable to ensure every thread see updated value for _instance.
public class Singleton{
private static volatile Singleton _instance; //volatile variable 
public static Singleton getInstance(){ 
   if(_instance == null){
            synchronized(Singleton.class){
              if(_instance == null)
              _instance = new Singleton();
            }
   }
   return _instance;
}
If Thread A is creating Singleton instance and just after creation lost the CPU, all other thread will not be able to see value of _instance as not null and they will believe its still null.

======
You can make an array volatile in Java, there is no problem with that, neither compiler will flag any error not JVM will throw any exception. But only changes to reference pointing to an array will be visible to all threads, not the whole array. 
protected volatile int[] primes = new int[10]; then if you assign a new array to primes variable, change will be visible to all threads, but changes to individual indices will not be covered under volatile guarantee i.e.
primes = new int[20]; will follow the "happens-before" rule and cause memory barrier refresh, but following code will not do so
primes[0] = 10;
primes[1] = 20;
primes[2] = 30;
primes[3] = 40;
This means, if multiple threads are changing individual array elements e.g. storing updates, there won’t be any happens-before guarantee provided by the volatile modifier for such modification.

======
You cannot make a member variable both final and volatile at the same time, but you can make a static variable volatile in Java.

======
Deadlock: The most common form of deadlock is when Thread 1 holds a lock on Object A and is waiting for the lock on Object B, and Thread 2 holds the lock on Object B and is waiting for the lock on Object A. Neither thread will ever acquire the second lock or relinquish the first lock. They will simply wait forever.

======
There are a few cases where you do not have to synchronize to propagate data from one thread to another, because the JVM is implicitly performing the synchronization for you.
These cases include:
• When data is initialized by a static initializer (an initializer on a static field or in a static{}block)
• When accessing final fields
• When an object is created before a thread is created
• When an object is already visible to a thread that it is then joined with
• Synchronization is needed only when there are mutable objects.

======
Following sample program incorrectly implements a simple form of the producer/consumer problem. It consists of four classes: Q, the queue that you're trying to synchronize; Producer, the threaded object that is producing queue entries; Consumer, the threaded object that is consuming queue entries; and PC, the tiny class that creates the single Q, Producer, and Consumer.

// An incorrect implementation of a producer and consumer. 
class Q { 
   int n; 
   synchronized int get() { 
      System.out.println("Got: " + n); 
      return n; 
   } 
   synchronized void put(int n) { 
	  this.n = n; 
	  System.out.println("Put: " + n); 
	} 
}
class Producer implements Runnable { 
	Q q; 
	Producer(Q q) { 
		this.q = q; 
		new Thread(this, "Producer").start(); 
   } 
   public void run() { 
      int i = 0; 
      while(true) { 
         q.put(i++); 
      } 
   } 
}
class Consumer implements Runnable { 
   Q q; 
   Consumer(Q q) { 
      this.q = q; 
      new Thread(this, "Consumer").start(); 
   } 
   public void run() { 
      while(true) { 
         q.get(); 
      } 
   } 
}
class PC { 
   public static void main(String args[]) { 
      Q q = new Q(); 
      new Producer(q); 
      new Consumer(q); 
      System.out.println("Press Control-C to stop."); 
   }  
}
Although the put( ) and get( ) methods on Q are synchronized, nothing stops the producer from overrunning the consumer, nor will anything stop the consumer from consuming the same queue value twice. Thus, you get the erroneous output shown here (the exact output will vary with processor speed and task load):
Put: 1 
Got: 1 
Got: 1 
Got: 1 
Got: 1 
Got: 1 
Put: 2 
Put: 3 
Put: 4 
Put: 5 
Put: 6 
As you can see, after the producer put 1, the consumer started and got the same 1 five times in a row. Then, the producer resumed and produced 2 through 7 without letting the consumer have a chance to consume them.

=======
The proper way to write this program in Java is to use wait( ) and notify( ) to signal in both directions, as shown here:
// A correct implementation of a producer and consumer. 
class Q { 
   int n; 
   boolean valueSet = false; 
   synchronized int get() { 
      if(!valueSet) 
         try { 
            wait(); 
         } catch(InterruptedException e) { 
            System.out.println("InterruptedException caught"); 
         } 
      System.out.println("Got: " + n); 
      valueSet = false; 
      notify(); 
      return n; 
   } 
   synchronized void put(int n) { 
      if(valueSet) 
         try { 
            wait(); 
         } catch(InterruptedException e) { 
            System.out.println("InterruptedException caught"); 
         } 
      this.n = n; 
      valueSet = true; 
      System.out.println("Put: " + n); 
      notify(); 
   } 
}
class Producer implements Runnable { 
   Q q; 
   Producer(Q q) { 
      this.q = q; 
      new Thread(this, "Producer").start(); 
   } 
   public void run() { 
      int i = 0; 
      while(true) { 
         q.put(i++); 
      } 
   } 
}
class Consumer implements Runnable { 
   Q q; 
   Consumer(Q q) { 
      this.q = q; 
      new Thread(this, "Consumer").start(); 
   } 
   public void run() { 
      while(true) { 
         q.get(); 
      } 
   } 
}
class PCFixed { 
   public static void main(String args[]) { 
      Q q = new Q(); 
      new Producer(q); 
      new Consumer(q); 
      System.out.println("Press Control-C to stop."); 
   } 
}

======
Difference between Callable and Runnable: Callable throws checked exception while Runnable does not throw checked exception. Return type of Runnable is void that is it does not return any value while Callable can return a Future object.

======
Difference between time slicing and preemptive scheduling: In preemptive scheduling the higher priority task executes until it enters the waiting or dead states or higher priority task comes into existence. In time slicing, a task runs for a predefined slice of time and then reenters the pool of ready tasks.

======
ReentrantLock is a concrete implementation of Lock interface provided in Java concurrency package from Java 1.5 onwards. As per Java-doc, ReentrantLock is mutual exclusive lock, similar to implicit locking provided by synchronized keyword in Java, with extended feature like fairness, which can be used to provide lock to longest waiting thread. synchronized keyword doesn't support fairness. Any thread can acquire lock once released, no preference can be specified, on the other hand you can make ReentrantLock fair by specifying fairness property, while creating instance of ReentrantLock. In case of synchronized keyword, a thread can be blocked waiting for lock, for an indefinite period of time and there was no way to control that. ReentrantLock provides a method called lockInterruptibly(), which can be used to interrupt thread when it is waiting for lock. Similarly tryLock() with timeout can be used to timeout if lock is not available in certain time period.

================
Blocking queue
================
It is a interface queue which is thread safe that blocks when you try to dequeue from it and the queue is empty, or if you try to enqueue items to it and the queue is already full. A thread trying to dequeue from an empty queue is blocked until some other thread inserts an item into the queue. A thread trying to enqueue an item in a full queue is blocked until some other thread makes space in the queue, either by dequeuing one or more items or clearing the queue completely. It doesn’t accept null values and throw NullPointerException if you try to store null value in the queue. Java BlockingQueue interface is part of java collections framework and it’s primarily used for implementing producer consumer problem. We don’t need to worry about waiting for the space to be available for producer or object to be available for consumer in BlockingQueue because it’s handled by implementation classes of BlockingQueue.
public class BlockingQueue {
  private List queue = new LinkedList();
  private int limit = 10;
  public BlockingQueue(int limit){
    this.limit = limit;
  }
  public synchronized void enqueue(Object item) throws InterruptedException  {
    while(this.queue.size() == this.limit) {
      wait();
    }
    if(this.queue.size() == 0) {
      notifyAll();
    }
    this.queue.add(item);
  }
  public synchronized Object dequeue() throws InterruptedException{
    while(this.queue.size() == 0){
      wait();
    }
    if(this.queue.size() == this.limit){
      notifyAll();
    }
    return this.queue.remove(0);
  }
}
http://tutorials.jenkov.com/java-concurrency/blocking-queues.html
https://www.mkyong.com/java/java-blockingqueue-examples/

=======
public class BlockingQueueExample {
    public static void main(String[] args) throws Exception {
        BlockingQueue queue = new ArrayBlockingQueue(1024);
        Producer producer = new Producer(queue);
        Consumer consumer = new Consumer(queue);
        new Thread(producer).start();
        new Thread(consumer).start();
        Thread.sleep(4000);
    }
}
Here is the Producer class. Notice how it sleeps a second between each put() call. This will cause the Consumer to block, while waiting for objects in the queue.
public class Producer implements Runnable{
    protected BlockingQueue queue = null;
    public Producer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
            queue.put("1");
            Thread.sleep(1000);
            queue.put("2");
            Thread.sleep(1000);
            queue.put("3");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
Here is the Consumer class. It just takes out the objects from the queue, and prints them to System.out.
public class Consumer implements Runnable{
    protected BlockingQueue queue = null;
    public Consumer(BlockingQueue queue) {
        this.queue = queue;
    }
    public void run() {
        try {
            System.out.println(queue.take());
            System.out.println(queue.take());
            System.out.println(queue.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

=======
Another example: https://www.codejava.net/java-core/concurrency/java-linkedblockingqueue-example

=======
The java.util.concurrent package has the following implementations of the BlockingQueue interface (in Java 6):
- ArrayBlockingQueue
- DelayQueue
- LinkedBlockingQueue
- PriorityBlockingQueue
- SynchronousQueue

=======
ArrayBlockingQueue class implements the BlockingQueue interface. ArrayBlockingQueue is a bounded, blocking queue that stores the elements internally in an array. That it is bounded means that it cannot store unlimited amounts of elements. There is an upper bound on the number of elements it can store at the same time. It stores the elements internally in FIFO (First In, First Out) order. The head of the queue is the element which has been in queue the longest time, and the tail of the queue is the element which has been in the queue the shortest time.

=======
DelayQueue class implements the BlockingQueue interface. It blocks the elements internally until a certain delay has expired. The elements must implement the interface java.util.concurrent.Delayed. Here is how the interface looks:
public interface Delayed extends Comparable {
  public long getDelay(TimeUnit timeUnit);
}
The value returned by the getDelay() method should be the delay remaining before this element can be released. If 0 or a negative value is returned, the delay will be considered expired, and the element released at the next take() etc. call on the DelayQueue. The TimeUnit instance passed to the getDelay() method is an Enum that tells which time unit the delay should be returned in. The TimeUnit enum can take these values: DAYS, HOURS, MINUTES, SECONDS, MILLISECONDS, MICROSECONDS, NANOSECONDS. The Delayed interface also extends the java.lang.Comparable interface, as you can see, which means that Delayed objects can be compared to each other. This is probably used internally in the DelayQueue to order the elements in the queue, so they are released ordered by their expiration time.
public class DelayElement implements Delayed {
  private String element;
  private long expiryTime;
  public DelayElement(String element, long delay) {
    this.element = element;
    this.expiryTime = System.currentTimeMillis() + delay;
  }
  @Override
  public long getDelay(TimeUnit timeUnit) {
    long diff = expiryTime - System.currentTimeMillis();
    return timeUnit.convert(diff, TimeUnit.MILLISECONDS);
  }
  @Override
  public int compareTo(Delayed o) {
    if (this.expiryTime < ((DelayElement) o).expiryTime) {
      return -1;
    }
    if (this.expiryTime > ((DelayElement) o).expiryTime) {
      return 1;
    }
    return 0;
  }
  @Override
  public String toString() {
    return element + ": " + expiryTime;
  }
}

=======
LinkedBlockingQueue class implements the BlockingQueue interface. The LinkedBlockingQueue keeps the elements internally in a linked structure (linked nodes). This linked structure can optionally have an upper bound if desired. If no upper bound is specified, Integer.MAX_VALUE is used as the upper bound. The LinkedBlockingQueue stores the elements internally in FIFO (First In, First Out) order. The head of the queue is the element which has been in queue the longest time, and the tail of the queue is the element which has been in the queue the shortest time.

=======
PriorityBlockingQueue class implements the BlockingQueue interface. It is an unbounded concurrent queue. It uses the same ordering rules as the java.util.PriorityQueue class. You cannot insert null into this queue. All elements inserted into the PriorityBlockingQueue must implement the java.lang.Comparable interface. The elements thus order themselves according to whatever priority you decide in your Comparable implementation. Notice that the PriorityBlockingQueue does not enforce any specific behavior for elements that have equal priority (compare() == 0).

=======
SynchronousQueue class implements the BlockingQueue interface. It is a queue that can only contain a single element internally. A thread inserting an element into the queue is blocked until another thread takes that element from the queue. Likewise, if a thread tries to take an element and no element is currently present, that thread is blocked until a thread insert an element into the queue.

=======
An atomic operation is an operation which is performed as a single unit of work without the possibility of interference from other operations. The Java language specification guarantees that reading or writing a variable is an atomic operation(unless the variable is of type long or double ). Operations variables of type long or double are only atomic if they declared with the volatile keyword. Assume i is defined as int. The i++ (increment) operation it not an atomic operation in Java. This also applies for the other numeric types, e.g. long. etc). The i++ operation first reads the value which is currently stored in i (atomic operations) and then it adds one to it (atomic operation). But between the read and the write the value of i might have changed. Since Java 1.5 the java language provides atomic variables, e.g. AtomicInteger or AtomicLong which provide methods like getAndDecrement(), getAndIncrement() and getAndSet() which are atomic.

======================
Thread pool, Executer
======================
Thread pools manage a pool of worker threads. The thread pools contains a work queue which holds tasks waiting to get executed. A thread pool can be described as a collection of Runnable objects. These threads are constantly running and are checking the work query for new work. If there is new work to be done they execute this Runnable. Thread pool is an important concept to maintain scalability, robustness, and stability of the system. The thread pool is one of essential facility any multi-threaded server side Java application requires. One example of using thread pool is creating a web server, which processes client request.

=======
Java executor framework (java.util.concurrent.Executor), released with the JDK 5 is used to run the Runnable objects without creating new threads every time and mostly re-using the already created threads.
ExecutorService executor = Executors.newFixedThreadPool(NTHREADS);
for (int i = 0; i < 500; i++) {
   Runnable worker = new MyRunnable(10000000L + i);
   executor.execute(worker);
}
// This will make the executor accept no new threads
// and finish all existing threads in the queue
executor.shutdown();
// Wait until all threads are finish
executor.awaitTermination();

=======
Even though both Thread and Executor, both are used to executed some code in parallel, there are some key differences between them. The main difference between a Thread and an Executor in Java is that later provides a thread pool in Java. Since creating, starting, and running a thread is a time-consuming and expensive operation includes memory overhead, many Java applications create a pool of thread at start-up and leverage that for executing the task in parallel until Java introduced the built-in thread pool. This thread-pool is known as Executor framework which relieved Java application developers from the responsibility of creating and managing threads. java.lang.Thread is a class in Java while java.util.concurrent.Executor is an interface. A Thread can only execute one Runnable task but an Executor can execute any number of Runnable task. In the case of a thread, it's developer's responsibility to create and start the thread, but in the case of Executor, the framework will create and start threads for you.

=======
Executor framework is a combination of Executor, Executors, and ExecutorService interface
- Executor — Used to submit a new task.
- ExecutorService — A sub-interface of Executor that adds methods to manage lifecycle of threads used to run the submitted tasks and methods to produce a Future to get a result from an asynchronous computation.
- ScheduledExecutorService — A sub-interface of ExecutorService, to execute commands periodically or after a given delay.

=======
Executor framework also provides a static utility class called Executors ( similar to Collections) which provides several static factory method to create various type of Thread Pool implementation in Java e.g. fixed size thread pool, cached thread pool and scheduled thread pool. Runnable and Callable interface are used to represent task executed by worker thread managed in these Thread pools. 

=======
Executor to remove the manual creation of threads to execute a command
Thread t = new Thread(new MyRunnable());
t.start();
can be replaced with the following code that instead uses an Executor:
Executor executor = ... // Executor creation
executor.execute(new MyRunnable());

=======
ExecutorService adds a more useful and advanced version method to execute commands, submit. Passing a Callable to the submit method is possible to get a Future object and use it to retrieve the result of the asynchronous computation. Additionally it is possible to shutdown an ExecutorService rejecting submissions of new commands. Using the shutdown method all submitted commands will be executed before stopping the ExecutorService, but no new command is accepted. A call to shutdownNow prevents waiting tasks to be executed and try to stop all currently executing commands.
Runnable myCommand1 = ...
Callable<String> myCommand2 = ...
ExecutorService executorService = ... // Build an executorService
executorService.submit(myCommand1);
// submit Accepts also a Callable
Future<String> resultFromMyCommand2 = executorService.submit(myCommand2);   
// Will wait for myCommand1 and myCommand2 termination
executorService.shutdown();  
Runnable myCommand3 = ...;
// Will throw a RejectedExecutionException because no new task can be submitted
executorService.submit(myCommand3);

=======
The ScheduledExecutorService is used to schedule command executions after a given delay or periodically, it must be used as a replacement for Timer and TimerTask. It uses the method schedule to run the command after a given delay of time, scheduleAtFixedRate and scheduleWithFixedDelay are used to execute a task periodically:
ScheduledExecutorService executor = ...;
Runnable command1 = ...;
Runnable command2 = ...;
Runnable command3 = ...;
// Will start command1 after 50 seconds
executor.schedule(command1, 50L, TimeUnit.SECONDS);
// Will start command 2 after 20 seconds, 25 seconds, 30 seconds ...
executor.scheduleAtFixedRate(command2, 20L, 5L, TimeUnit.SECONDS);
// Will start command 3 after 10 seconds and if command3 takes 2 seconds to be
// executed also after 17, 24, 31, 38 seconds...
executor.scheduleWithFixedDelay(command3, 10L, 5L, TimeUnit.SECONDS);

=======
How to Create an Executor: To create an Executor it is possible to use the factory Executors class. Most common methods are used to create:
1) an ExecutorService with a single thread to execute commands with method newSingleThreadExecutor. ExecutorService singleExecutorService = Executors.newSingleThreadExecutor();
2) a ScheduledExecutorService with a single thread to execute commands with the method newSingleThreadScheduledExecutor. ScheduledExecutorService singleScheduledExecutorService = Executors.newSingleThreadScheduledExecutor();
3) an ExecutorService that use a fixed length pool of threads to execute commands with the method newFixedThreadPool. ExecutorService fixedExecutorService = Executors.newFixedThreadPool(10);
4) an ExecutorService with a pool of threads that creates a new thread if no thread is available and reuse an existing thread if they are available with newCachedThreadPool. ExecutorService onDemandExecutorService = Executors.newCachedThreadPool();
5) a ScheduledExecutorService with a fixed length pool of threads to execute scheduled commands with the method newScheduledThreadPool. ScheduledExecutorService fixedScheduledExecutorService = Executors.newScheduledThreadPool(5);

=======
1) One of the key difference between Executor and ExecutorService interface is that former is a parent interface while ExecutorService extends Executor i.e. it's a sub-interface of Executor.
2) Another important difference between ExecutorService and Executor is that Executor defines execute() method which accepts an object of the Runnable interface, while submit() method can accept objects of both Runnable and Callable interfaces.
3) execute() method doesn't return any result, its return type is void but submit() method returns the result of computation via a Future object.
4) Apart from allowing a client to submit a task, ExecutorService also provides methods to control the thread pool e.g. terminate the thread pool by calling the shutDown() method. 
5) Executors class provides factory methods to create different kinds of thread pools e.g. newSingleThreadExecutor() creates a thread pool of just one thread, newFixedThreadPool(int numOfThreads) creates a thread pool of fixed number of threads and newCachedThreadPool() creates new threads when needed but reuse the existing threads if they are available.

=======
The Future object provides the facility of asynchronous execution, which means you don't need to wait until the execution finishes, you can just submit the task and go around, come back and check if Future object has the result, if execution is completed then it would have result which you can access by using the Future.get() method.

=======
Drawbacks with Futures and Callable: The Future interface is limited as a model of asynchronously executed tasks. Future allows a client to query a Callable task for its result. It does not provide the option to register a callback method. A callback method would allow you to get a callback once a task is done. In Java 5 you could use ExecutorCompletionService for this purpose but as of Java 8 you can use the CompletableFuture interface which allows to provide a callback interface which is called once a task is completed.

=======
CompletableFuture: Asynchronous task handling is important for any application which performs time consuming activities, as IO operations. Two basic approaches to asynchronous task handling are available to a Java application:
1) application logic blocks until a task completes
2) application logic is called once the task completes, this is called a nonblocking approach.

CompletableFuture extends the functionality of the Future interface for asynchronous calls. It also implements the CompletionStage interface. CompletionStage offers methods, that let you attach callbacks that will be executed on completion. It adds standard techniques for executing application code when a task completes, including various ways to combine tasks. CompletableFuture support both blocking and nonblocking approaches, including regular callbacks. This callback can be executed in another thread as the thread in which the CompletableFuture is executed. The following example demonstrates how to create a basic CompletableFuture.

CompletableFuture.supplyAsync(this::doSomething); CompletableFuture.supplyAsync runs the task asynchronously on the default thread pool of Java. It has the option to supply your custom executor to define the ThreadPool.
public class CompletableFutureCallback {
    public static void main(String[] args) {
        long started = System.currentTimeMillis();
        CompletableFuture<String>  data = createCompletableFuture()
                .thenApply((Integer count) -> {
                    int transformedValue = count * 10;
                    return transformedValue;
                }).thenApply(transformed -> "Finally creates a string: " + transformed);
            try {
                System.out.println(data.get());
            } catch (InterruptedException | ExecutionException e) {

            }
    }
    public static CompletableFuture<Integer> createCompletableFuture() {
        CompletableFuture<Integer>  result = CompletableFuture.supplyAsync(() -> {
            try {
                // simulate long running task
                Thread.sleep(5000);
            } catch (InterruptedException e) { }
            return 20;
        });
        return result;
    }
}
========
execute(Runnable) method takes a java.lang.Runnable object, and executes it asynchronously. Here is an example of executing a Runnable with an ExecutorService:
ExecutorService executorService = Executors.newSingleThreadExecutor();
executorService.execute(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});
executorService.shutdown(); There is no way of obtaining the result of the executed Runnable, if necessary. You will have to use a Callable for that 

========
submit(Runnable) method also takes a Runnable implementation, but returns a Future object. This Future object can be used to check if the Runnable as finished executing.
Here is a ExecutorService submit() example:
Future future = executorService.submit(new Runnable() {
    public void run() {
        System.out.println("Asynchronous task");
    }
});
future.get();  //returns null if the task has finished correctly.

========
submit(Callable) method is similar to the submit(Runnable) method except for the type of parameter it takes. The Callable instance is very similar to a Runnable except that its call() method can return a result. The Runnable.run() method cannot return a result. The Callable's result can be obtained via the Future object returned by the submit(Callable) method. Here is an ExecutorService Callable example:
Future future = executorService.submit(new Callable(){
    public Object call() throws Exception {
        System.out.println("Asynchronous Callable");
        return "Callable Result";
    }
});
System.out.println("future.get() = " + future.get());
The above code example will output this:
Asynchronous Callable
future.get() = Callable Result

========
invokeAny() method takes a collection of Callable objects, or sub-interfaces of Callable. Invoking this method does not return a Future, but returns the result of one of the Callable objects. You have no guarantee about which of the Callable's results you get. Just one of the ones that finish. If one of the tasks complete (or throws an exception), the rest of the Callable's are canceled. Here is a code example:

ExecutorService executorService = Executors.newSingleThreadExecutor();
Set<Callable<String>> callables = new HashSet<Callable<String>>();
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 1";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 2";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 3";
    }
});
String result = executorService.invokeAny(callables);
System.out.println("result = " + result);
executorService.shutdown();
This code example will print out the object returned by one of the Callable's in the given collection. Run it a few times, and the result changes. Sometimes it is "Task 1", sometimes "Task 2" etc.

=======
invokeAll() method invokes all of the Callable objects you pass to it in the collection passed as parameter. The invokeAll() returns a list of Future objects via which you can obtain the results of the executions of each Callable. Keep in mind that a task might finish due to an exception, so it may not have "succeeded". There is no way on a Future to tell the difference. Here is a code example:

ExecutorService executorService = Executors.newSingleThreadExecutor();
Set<Callable<String>> callables = new HashSet<Callable<String>>();
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 1";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 2";
    }
});
callables.add(new Callable<String>() {
    public String call() throws Exception {
        return "Task 3";
    }
});
List<Future<String>> futures = executorService.invokeAll(callables);
for(Future<String> future : futures){
    System.out.println("future.get = " + future.get());
}
executorService.shutdown();

======
ExecutorService Shutdown: When you are done using the ExecutorService you should shut it down, so the threads do not keep running. For instance, if your application is started via a main() method and your main thread exits your application, the application will keep running if you have an active ExexutorService in your application. The active threads inside this ExecutorService prevents the JVM from shutting down. To terminate the threads inside the ExecutorService you call its shutdown() method. The ExecutorService will not shut down immediately, but it will no longer accept new tasks, and once all threads have finished current tasks, the ExecutorService shuts down. All tasks submitted to the ExecutorService before shutdown() is called, are executed. If you want to shut down the ExecutorService immediately, you can call the shutdownNow() method. This will attempt to stop all executing tasks right away, and skips all submitted but non-processed tasks. There are no guarantees given about the executing tasks. Perhaps they stop, perhaps the execute until the end. It is a best effort attempt.

======
How do you ensure that N thread can access N resources without deadlock: The key point here is ordering, if you acquire resources in a particular order and release resources in the reverse order you can prevent deadlock.

======
Thread pool: Thread Pools are useful when you need to limit the number of threads running in your application at the same time. There is a performance overhead associated with starting a new thread, and each thread is also allocated some memory for its stack etc. Instead of starting a new thread for every task to execute concurrently, the task can be passed to a thread pool. As soon as the pool has any idle threads the task is assigned to one of them and executed. Internally the tasks are inserted into a Blocking Queue which the threads in the pool are dequeuing from. When a new task is inserted into the queue one of the idle threads will dequeue it successfully and execute it. The rest of the idle threads in the pool will be blocked waiting to dequeue tasks. Thread pools are often used in multi threaded servers. Each connection arriving at the server via the network is wrapped as a task and passed on to a thread pool. The threads in the thread pool will process the requests on the connections concurrently.

======
Implementation of thread pool: Thread pool represents a group of worker threads which execute tasks, each thread can be reused many times. If a new task is submitted when all threads are active, they will wait in the queue until a thread is available. Thread pool implementation internally uses LinkedBlockingQueue for adding and removing tasks to the queue.
public class ThreadPool {
    private final int nThreads;
    private final PoolWorker[] threads;
    private final LinkedBlockingQueue queue;
    public ThreadPool(int nThreads) {
        this.nThreads = nThreads;
        queue = new LinkedBlockingQueue();
        threads = new PoolWorker[nThreads];
        for (int i = 0; i < nThreads; i++) {
            threads[i] = new PoolWorker();
            threads[i].start();
        }
    }
    public void execute(Runnable task) {
        synchronized (queue) {
            queue.add(task);
            queue.notify();
        }
    }
    private class PoolWorker extends Thread {
        public void run() {
            Runnable task;
            while (true) {
                synchronized (queue) {
                    while (queue.isEmpty()) {
                        try {
                            queue.wait();
                        } catch (InterruptedException e) {
                            System.out.println("An error occurred while queue is waiting: " + e.getMessage());
                        }
                    }
                    task = queue.poll();
                }
                // If we don't catch RuntimeException, the pool could leak threads
                try {
                    task.run();
                } catch (RuntimeException e) {
                    System.out.println("Thread pool is interrupted due to an issue: " + e.getMessage());
                }
            }
        }
    }
}
public class Task implements Runnable {
    private int num;
    public Task(int n) {
        num = n;
    }
    public void run() {
        System.out.println("Task " + num + " is running.");
    }
}
public class Main {
    public static void main(String[] args) {
        ThreadPool pool = new ThreadPool(7);
        for (int i = 0; i < 5; i++) {
            Task task = new Task(i);
            pool.execute(task);
        }
}
We used notify() instead of notifyAll(). Because notify() has more desirable performance characteristics than notifyAll(); in particular, notify() causes many fewer context switches, which is important in a server application.

======
URBANCLAP
What is the difference Thread and Runnable
- Thread: Each thread creates a unique object and gets associated with it. As each thread create a unique object, more memory required. In Java, multiple inheritance not allowed hence, after a class extends Thread class, it can not extend any other class.
- Runnable: Multiple threads share the same objects. As multiple threads share the same object less memory is used. If a class define thread implementing the Runnable interface it has a chance of extending one class.
