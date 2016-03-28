---
layout:		post
title:			"ggplot2学习心得"
subtitle:		"\"Application!\""
date:			2016-01-24
author:		"fibears"
header-img:	"img/in-post/header/post-bg-rmap.png"
tags:
    - R语言应用
    - 可视化分析 

---

	From <<R数据可视化手册>> 

# Chap1. R 基础
## 加载文件

1. 默认情况下，数据集中的字符串（String）会被视为因子（Factor）处理，此时可以设置`stringAsFactors = FALSE`,将文本变量视为字符串表示。
2. 读取xlsx和xls文件：package`xlsx`(Java)和`gdata`(Perl)
3. `foreign`:read.spss;read.octave;read.systat;read.xport;read.dta

# Chap2. 快速探索数据
## 概述
`qplot()`函数的语法与基础绘图系统类似，简短易输入，通常用于探索性数据分析。
`qplot(x,y,data,geom=c(xx,xx))`

## 条形图
1. `barplot()`第一个向量用来设定条形的高度，第二个向量用来设定每个条形对应的标签（可选）。
2. `变量值条形图`： 两个输入变量，x为分类变量，y表示变量值
3. `频数条形图`：一个输入变量，需要注意连续x轴和离散x轴的差异。

## 直方图
与条形图不同的地方在于，x为连续型变量

## 箱线图
1. 需要传递两个向量：x和y
2. 在x轴上引入两变量的交互：`interaction()`
3. Question:基础绘图系统和ggplot2的箱线图略有不同。

## 绘制函数图像

```
ggplot(data.frame(x=c(0,20)), aes(x=x)) + stat_function(fun=myfun, geom = "line")
```

# Chap3. 条形图
## 概述
条形图通常用来展示不同的分类下（x轴）某个数值型变量的取值（y轴），其条形高度既可以表示数据集中变量的频数，也可以表示变量取值本身。

## 参数
1. `fill`:改变条形图的填充色；`colour`:添加边框线；`position`:改变条形图的类型；`linetype`:线型
2. `scale_fill_brewer()`和`scale_fill_manual()`设置颜色
3. `scale_fill_brewer(palette="Pastell")`

## 条形图
1. 频数条形图：只需要一个输入变量，当变量为连续型变量时，等价于直方图。
2. 颜色映射在`aes()`内部完成，而颜色的重新设定在`aes()`外部完成。
3. 排序：`ggplot(upc, aes=(x=reorder(Abb, Change)), y =Change, fill = Region)`
4. 正负条形图着色：首先，创建一个对取值正负情况进行标示的变量，然后参数设定为`position='identity'`,这可以避免系统因对负值绘制堆积条形而发出的警告信息。
5. `guide=FALSE`删除图例
6. `width`调整条形图的条形宽度；`position_dodge(0.7)`调整条形间距（中心距离）
7. 堆积条形图：`geom_bar(stat='identity')`默认情况
8. 更改图例颜色顺序:`guides(fill = guide_legend(reverse=TRUE))`
9. 调用调色板 `scale_fill_brewer(palette='Pastell')`和手动：`scale_fill_manual()`
10. 百分比堆积图：首先利用plyr包种的ddply()转化数据，然后再绘图.
11. 添加 数据标签：`geom_text(aes(y = label_y, label=Weight),vjust=xxx)`其中y用来控制标签的位置
12. 绘制Cleveland点图：通常都会设置成根据x轴对应的连续变量的大小取值对数据进行排序。
13. `reorder(x,y)`：先将x转化为因子，然后根据y对其进行排序。
14. 主题系统（Theming System）:`theme(panel.grid.major.x = element_blank(),panel.grid.minor.x = element_blank())`


# Chap4. 折线图

## 概述

折线图通常用来对两个连续变量之间的相互依存关系进行可视化。其中x也可以是因子型变量。

## 简单折线图

	geom_line()

