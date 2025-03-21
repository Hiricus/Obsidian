## CountDownLatch
Данный класс представляет собой потокобезопасный счётчик, способный отсчитывать от заданного значения до нуля. Также у него есть метод `await()`, который заставляет вызвавший поток ждать, пока счётчик не дойдёт до нуля.
```java
CountDownLatch latch = new CountDownLatch(THREAD_COUNT);  
for (int i = 0; i < THREAD_COUNT; i++) {  
    new Thread(new Runnable() {  
        @Override  
        public void run() {  
            System.out.println("Thread started working");  
  
            try {  
                Thread.sleep((long) (Math.random() * 5000 + 1000));  
                System.out.println("Thread finished long task");  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
  
            latch.countDown();  
            try {  
                latch.await();  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
            System.out.println("All threads finished working");  
        }  
    }).start();  
}
```
В данном примере создаётся несколько потоков, которые выполняют задачу некоторое неизвестное заранее время. После чего убавляют CountDownLatch и входят в режим ожидания до момента, пока счётчик не достигнет нуля.
Когда последний поток закончит задачу и счётчик достигнет нуля - все потоки одновременно продолжат работу.

## Semaphore
Этот класс можно сравнить с блоком `synchronized`, но на несколько потоков одновременно.
При создании указывается число потоков, которые одновременно могут работать с блоком кода. Далее при помощи метода `acquire()` семафор указывает, что дальнейший код может выполнятся только указанным числом потоков одновременно. Когда поток закончит выполнять синхронизированный код - должен быть вызван метод `release()`, который освобождает блок для выполнения другим потоком.
Метод `release()` следует вызывать в блоке `finally` т.к. иначе поток может не дойти до него и место так и не будет освобождено.

```java
Semaphore semaphore = new Semaphore(2);  
for (int i = 0; i < 10; i++) {  
    new Thread(new Runnable() {  
        @Override  
        public void run() {  
            System.out.println("Thread started working");  
  
            try {  
                semaphore.acquire();  
                System.out.println("Thead started working with file system");  
                Thread.sleep(1000);  
            } catch (InterruptedException e) {  
                throw new RuntimeException(e);  
            } finally {  
                semaphore.release();  
            }  
  
            System.out.println("Continue work");  
        }  
    }).start();  
}
```
В этом примере создаётся семафор на два потока. После создаётся 10 потоков, работающих с памятью. Одновременно с памятью должно работать не более двух потоков. Это достигается при помощи использования ранее созданного семафора.

## CyclicBarrier
Можно сравнить с `CountDownLatch`, который ресетается при достижении нуля. Часто применяется сравнение с барьером на скачках. Несколько потоков "подходят" к барьеру, после чего он "опускается" и эти потоки продолжают выполнение. После этого барьер "поднимается" обратно и ожидает следующие несколько потоков.
В конструкторе можно передать объект `Runnable`, который будет выполняться при "опускании" барьера.

```java
CyclicBarrier barrier = new CyclicBarrier(2, new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("Starting the race");  
    }  
});  
  
for (int i = 0; i < 10; i++) {  
    final int carIndex = i;  
    new Thread(new Runnable() {  
        @Override  
        public void run() {  
            Car car = new Car("Car №" + carIndex);  
            System.out.println("Preparing car: " + car.getName());  
  
            try {  
                Thread.sleep(1000);  
  
                System.out.println(car.getName() + "has reached the start");  
                barrier.await();  
            } catch (Exception e) {  
                throw new RuntimeException(e);  
            }  
  
            System.out.println(car.getName() + " has finished the race");  
        }  
    }).start();  
}
```
В данном примере создаётся барьер на два потока. После этого создаётся 10 потоков, каждый из которых имитирует машину, подъезжающую к стартовой линии.
При подъезде к старту у барьера вызывается метод `await()`. Когда к барьеру подъезжает две машины - вызывается метод `run()` из переданного в него объекта и начинается гонка. После этого барьер "опускается" и ожидает следующую пару машин.