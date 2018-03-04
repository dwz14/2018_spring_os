<html lang="en"><head>
    <meta charset="UTF-8">
    <title></title>
<style id="system" type="text/css">h1,h2,h3,h4,h5,h6,p,blockquote {    margin: 0;    padding: 0;}body {    font-family: "Helvetica Neue", Helvetica, "Hiragino Sans GB", Arial, sans-serif;    font-size: 13px;    line-height: 18px;    color: #737373;    margin: 10px 13px 10px 13px;}a {    color: #0069d6;}a:hover {    color: #0050a3;    text-decoration: none;}a img {    border: none;}p {    margin-bottom: 9px;}h1,h2,h3,h4,h5,h6 {    color: #404040;    line-height: 36px;}h1 {    margin-bottom: 18px;    font-size: 30px;}h2 {    font-size: 24px;}h3 {    font-size: 18px;}h4 {    font-size: 16px;}h5 {    font-size: 14px;}h6 {    font-size: 13px;}hr {    margin: 0 0 19px;    border: 0;    border-bottom: 1px solid #ccc;}blockquote {    padding: 13px 13px 21px 15px;    margin-bottom: 18px;    font-family:georgia,serif;    font-style: italic;}blockquote:before {    content:"C";    font-size:40px;    margin-left:-10px;    font-family:georgia,serif;    color:#eee;}blockquote p {    font-size: 14px;    font-weight: 300;    line-height: 18px;    margin-bottom: 0;    font-style: italic;}code, pre {    font-family: Monaco, Andale Mono, Courier New, monospace;}code {    background-color: #fee9cc;    color: rgba(0, 0, 0, 0.75);    padding: 1px 3px;    font-size: 12px;    -webkit-border-radius: 3px;    -moz-border-radius: 3px;    border-radius: 3px;}pre {    display: block;    padding: 14px;    margin: 0 0 18px;    line-height: 16px;    font-size: 11px;    border: 1px solid #d9d9d9;    white-space: pre-wrap;    word-wrap: break-word;}pre code {    background-color: #fff;    color:#737373;    font-size: 11px;    padding: 0;}@media screen and (min-width: 768px) {    body {        width: 748px;        margin:10px auto;    }}</style><style id="custom" type="text/css"></style></head>
<body marginheight="0"><h1>第二次作业</h1>
<h2>思考题</h2>
<h3>你理解的对于类似ucore这样需要进程/虚存/文件系统的操作系统，在硬件设计上至少需要有哪些直接的支持？至少应该提供哪些功能的特权指令？你理解的x86的实模式和保护模式有什么区别？物理地址、线性地址、逻辑地址的含义分别是什么？</h3>
<p>护模式和实模式的根本区别是进程内存是否受保护。实模式将整个物理内存看成分段的区域，程序代码和数据位于不同区域，系统程序和用户程序没有区别对待，而且每一个指针都是指向“实在”的物理地址。这样一来，用户程序的一个指针如果指向了系统程序区域或其他用户程序区域，并改变了值，那么对于这个被修改的系统程序或用户程序，其后果就很可能是灾难性的。为了克服这种低劣的内存管理方式，处理器厂商开发出保护模式。这样，物理内存地址不能直接被程序访问，程序内部的地址（虚拟地址）要由操作系统转化为物理地址去访问，程序对此一无所知。
物理地址：是处理器提交到总线上用于访问计算机系统中的内存和外设的最终地址。
逻辑地址：在有地址变换功能的计算机中，访问指令给出的地址叫逻辑地址。
线性地址：线性地址是逻辑地址到物理地址变换之间的中间层，是处理器通过段(Segment)机制控制下的形成的地址空间。
</p>
<h3>理解list_entry双向链表数据结构及其4个基本操作函数和ucore中一些基于它的代码实现（此题不用填写内容）</h3>
<p>对于如下的代码段，请说明":"后面的数字是什么含义

</p>
<p> /<em> Gate descriptors for interrupts and traps </em>/

</p>
<p> struct gatedesc {

</p>
<pre><code>unsigned gd_off_15_0 : 16;        // low 16 bits of offset in segment
unsigned gd_ss : 16;            // segment selector
unsigned gd_args : 5;            // # args, 0 for interrupt/trap gates
unsigned gd_rsv1 : 3;            // reserved(should be zero I guess)
unsigned gd_type : 4;            // type(STS_{TG,IG32,TG32})
unsigned gd_s : 1;                // must be 0 (system)
unsigned gd_dpl : 2;            // descriptor(meaning new) privilege level
unsigned gd_p : 1;                // Present
unsigned gd_off_31_16 : 16;        // high bits of offset in segment</code></pre>
<p> };


</p>
<p>“:”后的数字表示每一个域在结构体中所占的位数.

</p>
<h3>对于如下的代码段，</h3>
<p>/#define SETGATE(gate, istrap, sel, off, dpl) 
{

</p>
<pre><code>(gate).gd_off_15_0 = (uint32_t)(off) &amp; 0xffff;        \
(gate).gd_ss = (sel);                                \
(gate).gd_args = 0;                                    \
(gate).gd_rsv1 = 0;                                    \
(gate).gd_type = (istrap) ? STS_TG32 : STS_IG32;    \
(gate).gd_s = 0;                                    \
(gate).gd_dpl = (dpl);                                \
(gate).gd_p = 1;                                    \
(gate).gd_off_31_16 = (uint32_t)(off) &gt;&gt; 16;        \</code></pre>
<p>}
如果在其他代码段中有如下语句，

</p>
<p>unsigned intr;

</p>
<p>intr=8;

</p>
<p>SETGATE(intr, 1,2,3,0);

</p>
<p>请问执行上述指令后， intr的值是多少？

</p>
<p>对应代码如下：
/#include <stdio.h>

</stdio.h></p>
<p>typedef unsigned uint32_t;

</p>
<p>/#define STS_IG32 0xE  // 32-bit Interrupt Gate
/#define STS_TG32 0xF  // 32-bit Trap Gate

</p>
<p>/#define SETGATE(gate, istrap, sel, off, dpl)

</p>
<pre><code>{                                                    \
    (gate).gd_off_15_0 = (uint32_t)(off)&amp;0xffff;     \
    (gate).gd_ss = (sel);                            \
    (gate).gd_args = 0;                              \
    (gate).gd_rsv1 = 0;                              \
    (gate).gd_type = (istrap) ? STS_TG32 : STS_IG32; \
    (gate).gd_s = 0;                                 \
    (gate).gd_dpl = (dpl);                           \
    (gate).gd_p = 1;                                 \
    (gate).gd_off_31_16 = (uint32_t)(off) &gt;&gt; 16;     \
}</code></pre>
<p>/<em> Gate descriptors for interrupts and traps </em>/

</p>
<p>struct gatedesc {

</p>
<pre><code>unsigned gd_off_15_0 : 16;   // low 16 bits of offset in segment
unsigned gd_ss : 16;         // segment selector
unsigned gd_args : 5;        // # args, 0 for interrupt/trap gates
unsigned gd_rsv1 : 3;        // reserved(should be zero I guess)
unsigned gd_type : 4;        // type(STS_{TG,IG32,TG32})
unsigned gd_s : 1;           // must be 0 (system)
unsigned gd_dpl : 2;         // descriptor(meaning new) privilege level
unsigned gd_p : 1;           // Present
unsigned gd_off_31_16 : 16;  // high bits of offset in segment</code></pre>
<p>};

</p>
<p>int main(int argc, char const* argv[]) {
    unsigned intr = 8;

</p>
<pre><code>gatedesc gate = *(gatedesc*)&amp;intr;
SETGATE(gate, 1, 2, 3, 0);

intr = *(unsigned*)&amp;gate;
printf("0x%x\n", intr);
return 0;</code></pre>
<p>}
输出结果为0x20003，若将SETGATE(gate, 1, 2, 3, 0)改为SETGATE(gate, 0, 1, 2, 3)，则结果为0x10002。
Edit By <a href="http://mahua.jser.me">MaHua</a></p>
</body></html>