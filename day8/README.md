一.效率测试
    1. 结论: 在无阻塞状态下,多线程程序和单线程程序执行效率几乎差不多,甚至还不如单线程
        程序效率.但是多进程运行相同时间内容却可以有明显的效率提升.
    2. python线程的GIL问题(全局解释器锁)
        [1] 什么是GIL: 由于python解释器设计中加入了解释器锁,导致python解释器同一时刻
            只能解释执行一个线程,大大降低了线程的执行效率.
        [2] 导致后果: 因为遇到阻塞时线程会主动让出解释器去解释其他线程,所以Python多线程
            在执行多阻塞高延迟IO时可以提升程序效率,其他情况并不能对效率有所提升.
        [3] GIL问题建议: 
            * 尽量使用进程完成无阻塞的并发行为
            * 不适用c作为解释器(java,c#)

二.进程线程的区别和联系
    [1] 区别联系
        1. 两者都是多任务编程方式,都能使用计算机多核资源
        2. 进程的创建删除消耗的计算机资源比线程多
        3. 进程空间独立,数据互不干扰,有专门通信方法,线程使用全局变量通信
        4. 一个进程可以有多个分支线程,两者之间有包含关系
        5. 多个线程共享进程资源,在共享资源操作时往往需要同步互斥处理
        6. 进程线程在系统中都有自己的特有属性标志,如: ID,代码段,命令集等.
    [2] 使用场景
        1.任务场景: 如果是相对独立的任务模块,可能使用多进程,如果是多个分支共同形成一个整体任务,
            可能使用多线程
        2.项目结构: 多种编程语言实现不同的任务模块,可能是多进程,或者前后端分离应该各自为一个进程
        3.难易程度: 通信难度,数据处理的复杂度来判断用进程通信还是同步互斥方法
    
要求: 1. 对进程线程怎么理解/说说进程线程的差异
     2. 进程间通信知道哪些,有什么特点
     3. 什么是同步互斥,什么情况下使用,怎么用
     4. 给一个情形,说说用进程还是线程,为什么
     5. 问一些概念,僵尸进程的处理,GIL问题,进程状态

三. 网络通信模型
    1. 通信模型分类
        [1] 循环服务器模型: 循环接受客户端请求,处理请求.同一时刻只能处理一个请求,
            处理完一个在处理下一个.

            优点:　实现简单,占用资源少.
            缺点:　无法同时处理多个客户端请求

            适用情况：　处理的任务可以很快完成，无须长期占用服务端程序．
                    UDP比TCP更适合循环.
        
        [2] IO并发模型: 利用IO多路复用等技术同时处理多个客户端IO请求
            优点: 资源消耗少,能同时高效处理多个IO行为
            缺点: 只能处理并发产生的IO事件,无法处理cpu计算

            适用情况: HTTP请求,网络传输等都是IO行为,可以充分利用IO多路复用
        
        [3] 基于多进程/多线程的网络并发模型: 每当一个客户端连接一个服务器,就创建一个新的进程/线程       为该客户端服务,客户端退出时再销毁该进程/线程

            优点: 能同时满足多个客户端长期占有服务端需求,处理各种请求.
            缺点: 资源消耗较大

            适用情况: 客户端同时量较少,需要处理的行为较复杂的情况
    
    2. 多进程网络并发模型
    
        [1] 基于fork的多进程并发模型

            1. 创建监听套接字
            2. 等待接受客户端请求
            3. 客户端连接创建新的进程处理客户端请求
            4. 原进程继续等待其他客户端连接
            5. 如果客户端退出则销毁对应的进程

四. ftp文件服务器

    1. 功能
        [1] 服务器分为服务端和客户端,要求可以有多个客户端同时操作
        [2] 客户端可以查看服务器文件库中有什么文件
        [3] 客户端可以从文件库中下载文件到本地
        [4] 客户端可以上传一个本地文件到文件库
        [5] 使用print在客户端打印命令输入提示: 引导操作

    2. 技术分析
        [1] 使用fork多进程并发
        [2] TCP套接字传输
        [3] 获取文件列表    os.listdir()
            判断普通文件    os.path.isfile()

    3.结构设计

        [1] 客户端启动后打印界面
        [2] 客户端发起请求后服务器要判断能否处理,将结果反馈给客户端
        [3] 如果不能处理则终止请求行为,能处理则具体操作
        [4] 将文件具体处理功能封装为类
            
    4.具体功能分析
        [1] 搭建网络连接
        [2] 查看文件列表
        [3] 下载文件
        [4] 上传文件
        [5] 客户端退出