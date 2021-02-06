
## &#127800; 实现Runnable接口 &#127800;

<table>
  <th>实现Runnable接口</th>
  <tr>
    <td>
1. 创建一个实现了Runnable接口的类<br>
2. 实现类去实现Runnable中的抽象方法：run()<br>
3. 创建实现类的对象<br>
4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象<br>
5. 通过Thread类的对象调用start()<br>
    </td>
  </tr>
    <tr>
    <td>
推荐使用，比卖你单继承局限性，灵活方便，方便同一个对象被多个线程使用。
    </td>
  </tr>
</table>

<br>
<details>
<summary>&#127808; 实现Runnable接口 &#127808;</summary>
  
```java
package Multithreading.Runnable;

/**
 * 创建多线程的方式二：实现Runnable接口
 * 1. 创建一个实现了Runnable接口的类
 * 2. 实现类去实现Runnable中的抽象方法：run()
 * 3. 创建实现类的对象
 * 4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象
 * 5. 通过Thread类的对象调用start()
 *
 *
 * 比较创建线程的两种方式。
 * 开发中：优先选择：实现Runnable接口的方式
 * 原因：1. 实现的方式没有类的单继承性的局限性
 *      2. 实现的方式更适合来处理多个线程有共享数据的情况。
 *
 * 联系：public class Thread implements Runnable
 * 相同点：两种方式都需要重写run(),将线程要执行的逻辑声明在run()中。
 *
 * @author shkstart
 * @create 2019-02-13 下午 4:34
 */
//1. 创建一个实现了Runnable接口的类
public class MyRunnable implements Runnable{

    //2. 实现类去实现Runnable中的抽象方法：run()
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            if((i & 1) == 0){
                System.out.println(Thread.currentThread().getName() + ":" + i);
            }
        }
    }
}
```
                                
```java
package Multithreading.Test;

import Multithreading.Runnable.MyRunnable;

public class MyRunnableTest {

    public static void main(String[] args) {
        //3. 创建实现类的对象
        MyRunnable mThread = new MyRunnable();
        //4. 将此对象作为参数传递到Thread类的构造器中，创建Thread类的对象

        Thread t1 = new Thread(mThread);
        t1.setName("线程1: ");
        //5. 通过Thread类的对象调用start():① 启动线程 ②调用当前线程的run()-->调用了Runnable类型的target的run()
        t1.start();
        //再启动一个线程，遍历100以内的偶数

        Thread t2 = new Thread(mThread);
        t2.setName("线程2: ");
        t2.start();
    }
}                            
```

</details>
  
<details>
<summary>&#127808; View The Example &#127808;</summary>
  
```java
/**
 * 例子：创建三个窗口卖票，总票数为100张.使用实现Runnable接口的方式
 *
 * 1.问题：卖票过程中，出现了重票、错票 -->出现了线程的安全问题
 * 2.问题出现的原因：当某个线程操作车票的过程中，尚未操作完成时，其他线程参与进来，也操作车票。
 * 3.如何解决：当一个线程a在操作ticket的时候，其他线程不能参与进来。直到线程a操作完ticket时，其他
 *            线程才可以开始操作ticket。这种情况即使线程a出现了阻塞，也不能被改变。
 *
 *
 * 4.在Java中，我们通过同步机制，来解决线程的安全问题。
 *
 *  方式一：同步代码块
 *
 *   synchronized(同步监视器){
 *      //需要被同步的代码
 *
 *   }
 *  说明：1.操作共享数据的代码，即为需要被同步的代码。-->不能包含代码多了，也不能包含代码少了。
 *       2.共享数据：多个线程共同操作的变量。比如：ticket就是共享数据。
 *       3.同步监视器，俗称：锁。任何一个类的对象，都可以充当锁。
 *          要求：多个线程必须要共用同一把锁。
 *
 *       补充：在实现Runnable接口创建多线程的方式中，我们可以考虑使用this充当同步监视器。
 *  方式二：同步方法。
 *     如果操作共享数据的代码完整的声明在一个方法中，我们不妨将此方法声明同步的。
 *
 *
 *  5.同步的方式，解决了线程的安全问题。---好处
 *    操作同步代码时，只能有一个线程参与，其他线程等待。相当于是一个单线程的过程，效率低。---局限性
 *
 */
```
</details>
  
#### &#127800; 解决线程安全问题
**方法一：同步代码块**
- 同步代码块解决，实现Runnable
- 同步代码块解决，继承thread

