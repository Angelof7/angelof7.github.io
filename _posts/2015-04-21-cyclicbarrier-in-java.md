---
layout: post
title: Java线程之CyclicBarrier
categories: [java]
tags: [Concurrent]
---

## 1. CyclicBarrier是什么 ##
----------------------------
一个同步辅助类，它允许一组线程互相等待，直到到达某个公共屏障点 (common barrier point)。在涉及一组固定大小的线程的程序中，这些线程必须不时地互相等待，此时 CyclicBarrier 很有用。因为该 barrier 在释放等待线程后可以重用，所以称它为循环 的 barrier。


## 2. 使用场景 ##
---------------------------
需要所有的子任务都完成时，才执行主任务，这个时候就可以选择使用CyclicBarrier。

## 3. 实例 ##
---------------------------
赛跑时，等待所有人都准备好时，才起跑：
{% highlight java %}
    public class CyclicBarrierTest {

        public static void main(String[] args) throws IOException, InterruptedException {
            CyclicBarrier barrier = new CyclicBarrier(3);

            ExecutorService executor = Executors.newFixedThreadPool(3);
            executor.submit(new Thread(new Runner(barrier, "1号选手")));
            executor.submit(new Thread(new Runner(barrier, "2号选手")));
            executor.submit(new Thread(new Runner(barrier, "3号选手")));

            executor.shutdown();
        }
    }

    class Runner implements Runnable {
        // 一个同步辅助类，它允许一组线程互相等待，直到到达某个公共屏障点 (common barrier point)
        private CyclicBarrier barrier;

        private String name;

        public Runner(CyclicBarrier barrier, String name) {
            super();
            this.barrier = barrier;
            this.name = name;
        }
    
    @Override
    public void run() {
            try {
                Thread.sleep(1000 * (new Random()).nextInt(8));
                System.out.println(name + " 准备好了...");
                // barrier的await方法，在所有参与者都已经在此 barrier 上调用 await 方法之前，将一直等待。
                barrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
            System.out.println(name + " 起跑！");
        }
    }
{% endhighlight %}
