# ucos-II在STM32F103上的移植(正点例程)
## 移植步骤
> STEP1

在基础工程中建立相应的文件夹CONFIG,CORE,PORT
> STEP2

向各文件夹添加文件：</br>
1. 将ucosii源码里面的src里面的所有文件拷贝到CORE里面
2. 往CONFIG里面加入includes.h和os_cfg.h头文件（在例程里面找）
3. 向PORT里面添加相应的文件，也是从例程里面找
> STEP3

将ucos文件夹添加到工程中去，需要注意的是，__CORE__ 里面`ucos_ii.c`文件 *不能*添加到工程里！不然会报错.
__PORT__ 只需要添加`os_cpu_c.c`,`os_cpu.h`跟`os_cpu_a.asm`文件.
> STEP4 

1. 添加路径，并将`ucos_ii.h`里面的对头文件`app_cfg.h`的引用注释掉。
2. 存在函数的重复定义，将stm32f10x_it.c里面的`void PendSV_Handler(void)`函数注释掉。
> STEP5

修改`sys.h`文件，修改里面的`#define`,存在滴答时钟函数的重新定义，将stm32f10x_it.c的`(void)SysTick_Handler(void)`注释掉。*PORT里面的os_cpu_c.c里面本来也有地方需要修改，但是因为是从例程里面拷贝的，因此已经修改好了*

## 测试软件设计
### 1.要求
1. 创建一个start_task任务，Start任务用来创建其它任务。
2. 创建一个led0_task任务，用来控制led0亮灭
3. 创建一个led1_task任务，用来控制led1亮灭
4. 创建一个float_task任务，用来测试浮点数计算

## 各文件夹的内容
### CONFIG文件夹
1. `includes.h` 包含一些头文件
    其内包含了
    ~~~c
    #include <stdio.h>
    #include <string.h>
    #include <stdlib.h>
    #include <stdarg.h>
    #include "ucos_ii.h"
    #include "os_cpu.h"
    #include "os_cfg.h"
    #include <stm32f10x.h>
    ~~~
2. `os_cfg.h` ucosII的配置文件
    其内包含大量的宏定义，其内现在需要了解的是一个`#define OS_TICKS_PER_SEC`的宏定义，它设置了ucos的心跳频率，表示了ucos每秒钟心跳几次，例如`#define OS_TICKS_PER_SEC 200u`就是每秒钟跳200次，每次就是5ms.
### PORT文件夹
1. `os_cpu_c.c` 针对不同cpu移植时需要做出修改
2. `os_cpu_a.asm` 内部含有一个负责中断跟上下文切换的汇编文件
3. `os_cpu.h` 与编译器无关的宏定义变量
### CROE文件夹
这个文件夹下全是ucos的源码，与内核，调试项目，时间标志，消息邮箱，内存...相关

## ucosII体系结构的总结
（图片）

# ucos-III的任务管理
## ucos-III下的SYSTEM文件夹（正点编写的文件）
### delay.c
主要是利用滴答定时器来完成高精度延时。
内部函数：
`delay_osschedlock()` 任务调度器加锁</br>
`delay_osschedunlock()` 任务调度器解锁</br>
`delay_ostimedly()` 延时，按照节拍数延时</br>
`SysTick_Handler()` 滴答定时器中断服务函数</br>
`delay_init()` 滴答定时器/延时初始化</br>
`delay_us` 微妙延时，不会引发任务调度</br>
`Delay_ms` 毫妙延时，最小ms延时为ucos系统心跳时间</br>

> void delay_init(u8 SYSCLK)

使用M3内核的滴答定时器硬件来提供系统的心跳
~~~c
void delay_init(u8 SYSCLK)
{
    u32 reload; //定义一个变量来存储重装载的值
    SysTick_CLKSourceConfig(SysTick_CLKSource_HCLK_Div8);//使用外部时钟源来设置滴答定时器的频率
    fac_us=SYSCLK/8;
    reload=SYSCLK/8;
    reload*=1000000/delay_ostickspersec;
    fac_ms=1000/delay_ostickspersec;
    SysTick->CTRL|=SysTick_CTRL_TICKINT_Msk;
    SysTick->LOAD=reload;
    SysTick->CTRL|=SysTick_CTRL_ENABLE_Msk;
}
~~~

*需要注意的是，滴答定时器有自己的频率，并不是跟外部时钟源的频率相同的！*

__滴答定时器__:
|地址|寄存器|描述|
|---|---|---|
|0xE000E010|SysTick->CTRL|控制及状态寄存器|
|0xE000E014|SysTick->LOAD|重装载值寄存器|
|0xE000E018|SysTick->VAL|当前数值寄存器|
|0xE000E01C|SysTick->CALIB|校准数值寄存器|

