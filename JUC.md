# JUC原子类 #
---
目的是对相应的数据进行原子操作**（原子操作，是指操作过程不会被中断，保证数据操作是以原子方式进行的）**

1. 基本类型: AtomicInteger, AtomicLong, AtomicBoolean ;
2. 数组类型: AtomicIntegerArray, AtomicLongArray, AtomicReferenceArray ;
3. 引用类型: AtomicReference, AtomicStampedReference, AtomicMarkableReference ;
4. 对象的属性修改类型: AtomicIntegerFieldUpdater, AtomicLongFieldUpdater, AtomicReferenceFieldUpdater 。

---
### AtomicLong ###

**作用：**在32位操作系统中，64位的 **long** 和 **double** 变量由于会被JVM当作两个分离的32位来进行操作，所以不具有原子性。而使用AtomicLong能让long的操作保持原子性。


### AtomicLongArray ###

**作用：**对长整形数组进行原子操作。


### AtomicReference ###


**作用：**对 “对象” 进行原子操作。

1. value是volatile类型。这保证了：当某线程修改value的值时，其他线程看到的value值都是最新的value值，即修改之后的volatile的值。
2. 通过CAS设置value。这保证了：当某线程池通过CAS函数(如compareAndSet函数)设置value时，它的操作是原子的，即线程在操作value时不会被中断。


### AtomicLongFieldUpdater ###

**作用：**可以对指定类的 'volatile long' 类型的成员进行原子更新，基于反射原理实现（由于需求的更改，原子性需要在后面加入，类似于我不要求你这整个类操作具有原子性，我只要求你里面一个字段操作具有原子性）

 newUpdater()作用是获取一个AtomicIntegerFieldUpdater类型的对象，它实际上返回的是**CASUpdater对象**，或者**LockedUpdater对象**

    public static <U> AtomicLongFieldUpdater<U> newUpdater(Class<U> tclass, String fieldName) {
     Class<?> caller = Reflection.getCallerClass();
     if (AtomicLong.VM_SUPPORTS_LONG_CAS)
        return new CASUpdater<U>(tclass, fieldName, caller);
    else
        return new LockedUpdater<U>(tclass, fieldName, caller);
    }

 
---
---
# JUC锁 #
---
**同步锁：** 通过synchronized关键字来进行同步，实现对竞争资源的互斥访问的锁。

原理： 对于每一个对象，有且仅有一个同步锁；不同的线程能共同访问该同步锁；但是，在同一个时间点，该同步锁能且只能被一个线程获取到。这样，获取到同步锁的线程就能在CPU上执行；而没有获取到同步锁的线程，必须进行等待，直到获取到同步锁之后才能继续运行。

**JUC包中的锁：**为锁提供了一个框架，功能更强大，用法难。

包括：Lock接口，ReadWriteLock接口，LockSupport阻塞原语，Condition条件，AbstractOwnableSynchronizer/AbstractQueuedSynchronizer/AbstractQueuedLongSynchronizer三个抽象类，ReentrantLock独占锁，ReentrantReadWriteLock读写锁。和通过AQS实现的CountDownLatch，CyclicBarrier和Semaphore。


**锁的架构图**

