## Welcome to ZFY Study：

备战2022蓝桥杯单片机，模块代码总结：


### 1.基础模块通道选择


```H138
void H138(unsigned char pos, unsigned char dat)			//pos给入的是74H138通道数值
{
	 P0 =  dat;
	 P2 = (P2 & 0x1f) | (pos << 5) ;
	 P2 &= 0x1f;
}
```
### 2.数码管动态扫描函数
```seg
unsigned char code Ledcode[] = {0xc0,0xf9,0xa4,0xb0,0x99,0x92,0x82,0xf8,0x80,0x90,0xff};//想要数码管熄灭可用数组第十个数据
unsigned char LEDCHAR[] = {0,0,0,0,0,0,0,0};
unsigned char pos_SMG;

void SMGscan()			                                                 //数码管的显的程序，2ms
{
	H138(6,0x00);      					 //消隐
	H138(7,0xff);
	H138(6,1<<pos_SMG);                       			            
	H138(7,Ledcode[LEDCHAR[pos_SMG]]);
	pos_SMG++;
	pos_SMG &= 0x07;
}
```
### 3.独立按键
```key
#define keyport P3						//按键扫描三行代码
unsigned char Trg;
unsigned char cont;

void keyscan()                //周期扫描，3ms
{
	unsigned char ReadData;
	ReadData = keyport ^ 0xff ;
	Trg = ReadData & (ReadData ^ cont)	;
	cont = ReadData ;
}
### 判断键值用switch函数
if(key_flag)                  
{
  keyscan()；
  switch(Trg)
  {
    case  0x01:  ； break;
    case  0x02:  ； break;
    case  0x04:  ； break;
    case  0x08:  ； break;
  }
  key_flag = 0;
}
```
### 3.矩阵键盘
```key-matrix
#define keyport P3
unsigned char Trg;
unsigned char cont;

#define set(x)      P4 = (x >> 2) ; P3 = x
#define get()      ((P4 & 0x10) << 3) | ((P4 & 0x04) << 4) | (P3 & 0x3f) 

void key_scan()         //周期扫描，3ms
{
	unsigned char dat;
	set(0xf0);
	dat = get();
	set(0x0f);
	dat = (dat | get()) ^ 0xff;
	Trg = dat & (dat ^ cont);
	cont = dat;
}

if(key_flag)        //判断键值
{
  key_scan();
  switch(Trg)
  {
    case 0x81: num = 0;break;
    case 0x82: num = 1;break;
    case 0x84: num = 2;break;
    case 0x88: num = 3;break;

    case 0x41: num = 4;break;
    case 0x42: num = 5;break;
    case 0x44: num = 6;break;
    case 0x48: num = 7;break;

    case 0x21: num = 8;break;
    case 0x22: num = 9;break;
    case 0x24: num = 10;break;
    case 0x28: num = 11;break;

    case 0x11: num = 12;break;
    case 0x12: num = 13;break;
    case 0x14: num = 14;break;
    case 0x18: num = 15;break;

  }
  key_flag = 0;
}

```