**方法二：同步方法**
- 使用同步方法，解决实现Runnable，同步监视器省略为this
- 使用静态同步方法，解决继承Thread，同步监视器省略为this
  
#### &#127800; 注意
1. **使用代码块解决**：继承 Thread 时**不能**使用this，但是实现 Runnable接口**可以使用**
2. **使用同步方法解决时**：继承 Thread 时**必须使用**static，但是实现 Runnable接口**可以不使用**
<br>

  
<details>
<summary>&#127808; 解决线程的安全问题方法一: 实现Runnable &#127808;</summary>
  
```java
package Multithreading.ThreadSecurityIssues;

public class Solution_1 implements Runnable{
    private int ticket = 100;
//    Object obj = new Object();
    @Override
    public void run() {
        while(true){
            synchronized (this){ //此时的this:唯一的Window1的对象   //方式二：synchronized (obj) {
                if (ticket > 0) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);
                    ticket--;
                } else {
                    break;
                }
            }
        }
    }
}
```
```java
package Multithreading.Test;

import Multithreading.ThreadSecurityIssues.Solution_1;

public class Solution_1_Test {
    public static void main(String[] args) {
        Solution_1 w = new Solution_1();
        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);
  
        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");
  
        t1.start();
        t2.start();
        t3.start();
    }
}

```
</details> 

<details>
<summary>&#127808; 解决线程的安全问题方法一: 继承thread &#127808;</summary>
  
- 错误的方式：this代表着实例化的t1,t2,t3三个对象，即把互斥信号又同时交给每个对象，问题等于没解决
- synchronized 内需使用 (类名.class) 或者 (唯一私有成员变量)
```java
package Multithreading.ThreadSecurityIssues;

/**
* 使用同步代码块解决继承Thread类的方式的线程安全问题
*
* 例子：创建三个窗口卖票，总票数为100张.使用继承Thread类的方式
*
* 说明：在继承Thread类创建多线程的方式中，慎用this充当同步监视器，考虑使用当前类充当同步监视器。
*
*/
    public class Solution_2 extends Thread {

        private static int ticket = 100;
//        private static Object obj = new Object();
        @Override
        public void run() {
            while(true){
                //正确的
//            synchronized (obj){
                synchronized (Solution_2.class){//Class clazz = Window2.class , Window2.class只会加载一次
                //错误的方式：this代表着实例化的t1,t2,t3三个对象，即把互斥信号又同时交给每个对象，问题等于没解决
//              synchronized (this){
                    if(ticket > 0){
                        try {
                            Thread.sleep(10);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println(getName() + "：卖票，票号为：" + ticket);
                        ticket--;
                    }else{
                        break;
                    }
                }
            }
        }
    }
```
```java
package Multithreading.Test;

import Multithreading.ThreadSecurityIssues.Solution_2;

public class Solution_2_Test {
    public static void main(String[] args) {
        Solution_2 t1 = new Solution_2();
        Solution_2 t2 = new Solution_2();
        Solution_2 t3 = new Solution_2();

        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");

        t1.start();
        t2.start();
        t3.start();
    }
}

```
</details>

<details>
<summary>&#127808; 解决线程的安全问题方法二： &#127808;</summary>
  
```java
package Multithreading.ThreadSecurityIssues;

/**
 * 使用同步方法解决实现Runnable接口的线程安全问题
 *
 *  关于同步方法的总结：
 *  1. 同步方法仍然涉及到同步监视器，只是不需要我们显式的声明。
 *  2. 非静态的同步方法，同步监视器是：this
 *     静态的同步方法，同步监视器是：当前类本身
 *
 */
public class Solution_3  implements Runnable {
    private int ticket = 100;
    @Override
    public void run() {
        while (true) {
            show();
        }
    }

    private synchronized void show(){//同步监视器：this
        //synchronized (this){
        if (ticket > 0) {
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + ":卖票，票号为：" + ticket);
            ticket--;
        }
        //}
    }
}
```
```java
package Multithreading.Test;

import Multithreading.ThreadSecurityIssues.Solution_3;

public class Solution_3_Test {
    public static void main(String[] args) {
        Solution_3 w = new Solution_3();

        Thread t1 = new Thread(w);
        Thread t2 = new Thread(w);
        Thread t3 = new Thread(w);
        t1.setName("窗口1");
        t2.setName("窗口2");
        t3.setName("窗口3");
        t1.start();
        t2.start();
        t3.start();
    }
}
```
</details>
