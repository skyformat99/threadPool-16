
# 使用说明
如果在windows用这个库，要安装pthread库, gcc库（原子操作）， linux可以直接用

# 性能
测试环境
 i5 6200, 2G ddr4 , 10万次任务，10线程并发 
 elapsed time: 0.073756 usec, tps:=1355821.899235

# 生产环境例子
```
#include <unistd.h>
#include <stdio.h>
#include <iostream>
#include "threadPool.h"
#include "thread.h"
#include "task.h"

using namespace std;

class  TestTask : public Task
{
public:
    TestTask(int i){
        m_i = i;
    }

    void run() {
        //printf("hello %d\n", m_i);
    }
private:
    int m_i;
};

int main(int argc, char *argv[])
{

    int max_task_cnt = 10 * 10000;
    ThreadPool thread_pool; // 建立线程池
     thread_pool.Init(10); // 线程池的数量
     thread_pool.SetThresholdRedistribution(1); //设置重新分配的阀值，当存在空闲线程时，超过阀值时, 当前工作线程就会把超过阀值的任务重新分派出去

     TestTask *t = NULL;
     for (int i=0 ; i< max_task_cnt; i++) {
         t = new TestTask(i);
         thread_pool.AddTask(t);
     }
     pause();
    return 0;
}
```

# 测试环境例子（非生产环境）

```
#include <unistd.h>
#include <stdio.h>
#include <iostream>
#include "threadPool.h"
#include "thread.h"
#include "task.h"

using namespace std;

class  TestTask : public Task
{
public:
    TestTask(int i){
        m_i = i;
    }

    void run() {
        //printf("hello %d\n", m_i);
    }
private:
    int m_i;
};

int main(int argc, char *argv[])
{

    int max_task_cnt = 10 * 10000;
    ThreadPool thread_pool; // 建立线程池
     thread_pool.Init(10); // 线程池的数量
     thread_pool.SetThresholdRedistribution(1); //设置重新分配的阀值，当存在空闲线程时，超过阀值时, 当前工作线程就会把超过阀值的任务重新分派出去
     thread_pool.SetMaxTaskCount(max_task_cnt); // 设置任务数，该调用出发后完成任务数后进程退出，所以该调用只用于测试环境，生产环境不要调用该方法

     TestTask *t = NULL;
     for (int i=0 ; i< max_task_cnt; i++) {
         t = new TestTask(i);
         thread_pool.AddTask(t);
     }
     pause();
    return 0;
}
```