SysTick控制及状态寄存器：
|位段|名称|类型|复位值|描述|
|---|---|---|---|---|
|16|COUNTFLAG|R|0|如果上次读取本寄存器后，SysTick已经数到了0，则该位为1.如果读取该为，则将自动清零|
|2|CLKSOURCE|R/W|0|=0外部时钟，=1内部时钟|
|1|TICKINT|R/W|0|=1 SysTick倒数到0时产生中断 =0时无动作|
|0|ENABLE|R/W|0|SysTick的使能位|

SysTick重装载数值寄存器：
|位段|名称|类型|复位值|描述|
|---|---|---|---|---|
|23：0|RELOAD|R/W|0|当倒数到0时，会被重新装载的值|

### usart.c和sys.c
使用ucos和不使用ucos下usart.c的最大区别就是串口1中断服务函数`USART1_IRQHandler()`，sys.c文件则完全相同。
~~~c
void XXX_Handler(void)
{
    OSIntEnter();
    //自己的中断函数代码
    OSIntExit();
}
~~~

## 任务的基本概念
在设计复杂程序时，通常可以将程序分割成许多简单的小程序，这些小程序就是单个的任务（一般一个任务就是一个while循环）。在操作系统中，可以实现这小小任务的并发执行，从而提高cpu的使用效率。
UCOS-III中的任务由三部分组成：任务堆栈、任务控制块、任务函数。
1. 任务堆栈：上下文切换时用来保存任务的工作环境，就是stm32的内部寄存器值
2. 任务控制块：任务控制块用来记录任务的各个属性
3. 任务函数：由用户编写的任务处理代码，一般写法如下：
~~~c
void Task_name(void *p_arg)
{
    OS_ERR err;
    p_arg = p_arg;
    while(1)
    {
        ....
    }
}
~~~

### UCOSIII的系统任务
UCOSIII默认有5个系统任务：
1. 空闲任务：UCOSIII自动创建的第一个任务。
2. 时钟节拍任务：此任务也是必须创建的任务（系统自动创建）。
3. 统计任务：可选任务，用来统计CPu使用率和各个任务的堆栈使用量。由宏`OS_CFG_STAT_TASK_EN`控制是否使用此任务。
4. 定时任务：用来向用户提供定时服务，由宏`OS_CFG_TMR_EN`控制
5. 中读服务管理任务：可选任务，由宏`OS_CFG_ISR_POST_DEFERRED_EN`控制
### 任务的状态
一共有5种状态
1. 休眠态：任务已将在flash中了 ，但是不受UCOS管理。
2. 就绪态：系统位任务分配了任务控制块，并且任务已经在就绪表中登记，这时这个任务就具有了运行的条件，此时任务的状态就是就绪态（已经受UCOS管理）。
3. 运行态：任务获取CPU使用权，正在运行。
4. 等待态：正在运行的任务需要等待一段时间，或等待某个事件，这个任务就进入了等待态，此时系统会把CPU的使用权转交给其它任务。
5. 中断服务态：当发送中断时，当前运行的任务会被挂起，CPU转而去执行中断服务函数，此时被挂起的任务叫做中断服务函数。

## UCOSIII任务堆栈
RAM中遵照先进先出（FIFO）的原则组织的一块连续的存储空间。
任务堆栈的创建：
~~~c
#define START_STK_SIZE 512 //堆栈大小
CPU_STK START_TASK_STK[START_STK_SIZE];定义一个数组来作为任务堆栈
~~~
这里的512不一定是512字节，`CPU_STK`为`CPU_INT32U`类型，也就是`unsigned int`类型，是4字节的，那么任务堆栈的大小就是512x4=2048字节
### 任务堆栈的初始化
任务切换回上一个任务并接着从上次断开的地方开始运行，称为恢复现场，现场就是CPU的内部各个寄存器。因此创建一个任务时，必须把系统启动这个任务时所需的CPU各个寄存器初始值存放在任务堆栈中。这样当任务获得CPU使用权时，就把任务堆栈中的内容复制到CPu的各个寄存器中，从而让任务顺利启动并运行。把任务初始数据存放到任务堆栈中的工作就叫任务堆栈初始化，函数是`OSTaskStkInit()`，但是用户一般不会直接操作这个函数，而是通过任务创建函数`OSTaskCreate()`调用。
### 使用创建的任务堆栈
创建的任务堆栈其实是作为`OSTaskCreate()`的参数之一。

