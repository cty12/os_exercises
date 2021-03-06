# lab0 SPOC思考题

## 个人思考题

---

能否读懂ucore中的AT&T格式的X86-32汇编语言？请列出你不理解的汇编语言。

>  http://www.imada.sdu.dk/Courses/DM18/Litteratur/IntelnATT.htm  
>  不理解 instr 这个指令，例如这个用法：`instr source,dest`  


虽然学过计算机原理和x86汇编（根据THU-CS的课程设置），但对ucore中涉及的哪些硬件设计或功能细节不够了解？  

>  X86 的四个工作模式我不了解；  
>  硬件课只讲了简单的 TLB 和页表，对于 X86 的实现不太清楚。

哪些困难（请分优先级）会阻碍你自主完成lab实验？

>  对于 C 的一些语法细节不熟悉；  
>  对 X86 架构不了解；  
>  对操作系统的一些常规实现不了解；  
>  不会写 makefile 。  

如何把一个在gdb中或执行过程中出现的物理/线性地址与你写的代码源码位置对应起来？

>  file 命令可以可以加载程序，就可以查看符号表里的符号对应的物理/线性地址。  

了解函数调用栈对lab实验有何帮助？

>  能学会管理进程。  

你希望从lab中学到什么知识？

>  写 bootloader ；  
>  如何实现文件系统；  
>  内存管理方法。  

---

## 小组讨论题

---

搭建好实验环境，请描述碰到的困难和解决的过程。

>  我使用的是 VMWare Fusion ，导入虚拟机镜像选的是提供的 OVF 镜像，过程中需要选择跳过检查。  
>  另外，为了得到显卡支持修改分辨率需要安装 VMTools 。  

熟悉基本的git命令行操作命令，从github上的[ucore git repo](http://www.github.com/chyyuu/ucore_lab)下载ucore lab实验

>  已完成。

尝试用qemu+gdb（or ECLIPSE-CDT）调试lab1

>  已完成，正确编译。

对于如下的代码段，请说明”：“后面的数字是什么含义
```
/* Gate descriptors for interrupts and traps */
struct gatedesc {
    unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
    unsigned gd_ss : 16;            // segment selector
    unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
    unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
    unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
    unsigned gd_s : 1;                // must be 0 (system)
    unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
    unsigned gd_p : 1;                // Present
    unsigned gd_off_31_16 : 16;        // high bits of offset in segment
};
```

>  表示的是位数，例如 gd_off_15_0 的意思就是 gatedesc 的后 16 位。

对于如下的代码段，
```
#define SETGATE(gate, istrap, sel, off, dpl) {            \
    (gate).gd_off_15_0 = (uint32_t)(off) & 0xffff;        \
    (gate).gd_ss = (sel);                                \
    (gate).gd_args = 0;                                    \
    (gate).gd_rsv1 = 0;                                    \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
    (gate).gd_s = 0;                                    \
    (gate).gd_dpl = (dpl);                                \
    (gate).gd_p = 1;                                    \
    (gate).gd_off_31_16 = (uint32_t)(off) >> 16;        \
}
```
如果在其他代码段中有如下语句，
```
unsigned intr;
intr=8;
SETGATE(intr, 0,1,2,3);
```
请问执行上述指令后， intr的值是多少？

>  首先需要注意 intr 是 32 位的，而在上述宏中当它是一个 64 位的使用，所以它前面的 32 位会被修改。  
>  结果是 0x00010002 。

请分析 [list.h](https://github.com/chyyuu/ucore_lab/blob/master/labcodes/lab2/libs/list.h)内容中大致的含义，并能include这个文件，利用其结构和功能编写一个数据结构链表操作的小C程序

```
#include <stdio.h>
#include <stdlib.h>
#include "list.h"

struct entry {

    list_entry_t node;
    int num;
};

int main() {
    struct entry head;
    list_entry_t* p = &head.node;
    list_init(p);
    head.num = 0;
    int i;
    for (i = 1; i != 10; i ++) {
        struct entry * e = (struct entry *)malloc(sizeof(struct entry));
        e->num = i;
        list_add(p, &(e->node));
        p = list_next(p);
    }
    //遍历输出除头尾以外的数字
    while ((p = list_prev(p)) != &head.node)
        printf("%d\n", ((struct entry *)p)->num);
    return 0;
}
```

---

## 开放思考题

---

是否愿意挑战大实验（大实验内容来源于你的想法或老师列好的题目，需要与老师协商确定，需完成基本lab，但可不参加闭卷考试），如果有，可直接给老师email或课后面谈。
- [x]  

>  

---
