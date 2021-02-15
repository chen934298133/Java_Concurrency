## &#127800; 再次认识Lambda &#127800;

#### Tips
**静态代理优点：**
 * 1. 代理对象可以做很多真实对象做不了的事情
 * 2. 真实对象可以专注做自己的事情

**要求：**
 * 1. 真实对象与代理对象需要实现同一个接口
 * 2. 代理对象要代理真实的角色
 
<br>
<details>
<summary>&#127808; View The Points &#127808;</summary>
  
```java
package Design_Pattern.StaticProxy;

/**
 *
 * 静态代理优点：
 * 1. 代理对象可以做很多真实对象做不了的事情
 * 2. 真实对象可以专注做自己的事情
 *
 * 要求：
 * 1. 真实对象与代理对象需要实现同一个接口
 * 2. 代理对象要代理真实的角色
 *
 */
public class StaticProxy {
    public static void main(String[] args){
        You chen = new You();
//        chen.HappyMarry();

        Marry proxy = new WeddingCompany(chen);
        proxy.HappyMarry();



        // -----------------对比静态代理 与 Thread-----------------------
//        new Thread(){
//            @Override
//            public void run() {
//                System.out.println("tempThread");
//            }
//        }.start();

        new Thread(()-> System.out.println("tempThread")).start();
        new WeddingCompany(chen).HappyMarry();

        // 即 Thread 代理了 Runnable 接口的 start 方法
        // 此处 WeddingCompany 代理了 Marry 的 HappyMarry 方法

    }
}

interface Marry{
    void HappyMarry();
}

// Real object
class You implements Marry{

//    public You() {
//    }
    @Override
    public void HappyMarry() {
        System.out.println("我来到现场");
    }
}

// Proxy object
class WeddingCompany implements Marry{

    private Marry target;

    public  WeddingCompany(Marry target){
        this.target = target;
    }
    @Override
    public void HappyMarry() {
        before();
        this.target.HappyMarry();
        after();

    }

    private void before() {
        System.out.println("布置现场");
    }

    private void after() {
        System.out.println("婚礼结束");
    }

}
```
</details>