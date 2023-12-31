# Практическая работа по Verilog №2 
## Verilog always block
В этом подпункте реализованы примеры со страницы 

[verilog-always-block](https://www.chipverify.com/verilog/verilog-always-block)
### Пример 1
Блок always обладает следующей структурой: 
```verilog
  always @(<sensitivity_list>) <statements>
```
Этот блок выполняется при изменении сигналов, описанных в <sensitivity_list>. 
В примере демонстрируется использование блока always без sensitivity list. 
Если при этом не указать иным способом время задержки, симуляция зависнет. Ниже приведен код и скриншоты симуляции, демонстрирующие это. 
```verilog
  module testbench; 
  reg cls;
    initial begin
      cls = 1;
    end
    always cls = ~cls;
  endmodule
```
![Изображение 1](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/1.png)
Чтобы избежать этого, можно прямо указать время задержки:  
```verilog
  //`timescale 1ns / 1ns 
  module testbench; 
  reg cls;
    initial begin
      cls = 1;
    end
    always #10 cls = ~cls;
  endmodule
```
В этом случае переменная cls будет менять свое значение каждые 10 ps.
![Изображение 2](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/2.png)
### Пример 2 (sequential element design example )
В этом примере создается модуль ttf, получающий на вход d, clk и rstn и отдающий на выход q. Этот модуль содержит блок always, срабатывающий по отрицательному фронту входного значения rstn и по положительному фронту значения clk. 
```verilog
module ttf (input d,
		  clk,
      		  rstn, 
	    output reg q);
	always @(posedge clk or negedge rstn) begin
		if(!rstn)
			q<=0;
		else
			if (d)
				q<= ~q;
			else
				q <= q;
	end
endmodule
```
В таблице ниже приведены все возможные варианты работы этого модуля. 
<table>
    <thead>
        <tr>
            <th> sensitivity list </th>
            <th> rst </th>
            <th> d </th>
	    <th> q </th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3 align="center">положительный фронт clk</td>
            <td align="center">  0 </td>
	    <td align="center"> ∀ </td>
            <td align="center">  0 </td>
        </tr>
        <tr>
            <td rowspan=2 align="center"> 1</td>
	    <td align="center">  1 </td>
	    <td align="center"> -q  </td>
        </tr>
        <tr>
	    <td align="center">  0 </td>
	    <td align="center">  q </td>
        </tr>
        <tr>
            <td align="center">отрицательный фронт rstn</td>
	    <td align="center">  0 </td>
	    <td align="center"> ∀  </td>
            <td align="center"> 0</td>
        </tr>
    </tbody>
</table>
Все эти случаи можно увидеть на рис. ниже. На нем представленны результаты симуляции и синтеза. Исходные файлы лежат в этой папке под названиями ttf.v и ttf_testbench.v: 

1. [ttf.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/ttf.v)
2. [ttf_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/ttf_testbench.v)

![Изображение 3](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/3.png)

### Пример 3 (combinational element design)

Этот пример показывает, как используется блок always в реализации комбинационных схем. В данном случае была взята следующая схема: 

![Изображение 4](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/4.png)

Ee реализация на языке verilog:
```verilog
module combo   (input a,
		input b,
		input c,
		input	d,
		output reg o);
	always @(a or  b or c or d) begin
		o <= ~((a & b) | (c^d)); 
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 5](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/5.png)

Для проверки корректности работы можно сравнить таблицу истинности комбинационной схемы (слева) и вывод симуляции (справа) 

![Изображение 6](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/6.png)

Исходники примера хранятся в следующих файлах:

1. [combo.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/combo.v)
2. [combo_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/combo_testbench.v)

## Combinational Logic with always 
В этом подпункте реализованы примеры со страницы 

