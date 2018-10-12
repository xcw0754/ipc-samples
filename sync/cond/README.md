# cond 条件变量

条件变量比较罕见，它和信号量有些相似，也是线程同步的一种方式，但是不同的地方是条件变量具有广播的功能，比如n个线程在等待一个条件变量时进行广播就能同时唤醒这n个进程。当然，条件变量也能用于不同进程中，需要一些技巧，暂时不讲。条件变量的适用有点繁，还需要一个额外的互斥量来配合使用，因为条件变量的接口并不是线程安全的。


条件变量的接口：
```
#include <pthread.h>

int pthread_cond_timedwait(pthread_cond_t *restrict cond,
	  pthread_mutex_t *restrict mutex,
	  const struct timespec *restrict abstime);
int pthread_cond_wait(pthread_cond_t *restrict cond,
	  pthread_mutex_t *restrict mutex);

int pthread_cond_broadcast(pthread_cond_t *cond);
int pthread_cond_signal(pthread_cond_t *cond);
```

下面是条件变量创建和销毁的接口：
```
#include <pthread.h>

int pthread_cond_destroy(pthread_cond_t *cond);
int pthread_cond_init(pthread_cond_t *restrict cond, const pthread_condattr_t *restrict attr);
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
```


singal接口就是只能唤醒单个等待的线程，具体是哪个线程可以认为是随机的，而broadcast接口可以唤醒所有等待的线程，推荐使用broadcast接口。wait接口是阻塞地等待，而timedwait接口可以指定最多等待多久时间，防止永久等待。


常见的使用方法：
```
#include <pthread.h>
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;

// 线程A
int pthreadA()
{
	pthread_mutex_lock(mutex);	
	pthread_cond_wait(cond);
	pthread_mutex_unlock(mutex);
	
	return 0;
}

// 线程B
int pthreadB()
{
	pthread_mutex_lock(mutex);	
	pthread_cond_signal(cond);
	pthread_mutex_unlock(mutex);
	
	return 0;
}
```

条件变量和信号量的区别是明显的，条件变量还得配合互斥量，很繁琐，而且它不能代替信号量。考虑这样的一种场景，当前没有进程在等待，调用pthread_cond_signal会怎样？啥事都没发生，下次有线程调用pthread_cond_wait的话，也是需要等待的。但是信号量就不是这样了，释放信号量是可以累加的，下次有线程需要获取信号量的时候就无需等待立即获得，且扣减信号量。
