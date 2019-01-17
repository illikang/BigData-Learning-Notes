# Python处理CSV和JSON文件

## CSV
CSV（Comma-Separated Values），即逗号分隔值，可以用Excel打开查看。由于是纯文本，任何编辑器也都可以打开，与Excel文件不同，CSV文件中值没有类型，所有指都是字符串。
```
import csv
  with open('Stu_csv.csv','r') as file:
    lines=csv.reader(file)
    for line in lines:
        print(line)
//>['marry', '26', 'male']
//>['bob', '23']
//>['henry', '30']
```
csv.reader(csvfile,dailect='excel',fmtparams),方法读取csvfile文件中的所有内容，并返回一个可遍历的list集合对象。
* csvfile,必须是支持迭代（Iterator）的对象，可以是文件（file）对象或者列表（list）对象
* dialect，编码风格，默认是excel风格，也就是用逗号分隔。dialect方式也支持自定义，通过调用register_dialect方法来注册。
* fmtparam，格式化参数，用来覆盖之前dialect对象指定的编码风格。

```
with open('Stu_csv.csv','a') as file:
    writer=csv.writer(file)
    writer.writerow([7,'g'])
    writer.writerow([8, 'h'])
    myList=[[1,2,3],[4,5,6]]
    writer.writerows(myList)
```
csv.writer()方法返回一个写文件对象writer。该对象可以用writerow()方法，单行写入内容，也可以用writerows()多行写入内容。

## JSON
```
import json

```