~~~c
void OSTaskCreate(OS_TCB *p_tcb,
                CPU_CHAR *p_name,
                OS_TASK_PTR p_task,
                void *p_arg,
                OS_PRIO prio,
                CPU_STK *p_stk_base, //任务堆栈的基地址
                CPU_STK_SIZE stk_limit,//任务堆栈栈深
                CPU_STK_SIZE stk_size,//任务堆栈大小
                OS_MSG_QTY q_size,
                OS_TICK time_quanta,
                void *p_ext,
                OS_OPT opt,
                OS_ERR *p_err)
~~~

>什么是任务堆栈的栈深？

当剩余的堆栈量是`stk_limit`时，警告我们堆栈就要用完了，没有什么实际作用，只是起一个限度的效果。一般取10%的堆栈总量。

## 任务控制块
用来记录与任务相关的信息的数据结构。每个任务都有自己的任务控制块。使用简单的代码即可创建一个任务控制块`OS_TCB TaskNameTCB;`
### 任务控制块初始化
函数`OSTaskCreate()`在创建任务时会对任务的任务控制块进行初始化，用户不需要自己进行初始化。
由于`OS_TCB`任务控制块是一个数据结构，因此其实初始化就是给成员变量赋初值（一般是0）.
### 任务就绪表
>优先级

__在ucosIII中的优先级数字越小，实际优先级就越高！__
最低可用优先级就是`OS_CFG_PRIO_MAX-1`.`OS_CFG_PRIO_MAX`是优先级数的宏定义，用户可以自行修改（默认64）.
>就绪表

就绪表包含2个部分，优先级位映射表`OSPrioTbl[]`和就绪任务列表`OSRdyList[]`
1. 优先级位映射表`OSPrioTbl[]`用来记录哪个优先级下有任务就绪。这个表位于`os_prio.c`中，</br>
`CPU_DATA OSPrioTbl[OS_PRIO_TBL_SIZE];`</br>
其中`CPU_DATA`是unsigned int,在stm32占有4字节，32位，因此：

~~~c 
OS_PRIO_TBL_SIZE=((OS_CFG_PRIO_MAX-1u)/DEF_INT_CPU_NBR_BITS+1);
DEF_INT_CPU_NBR_BITS=CPU_CFG_DATA_SIZE*DEF_OCTET_NBR_BITS;
CPU_CFG_DATA_SIZE=CPU_WORD_SIZE_32=4;
DEF_OCTET_NBR_BITS=8;
//所以，当有64个优先级时
OS_PRIO_TBL_SIZE=（（64-1）/(4*8)+1）=2
~~~
通过函数`OS_PrioGetHighest()`找到就绪了的最高优先级的任务。__计算前导零法__（计算某个2进制数的前面有几个零）
~~~c 
OS_PRIO OS_PrioGetHighest(void)
{
    CPU_DATA *p_tbl;
    OS_PRIO prio;
    prio=(OS_PRIO)0;
    p_tbl=&OSPrioTbl[0];
    while(*p_tbl==(CPU_DATA)0)
    {
        prio+=DEF_INT_CPU_NBR_BITS;
        p_tbl++;
    }    
    prio+=(OS_PRIO)CPU_CntLeadZeros(*p_tbl);//计算前导零（硬件计算）
    return(prio);
}
~~~

2. 就绪任务列表`OSRdyList[]`用来记录每一个优先级下所有就绪的任务。`OSRdyList[]`是一个结构体数组，它的成员有`HeadPtr`（任务链表头指针）,`TailPtr`（任务链表尾指针）,`NbrEntries`（这个优先级下的任务总数）。

*2020/8/27 21：28*

# ucosIII的任务调度
## 可剥夺型任务调度
任务调度就是中止当前正在运行的任务转而去执行其它任务。
UCOSIII中的任务调度是通过任务调度器来完成的，任务调度器有2种，任务级调度器`OSSched()`和中断级调度器`OSIntExit()`。
## 任务调度点
顾名思义就是发生任务调度的时间点。
1. 释放信号量或者发送信息，也可以通过配置相应的参数不发生任务调度。
2. __使用延时函数`OSTimeDly()`或`OSTimeDlyHMSM()`__(由于正点原子的delay.c里面的delay_ms函数时对OSTimeDly的封装，因此调用delay_ms也会发生任务调度)
3. 任务等待的事情还没有发生（等待信号量，消息队列等）
4. 任务取消等待
5. 创建、删除任务
6. 删除一个内核对象
7. 任务改变自身的优先级或其它任务的优先级
8. 任务通过调用`OSTaskSuspend()`将自身挂起
9. 任务解挂某个任务
10. 退出所有的嵌套中断
11. 通过`OSSchedUnlock()`给调度器解锁
12. 调用`OSSchedRoundRobinYield()`放弃其执行时间片
13. __用户调用`OSSched()`__
## 调度器上锁和解锁
有时候我们不希望发生任务调度，因此可以使用函数`OSSchedLock()`给调度器上锁，调用`OSSchedUnlock()`给调度器解锁。
## 时间片轮转调度
UCOSIII允许一个优先级下有多个任务，每个任务可以执行指定的时间（时间片），然后轮到下一个任务，当一个任务不想再运行时，可以放弃其时间片。

