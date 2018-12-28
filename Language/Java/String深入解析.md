# String深入解析

## String的源代码
```
public final class String
  mplements java.io.Serializable, Comparable<String>, CharSequence{
    //String是通过一个final的char数组实现的
    private final char value[];
    private int hash;
    //默认构造方法
    public String() {
        this.value = "".value;
    }
  }
```

## String源码解析
String类是由fianl char[]构建而成。也就是说字符串对象一旦创建，他的值就不能再改变。无论是sub操作、concat还是replace等操作，都不是在原有的字符串上进行的，而是重新生成了一个新的字符串对象。也就是说进行这些操作后，最原始的字符串并没有被改变。

<strong>对String对象的任何改变都不影响到原对象，相关的任何change操作都会生成新的对象。</strong>

## 深入理解String,StringBuffer,StringBuilder