1. 对于因子型变量，必须使用`aes(group=1)`以确保`ggplot()`知道这些数据点属于同一个分组，从而应该用一条折线连在一起。
2. 数据标记相互重叠：需要相应地左移或者右移连接线以避免点线偏离。	`geom_line(position=position_dodge(0.2))`
3. 参数：线型(linetype),线宽（size）,颜色（colour）:边框线
4. 在`aes()`函数外部设定颜色、线宽、线型和点型等参数会将所有目标对象设定为同样的参数值。
5. 面积图：`geom_area()`,alpha调节透明度
6. 堆积面积图：`geom_area()`基础上，映射一个因子型比那里给填充色（fill）即可
7. 添加置信域：`geom_ribbon()`,然后分贝映射一个变量给ymin和ymax。`geom_ribbon(aes(ymin=xx,ymax=xx), alpha = 0.2)`


# Chap5. 散点图

## 概述
散点图通常用来刻画两个连续型变量之间的关系。

## 散点图

	geom_point()

1. 参数值：`shape()`,`size=2`,`colour()`
2. 更改配色与点形：`scale_colour/shape_brewer/manual()`
3. 尽量将不需要高精度的变量映射给图形的大小和颜色属性。
4. 调用`scale_size_area()`函数使数据点的面积正比于变量值。
5. 处理图形重叠问题（overplotting）:	
	- 使用半透明的点
	- 将数据分箱(bin)，并用矩形表示（适用于量化分析）`stat_bin2d()`
	- 将数据分箱(bin)，并用六边形表示`stat_binhex`(packages:"hexbin")
	- 使用箱线图
	- 离散x：调用`geom_point(position_jitter())`函数给数据点增加随机扰动。
6. 添加回归模型拟合线：`stat_smooth(method=lm,level=0.95)`
7. 添加自己构建的模型拟合线：`geom_line(data=predicted,size=1)`
8. `dlply()`和`ldply()`函数：切分数据，对各个部分执行某一函数，并对执行结果进行重组。
9. 散点图中添加模型系数：`+ annotate(parse = TRUE)`函数添加文本。利用`expression()`检验输出结果
10. 添加边际地毯(Marginal rugs)：`geom_rug()`
11. 添加标签：`geom_text(aes(label=xxx),size=xx,x=xx+0.1)`
	- `vjust=1`:标签文本的顶部与数据点对齐
	- `vjust=0`:标签文本的底部与数据点对齐
	- `hjust=1/0`:右对齐/左对齐
	- 通常先设定`hjust()`和`vjust()`的值为0或1，然后再调整x或y的值来调整文本标签的位置.
	- 对数坐标轴：需令x或者y乘以一个数值才可以。
	- 去掉不需要的标签：将不需要刻画出来的标签赋值为`NA`
12. 绘制气泡图:`geom_point()`和`scale_size_area(max_size=15)`
13. 散点图矩阵：base基础绘图系统，`pairs()`

	
# Chap6. 描述数据分布
数据分布可视化方法

## 直方图

1. `geom_histogram()`
2. `binwidth`设置组距；`origin`设置分组原点
3. 各分组区间左闭右开
4. 分面：`facet_grid(x ~.)`
	- 修改分类标签：`revalue(x, c("0"="No smoke","1"="smoke"))`
	- 参数`scales = free`可以单独设定各个分面的Y轴标度。

## 核密度曲线
 `geom_line(stat='identity')`或者`geom_density()`
 
## 频数多边形

	geom_freqpoly()
	
频数多边形描述了数据本身的信息，而核密度曲线只是一个估计，需要认为输入带宽参数。

## 箱线图

	geom_boxplot()

1. 参数：`width`,`outlier.size`,`outlier.shape`
2. 添加槽口(notch):用来帮助查看不同分布的中位数是否有差异。
3. 添加均值：`geom_summary(fun.y="mean",geom="point"...)`

## 小提琴图

	geom_violin()

1. 小提琴图用来比较多组数据分布情况的方法，其也是核密度估计。
2. 坐标范围：从最小值到最大值，与箱线图不同。
3. 参数：`adjust`调整平滑程度，`scale="count"`使得图的面积与每组观测值数目成正比。

