## 一. 进程调度算法

### 1. 先来先服务（`FCFS`）
- **机制**：按进程请求顺序调度。
- **优点**：简单，无饥饿问题。
- **缺点**：
  - 长作业可能阻塞短作业，平均等待时间较长。
  - 无法适应动态任务场景。

### 2. 最短作业优先（`SJF`）
- **机制**：非抢占式，优先调度估计运行时间最短的进程。
- **优点**：最小化平均等待时间。
- **缺点**：
  - 可能导致长作业饥饿。
  - 需要预知作业运行时间（实际场景难以实现）。

### 3. 最短剩余时间优先（`SRTF`）
- **机制**：抢占式，动态比较新到达进程与当前进程的剩余时间。
- **优点**：优化响应时间和周转时间。
- **缺点**：
  - 频繁上下文切换增加开销。
  - 长作业可能长期得不到调度。

### 4. 优先级调度
- **机制**：按进程优先级调度（静态或动态优先级）。
- **改进策略**：
  - 动态调整优先级（如等待时间越长优先级越高）。
- **缺点**：
  - 低优先级进程可能饥饿。

### 5. 时间片轮转（`RR`）
- **机制**：为每个进程分配固定时间片，超时后重新排队。
- **优点**：公平性高，适用于分时系统。
- **缺点**：
  - 时间片过短导致频繁切换，过长则退化为FCFS。

### 6. 多级队列（`MLQ`）
- **机制**：
  - 将进程按优先级分组到不同队列（如前台交互队列、后台批处理队列）。
  - 不同队列可采用不同调度策略（如高优先级队列用RR，低优先级队列用FCFS）。
- **优点**：灵活适应多种任务类型。
- **缺点**：配置复杂，可能引发队列间优先级反转。

---

## 二. 进程间通信（`IPC`）方式

| **方式**       | **特点**                                                                 | **适用场景**                     |
|----------------|--------------------------------------------------------------------------|----------------------------------|
| **管道**       | 单向通信，仅限父子进程                                                   | 简单数据流传输（如 `ls \| grep`）|
| **命名管道**   | 允许无关进程通过文件系统路径访问                                         | 跨进程固定通信链路               |
| **消息队列**   | 支持消息优先级，异步通信                                                 | 结构化数据传输（如任务调度）     |
| **信号**       | 异步通知事件（如 `SIGKILL` 终止进程）                                    | 进程控制与异常处理               |
| **信号量**     | 计数器机制，控制资源访问（`PV`操作）                                       | 同步共享资源访问（如文件锁）     |
| **共享内存**   | 直接内存映射，通信速度最快（需同步机制）                                 | 高频大数据交互（如数据库缓存）   |
| **Socket**     | 支持网络通信（`TCP/UDP`），跨主机进程通信                                  | 分布式系统或客户端-服务器架构    |

---

## 三. 进程同步与互斥

### 1. 基本概念
- **同步**：协调多个进程的执行顺序（如生产者-消费者模型）。
- **互斥**：保证同一时刻仅一个进程访问共享资源（如打印机）。

### 2. 实现机制

#### （1）信号量（`Semaphore`）
- **机制**：
  - **P操作（`Wait`）**：申请资源（信号量减1，若为负则阻塞）。
  - **V操作（`Signal`）**：释放资源（信号量加1，唤醒等待进程）。
- **类型**：
  - 二元信号量（互斥锁）。
  - 计数信号量（资源池管理）。

#### （2）临界区（`Critical Section`）
- **规则**：
  - 进入区：检查是否可进入临界区。
  - 退出区：释放资源并标记退出。
- **特点**：需硬件支持（如原子指令 `TestAndSet`）。

#### （3）互斥锁（`Mutex`）
- **机制**：仅允许持有锁的进程访问资源。
- **操作**：
  - `lock()`：获取锁（失败则阻塞）。
  - `unlock()`：释放锁。

#### （4）条件变量（`Condition Variable`）
- **用途**：在特定条件满足时唤醒进程（常与互斥锁配合）。
- **操作**：
  - `wait()`：释放锁并阻塞，等待条件触发。
  - `signal()`：唤醒一个等待进程。
  - `broadcast()`：唤醒所有等待进程。

### 3. 经典问题与解决方案
| **问题**             | **解决方案**                              |
|----------------------|------------------------------------------|
| 生产者-消费者问题    | 信号量 + 互斥锁 + 条件变量               |
| 读者-写者问题        | 读写锁（优先读或优先写）                 |
| 哲学家进餐问题       | 资源分级分配或限制并发数                 |
