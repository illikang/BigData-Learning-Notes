# Java中的日期与时间
## 概述
Date现在建议作为时间轴上的瞬时代表，要格式化时间日期则通过DateFormat，如果想要取得某个时间日期信息，或者是对时间日期进行操作，可以使用Calendar实例。
下面分别详细介绍这三种数据类型。

## epoch毫秒数
如果想要取得系统时间，方法之一是使用:
```
Long a=System.currentTimeMillis();
System.out.println(a);
//1546661113384
```
currentTimeMillis()方法返回的是一长串epoch毫秒数。它是个long类型整数，代表1970年1月1日0时0分0毫秒至今经过的毫秒数，代表着时间轴上的某一瞬间。
## Java.util.Date（时间轴上的瞬间）
Date有两个构造函数可以使用，一个可使用epoch毫秒数构建（Long类型整数），另一个为无参数构造函数，内部亦是使用System.currentTimeMillis()取得epoch毫秒数来构建Date对象。调用Date对象的getTime()方法可取得内部保存的epoch毫秒数值。
Date类是从JDK1.0就已存在的API，目前除了getTime()和setTime()之外的getXXX(),setXXX()都已经废弃了。也就是说，Date实例基本上建议之用来当作时间轴上的某一瞬间，也就是1970年1月1日0时0分0毫秒至今经过的毫秒数，其他对时间日期字段的设定与取得，建议通过Calendar进行。

## Java.text.DateFormat（格式化时间日期）
有关字符串时间格式的处理，职责落到了Java.text.DateFormat身上。DateFormat是个抽象类，其操作类是SimpleDateFormat。它的作用是制定一个字符串格式，让字符串格式的时间以及Date实例时间可以互相转化。

获取SimpleDateFormat实例有多种方法：
```
//方法一：指定了转换格式为"YYYY-MM-DD HH:mm:ss"的SimpleDateFormat实例
String strDateFormat = "YYYY-MM-DD HH:mm:ss";
SimpleDateFormat simpleDateFormat=new SimpleDateFormat(strDateFormat);
//方法二：指定了转换格式为"2019-1-5"的SimpleDateFormat实例
DateFormat dateFormat1=DateFormat.getDateInstance();
//方法三：指定了转换格式为"21:16:52"的SimpleDateFormat实例
DateFormat dateFormat2=DateFormat.getTimeInstance();
//方法四：指定了转换格式为"2019-1-5 21:16:52"的SimpleDateFormat实例
DateFormat dateFormat3=DateFormat.getDateTimeInstance();
```

有了SimpleDateFormat实例以后，就可以处理时间格式转换的问题：
```
//将一个Date实例转化为约定格式的字符串
String datetime1=simpleDateFormat.format(new Date());
System.out.println(datetime1);  
//输出结果2019-01-05 21:29:21
//再将字符串格式的时间转回为Date实例
Date currenttime=simpleDateFormat.parse(datetime1);
System.out.println(currenttime.toString());
//输出结果为Sun Dec 30 21:31:48 CST 2018
```

## Java.util.Calendar(处理时间日期)
Calendar是个抽象类，Java.util.GregorianCanlendar是其子类。通过Calendar的getInstance()取得的Calendar实例，默认就是取得GregorianCanlendar。
```
Calendar calendar=Calendar.getInstance();
calendar.setTime(new Date());
int day=calendar.get(Calendar.DAY_OF_YEAR);
nt month=calendar.get(Calendar.MONTH);
//比较时间可以用after()和before()方法
```
## 小结
如果需要处理格式问题，就用DateFormat；如果需要处理时间，比如判断时间的先后，对时间做加减运算等，就使用Calendar。