## Wilkinson点图

	geom_dotplot()

1. Wilkinson点图：沿着x轴方向对数据进行分组，并在y轴上对点进行堆积。图形上Y轴的刻度线没有明确的含义。
2. 备注：移除刻度线`scale_y_continuous(break=NULL)`,移除坐标轴标签`theme(axis.title.y=element_blank())`
3. 参数`binaxis=”y”`，将数据点沿着Y轴进行堆叠，并沿着x轴分组。`stackdir="center"`:中心堆叠

## 二维数据的密度图

	stat_density2d()


# Chap7. 注解

## 文本注解

1. `geom_text()`
2. `annotate()`：可以添加任意类型的几何对象。 `annotate("text",x=,y=,label=,...)`

## 数学表达式
1. `annotate("text",...,parse=TRUE,...)`
2. 引入常规文本：在双引号内使用单引号标出纯文本的部分即可。
3. 不能简单地把一个变量之间放到另一个变量旁边却不在中间添加任何记号。
4. `?plotmath`和`?demo(plotmath)`

## 添加直线
1. 横线和竖线：`geom_hline(yintercept=)` & `geom_vline()`
2. 有角度的直线：`geom_abline(intercept=,slope=)`

## 添加线段和箭头
1. 线段：`annotate("segment",x=,xend=,y=,yend=)`
2. 利用grid包中的`arrow()`函数向线段两端添加箭头或平头。

## 添加矩形阴影
`annotate("rect",xmin=,xmax=,ymin=,ymax=,alpha=,fill=)`

## 添加误差线
	geom_errorbar(aes(ymin=,ymax=),width=,position=)
	
## 向独立分面添加注解
	# 基本图形
	p <- ggplot(mpg, aes(x=displ,y=hwy)) +
	geom_point() + facet_grid(.~drv)
	#存有每个分面所需标签的数据框
	f_labels <- data.frame(drv=c("4","f","r"), 
	label=c("4wd","Front","Rear"))
	p + geom_text(x=6,y=40,aes(label=label), data= f_labels)
1. `sprintf()`：returns a character vector containing a formatted combination of text and variable values.
2. `sprintf("italic(y) == %.2f %+.2f * italic(x)",round(coef(mod)[1],2),round(coef(mod)[2],2))`

# Chap8. 坐标轴

## 交换x轴和y轴

	coord_filp()
	# x是因子型变量
	+ scale_x_discrete(limits=rev(levels(..)))

## 坐标轴的值域
1. `ylim()`或者`xlim()`
2. 全称：`scale_y_continuous(limits=c(,),breaks=c(.,.,.))`
3. 注意区分`scale_y_continuous()`和`coord_cartesian()`;其中前者表示使用标度限制y到更小的范围，范围外的数据被丢弃，而后者则是利用坐标变换放大或缩小了数据。
4. `expand_limits()`：只能用来扩展数据，而不能用来缩减值域。

## 反转一条连续型坐标轴
	scale_y_reverse()
	scale_x_reverse()
	
## 修改类别型坐标轴上项目的顺序
	scale_x_discrete(limits=c(.,.,.))
	# 反转坐标轴
	scale_x_discrete(limits=rev(levels(...)))
	
## 设置x轴和y轴的缩放比例
`coord_fixed(ratio=1)`

默认情况下，ggplot2使两轴的总长宽比例为1：1，从而形成正方形的绘图区域，而本节中所提到的比例为：坐标轴单位长度表示的数值范围

## 设置刻度线的位置
`参数breaks`
	
离散型变量的坐标轴：设置`limits`以重排序或移除项目，而设置`breaks`来控制哪些项目拥有标签。

## 移除刻度线和标签
	# 移除刻度标签
	theme(axis.text.y = element_blank())
	# 移除刻度线
	theme(axis.ticks = element_blank())
	# 同时移除连续型变量的刻度线、刻度标签和网格线
	scale_y_continuous(breaks= NULL)
刻度标签可以单独控制，但是刻度线和网格线必须同时控制。

## 修改刻度标签的文本
`参数：breaks & labels`

