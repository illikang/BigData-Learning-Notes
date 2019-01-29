# Matplotlib基本用法
通过一个绘图的流程来介绍Matplotlib基本用法

##准备工作
### 1.Figure(画板)
在任何绘图之前，我们需要一个Figure对象，可以理解成我们需要一张画板才能开始绘画。
```
import matplotlib.pyplot as pyplot
fig=plt.figure()
```
### 2.Axes（坐标轴）
在拥有Figure对象之后，在作画前我们还需要轴，没有轴的话就没有绘图基准，所以需要添加Axes。可以理解为在画板上准备带有坐标轴的绘图纸。
```
fig=plt.figure()
ax=fig.add_subplot(111)
#111表示在画板的第1行第1列的第一个位置上生成一个Axis对象来准备作画。
ax.set(xlim=[0.5,4.5],[-2,8],title='An Example Axes',ylabel='Y-Axis',xlabel='X-Axis')
#Axes可以设置图的各种属性
```
### 3.Axes VS pyplot
Axes对象和pyplot对象上都可以用来画图。pyplot画图简单易懂，但只适合简单的绘图，快速的将图绘出。在处理复杂的绘图工作时，我们还是需要使用Axes来完成做画的。
```
#X，Y的取值范围
plt.plot([1, 2, 3, 4], [10, 20, 25, 30], color='lightblue', linewidth=3)
#X轴的范围
plt.xlim(0.5, 4.5)
plt.show()
```
## 2D基本绘图
### 1.画线（plot）
plot()函数画出一些列的点，并且用线将他们连接起来。
```
x = np.linspace(0, np.pi)
y_sin = np.sin(x)
y_cos = np.cos(x)

ax1.plot(x, y_sin)
ax2.plot(x, y_sin, 'go--', linewidth=2, markersize=12)
ax3.plot(x, y_cos, color='red', marker='+', linestyle='dashed')
```
### 2.散点图（scatter）
只画点，但是不用线连接起来。
```
x = np.arange(10)
y = np.random.randn(10)
plt.scatter(x, y, color='red', marker='+')
plt.show()
```
