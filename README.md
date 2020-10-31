# Shell_Protect
PE虚拟器框架，项目意图给学习软件保护加壳的初学者提供一些思路和引导。支持一键加壳/脱壳，全压缩/加密，IAT加密等。
1. 压缩库使用lz4/quicklz，以保证x32/x64压缩稳定。

2. 虚拟壳目前仅支持x64加代码片段(Main)，这只是一个示例和思路，如何自己编写虚拟机，运行态保存上下文环境及维护堆栈。
- 加壳器对需要加密的代码段进行Vmcode，简单点说加密，再dll中也就是壳代码执行vmentry，进入虚拟机。
- 虚拟机会读取加密的代码段进行解密，变成正常的Opcode或者直接识别Vmcode都可以，解析器负责对Vmcode进行分析和区分什么汇编。
mov eax, 23h  Vmcode: xx xx xx xx 进入Vmcode读取xx xx xx xx，进入Vm解析器分析，解析器分析后知道他是mov eax,23。
- 解析器解析完成之后将这条指令分发处理，比如mov类的指令就分发到mov相关处理函数，mov处理函数再进行进一步字节码匹配.
- mov rax/rbx/rcx/r8/mm/imm等等，分别挂钩不同的handle(混淆执行器)来执行等同于mov eax,23h代码。
- 退出和进入Vmhandle时候会出现寄存器数据丢失，栈数据丢失，上下代码执行就会导致数据丢失，因为都是虚拟机再操作，所以要开辟对应的代码段的栈空间及寄存器环境。

虚拟机没有高级算法，只是简单加密用来阐述过程，壳中引入汇编引擎分析是错误的绝对，将会增加壳的体积。

因为能力/精力有限，没有去构造Vmcode代码分析引擎，只是构造了解密后代码分析引擎挂钩handle处理。所以再加壳时候记录了加密汇编大小/长度/基于该代码段的起始偏移，用来快速解密和处理分发。

注意：本项目仅支持加壳器中的Main函数，因处理的汇编映射指令有限如mov/lea/xor等，其它指令没有做处理。

加密解密书籍有虚拟壳代码，造轮子的意义在于学习：虚拟机和指令集映射，更好的理解虚拟机结构和协作。

推荐专业的虚拟机分析引擎(看雪版主玩命)： https://github.com/devilogic/cerberus.git 