package:scales自带了一些内置的格式化函数，比如`comma(),dollar(),percent(),scientific()`

## 修改刻度标签的外观
	# 文本旋转
	theme(axis.text.x = element_text(angle=90,hjust=,vjust=))
	# 其他文本属性
	theme(axis.text.x = element_text(family=,face=,colour=,size=rel()))
	rel(0.9):表示为当前主题基础字体大小的0.9倍
	
## 修改坐标轴标签的文本
	# 简便法
	xlab() & ylab()
	# 完整法
	scale_x_continuous(name="")
文本中可包含`\n,\t`等文本符号

## 移除坐标轴标签
	theme(axis.title.x = element_blank())
	
## 对数坐标轴
	scale_x_log10() & scale_y_log10()
	# 刻度标签转而使用指数计数法
	library(scales)
	scale_x_log10(breaks=10^(-1:5),	labels=trans_format("log10",math_format(10^.x)))
	# 自然对数
	trans=log_trans()
	# 以2为底的对数
	trans = log2_trans()

## 对数坐标轴添加刻度
	annotation_logticks()

## 坐标轴上使用日期
```
library(scales)
# Date 对象
p + scale_x_time(breaks = datebreaks, date_format("%Y-%m"))
# POSIXt对象
p + scale_x_datetime(breaks = datebreaks,date_format()) 
  + theme(axis.text.x = element_text(angle=90, hjust=1))
```

# Chap9.控制图形的整体外观

## 设置图形标题

```
# 图形标题,"\n"换行
ggtitle()
labs(title="")
# 将标题移到内部
annotate("text",x=mean(range(x)),y=Inf,label="Age",vjust=1.5,size=6)
```

## 修改文本外观
文本项目分为两类：主题元素和文本几何对象。主题元素包括图形中的所有非数据元素：如标题、图例和坐标轴。文本几何对象则属于图形本身的一部分。

- family: Helvatica、Times、Courier
- face: plain、bold、italic、bold.italic
- lineheight: 行间距倍数
- angle: 旋转角度(逆时针)
- size: 字体大小(主题为磅，几何对象为毫米)
- strip.text: 双向分面标签的外观

## 使用主题
```
# 预制的主题：
theme_bw()
theme_grey()
# 设置默认主题
theme_set(theme_bw())
```
## 修改主题元素的外观

要修改一套主题，配合相应的`element_xx`对象添加`theme()`函数即可。`element_xx`对象包括`element_line`、`element_rect`和`element_text`。

## 创建自定义主题

```
mytheme = theme_bw() + 
theme(text = element_text(colour="red"),axis.title=element_text(size=rel(1.25)))
p + mytheme
```

## 隐藏网格线

- 主网格线：panel.grid.major
- 次网格线：panel.grid.minor

# Chap10. 图例
像x轴和y轴一样，图例也是一种引导元素：它可以向人们展示如何从视觉上的图形属性映射回数据本身。

```
# 移除图例(指明图例属性)
guides(fill=FALSE)
# 修改图例的位置
theme(legend.position="bottom")
# 将图例置于图像中
theme(legend.position=c(1,0)) +
theme(legend.bakground=element_rect(fill="white",colour="black")) 
# 修改图例项目的顺序
scale_fill_discrete(limits=c("a","b","c"))
# 反转图例顺序(属性fill)
guides(fill=guide_legend(reverse=TRUE))
# 设置图例标题(属性fill)
labs(fill="Condition")
guides(fill=guide.legend(title="Condition"))
# 修改图例文本
theme(legend.title=element_text(face="italic",family="Times",colour="red",size=14))
# 修改图例标签
scale_fill_discrete(limits=c("a","b","c"),labels=c("A","B","C"))
# 修改图例标签的外观
theme(legend.text=element_text(xx))
# 使用含多行文本的标签
library(grid)
## 增加图例说明的高度并减小各行的间距
theme(legend.text=element_text(lineheight=0.8),legend.key.height=unit(1,"cm")) 
```