![](https://i.imgur.com/dTU4rxA.jpg)

---
### 互斥锁ReentrantLock ###

ReentrantLock是独占锁，同一个时间点只能被一个线程锁获取到，可以被单个线程多次获取。ReentrantLock分为 **公平锁** 和 **非公平锁** ，区别体现在获取锁的机制上是否公平，ReentraantLock是通过一个FIFO的等待队列来管理获取该锁所有线程的。公平锁下线程依次排队获取锁， 非公平锁不管自己是不是在队列开头都会获取锁。 **其默认为非公平锁**

由类图可知，ReentrantLock与sync是组合关系，ReentrantLock到底是公平锁还是非公平锁，取决于sync对象是"FairSync的实例"还是"NonFairSync的实例"。

类Sync的源码继承于AQS，其包含在ReentrantLock.java中。

**类图**

  ![](https://i.imgur.com/yMB43eW.jpg)

**源码：**

## 公平锁获取锁 ##

* lock()  在ReentrantLock.java的FairSync类中实现

        final void lock() {
         acquire(1);   //锁处于可获取状态时，它的状态值是0；锁被线程初次获取到了，它的状态值就变成了1
                       //再次获取锁时，将锁的状态值设为2；依次类推...
        }
    
* acquire() 在AQS中实现的

        public final void acquire(int arg) {
          if (!tryAcquire(arg) &&
             acquireQueued(addWaiter(Node.EXCLUSIVE), arg))     
		  
		  //1.首先通过tryAcquire()尝试获取锁
          //2.尝试失败的情况下，通过addWaiter(Node.EXCLUSIVE)来将“当前线程”加入到"CLH队列(非阻塞的FIFO队列)"末尾
          //3.调用acquireQueued()，根据公平性原则来获取锁
		  //4.如果“当前线程”在休眠等待过程中被中断过,会调用selfInterrupt()来自己给自己产生一个中断
             
			selfInterrupt();
        }

* ###　**tryAcquire()** 在ReentrantLock.java的FairSync类中实现

    	protected final boolean tryAcquire(int acquires) {
	    // 获取“当前线程”
	    	final Thread current = Thread.currentThread();
	    // 获取“独占锁”的状态
	    	int c = getState();
	    // c=0意味着“锁没有被任何线程锁拥有”，
	    	if (c == 0) {
	    // 若“锁没有被任何线程锁拥有”，
	    // 则判断“当前线程”是不是CLH队列中的第一个线程线程，
	    // 若是的话，则获取该锁，设置锁的状态，并且设置锁的拥有者为“当前线程”。
	    		if (!hasQueuedPredecessors() &&
	    			compareAndSetState(0, acquires)) {
	    			setExclusiveOwnerThread(current);
	   			 	return true;
	      }
	    }
	        else if (current == getExclusiveOwnerThread()) {
	    // 如果“独占锁”的拥有者已经为“当前线程”，
	    // 则将更新锁的状态。
	    			int nextc = c + acquires;
	    			if (nextc < 0)
	   					 throw new Error("Maximum lock count exceeded");
	    			setState(nextc);
	   				 return true;
	    		}
	   		return false;
	    }
    
* hasQueuedPredecessors() 在AQS中实现

    	public final boolean hasQueuedPredecessors() {
    		Node t = tail; 
    		Node h = head;
    		Node s;
    		return h != t &&
    			((s = h.next) == null || s.thread != Thread.currentThread());
    	}


* Node 是CLH队列的节点，在AQS中实现

		private transient volatile Node head;    // CLH队列的队首
		private transient volatile Node tail;    // CLH队列的队尾
		
		// CLH队列的节点
		static final class Node {
		    static final Node SHARED = new Node();
		    static final Node EXCLUSIVE = null;
		
		    // 线程已被取消，对应的waitStatus的值
		    static final int CANCELLED =  1;
		    // “当前线程的后继线程需要被unpark(唤醒)”，对应的waitStatus的值。
		    // 一般发生情况是：当前线程的后继线程处于阻塞状态，而当前线程被release或cancel掉，因此需要唤醒当前线程的后继线程。
		    static final int SIGNAL    = -1;
		    // 线程(处在Condition休眠状态)在等待Condition唤醒，对应的waitStatus的值
		    static final int CONDITION = -2;
		    // (共享锁)其它线程获取到“共享锁”，对应的waitStatus的值
		    static final int PROPAGATE = -3;
		
		    // waitStatus为“CANCELLED, SIGNAL, CONDITION, PROPAGATE”时分别表示不同状态，
		    // 若waitStatus=0，则意味着当前线程不属于上面的任何一种状态。
		    volatile int waitStatus;
		
		    // 前一节点
		    volatile Node prev;
		
		    // 后一节点
		    volatile Node next;
		
		    // 节点所对应的线程
		    volatile Thread thread;
		
		    // nextWaiter是“区别当前CLH队列是 ‘独占锁’队列 还是 ‘共享锁’队列 的标记”
		    // 若nextWaiter=SHARED，则CLH队列是“独占锁”队列；
		    // 若nextWaiter=EXCLUSIVE，(即nextWaiter=null)，则CLH队列是“共享锁”队列。
		    Node nextWaiter;
		
		    // “共享锁”则返回true，“独占锁”则返回false。
		    final boolean isShared() {
		        return nextWaiter == SHARED;
		    }
		
		    // 返回前一节点
		    final Node predecessor() throws NullPointerException {
		        Node p = prev;
		        if (p == null)
		            throw new NullPointerException();
		        else
		            return p;
		    }
		
		    Node() {    // Used to establish initial head or SHARED marker
		    }
		
		    // 构造函数。thread是节点所对应的线程，mode是用来表示thread的锁是“独占锁”还是“共享锁”。
		    Node(Thread thread, Node mode) {     // Used by addWaiter
		        this.nextWaiter = mode;
		        this.thread = thread;
		    }
		
		    // 构造函数。thread是节点所对应的线程，waitStatus是线程的等待状态。
		    Node(Thread thread, int waitStatus) { // Used by Condition
		        this.waitStatus = waitStatus;
		        this.thread = thread;
		    }
		}
* compareAndSetState() 在AQS中实现

       	protected final boolean compareAndSetState(int expect, int update) {
    		return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
		}
* setExclusiveOwnerThread() 在AbstractOwnableSynchronizer.java中实现

		// exclusiveOwnerThread是当前拥有“独占锁”的线程
		private transient Thread exclusiveOwnerThread;
		protected final void setExclusiveOwnerThread(Thread t) {
		    exclusiveOwnerThread = t;
		}
* getState()和setState() 都在AQS中实现

		// 锁的状态
		private volatile int state;
		// 设置锁的状态
		protected final void setState(int newState) {
		    state = newState;
		}
		// 获取锁的状态
		protected final int getState() {
		    return state;
		}

* ### **addWaiter()**  在AQS中实现

		private Node addWaiter(Node mode) {
		    // 新建一个Node节点，节点对应的线程是“当前线程”，“当前线程”的锁的模型是mode。
		    Node node = new Node(Thread.currentThread(), mode);
		    Node pred = tail;
		    // 若CLH队列不为空，则将“当前线程”添加到CLH队列末尾
		    if (pred != null) {
		        node.prev = pred;
		        if (compareAndSetTail(pred, node)) {
		            pred.next = node;
		            return node;
		        }
		    }
		    // 若CLH队列为空，则调用enq()新建CLH队列，然后再将“当前线程”添加到CLH队列中。
		    enq(node);
		    return node;
		}
* compareAndSetTail() 在AQS中实现

		private final boolean compareAndSetTail(Node expect, Node update) {
		    return unsafe.compareAndSwapObject(this, tailOffset, expect, update);
		}
* enq() 在AQS中实现

		private Node enq(final Node node) {
		    for (;;) {
		        Node t = tail;
		        if (t == null) { // Must initialize
		            if (compareAndSetHead(new Node()))
		                tail = head;
		        } else {
		            node.prev = t;
		            if (compareAndSetTail(t, node)) {
		                t.next = node;
		                return t;
		            }
		        }
		    }
		}

* ### acquireQueued() 在AQS中实现
		
		final boolean acquireQueued(final Node node, int arg) {
		    boolean failed = true;
		    try {
		        // interrupted表示在CLH队列的调度中，
		        // “当前线程”在休眠时，有没有被中断过。
		        boolean interrupted = false;
		        for (;;) {
		            // 获取上一个节点。
		            // node是“当前线程”对应的节点，这里就意味着“获取上一个等待锁的线程”。
		            final Node p = node.predecessor();
		            if (p == head && tryAcquire(arg)) {
		                setHead(node);
		                p.next = null; // help GC
		                failed = false;
		                return interrupted;
		            }
		            if (shouldParkAfterFailedAcquire(p, node) &&
		                parkAndCheckInterrupt())
		                interrupted = true;
		        }
		    } finally {
		        if (failed)
		            cancelAcquire(node);
		    }
		}

*  shouldParkAfterFailedAcquire() 在AQS中实现

		// 返回“当前线程是否应该阻塞”
		private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
		    // 前继节点的状态
		    int ws = pred.waitStatus;
		    // 如果前继节点是SIGNAL状态，则意味这当前线程需要被unpark唤醒。此时，返回true。
		    if (ws == Node.SIGNAL)
		        return true;
		    // 如果前继节点是“取消”状态，则设置 “当前节点”的 “当前前继节点”  为  “‘原前继节点’的前继节点”。
		    if (ws > 0) {
		        do {
		            node.prev = pred = pred.prev;
		        } while (pred.waitStatus > 0);
		        pred.next = node;
		    } else {
		        // 如果前继节点为“0”或者“共享锁”状态，则设置前继节点为SIGNAL状态。
		        compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
		    }
		    return false;
		}
*  parkAndCheckInterrupt() 在AQS中实现

		private final boolean parkAndCheckInterrupt() {
		    // 通过LockSupport的park()阻塞“当前线程”。
		    LockSupport.park(this);
		    // 返回线程的中断状态。
		    return Thread.interrupted();
		}

* ### selfInterrupt() 在AQS中实现

		private static void selfInterrupt() {
		    Thread.currentThread().interrupt();
		}


## 公平锁释放锁 ##

* unlock() 在ReentrantLock.java中实现的

		public void unlock() {
		    sync.release(1);
		}

* release() 在AQS中实现的

		public final boolean release(int arg) {
		    if (tryRelease(arg)) {
		        Node h = head;
		        if (h != null && h.waitStatus != 0)
		            unparkSuccessor(h);
		        return true;
		    }
		    return false;
		}

* ###tryRelease() 在ReentrantLock.java的Sync类中实现

		protected final boolean tryRelease(int releases) {
		    // c是本次释放锁之后的状态
		    int c = getState() - releases;
		    // 如果“当前线程”不是“锁的持有者”，则抛出异常！
		    if (Thread.currentThread() != getExclusiveOwnerThread())
		        throw new IllegalMonitorStateException();
		
		    boolean free = false;
		    // 如果“锁”已经被当前线程彻底释放，则设置“锁”的持有者为null，即锁是可获取状态。
		    if (c == 0) {
		        free = true;
		        setExclusiveOwnerThread(null);
		    }
		    // 设置当前线程的锁的状态。
		    setState(c);
		    return free;
		}

* ### unparkSuccessor() 在AQS中实现 

		private void unparkSuccessor(Node node) {
		    // 获取当前线程的状态
		    int ws = node.waitStatus;
		    // 如果状态<0，则设置状态=0
		    if (ws < 0)
		        compareAndSetWaitStatus(node, ws, 0);
		
		    //获取当前节点的“有效的后继节点”，无效的话，则通过for循环进行获取。
		    // 这里的有效，是指“后继节点对应的线程状态<=0”
		    Node s = node.next;
		    if (s == null || s.waitStatus > 0) {
		        s = null;
		        for (Node t = tail; t != null && t != node; t = t.prev)
		            if (t.waitStatus <= 0)
		                s = t;
		    }
		    // 唤醒“后继节点对应的线程”
		    if (s != null)
		        LockSupport.unpark(s.thread);
		}





















----------

### 共享锁ReentrantReadWriteLock ###

ReentrantReadWriteLock是读写锁接口ReadWriteLock的实现类，它包括子类ReadLock和WriteLock。ReadtLock是共享锁，而WriteLock是独占锁

**类图**

![](https://i.imgur.com/YsIWEBt.jpg)


----------

### Condition和LockSupport ###








----------

### CountDownLatch ###

CountDownLatch是一个同步辅助类，在完成一组正在其他线程中执行的操作之前，它允许一个或多个线程一直等待。

**类图**

![](https://i.imgur.com/VrsjXO3.jpg)


----------
### CyclicBarrier ###

CyclicBarrier是一个同步辅助类，允许一组线程互相等待，直到到达某个公共屏障点。该 barrier 在释放等待线程后可以重用，所以称它为循环 的 barrier。

**类图**

![](https://i.imgur.com/8D8jw8t.jpg)

----------
### Semaphore ###

Semaphore是一个计数信号量，维护了一个信号量许可集，线程可以通过调用acquire()来获取信号量的许可；当信号量中有可用的许可时，线程能获取该许可；否则线程必须等待，直到有可用的许可为止。线程可以通过release()来释放它所持有的信号量许可。

**类图**

![](https://i.imgur.com/lFx1JBD.jpg)
























---
---
# JUC集合 #
---
java集合包大多是 **非线程安全的** ,虽然可以通过Collections工具类中的方法获取java集合包对应的同步类，但是这些同步类的并发效率并不是很高。所以JUC包中添加了Java集合包中单线程类所对应的支持高并发的类。

## **List和Set**

![](https://i.imgur.com/UB5K5Sp.jpg)

**CopyOnWriteArrayList** 相当于线程安全的ArrayList，它实现了List接口

**CopyOnWriteArraySet**相当于线程安全的HashSet,它继承于AbstractSet类，CopyOnWriteArraySet内部包含一个CopyOnWriteArrayList对象

## **Map**

![](https://i.imgur.com/qI9uvG7.jpg)

**ConcurrentHashMap**是线程安全的HashMap，通过“锁分段”来实现的，它支持并发。

**ConcurrentSkipListMap**是线程安全的TreeMap，通过“跳表”来实现的，它支持并发。

**ConcurrentSkipListSet**是线程安全的TreeSet，通过ConcurrentSkipListMap实现的，它支持并发

## **Queue**

![](https://i.imgur.com/9JjYura.jpg)

**ArrayBlockingQueue**是数组实现的线程安全的有界的阻塞队列

**LinkedBlockingQueue**是单向链表实现的(指定大小)阻塞队列，按 FIFO（先进先出）排序元素

**LinkedBlockingDeque**是双向链表实现的(指定大小)双向并发阻塞队列，该阻塞队列同时支持FIFO和FILO两种操作方式

**ConcurrentLinkedQueue**是单向链表实现的无界队列，该队列按 FIFO（先进先出）排序元素

**ConcurrentLinkedDeque**是双向链表实现的无界队列，该队列同时支持FIFO和FILO两种操作方式















---
---
# JUC线程池 #
---
**线程池架构图**

![](https://i.imgur.com/pdLHh5y.jpg)

* **Executor**

是"执行者"接口，提供了execute()接口来执行已提交的 Runnable 任务的对象，目的是提供一种将"任务提交"与"任务如何运行"分离开来的机制

	void execute(Runnable command)


* **ExecutorService**

是"执行者服务"接口，提供了各种为执行者接口服务的方法接口，比如将任务提交给执行者的接口(submit方法)，让执行者执行任务(invokeAll, invokeAny方法)等

* **AbstractExecutorService**

实现了ExecutorService接口的抽象类

* **ThreadPoolExecutor**

核心：**线程池**

* **ScheduledExecutorService**

相当于提供了"延时"和"周期执行"功能的ExecutorService，提供了相应的函数接口，可以安排任务在给定的延迟后执行，也可以让任务周期执行。

* **ScheduledThreadPoolExecutor**

相当于提供了"延时"和"周期执行"功能的ThreadPoolExecutor

* **Executors**

Executors是个静态工厂类。它通过静态工厂方法返回ExecutorService、ScheduledExecutorService、ThreadFactory 和 Callable 等类的对象

---
## 线程池的原理 ##

**1.线程池：**

存放一定数量线程的一个线程集合。线程池允许若干线程同时运行，允许同时运行的线程数量就是线程池的容量；当添加的到线程池中的线程超过它的容量时，会有一部分线程阻塞等待。线程池会通过相应的调度策略和拒绝策略，对添加到线程池中的线程进行管理。

**2.线程池的数据结构：**

![](https://i.imgur.com/WlLikM9.jpg)

	// 阻塞队列
	private final BlockingQueue<Runnable> workQueue;
	// 互斥锁
	private final ReentrantLock mainLock = new ReentrantLock();
	// 线程集合。一个Worker对应一个线程。
	private final HashSet<Worker> workers = new HashSet<Worker>();
	// “终止条件”，与“mainLock”绑定。
	private final Condition termination = mainLock.newCondition();
	// 线程池中线程数量曾经达到过的最大值。
	private int largestPoolSize;
	// 已完成任务数量
	private long completedTaskCount;
	// ThreadFactory对象，用于创建线程。
	private volatile ThreadFactory threadFactory;
	// 拒绝策略的处理句柄。
	private volatile RejectedExecutionHandler handler;
	// 保持线程存活时间。
	private volatile long keepAliveTime;
	
	private volatile boolean allowCoreThreadTimeOut;
	// 核心池大小
	private volatile int corePoolSize;
	// 最大池大小
	private volatile int maximumPoolSize;

* **workers**

workers是HashSet<Work>类型，线程池通过workers包含了 **一个线程集合**。

运行过程：Worker对应的线程池启动时，它会执行线程池中的任务；当执行完一个任务后，它会从线程池的阻塞队列中取出一个阻塞的任务来继续运行。

作用：线程池通过它实现了允许多个线程同时运行。

* **workQueue**

workQueue是BlockingQueue类型，它是一个阻塞队列。

运行过程：当线程池中的线程数超过它的容量的时候，线程会进入阻塞队列进行阻塞等待。

作用：线程池通过它实现了阻塞功能。

* **mainLock**

mainLock是互斥锁

作用：实现了对线程池的互斥访问

* **corePoolSize 和 maximumPoolSize**

corePoolSize是"核心池大小"，maximumPoolSize是"最大池大小"

运行过程：新任务提交给线程池时（通过execute方法），如果线程池中运行的线程数量< corePoolSize，则创建新线程来处理请求；如果线程池中运行的线程数量> corePoolSize，但是却< maximumPoolSize，则仅当阻塞队列满时才创建新线程。

作用：调整线程池中实际运行的线程的数量。

* **poolSize**

poolSize是当前线程池的实际大小，即线程池中任务的数量。

* **allowCoreThreadTimeOut 和 keepAliveTime**

allowCoreThreadTimeOut表示是否允许 **线程在空闲状态时，仍然能够存活**；keepAliveTime是当线程处于空闲状态的时候，超过keepAliveTime时间之后，空闲的线程会被终止。

作用：对于空闲状态下的线程的处理

* **threadFactory**

threadFactory是ThreadFactory对象，是一个线程工厂类。

作用：线程池通过ThreadFactory创建线程

* **handler**

handler是RejectedExecutionHandler类型，是 **线程池拒绝策略** 的句柄。

作用：当某任务添加到线程池中，而线程池拒绝该任务时，线程池会通过handler进行相应的处理

<font color='red'>**总结：**</font>
线程池通过workers来管理"线程集合"，每个线程在启动后，会执行线程池中的任务；当一个任务执行完后，它会从线程池的阻塞队列中取出任务来继续运行。阻塞队列是管理线程池任务的队列，当添加到线程池中的任务超过线程池的容量时，该任务就会进入阻塞队列进行等待。

**3.线程池调度：**

图1

![](https://i.imgur.com/gO5rCdq.jpg)

图2

![](https://i.imgur.com/WF61GP9.jpg)


**4.线程池操作源码分析：**

* **创建线程池**

 * **newFixedThreadPool()** 在Executors.java中定义
 * **ThreadPoolExecutor()** 在ThreadPoolExecutor.java中定义
 * **defaultThreadFactory()** 在Executors.java中定义
 

* **添加任务到线程池**
 * **execute()** 在ThreadPoolExecutor.java中定义
 * **addWorker()**

* **关闭线程池**
 * **shutdown()**

			public void shutdown() {
			    final ReentrantLock mainLock = this.mainLock;
			    // 获取锁
			    mainLock.lock();
			    try {
			        // 检查终止线程池的“线程”是否有权限。
			        checkShutdownAccess();
			        // 设置线程池的状态为关闭状态。
			        advanceRunState(SHUTDOWN);
			        // 中断线程池中空闲的线程。
			        interruptIdleWorkers();
			        // 钩子函数，在ThreadPoolExecutor中没有任何动作。
			        onShutdown(); // hook for ScheduledThreadPoolExecutor
			    } finally {
			        // 释放锁
			        mainLock.unlock();
			    }
			    // 尝试终止线程池
			    tryTerminate();
			}

**5.线程池的生命周期：**

线程池的5种状态是：Running, SHUTDOWN, STOP, TIDYING, TERMINATED

线程池状态定义代码

	private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
	private static final int COUNT_BITS = Integer.SIZE - 3;
	private static final int CAPACITY = (1 << COUNT_BITS) - 1;
	
	private static final int RUNNING = -1 << COUNT_BITS;
	private static final int SHUTDOWN = 0 << COUNT_BITS;
	private static final int STOP = 1 << COUNT_BITS;
	private static final int TIDYING = 2 << COUNT_BITS;
	private static final int TERMINATED = 3 << COUNT_BITS;
	private static int ctlOf(int rs, int wc) { return rs | wc; }


ctl是一个AtomicInteger类型的原子对象。ctl记录了"线程池中的任务数量"和"线程池状态"2个信息。

<font color='red'>ctl共包括32位。其中，高3位表示"线程池状态"，低29位表示"线程池中的任务数量"。</font>

	RUNNING    -- 对应的高3位值是111。
	SHUTDOWN   -- 对应的高3位值是000。
	STOP       -- 对应的高3位值是001。
	TIDYING    -- 对应的高3位值是010。
	TERMINATED -- 对应的高3位值是011。


线程池的状态切换

![](https://i.imgur.com/NZp57CU.jpg)

* **RUNNING**

状态说明：线程池处在RUNNING状态时，能够接收新任务，以及对已添加的任务进行处理。

状态切换：线程池的初始化状态是RUNNING。线程池被一旦被创建，就处于RUNNING状态！

* **SHUTDOWN**

状态说明：线程池处在SHUTDOWN状态时，不接收新任务，但能处理已添加的任务。

状态切换：调用线程池的shutdown()接口时，线程池由RUNNING -> SHUTDOWN。

* **STOP**

状态说明：线程池处在STOP状态时，不接收新任务，不处理已添加的任务，并且会中断正在处理的任务。

状态切换：调用线程池的shutdownNow()接口时，线程池由(RUNNING or SHUTDOWN ) -> STOP。

* **TIDYING**

状态说明：当所有的任务已终止，ctl记录的"任务数量"为0，线程池会变为TIDYING状态，此时会执行钩子函数terminated()

状态切换：当线程池在SHUTDOWN状态下，阻塞队列为空并且线程池中执行的任务也为空时，就会由 SHUTDOWN -> TIDYING。
当线程池在STOP状态下，线程池中执行的任务为空时，就会由STOP -> TIDYING。

* **TERMINATED**

状态说明：线程池彻底终止，就变成TERMINATED状态。

状态切换：线程池处在TIDYING状态时，执行完terminated()之后，就会由 TIDYING -> TERMINATED。


**6.线程池的拒绝策略：**

线程池的拒绝策略，是指当任务添加到线程池中被拒绝，而采取的处理措施。被拒绝的可能原因有：线程池异常关闭、任务数量超过线程池的最大限制。

线程池共包括4种拒绝策略

	AbortPolicy         -- 当任务添加到线程池中被拒绝时，它将抛出 RejectedExecutionException 异常。  
	CallerRunsPolicy    -- 当任务添加到线程池中被拒绝时，会在线程池当前正在运行的Thread线程池中处理被拒绝的任务。
	DiscardOldestPolicy -- 当任务添加到线程池中被拒绝时，线程池会放弃等待队列中最旧的未处理任务，然后将被拒绝的任务添加到等待队列中。
	DiscardPolicy       -- 当任务添加到线程池中被拒绝时，线程池将丢弃被拒绝的任务。

<font color='red'>线程池默认的处理策略是AbortPolicy！</font>

参考博客 [https://www.cnblogs.com/skywang12345/p/3512947.html](https://www.cnblogs.com/skywang12345/p/3512947.html)