[verilog-combinational-logic-always](https://www.chipverify.com/verilog/verilog-combinational-logic-always)

### Пример 1 (simple combinational logic)

В этом примере также реализовывается комбинационная схема (изображена на рисунке ниже).

![Изображение 7](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/7.png)

Ee реализация на языке verilog:
```verilog
module combo_2 (input a, 
		input b,
		input c,
		input d,
		input e,
		output reg z);
	always @(a or  b or c or d or e) begin
		z = ((a & b) | (c^d) & (~e)); 
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 8](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/8.png)

Для проверки корректности работы можно сравнить таблицу истинности из примера (слева) и вывод симуляции (справа) 

![Изображение 9](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/9.png)

Исходники примера хранятся в следующих файлах:

1. [combo_2.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/combo_2.v)
2. [combo_2_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/combo_2_testbench.v)

### Пример 2 (half adder)

В этом примере реализован полусумматор - схема, вычисляющая сумму двух одноразрядных двоичных чисел. Она имеет 2 входа - a и b, и 2 выхода - sum и cout. В sum хранится младший разряд, в cout - старший. Схема представлена на рисунке ниже 

![Изображение 10](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/10.png)

Ee реализация на языке verilog:
```verilog
module ha (input a, 
	   input b,
	   output reg sum, cout);
	always @(a or  b) begin
		{cout, sum} = a + b;
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 11](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/11.png)

Вывод симуляции соответствует ее теоретическому описанию  

![Изображение 12](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/12.png)

Исходники примера хранятся в следующих файлах:

1. [ha.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/ha.v)
2. [ha_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/ha_testbench.v)

### Пример 3 (full adder)

В этом примере реализован полный сумматор - схема, вычисляющая сумму трех одноразрядных двоичных чисел. Она имеет 3 входа - a, b и cin, и 2 выхода - sum и cout. В sum хранится младший разряд, в carry - старший. Схема представлена на рисунке ниже 

![Изображение 13](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/13.png)

Ee реализация на языке verilog:
```verilog
module ha (input a, 
	   input b,
	   input cin,
	   output reg sum, cout);
	always @(a or  b) begin
		{cout, sum} = a + b + cin;
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 14](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/14.png)

Вывод симуляции соответствует ее теоретическому описанию  

![Изображение 15](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/15.png)

Исходники примера хранятся в следующих файлах:

1. [fa.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/fa.v)
2. [fa_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/fa_testbench.v)

### Пример 4 (2x1 multiplexer)

В этом примере реализован мультиплексор - схема, позволяеющая передать сигнал с одного из двух входов (a или b) на выход c; при этом выбор желаемого входа осуществляется подачей управляющего сигнала sel. Схема представлена на рисунке ниже 

![Изображение 16](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/16.png)

Ee реализация на языке verilog:
```verilog
module mux_2x1 (input a, b, sel, 
		output reg c);
				
	always @(a or b or sel) begin
		c = sel ? a : b;
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 17](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/17.png)

Вывод симуляции соответствует ее теоретическому описанию  

![Изображение 18](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/18.png)

Исходники примера хранятся в следующих файлах:

1. [mux_2x1.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/mux_2x1.v)
2. [mux_2x1_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/mux_2x1_testbench.v)

### Пример 5 (1x4 demultiplexer)

В этом примере реализован демультиплексор 1x4 - схема, подключающая входной сигнал f к одному из четырех выходов (a,b,c,d), номер которого задается sel. Схема представлена на рисунке ниже 

![Изображение 19](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/19.png)

Ee реализация на языке verilog:
```verilog
module demux_1x4 (input f,
		  input [1:0] sel, 
		  output reg a, b, c, d);
				
	always @(f or sel) begin
		a = f & ~sel[1] & ~sel[0];
		b = f & sel[1] & ~sel[0];
		c = f & ~sel[1] & sel[0];
		d = f & sel[1] & sel[0];
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 20](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/20.png)

Вывод симуляции соответствует ее теоретическому описанию  

![Изображение 21](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/21.png)

Исходники примера хранятся в следующих файлах:

1. [demux_1x4.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/demux_1x4.v)
2. [demux_1x4_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/demux_1x4_testbench.v)

### Пример 6 (4x16 decoder)  

В этом примере реализуется 3-битный декодер с восьмью выходами. Он включает один из восьми выходов, соответствующий значению входа in, только если вход en равен 1. В противном случае все выходы будут равны 0. Схема представлена на рисунке ниже 

![Изображение 24](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/24.png)

Ee реализация на языке verilog:
```verilog
module dec_3x8 (input en,
		input [3:0] in, 	 
		output reg [15:0] out);
				
	always @(en or in) begin
		out = en ? 1 << in: 0;
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 25](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/25.png)