# Chap11. 分面
数据可视化中最实用的技术之一就是将分组数据并列呈现，这样使得组间的比较变得轻而易举。

```
# 分面函数
facet_grid(. ~ cyl,scale="free")
facet_wrap(~class, nrow=2)
# 修改分面标签和标题的外观
theme(strip.text=element_text(face="bold",size=rel(1.5)),strip.background=element_rect(fill="lightblue",colour="black",size=1))
```

# Chap12. 配色

## 离散型变量调色板

- 等距色：scale\_fill_discrete()
- 色轮等距色：scale\_fill_hue()
- 灰度调色板：scale\_fill_grey()
- 调色板颜色：scale\_fill_brewer()
- 自定义颜色：scale\_fill_manual()

```
# 设置亮度参数(Default:l=65)
scale_fill_hue(l=45)
# 调用调色板
library(RColorBrewer)
display.brewer.all()
# 使用自定义调色板
scale_fill_manual(values=mycol)
```

对类别型数据中的点而言，最好选择调色板`Set1`和`Dark2`；对面积而言，`Set2`、`Pastel1`、`Pastel2`和`Accent`都是不错的选择方案。

## RGB颜色
RGB颜色是由六个数字组成(十六进制数)，形式如“#RRGGBB”。在十六进制中，数字先从0到9，然后紧接着是A到F。每一个颜色都由两个数字表示，范围从00到FF。比如颜色“#FF0099”中，255表示红色，0表示绿色，153表示蓝色，整体表示品红色。十六进制数中每个颜色通道常常重复同样的数字，因子这样更容易阅读并且第二个数字的精确值对外观的影响并不是很明显。

