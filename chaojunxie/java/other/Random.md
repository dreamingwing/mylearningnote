## Random

java中的Random类是个线程安全的随机数生产，它是基于一个seed，来产生随机数以及下一个seed。

```java
protected int next(int bits) {
        long oldseed, nextseed;
        AtomicLong seed = this.seed;
        do {
            oldseed = seed.get();
            nextseed = (oldseed * multiplier + addend) & mask;
        } while (!seed.compareAndSet(oldseed, nextseed));
        return (int)(nextseed >>> (48 - bits));
    }
```

以上方法是其中生成随机数的核心方法，可以看到，是通过Atomic类CAS的方式来设置下一个seed和随机数。

