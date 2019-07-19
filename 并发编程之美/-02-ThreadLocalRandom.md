# ThreadLocalRandom

## random 示例

```java

    public static void main(String[] args) {
        Random random=new Random();
        Runnable job= ()->{
            String name = Thread.currentThread().getName();
            System.out.println(name+"-随机数:" + random.nextInt(5));
            getSeed( name,random);
        };
        for (int i=0;i<=10;i++){
            new Thread(job).start();
        }
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        String name = Thread.currentThread().getName();
        System.out.println(name+"-单线程------------------");
        for (int i=0;i<=10;i++){
            System.out.println(name+"-随机数:" + random.nextInt(5));
            getSeed( name,random);
        }
    }

    private static void getSeed(String name,Random random) {
        Field seed = null;
        try {
            seed = Random.class.getDeclaredField("seed");
            //私有变量可访问
            seed.setAccessible(true);
            Object o = seed.get(random);
            System.out.println(name+"-种子:" + o);
            seed.setAccessible(false);
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
```

生成随机数需要两个过程

1. 根据老种子生成新种子
2. 根据新的种子生成随机数

> 多线程调用情况下,使用同一个种子生成新种子

## ThreadLocalRandom 示例

```java
    public static void main(String[] args) {

        Runnable job= ()->{
            /**
             *必须要在执行随机数的线程中获取否则会出现种子都相同
             */
            ThreadLocalRandom random=ThreadLocalRandom.current();
            String name = Thread.currentThread().getName();
            System.out.println(name+"-随机数:" + random.nextInt(5));
            getSeed( name,random);
        };
        for (int i=0;i<=10;i++){
            new Thread(job).start();
        }
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        String name = Thread.currentThread().getName();
        System.out.println(name+"-单线程------------------");
        ThreadLocalRandom random=ThreadLocalRandom.current();
        for (int i=0;i<=10;i++){
            System.out.println(name+"-随机数:" + random.nextInt(5));
            getSeed( name,random);
        }
    }
    private static void getSeed(String name,ThreadLocalRandom random) {
        Field seed = null;
        Field unsafe = null;
        try {
            unsafe = random.getClass().getDeclaredField("UNSAFE");
            unsafe.setAccessible(true);
            Unsafe unsafeValue = ((Unsafe) unsafe.get(random));
            unsafe.setAccessible(false);

            seed = random.getClass().getDeclaredField("SEED");
            seed.setAccessible(true);
            Long seedValue = ((long) seed.get(random));
            long aLong = unsafeValue.getLong(Thread.currentThread(),seedValue);
            System.out.println(name+"-种子:" + aLong);
            unsafe.setAccessible(false);
        } catch (NoSuchFieldException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }
```

>> 避免了出现种子间的竞争