## RGB经验法则
- 一般情况下，较大的数字更明亮，较小的数字更暗淡。
- 如果要得到灰色，将三个颜色通道设置为相同的值。
- CMY(印刷三原色)：青(cyan)、品红(magenta)、黄(yellow)。
- [RGB颜色](http://html-color-codes.com/)

## 色盲友好式调色板
```
cb_col = c("#000000","#E69F00","#56B4E9","#009E73","#F0E442","#0072B2","#D55E00","#CC79A7")
scale_fill_manual(values=cb_col)
# 调用dichromat包
```

## 连续型变量调色板

- 两色渐变:scale\_fill_gradient()
- 三色渐变:scale\_fill_gradient2()
- 等间隔n色渐变:scale\_fill_gradientn()

# Chap15. 其他图形

## 相关矩阵图

```
library(corrplot)
corrplot(cor(x),method="shade",shade.col=NA,tl.col="black",tl.srt=45,col=col(200),addCoef.col="black",cl.pos="no",order="AOE")
```

## 绘制函数曲线

```
# 函数曲线
stat_function(fun=myfun, n=200)
# 函数曲线下添加阴影
## 定义一个新函数，把x范围外的值替换为NA
p + stat_function(fun=myfun, geom="area", fill="blue",alpha=0.2) +
stat_function(fun=myfun)
# 绘制经验累积分布函数图
stat_ecdf()
```

## 绘制热图

使用`geom_tile()`或者`geom_raster()`，并将一个连续变量映射到fill上。

```
p + geom_tile() +
scale_x_continuous(breaks = seq(1940,1976,by=4)) +
scale_y_reverse() +
scale_fill_gradient2(midpoint=50,mid="grey70",limits=c(0,100))
```

## 三维散点图

```
library(rgl)
# 绘制点
plot3d(x,y,z,type="s",size=0.75,lit=FALSE)
# 添加线段
interleave = function(v1,v2) as.vector(rbind(v1,v2))
segment3d(interleave(x,x),
interleave(y,y),interleave(z,min(z)),alpha=0.4,col="blue")
# 绘制盒子
rgl.bbox(color="grey50", emission="grey50",xlen=0,ylen=0,zlen=0)
# 保存图像
rgl.snapshot("3dplot.png", fmt="png")
rgl.postscript("3dplot.pdf",fmt="pdf")
# 三维图动画
plot3d(x,y,z,type="s",size=0.75,lit=FALSE)
play3d(spin3d())
movie3d(spin3d(axis=c(1,0,0),rpm=4),duration=15,fps=50)
```

### 绘制谱系图

```
hc = hclust(scale(x))
plot(hc, hang=-1)
```

### 绘制QQ图

```
# base画图
qqnorm(x)
qqline(x)
# ggplot2画图
predicted <- data.frame(i = 1:length(VALUE), x=1:length(VALUE), y=1:length(VALUE))
predicted$x <- qnorm((predicted$i-0.375)/(nrow(predicted+0.25)))
predicted$y <- sd(VALUE)*predicted$x+mean(VALUE)
ggplot(data.frame(VALUE), aes(sample = VALUE)) +
    stat_qq() + 
    geom_line(data=predicted[,-1],aes(x=x,y=y),size=1,colour="red") 
```

## 绘制马赛克图

```
# 主要用来可视化列联表
library(vcd)
mosaic(~x+y+z, data, highlighting="x",highlighting_fill=c(lightblue","pink"),direction=c("v","h","v"))
```

## 绘制饼图

```
fold = table(survey$Fold)
pie(fold,labels=c("x","y","z"))
```

## 绘制地图
[如何用R语言绘制地图](http://fibears.github.io/2016/01/06/%E5%A6%82%E4%BD%95%E7%94%A8R%E8%AF%AD%E8%A8%80%E7%BB%98%E5%88%B6%E5%9C%B0%E5%9B%BE/)

# Chap14. 保存图形
Web浏览器更支持SVG文件，而LaTeX则更支持PDF文件。

## 输出为PDF矢量文件

```
#width和height的单位为英寸
pdf("myplot.pdf",width=4,height=4,useaDingbats=FALSE)
plot(x,y)
print(ggplot(data,aes(x=x,y=y))+geom_point())
dev.off()
# 如果图形多于一幅，则每一幅将在PDF输出中列于独立的一页。
# ggsave()不能用于创建多页图形
ggsave("myplot.pdf",width=8,height=8,units="cm",useaDingbats=FALSE)
```

## 输出为SVG矢量文件

```
svg("myplot.svg",width=4,height=4)
plot()
dev.off()
```

## 输出为WMF矢量文件
Windows图元文件(WMF),即只能在Windows上创建。

```
win.metafile("myplot.wmf",width=4,height=4)
plot()
dev.off()
# ggsave()
ggsave("myplot.wmf",width=8,height=8,units="cm")
```

## 输出为点阵(PNG/TIFF)文件

```
png("myplot.png",width=400,height=400)
plot(x,y)
dev.off()
# 多幅图形
png("myplot-%d.png",width=400,height=400)
plot()
print(ggplot())
dev.off()
# ggsave函数
ggsave("myplot.png",width=8,height=8,unit="cm",dpi=300)
# CairoPNG():支持抗锯齿和alpha通道的特性
install.packages("Cairo")
CairoPNG("myplot.png")
plot()
dev.off()
```

## 在图中显示中文

```
# MAC用户
plot(x, main="散点图"，xlab="数",ylab="值",family="SimSun")
ggplot(data, aes(x=x,y=y))+
geom_point()+
annotate("text",x=10,y=30,size=10,label="我就是我！",family="SimSun")+
xlab("数")+
ylab("值")+
theme(title=element_text(family="SimSun"))
```

## 一页多图
视图窗口(viewport):显示设备的一个矩阵子区域。`grid.layout()`设置了一个任意高和宽的视图窗口布局。

```
pdf("myplot.pdf",width=8,height=6)
grid.newpage()
pushViewport(viewport(layout=grid.layout(2,2)))
vplayout = function(x,y){
viewport(layout.pos.row=x, layout.pos.col=y)
}
print(a, vp = vplayout(1, 1:2))
print(b, vp = vplayout(2, 1))
print(c, vp = vplayout(2, 2))
dev.off()
```

默认的`grid.layout()`中，每个单元格的大小都相同，可以设置`widths`和`heights`参数使得它们具有不同的大小。

***2015年某天***