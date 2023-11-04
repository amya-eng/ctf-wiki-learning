# ctf-wiki-learning
我开始学习ctf wiki并按照系统路线记录一些感悟和笔记。
## 端序（转载自https://zhuanlan.zhihu.com/p/352145413）
### 字节序，又称端序或尾序（英语中用单词：Endianness 表示），在计算机领域中，指电脑内存中或在数字通信链路中，占用多个字节的数据的字节排列顺序。
在几乎所有的平台上，多字节对象都被存储为连续的字节序列。例如在 Go 语言中，一个类型为int的变量x地址为0x100，那么其指针&x的值为0x100。且x的四个字节将被存储在内存的0x100, 0x101, 0x102, 0x103位置。  
字节的排列方式有两个通用规则:  
大端序（Big-Endian）将数据的低位字节存放在内存的高位地址，高位字节存放在低位地址。这种排列方式与数据用字节表示时的书写顺序一致，符合人类的阅读习惯。  
小端序（Little-Endian），将一个多位数的低位放在较小的地址处，高位放在较大的地址处，则称小端序。小端序与人类的阅读习惯相反，但更符合计算机读取内存的方式，因为CPU读取内存中的数据时，是从低地址向高地址方向进行读取的。  
上面的文字描述有点抽象，我们拿一个例子来解释一下字节排列时的大端序和小端序。  
在内存中存放整型数值168496141 需要4个字节，这个数值的对应的16进制表示是0X0A0B0C0D，这个数值在用大端序和小端序排列时的在内存中的示意图如下：  
![端序理解图](https://pic3.zhimg.com/v2-233887068992518bb4eec56de043d1b2_r.jpg "端序理解图")
### 为何要有字节序
很多人会问，为什么会有字节序，统一用大端序不行吗？答案是，计算机电路先处理低位字节，效率比较高，因为计算都是从低位开始的。所以，计算机的内部处理都是小端字节序。   
在计算机内部，小端序被广泛应用于现代 CPU 内部存储数据；而在其他场景，比如网络传输和文件存储则使用大端序。   
## python struct模块（转载自https://www.cnblogs.com/tomato0906/articles/4826759.html）   

import struct  
a = 20  
b = 400 
str = struct.pack("ii", a, b)  #转换后的str虽然是字符串类型，但相当于其他语言中的字节流（字节数组），可以在网络上传输  
print 'length:', len(str)  
print str  
print repr(str)  

#---- result  
#length: 8 
#----这里是乱码  
#'/x14/x00/x00/x00/x90/x01/x00/x00' 

格式符"i"表示转换为int，'ii'表示有两个int变量。进行转换后的结果长度为8个字节（int类型占用4个字节，两个int为8个字节），可以看到输出的结果是乱码，
因为结果是二进制数据，所以显示为乱码。可以使用python的内置函数repr来获取可识别的字符串，其中十六进制的0x00000014, 0x00001009分别表示20和400。   

struct.unpack  #struct.unpack做的工作刚好与struct.pack相反，用于将字节流转换成python数据类型。它的函数原型为：struct.unpack(fmt, string)，该函数返回一个元组。 下面是一个简单的例子：str = struct.pack("ii", 20, 400)    
a1, a2 = struct.unpack("ii", str)    
print 'a1:', a1    
print 'a2:', a2     
  
#---- result:    
#a1: 20    
#a2: 400   
str = struct.pack("ii", 20, 400) 
a1, a2 = struct.unpack("ii", str) 
print 'a1:', a1 
print 'a2:', a2 

#---- result: 
#a1: 20 
#a2: 400 
 
struct.calcsize 
　　struct.calcsize用于计算格式字符串所对应的结果的长度，如：struct.calcsize('ii')，返回8。因为两个int类型所占用的长度是8个字节。 

struct.pack_into, struct.unpack_from 
 　　这两个函数在Python手册中有所介绍，但没有给出如何使用的例子。其实它们在实际应用中用的并不多。Google了很久，才找到一个例子，贴出来共享一下： 

import struct     
from ctypes import create_string_buffer     
  
buf = create_string_buffer(12)    
print repr(buf.raw)     
  
struct.pack_into("iii", buf, 0, 1, 2, -1)    
print repr(buf.raw)    
  
print struct.unpack_from('iii', buf, 0)    
  
#---- result    
#'/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00'    
#'/x01/x00/x00/x00/x02/x00/x00/x00/xff/xff/xff/xff'    
#(1, 2, -1)   
import struct 
from ctypes import create_string_buffer 

buf = create_string_buffer(12) 
print repr(buf.raw) 

struct.pack_into("iii", buf, 0, 1, 2, -1) 
print repr(buf.raw) 

print struct.unpack_from('iii', buf, 0) 

#---- result  
#'/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00/x00'   
#'/x01/x00/x00/x00/x02/x00/x00/x00/xff/xff/xff/xff'   
#(1, 2, -1)   

