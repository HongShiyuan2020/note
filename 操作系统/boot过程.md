当PC 的电源打开后，80x86 结构的CPU 将自动进入实模式，并从地址**0xFFFF0** 开始自动执行程序代码，这个地址通常是**ROM-BIOS** 中的地址。PC 机的BIOS 将执行某些系统的检测，并在**物理地址0** 处开始初始化**中断向量**。此后，它将可启动设备的**第一个扇区（磁盘引导扇区，512 字节）读入内存地址0x7C00 处**，并跳转到这个地方。

Linux 的最最前面部分是用8086 汇编语言编写的(boot/bootsect.s)，它将由BIOS 读入到内存0x7C00处，当它被执行时就会把自己移到绝对地址0x90000 处，并将启动设备(boot/setup.s)的下2kB 字节的代码读入内存0x90200 处，而内核的其它部分（system 模块）则被读入到从地址0x10000 开始处，因为当时system 模块的长度不会超过0x80000（即512KB），所以它不会覆盖在0x90000 处开始的bootsect 和setup模块。然后控制权将传递给boot/setup.s 中的代码，这是另一个实模式汇编语言程序。

启动部分识别主机的某些特性以及vga 卡的类型。如果需要，它会要求用户为控制台选择显示模式。然后将整个系统从地址0x10000 移至0x0000 处，进入保护模式并跳转至系统的余下部分（在0x0000 处）。此时所有32 位运行方式的设置启动被完成: **IDT、GDT 以及LDT 被加载**，**处理器和协处理器也已确认**，**分页工作**也设置好了；**最终调用init/main.c** 中的main()程序。

![[Pasted image 20240731195046.png]]