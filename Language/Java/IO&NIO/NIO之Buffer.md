# NIO之Buffer

## Buffer的结构图


## 部分源码解析
1. buffer:抽象类，本身不定义具体数组，但是却通过几个int变量（mark=-1;position=0;limit;capacity）指定了很多基本功能。
```
public abstract class Buffer {
    private int mark = -1;   #定义了初始化的位置
    private int position = 0;    #跟踪当前位置
    private int limit;    # 制定当前缓存的大小限制
    private int capacity;   # 定义数组的大小
    public abstract Object array();
    public abstract int arrayOffset();

    Buffer(int mark, int pos, int lim, int cap)  # 初始化Buffer
    #重置
    public final Buffer reset(){   
      int m = mark;
      if (m < 0)
      throw new InvalidMarkException();
      position = m;     #将当前位置回到mark
      return this;
    }
    #清空
    public final Buffer clear() {
        position = 0;
        limit = capacity;
        mark = -1;     
        return this;
    }
    #反转
    public final Buffer flip() {
        limit = position;   # 设定当前位置为限制位置
        position = 0;
        mark = -1;
        return this;
    }
    #计算剩余容量
    public final int remaining() {
        return limit - position;
    }
    #判断是否有剩余
    public final boolean hasRemaining() {
        return position < limit;
    }
}
```
