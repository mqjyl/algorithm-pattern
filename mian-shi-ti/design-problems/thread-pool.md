# 实现线程池

## ✏ 线程池

### 🖋 1、作用：异步解耦

![](../../.gitbook/assets/39%20%281%29.png)

**线程池的运行逻辑：线程池中的线程在不断竞争任务队列中的任务。**

### 🖋 2、组成

1. 任务队列
2. 线程集合
3. 管理组件

### 🖋 3、TODO

1. 增加线程
2. 删除过度冗余的线程
3. 快手面试题：查询一个任务是否被执行完
4. 头条面试题：如果一个任务在执行过程中线程被中断（也许是时钟周期已到，也许是等待资源，也许是被中断，等等），后面怎样保证继续被同一个线程执行。

{% tabs %}
{% tab title="C-glibc" %}
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>

#define LL_ADD(item, list) do { \
	item->prev = NULL;          \
	item->next = list;          \
	if (list != NULL)           \
		list->prev = item;      \
	list = item;                \
}while(0) // 和使用时的分号做统一

#define LL_REMOVE(item, list) do {     \
	if(item->prev != NULL)             \
		item->prev->next = item->next; \
	if(item->next != NULL)             \
		item->next->prev = item->prev; \
	if(item == list)                   \
		list = item->next;             \
	item->prev = item->next = NULL;    \
}while(0)
// 任务结构体
struct NJOB {
	void (*func)(int arg, pthread_t id);
	int user_data;
	pthread_t id;

	struct NJOB *next;
	struct NJOB *prev;
};

struct NWORKER {
	pthread_t id;
	int terminate; // 是否工作

	struct NMANAGER *pool;

	struct NWORKER *prev;
	struct NWORKER *next;
};

typedef struct NMANAGER {
	pthread_mutex_t mtx;
	pthread_cond_t cond;

	struct NJOB *jobs;
	struct NWORKER *workers;
}nThreadPool;

// 线程的回调函数
void *thread_cb(void *arg){
	struct NWORKER *worker = (struct NWORKER *)arg;

	while (1) {
		pthread_mutex_lock(&worker->pool->mtx);
		// 没有任务则等待
		while(worker->pool->jobs == NULL){
			if (worker->terminate)
				break;
			pthread_cond_wait(&worker->pool->cond, &worker->pool->mtx);
		}
		// 执行任务
		struct NJOB *job = worker->pool->jobs;
		if(job != NULL){
			LL_REMOVE(job, worker->pool->jobs);
		}
		pthread_mutex_unlock(&worker->pool->mtx);
		job->func(job->user_data, worker->id);
	}
	free(worker);
	pthread_exit(NULL);
}

// SDK
int nThreadPoolCreate(nThreadPool *pool, int numWorkers){
	// 参数判断
	if(numWorkers <= 0)
		numWorkers = 1;
	if(pool == NULL)
		return -1;
	memset(pool, 0, sizeof(nThreadPool));

	pthread_mutex_t blank_mutex = PTHREAD_MUTEX_INITIALIZER;
	memcpy(&pool->mtx, &blank_mutex, sizeof(pthread_mutex_t));

	pthread_cond_t blank_cond = PTHREAD_COND_INITIALIZER;
	memcpy(&pool->cond, &blank_cond, sizeof(pthread_cond_t));

	// workers
	int i = 0;
	for(i = 0; i < numWorkers; i ++){
		struct NWORKER *worker = (struct NWORKER*)malloc(sizeof(struct NWORKER));
		if(worker == NULL)
		{
			perror("malloc error");
			return -2;
		}
		memset(worker, 0, sizeof(struct NWORKER));
		worker->pool = pool;
		pthread_create(&worker->id, NULL, thread_cb, worker);
		LL_ADD(worker, pool->workers);
	}
	return 0;
}

int nThreadPoolDestory(nThreadPool *pool){
	struct NWORKER *worker = NULL;
	for(worker = pool->workers; worker != NULL; worker = worker->next){
		worker->terminate = 1;
	}
	pthread_mutex_lock(&pool->mtx);
	pthread_cond_broadcast(&pool->cond);
	pthread_mutex_unlock(&pool->mtx);
}

int nThreadPoolPush(nThreadPool *pool, struct NJOB *job){
	pthread_mutex_lock(&pool->mtx);
	LL_ADD(job, pool->jobs);
	pthread_cond_signal(&pool->cond);
	pthread_mutex_unlock(&pool->mtx);
	return 0;
}

void printer(int arg, pthread_t id){
	printf("thread %ld -- %d\n", id, arg);
	usleep(2);
}

#if 1
// 0 --> 1000
int main(){
	nThreadPool *mem_pool = (nThreadPool *)malloc(sizeof(nThreadPool));
	int re = nThreadPoolCreate(mem_pool, 10);
	if(re == 0){
		int i = 0;
		for(i = 0; i <= 1000; i++){
			struct NJOB *job = (struct NJOB*)malloc(sizeof(struct NJOB));
			job->func = printer;
			job->user_data = i;
			nThreadPoolPush(mem_pool, job);
		}
	}
	sleep(3);
	nThreadPoolDestory(mem_pool);
	return 0;
}
#endif
```
{% endtab %}

{% tab title="C++11" %}
```

```
{% endtab %}
{% endtabs %}

