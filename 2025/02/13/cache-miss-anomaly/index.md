
# 缓存 miss 类型

## compulsory miss
> 一开始缓存里啥都没有，cold-start miss
## capacity miss
> 缓存容量有限，需要 evict 一些页
## conflict miss
> cpu cache 的 set-associativity 的缓存行

# 缓存越大，命中率就越高么？不是的

> 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5 
> 采用 FIFO 的 swap 策略。容量为 3 的缓存，比容量为 4 的缓存命中率更高

```java

import java.util.concurrent.ArrayBlockingQueue;

public class CacheFifo {
    public static void main(String[] args) {
        Integer[] pages = {1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5};
        calculateCacheHit(pages, 3);
        calculateCacheHit(pages, 4);
    }

    private static void calculateCacheHit(Integer[] pages, int cacheSize) {
        System.out.print("缓存页: ");
        dumpArray(pages);
        ArrayBlockingQueue<Integer> queue = new ArrayBlockingQueue(cacheSize);
        int miss = 0;
        for (int page : pages) {
            if (!queue.contains(page)) {
                miss = miss + 1;
                if (!queue.isEmpty() && queue.size() == cacheSize) {
                    dumpArray(queue.toArray());
                    queue.poll();
                }
                queue.offer(page);
                dumpArray(queue.toArray());
            }
        }
        System.out.println("cache miss:" + miss);
        System.out.println("total :" + pages.length);
        System.out.println("cache hit:" + (1 - miss * 1.0 / pages.length));
    }

    private static void dumpArray(Object[] array) {
        for (Object o : array) {
            System.out.print(o + " ");
        }
        System.out.println();
    }
}


```








# References
1. [https://pages.cs.wisc.edu/~remzi/OSTEP/vm-beyondphys-policy.pdf](https://pages.cs.wisc.edu/~remzi/OSTEP/vm-beyondphys-policy.pdf)