时间片轮转调度器为`OS_SchedRoundRobin()`

## 任务切换
当UCOSIII需要切换到另一个任务时，它将保存当前任务的现场到当前任务的堆栈中，主要是CPU寄存器值，然后恢复现场并执行新的任务，这个过程就是任务切换，任务切换分2种，任务级切换`OSCtxSw()`和中断级切换函数`OSIntCtxSw()`

# UCOSIII系统初始化和启动
## 初始化
使用UCOSIII之前必须先初始化UCOSIII，函数`OSInit()`用来完成UCOSIII的初始化，并且需要先于其它UCOSIII函数使用，包括`OSStart()`

~~~c
int main(void)
{
    OS_ERR err;
    ....
    //其它外设等的初始化
    ....
    OSInit(&err);
    ....
    //其它函数，一般是任务创建函数
    ....
    OSStart(&err);
}
~~~

以下5个优先级分配给了系统任务，用户无法使用：
1. 优先级0：中断服务管理任务
2. 优先级1：时钟节拍任务
3. 优先级2：定时任务
4. 优先级OS_CFG_PRIO_MAX-2：统计任务
5. 优先级OS_CFG_PRIO_MAX-1：空闲任务
## 系统启动
使用函数`OSStart()`来启动ucosiii

# UCOSIII中任务管理相关的API
## 任务的创建`OSTaskCreate()`
函数原型：</br>
>opt选项

1. OS_OPT_TASK_NONE 无选项
2. OS_OPT_TASK_STK_CHK 指定是否允许检测该任务的堆栈
3. OS_OPT_TASK_STK_CLR 指定是否清除该任务的堆栈
4. OS_OPT_TASK_SAVE_FP 指定是否存储浮点寄存器，CPU需要有浮点寄存器并且有专用代码保存浮点寄存器。

一个任务的创建流程

~~~c
//定义任务优先级
#define START_TASK_PRIO 3

//定义任务堆栈大小
#define START_TASK_STK_SIZE 128

//定义任务控制块
OS_TCB StartTaskTCB;

//定义任务堆栈
CPU_STK START_TASK_STK[START_TASK_STK_SIZE]

//声明一下任务函数
void Start_task(void *p_arg);//其中这个参数p_arg是必须的，即使你不需要它


int main(void)
{
    OS_ERR err;
    CPU_SR_ALLOC();
    delay_init(72);//时钟初始化
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2);//中断组配置
    uart_init(115200);//串口波特率初始化

    OSInit(&err);
    OS_CRITICAL_ENTER();//进入临界区保护

    OSTaskCreate((OS_TCB*)&StartTaskTCB,
                (CPU_CHAR*)"start task",
                (OS_TASK_PTR)Start_task,
                (void*)0,
                (OS_PRIO)START_TASK_PRIO,
                (CPU_STK*)&START_TASK_STK[0],
                (CPU_STK_SIZE)START_TASK_STK_SIZE/10,
                (CPU_STK_SIZE)START_TASK_STK_SIZE,
                (OS_MSG_QTY)0,
                (OS_TICK)0,
                (void*)0,
                (OS_OPT)OS_OPT_TASK_STK_CHK|OS_OPT_TASK_STK_CLR,
                (OS_ERR*)&err
                )

    OS_CRITICAL_EXIT();//退出临界区
    OSStart(&err);
}
void start_task(void *p_arg)
{
    OS_ERR err;
    CPU_SR_ALLOC();
    p_arg=p_arg;//这一步的作用是防止编译器报错，说我们没有用到参数p_arg

    CPU_Init();
    //。。。。中间部分见例程
    OS_CRITICAL_ENTER();
    //创建其它任务
    OS_CRITICAL_EXIT();
    OSTaskDel((OS_TCB*)0,&err);//删除start_task本身
}
~~~
一般我们在main里面先创建这个start任务 ，然后在start任务里面写其它的任务！

## 任务的删除`void OSTaskDel(OS_TCB *p_tcb,OS_ERR *p_err)`
删除某个任务后，它占用的OS_TCB和堆栈就可以再次利用来创建其它任务，但需要注意的是，如果要删除的任务占有有其它任务共享的资源，在删除任务前这个资源没有被释放，就可能出现奇怪的现象。
*2020/8/28 15:56*

