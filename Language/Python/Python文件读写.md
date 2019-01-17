# Python文件读写

## 读文件
```
with open('text.txt','r') as file:   //'r' 读取文本文件
with open('text.txt','rb') as file:   //'r' 读取二进制文件
```
方法返回一个可迭代对象file，file对象提供了三种读方法：
```
file.read(n)    
//每次读取整个文件，通常用于将文件内容放到一个字符串变量中。如果文件大于内存，为了保险起见，可以反复调用read(size)方法，每次最多读取size个自己的内容。
file.readlines()
//自动将文件内容分析成一个行的列表，该列表可以由Python的for...in...处理
file.readline()
//每次只读取一行。
```
注意：这三种方法把每行末尾的‘\n’也读进来了，它并不会默认去掉。这样在print的时候可以正常显示。但是如果需要按行读取后处理数据，则需要手动将‘\n’去掉。另外还有注意，在一次with open中，file.read()方法会一次读入全部内容，如果再这句后再执行file.readline()，将读不到任何数据。

一个Python面试题的例子：
有两个文件，每个都有很多行ip地址，求出两个文件中相同的ip:
```
# coding:utf-8
import bisect

with open('test1.txt', 'r') as f1:
    list1 = f1.readlines()
for i in range(0, len(list1)):
    list1[i] = list1[i].strip('\n')
with open('test2.txt', 'r') as f2:
    list2 = f2.readlines()
for i in range(0, len(list2)):
    list2[i] = list2[i].strip('\n')

list2.sort()
length_2 = len(list2)
same_data = []
for i in list1:
    pos = bisect.bisect_left(list2, i)
    if pos < len(list2) and list2[pos] == i:
        same_data.append(i)
same_data = list(set(same_data))
print(same_data)
```

## 写文件
```
with open('test.txt', 'w') as file:   //写文本文件
with open('test.txt', 'wr') as file:  //写二进制文件
```

'w'模式是：如果要写的文件不存在，就创建一个；如果有，那么就会先把原文件的内容清空再写入新的东西。
```
file.write("Hello,World!")
//write()方法和read(),readline()方法对应，将字符串写入到文件中
file.writelines(["1\n","2\n","3\n",])
//writelines()和readlines()方法对应，也是针对列表的操作。它接收一个字符串列表作为参数，将他们写入到文件中，换行符不会自动的加入，因此，需要显式的加入换行符。
```

##补充内容
1. open()的mode参数：
  * 'r'：读
  * 'w'：写
  * 'a'：追加
  * 'r+'：r+w，可读可写，文件若不存在就报错
  * 'w+'：w+r，可读可写，文件若不存在就创建
  * 'a+'：a+r,可追加可读，文件若不存在就创建
  * 对应的，如果是二进制文件，就都加一个b就好了：'rb'　　'wb'　　'ab'　　'rb+'　　'wb+'　　'ab+'

2. 字符编码
要读取非UTF-8编码的文本文件，需要给open()函数传入encoding参数，例如，读取GBK编码的文件：
```
f = open('test.txt', 'r', encoding='gbk')
```
遇到有些编码不规范的文件，可能会遇到UnicodeDecodeError,因为在文本文件中可能夹杂了一些非法编码的字符。遇到这种情况，open()函数还接收一个errors参数，表示遇到编码错误后如何处理。最简答的方式是直接忽略：
```
f = open('test.txt', 'r', encoding='gbk', errors='ignore')
```