Вывод симуляции (справа) соответствует ее теоретическому описанию и совпадает с выводом, приведенным в примере (слева)

![Изображение 26](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/26.png)

Исходники примера хранятся в следующих файлах:

1. [dec_3x8.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/dec_3x8.v)
2. [dec_3x8_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/dec_3x8_testbench.v)

## Sequential logic with always
В этом подпункте реализованы примеры реализации схем последовательной логики со страницы 

[verilog-always-block](https://www.chipverify.com/verilog/verilog-sequential-logic-always)

### Пример 1 (jk flip flop)  

В этом примере реализуется JK-триггер с асинхронным сбросом, который может изменять свое состояние в зависимости от значений входов j и k, а также сигнала сброса rstn при наличии положительного фронта тактового сигнала clk.

![Изображение 27](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/27.png)

Ee реализация на языке verilog:
```verilog
module jk_ff    (input j,
		input k,
		input rstn,
		input clk,
		output reg q);
				
	always @(posedge clk or negedge rstn) begin
		if(!rstn) begin
			q <= 0;
		end else begin
			q <= (j & ~q) | (~k & q);
		end
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 28](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/28.png)


Исходники примера хранятся в следующих файлах:

1. [jk_ff.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/jk_ff.v)
2. [jk_ff_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/jk_ff_testbench.v)


### Пример 2 (modulo-10 counter)  

В этом примере реализуется 4-битный модульный счетчик с асинхронным сбросом.
При положительном фронте тактового сигнала clk происходит выполнение следующих операций:

1. Если сигнал сброса rstn равен 0, то выход out устанавливается в 0 (сброс счетчика).

2. Если сигнал сброса rstn равен 1, то выполняется следующая логика:
   - Если текущее значение счетчика out равно 10, то выход out устанавливается в 0 (обнуление счетчика).
   - В противном случае, текущее значение счетчика out увеличивается на 1.
     
Ee реализация на языке verilog:
```verilog
module mod10_counter (input clk,
	input rstn,
	output reg [3:0] out);
				
	always @(posedge clk) begin
		if(!rstn) begin
			out <= 0;
		end else begin
			if (out == 10)
				out <= 0;
			else 
				out <= out + 1;
		end
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 29](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/29.png)

Исходники примера хранятся в следующих файлах:

1. [mod10_counter.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/mod10_counter.v)
2. [mod10_counter_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/mod10_counter_testbench.v)

 ### Пример 3 (4bit left shift register)  
 
 В этом примере реализуется 4-битный регистр с левым сдвигом и асинхронным сбросом.
При положительном фронте тактового сигнала clk происходит выполнение следующих операций:

При положительном фронте тактового сигнала clk происходит выполнение следующих операций:

1. Если сигнал сброса rstn равен 0, то выход out устанавливается в 0 (сброс регистра).

2. Если сигнал сброса rstn равен 1, то выполняется следующая логика:
   - Значение выхода out обновляется путем сдвига его битов влево на одну позицию.
   - Младший бит регистра заменяется значением входа данных d.

Ee реализация на языке verilog:
```verilog
module lshift_4b_reg (input d,
			input clk, 
			input rstn,
			output reg [3:0] out);
				
	always @(posedge clk) begin
		if (!rstn) begin
			out <= 0;
		end else begin
			out <= {out[2:0], d};
		end
	end
endmodule
```
Результат симуляции и синтеза представленны на рисунке ниже. 

![Изображение 23](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/images/23.png)

Исходники примера хранятся в следующих файлах:

1. [lshift_4b_reg.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/lshift_4b_reg.v)
2. [lshift_4b_reg_testbench.v](https://github.com/Tamara-Kaplun/hw_fpga/blob/main/hw2/lshift_4b_reg_testbench.v)


