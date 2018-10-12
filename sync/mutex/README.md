# mutex 互斥量

互斥量也称互斥锁。互斥量比较常见，课本里都经常出现。访问共享资源的时候就会有互斥量的存在，通常是在同个进程内的不同线程之间适用它。但是不同进程之间也可以通过技巧来实现，暂时不讲。

下面是互斥量的接口：
```
#include <pthread.h>

int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

下面是互斥量创建和销毁的接口(比较少用)：
```
# 最简单的初始化方法，通常mutex是静态变量
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
# 手动初始化
int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
# 销毁互斥量
int pthread_mutex_destroy(pthread_mutex_t *mutex);
```


常见的使用方法：
```
#include <pthread.h>
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

int main()
{
	// 会阻塞，直到成功上锁。若要非阻塞，适用trylock。
	pthread_mutex_lock(mutex);	
	// do something
	pthread_mutex_unlock(mutex);	// 解锁
	
	return 0;
}
```



