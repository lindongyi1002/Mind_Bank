## 1. continous assignment
```verilog
assign left_side = right_side;
```
* 赋值是连续的, 即右侧变化的同时左侧也会跟着变化, 所以叫做连续.
* 连续赋值不仅仅在一个时钟内进行和完成.

## 2. "&&"逻辑与;
"||"逻辑或;
"!"逻辑非;
nor或非;
xor异或;
xnor异或非(同或);

## 3. "!"表示逻辑取反,"~"表示按位取反.

## 4. \`define 与parameter的区别
\`define作用于整个工程，而parameter只作用于本模块，一旦\`define指令被编译，则在整个编译过程中都有效，所以仿真时使用\`define相对于parameter重声明占用更少的内存.

## 5. 一元约简运算符
* 对于如下代码如下的代码
```verilog
always @ (posedge clk or negedge rst_n)
    begin
	    if (!rst_n)
		    data <= 14'b0;
    	else if (state == CNV)
	    if ((|bit_cnt[4:1]) && (~&bit_cnt[4:1]) && time_cnt[0])	
	    	data <= {data[12:0],miso};
	    else
			data <= data;
    end
```
对第二个if else循环中的&、|有疑问，明明这两个在verilog中都是双目运算符，为什么在这里只有一侧有操作数，后来在其他博主那里看到，原来这样的用法叫做“一元约简运算符”。

1. 一元约简运算符解释
* 这种运算符和位运算符的符号是一模一样的，但是仍然能够正常阅读而不产生歧义，一元约简运算符是将后面的多位操作数依次进行位运算。

2. 例子
```verilog
|4'b1010    //1 | 0 | 1 | 0 即为1.
|4'b0010    //0 | 0 | 1 | 0 即为1.
|4'b1000    //1 | 0 | 0 | 0 即为1.

&4'b1010    //1 & 0 & 1 & 0 即为0.
&4'b0010    //0 & 0 & 1 & 0 即为0.
&4'b1000    //1 & 0 & 0 & 0 即为0.
```
3. 图一代码解释
* |bit_cnt[4:1] 代表从bit_cnt的第4位开始，依次进行或运算至第1位，得出最终结果。
* ~&bit_cnt[4:1] 代表从bit_cnt的第4位开始，依次进行与运算至第1位，然后再进行取反运算得出最终结果。

## 6. if-else和case语法不能传播不定态
因此有不定态输入的时候, 无法将异常在仿真时暴漏出来, 用assign语句替代更好,