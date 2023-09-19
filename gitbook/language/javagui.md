## 打包jar包

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/216264/11/31662/53363/647d5508F06a65766/8ca588bb05714930.jpg)
![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/206771/23/33987/37168/647d5527F105d593d/109597e540aef356.jpg)
![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/135264/3/38697/15932/647d553cFb47559c7/80cf754a3d3a33dd.jpg)
![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/91329/16/36574/20507/647d5545Fac1b353c/c27d46f0ba6d5fe1.jpg)

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/176181/6/33278/16123/647d5555Fd06dec54/a084b924d12461dc.jpg)



## Swing

一个 Java 的图形界面，由各种不同类型的“元素”组成，例如: 窗口、菜单栏、对话框、标签、按钮、文本框等等，这些“元素”统一被称为 组件（Component）。

组件按照不同的功能，可分为 顶层容器、中间容器、基本组件。一个简单窗口的组成，如
下层级结构所示:

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/50645/38/24545/3393/647c8cc7F1c7ea5c8/53d56ef4d73bf665.jpg)

组件类型的继承关系:

~~~
顶层容器 属于窗口类组件，继承自 java.awt.Window；
中间容器 和 基本组件 继承自 javax.swing.JComponent
~~~

### Swing组件

#### 2.1 顶层容器

顶层容器属于窗口类组件，可以独立显示，一个图形界面至少需要一个窗口，例如:

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/213065/7/31715/5517/647c8d39Fb9995b16/954c7b3fea2e5eee.jpg)

#### 2.2 中间容器

中间容器充当基本组件的载体，不可独立显示。中间容器可以添加若干基本组件（也可以嵌
套添加中间容器），对容器内的组件进行管理，类似于给各种复杂的组件进行分组管理。最
顶层的一个中间容器必须依托在顶层容器（窗口）内。
常用的中间容器（面板）:

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/92319/4/37058/8561/647c8d3eF2400e06d/50ffe1d0d87a5aac.jpg)

特殊的中间容器:

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/175186/22/35291/5668/647c8d69F4e6c207a/1bb1bed28efc25c5.jpg)



#### 2.3 基本组件

基本组件是直接实现人机交互的组件。
常用的简单的基本组件:

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/144622/33/36939/12830/647c8d88F7aeb1acf/637e43669fe383c1.jpg)

选取器组件:

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/145465/4/25470/3608/647c8d8cF02c0d46e/9c241cda828fc651.jpg)

其他较为复杂的基本组件:

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/122641/29/38493/2708/647c8d90Fc9fdf16e/1ddd479855ce8f6a.jpg)


布局管理器

~~~
把 Swing 的各种组件(JComponent)添加到面板容器中(JPanel)，需要给面板容器指定布局管
理器(LayoutManager)，明确容器(Container)内的各个组件之间的排列布局方式。
~~~

常用的布局管理器:

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/147950/16/34286/34825/647c8daeF38bd7bd0/1c94872be3caf68c.jpg)

初始代码：

~~~java
import javax.swing.*;

public class Hello {

    public static void main(String[] args) {
        //1:创建一个顶层容器
        JFrame jf=new JFrame();
        //设置大小
        jf.setSize(400, 300);
        //设置位置【居中】
        jf.setLocationRelativeTo(null);//基于当前窗口居中 null 默认表示当前系统
        //设置关闭时退出 JVM
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        //设置标题
        jf.setTitle("batmanfuture");
        //设置不能最大化
        jf.setResizable(false);
        //2 创建中间容器
        JPanel panel =new JPanel();
        //3 创建一个按钮组件
        JButton btn=new JButton("我是按钮");
        //3 将按钮添加到 panel 中
        panel.add(btn);
        //3 创建一个按钮组件
        JButton btn2=new JButton("我是按钮 2");
        //3 将按钮添加到 panel 中
        panel.add(btn2);
        //4 将中间容器面板添加到窗口中
        jf.setContentPane(panel);
        //5 显示窗口
        jf.setVisible(true);
    }
}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/146106/10/34564/16671/647c93b8Fec023b40/22f2cca9c8694284.jpg)

### JavaSwing 布局管理器

#### FlowLayout（流式布局）



> 官方 JavaDocsApi: https://docs.oracle.com/javase/8/docs/api/java/awt/FlowLayout.html
> FlowLayout，流式布局管理器。按水平方向依次排列放置组件，排满一行，换下一行继续排
> 列。排列方向（左到右 或 右到左）取决于容器的 componentOrientation 属性（该属性属
> 于 Component），它可能的值如下:	

~~~
ComponentOrientation.LEFT_TO_RIGHT（默认）
ComponentOrientation.RIGHT_TO_LEFT
~~~

同一行（水平方向）的组件的对齐方式由 FlowLayout 的 align 属性确定，它可能的值如下:

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/90310/18/24554/23296/647c9141Fcd02dc31/9253bb00ccdfd1ae.jpg)

FlowLayout 的 构造方法：

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/217239/4/30647/18804/647c9154F6339c59c/b8486f5e22b808da.jpg)

代码

~~~java
import javax.swing.*;
import java.awt.*;

public class Hello {
    public static void main(String[] args) {
//创建顶层容器
        JFrame jf=new JFrame();
        jf.setSize(400, 300);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        jf.setLocationRelativeTo(null);
//创建中间容器[设置布局]
//JPanel panel=new JPanel(new FlowLayout(FlowLayout.RIGHT));
//JPanel panel=new JPanel(new FlowLayout(FlowLayout.LEFT));
        JPanel panel=new JPanel(new FlowLayout(FlowLayout.CENTER,30,30));
//创建按钮
        JButton btn1=new JButton("按钮 1");
        JButton btn2=new JButton("按钮 2");
        JButton btn3=new JButton("按钮 3");
        JButton btn4=new JButton("按钮 4");
        JButton btn5=new JButton("按钮 5");
//将按钮添加到面板中
        panel.add(btn1);
        panel.add(btn2);
        panel.add(btn3);
        panel.add(btn4);
        panel.add(btn5);
//将中间容器面板添加到窗口中
        jf.setContentPane(panel);
//显示
        jf.setVisible(true);
    }
}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/117721/23/36029/16934/647c93a7F51d77bd8/2e995449c53ead9e.jpg)

#### GridLayout（网格布局）

> 官方 JavaDocsApi: https://docs.oracle.com/javase/8/docs/api/java/awt/GridLayout.html
> GridLayout，网格布局管理器。它以矩形网格形式对容器的组件进行布置，把容器按行列分
> 成大小相等的矩形网格，一个网格中放置一个组件，组件宽高自动撑满网格。
> 以行数和总数优先: 通过构造方法或 setRows 和 setColumns 方法将行数和列数都设置为
> 非零值时，指定的列数将被忽略。列数通过指定的行数和布局中的组件总数来确定。因此，

例如，如果指定了三行和两列，在布局中添加了九个组件，则它们将显示为三行三列。仅当
将行数设置为零时，指定列数才对布局有效。

GridLayout 构造方法:

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/63541/29/21746/19292/647c923fFda4c029b/80fc320580f09691.jpg)

~~~java
package layout.grid;

import java.awt.GridLayout;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试网格布局
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        JFrame jf=new JFrame();
        jf.setSize(400, 300);//大小
        jf.setTitle("网格布局");//设置标题
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);//关闭并退出
        jf.setLocationRelativeTo(null);//居中

        //创建3*3的网格布局
        //GridLayout layout=new GridLayout(3, 3);//1：行数 2：列数
        GridLayout layout=new GridLayout(3, 2,10,10);//1：行数 2：列数 3：水平间隙 4：竖直间隙
        //设置间隙
        //layout.setHgap(10);//水平
        //layout.setVgap(10);//竖直
        //创建中间容器 并设置网格布局
        JPanel panel=new JPanel(layout);

        for(int i=1;i<=9;i++){
            //创建按钮
            JButton btn=new JButton("按钮"+i);
            //添加到中间容器
            panel.add(btn);
        }
        //将中间容器添加到窗口中
        jf.setContentPane(panel);
        //显示
        jf.setVisible(true);
        jf.setVisible(true);
    }
}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/208691/9/36029/17607/647c9398F2bae4d63/78ce0d3bc8f8d0b7.jpg)

#### GridBagLayout（网格袋布局）

> 官方 JavaDocsApi: https://docs.oracle.com/javase/8/docs/api/java/awt/GridBagLayout.html
> GridBagLayout，网格袋布局管理器。它不要求组件的大小相同便可以将组件垂直、水平或
> 沿它们的基线对齐。每个 GridBagLayout 对象维持一个动态的矩形单元格（动态计算出单
> 个单元格的大小），每个组件占用一个或多个的单元格，该单元格被称为 显示区域。每个
> 组件显示区域按 从左到右，从上到下，依次排列。

约束: GridBagConstraints
GridBagConstraints，封装了若干对组件的约束属性，每个由 GridBagLayout 管理的 组件
都关联一个该约束实例，以指定 组件所在显示区域 的具体放置位置，以及 组件在其显示
区域中 的对齐方式。

给组件添加约束: GridBagLayout.setConstraints(Component comp, GridBagConstraints c)
PS: 要理解 组件（所在）显示区域（一个或多个单元格组成） 和 组件在其显示区域中的
对齐方式 这两个不同的概念。

属性: GridBagConstraints 的属性
下面属性描述中的相关大写字母常量均定义在 GridBagConstraints 类中。

3.1 显示区域 约束属性
组件的显示区域相关约束属性，直接作用在组件所在显示区域上。
（1）gridx, gridy
组件显示区域 开始显示的位置（单元格坐标），容器左上角第一个单元格位置为 (0, 0)，
默认值为 RELATIVE，表示放置在 “上一个组件”（所在行列最后添加的一个组件）的 “后面”。
（2）gridwidth, gridheight
组件显示区域 水平/竖直方向 所占单元格的个数，默认值为 1，如要占多行/列，需整体结

果有足够的行/列。有如下两个常量特殊值可选:
REMAINDER: 占完所在行/列余下所有单元格（该值可实现 换行 作用）；
RELATIVE: 占到所在行/列余下的倒数第二个单元格（使用该值，所在行/列的最后一个单元
格需要“合理”安排组件，并手动换行）。
设置该值不能导致 “前面” 或 “后面” 单元格有留空白，否则可能无效。
（3）weightx, weighty
如何 分布额外空间（单元格区域外，容器边缘内 的间隔），当指定行/列中的其中 任意一
个组件的权重（大于 0），则该行/列将（和其他行/列按权重比例）分配额外的水平/竖直空
间。当权重为 0（默认值）时，则 整个单元格区域 居中于容器中心。
PS: 如果调用了 java.awt.Window.pack()方法，该值无效，因为 pack 后已无额外的空间。



3.2 组件 约束属性
组件相关约束属性，直接作用在组件上。
（1）fill
当 显示区域 大小大于 组件 所需要的大小时，组件 在其 显示区域内 的填充方式。可能
的值如下:
 NONE: （默认）不调整组件大小；
 HORIZONTAL: 加宽组件，使它在水平方向上填满其显示区域，但是不改变高度；
 VERTICAL: 加高组件，使它在垂直方向上填满其显示区域，但是不改变宽度；
 BOTH: 使组件完全填满其显示区域。
（2）anchor
组件 在 显示区域内 的位置（对齐方式），可能有如下三种值:

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/67197/8/22348/17406/647c943bFee05e52c/829f23a6b9eb7410.jpg)

（3）ipadx, ipady
组件的内部填充（可看做是 组件的内边距），即对组件最小大小的添加量。组件的宽度至

少为其最小宽度/高度加上 ipadx/ipady 像素。
（4）insets
组件的外部填充（可看做是 组件的外边距，也可看做是 显示区域 的内边距），即 组件 与
其 显示区域边缘 之间间距的最小量。
PS: 上面各属性值之间，以及其他因素，有可能存在冲突或不兼容，不一定设置了即有效，
建议多做实验，多写测试 Demo 去尝试。

JFrame 中 pack()方法
pack() 调整此窗口的大小，以适合其子组件的首选大小和布局
单独使用 setSize()时，不能使用 pack()，否则按照 pack()自动适配
单独使用 pack()时，是按照组件的大小自动适配的
单独使用 setPreferredSize()时，设置的大小无效，必须在后面添加 pack()配合显示，否则设
置效果不生效

~~~java
package layout.gridbag;

import java.awt.GridBagConstraints;
import java.awt.GridBagLayout;
import java.awt.Insets;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试网格袋布局
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setTitle("测试网格袋布局");

        //设置退出并关闭
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //创建布局
        GridBagLayout layout=new GridBagLayout();
        //创建一个空的约束对象
        GridBagConstraints cons=null;

        //创建中间容器
        JPanel panel=new JPanel(layout);

        //创建按钮
        JButton btn1=new JButton("按钮1");
        JButton btn2=new JButton("按钮2");
        JButton btn3=new JButton("按钮3");
        JButton btn4=new JButton("按钮4");
        JButton btn5=new JButton("按钮5");
        JButton btn6=new JButton("按钮6");
        JButton btn7=new JButton("按钮7");
        JButton btn8=new JButton("按钮8");
        JButton btn9=new JButton("按钮9");
        JButton btn10=new JButton("按钮10");

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        //cons.gridx=20; 设定显示的位置偏移
        //cons.gridy=20;
        //cons.insets=new Insets(10, 10, 10, 10);//表示与其它控件的距离
        layout.addLayoutComponent(btn1, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        layout.addLayoutComponent(btn2, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        layout.addLayoutComponent(btn3, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.REMAINDER;//占满剩下的部分
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn4, cons);//内部使用的是cons副本


        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.REMAINDER;//占满剩下的部分
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn5, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.RELATIVE;//占到所在行/列余下的倒数第二个单元格
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn6, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.REMAINDER;//占满剩下的部分 换行
        layout.addLayoutComponent(btn7, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridheight=2;
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn8, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.REMAINDER;//占满剩下的部分 换行
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn9, cons);//内部使用的是cons副本

        //添加组件和约束到布局管理器
        cons=new GridBagConstraints();
        cons.gridwidth=GridBagConstraints.REMAINDER;//占满剩下的部分 换行
        cons.fill=GridBagConstraints.BOTH;
        layout.addLayoutComponent(btn10, cons);//内部使用的是cons副本


        //将组添加到panel中
        panel.add(btn1);
        panel.add(btn2);
        panel.add(btn3);
        panel.add(btn4);
        panel.add(btn5);
        panel.add(btn6);
        panel.add(btn7);
        panel.add(btn8);
        panel.add(btn9);
        panel.add(btn10);

        //将中间容器添加到窗口中
        jf.setContentPane(panel);
        //
        jf.pack();
        //显示
        jf.setVisible(true);


    }

}

~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/160795/3/35808/19489/647c94abFfd029915/173de1cf8b555fb6.jpg)

#### BoxLayout（箱式布局）

~~~
BoxLayout，箱式布局管理器。
它把若干组件按水平或垂直方向依次排列放置。Swing 提供了一个实现了 BoxLayout 的容
器组件 Box。使用 Box 提供的静态方法，可快速创建水平/垂直箱容器(Box)，以及填充组件
之间空隙的不可见组件。用水平箱和垂直箱的组合嵌套可实现类似于 GridBagLayout 的效
果，但没那么复杂。
创建水平/垂直箱容器（Box）:
// 创建一个水平箱容器
Box hBox = Box.createHorizontalBox();
// 创建一个垂直箱容器
Box vBox = Box.createVerticalBox();
Box 内的组件之间默认没有空隙并居中，如果想在组件之间（或头部/尾部）添加空隙，可
以在其中添加一个影响布局的不可见组件。Box 提供了三种用于填充空隙的不可见组件：
glue、struts 和 rigidAreas。
创建 胶状（宽/高可伸缩）的不可见组件（glue）:
// 创建一个 水平方向胶状 的不可见组件，用于撑满水平方向剩余的空间（如果有多个
该组件，则平分剩余空间）
Component hGlue = Box.createHorizontalGlue();
// 创建一个 垂直方向胶状 的不可见组件，用于撑满垂直方向剩余的空间（如果有多个
该组件，则平分剩余空间）
Component vGlue = Box.createVerticalGlue();
// 创建一个 水平和垂直方向胶状 的不可见组件，用于撑满水平和垂直方向剩余的空间
（如果有多个该组件，则平分剩余空间）
Component glue = Box.createGlue();
创建 固定宽度或高度 的不可见组件（struts）:
// 创建一个 固定宽度 的不可见组件（用于水平箱）
Component hStrut = Box.createHorizontalStrut(int width);
// 创建一个 固定高度 的不可见组件（用于垂直箱）
Component vStrut = Box.createVerticalStrut(int height);
创建 固定宽高 的不可见组件（rigidAreas）:
// 创建 固定宽高 的不可见组件
Component rigidArea = Box.createRigidArea(new Dimension(int width, int height));
~~~



代码

~~~java
package layout.box;

import javax.swing.Box;
import javax.swing.JButton;
import javax.swing.JFrame;

/**
 * 测 试箱式布局
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        //创建窗口
        JFrame jf=new JFrame();
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //创建按钮
        JButton btn1=new JButton("JButon1");
        JButton btn2=new JButton("JButon2");
        JButton btn3=new JButton("JButon3");
        JButton btn4=new JButton("JButon4");
        JButton btn5=new JButton("JButon5");

        //创建一个水平箱子容器
        Box hBox1=Box.createHorizontalBox();
        hBox1.add(btn1);
        hBox1.add(btn2);
        hBox1.add(btn3);

        //创建第二个水平箱容器
        Box hBox2=Box.createHorizontalBox();
        hBox2.add(btn4);
        hBox2.add(Box.createHorizontalGlue());//添加一个水平方向胶状不可见的组件
        //hBox2.add(Box.createHorizontalStrut(100));//固定宽度
        hBox2.add(btn5);

        //创建一个装两个的竖直方式的盒子
        Box vbox=Box.createVerticalBox();
        vbox.add(hBox1);
        //vbox.add(Box.createVerticalStrut(20));//添加固定高度的不可见组件
        vbox.add(hBox2);

        //将大盒子添加到面板中
        jf.setContentPane(vbox);
        //让组件中的空白去除 适应当前的最小格局大小
        jf.pack();
        //居中
        jf.setLocationRelativeTo(null);
        //显示
        jf.setVisible(true);

    }

}

~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/101847/28/35155/21243/647c9573F29626110/10cff0414e82fe89.jpg)

#### CardLayout（卡片布局）

> CardLayout，卡片布局管理器。
> 它将容器中的每个组件看作一张卡片，一次只能看到一张卡片，容器则充当卡片的堆栈，默
> 认显示第一张卡片。

~~~
CardLayout 构造方法
// 创建一个间距大小为 0 的卡片布局
CardLayout()
// 创建一个指定水平/垂直间距大小的卡片布局。
CardLayout(int hgap, int vgap)
CardLayout 常用方法
// 显示第一张卡片
void first(Container parent);
// 显示最后一张卡片
void last(Container parent);
// 显示下一张卡片（自动循环显示）
void next(Container parent);
// 显示上一张卡片（自动循环显示）
void previous(Container parent);
// 显示指定名称的组件（添加组件到容器时，可同时添加组件的名称）
void show(Container parent, String name);
~~~

代码

~~~java
package layout.card;

import java.awt.CardLayout;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试卡片布局
 * @author wenber
 *
 */
public class TestCardLayout {
	
	public static void main(String[] args) {
		JFrame jf=new JFrame("测试卡片布局");
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		jf.setSize(400, 300);
		
		//创建卡片布局管理器
		final CardLayout layout=new CardLayout(10, 10);
		
		//创建一个容器 指定布局管理器为卡片式
		final JPanel panel=new JPanel(layout);
		
		//创建按钮
		JButton btn1=new JButton("JButton1");
		JButton btn2=new JButton("JButton2");
		JButton btn3=new JButton("JButton3");
		
		//添加到卡片布局的面板中panel
		panel.add(btn1, "btn01");
		panel.add(btn2, "btn02");
		panel.add(btn3, "btn03");
		
		
		//显示指定的
		layout.show(panel, "btn01");
		
		new Thread(){
			public void run() {
				while(true){
					try {
						Thread.sleep(1000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
					//暂停1秒后显示下一张
					layout.next(panel);
				}
			};
		}.start();
		
		//将面板添加到窗口
		jf.setContentPane(panel);
		jf.setLocationRelativeTo(null);
		jf.setVisible(true);
		
	}

}
~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/218908/16/31370/33829/647c968fF65db9124/759c745a5571b5cb.jpg)

.

#### BorderLayout（边界布局）

> 官方 JavaDocsApi: https://docs.oracle.com/javase/8/docs/api/java/awt/BorderLayout.html
> BorderLayout，边界布局管理器。
> 它把 Container 按方位分为 5 个区域（东、西、南、北、中），每个区域放置一个组件。

BorderLayout 构造方法:

~~~
// 构造一个组件之间没有间距的新边框布局
BorderLayout()
// 构造一个具有指定组件间距的边框布局
BorderLayout(int hgap, int vgap)
BorderLayout 表示方位的 5 个常量:
BorderLayout.NORTH // 容器的北边
BorderLayout.SOUTH // 容器的南边
BorderLayout.WEST // 容器的西边
BorderLayout.EAST // 容器的东边
BorderLayout.CENTER // 容器的中心
~~~



~~~java
package layout.border;

import java.awt.BorderLayout;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.border.Border;

/**
 * 测试边界布局
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        //创建窗口
        JFrame jf=new JFrame("边界布局");
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        jf.setSize(400,300);

        //创建面板
        JPanel panel=new JPanel(new BorderLayout());//边界布局的每个区域只能存放一个组件

        //创建五个按钮
        JButton btnNorth=new JButton("Btn_North");
        JButton btnSouth=new JButton("Btn_South");
        JButton btnWest=new JButton("Btn_West");
        JButton btnEast=new JButton("Btn_East");
        JButton btnCenter=new JButton("Btn_Center");

        //添加到指定位置
        panel.add(btnCenter,BorderLayout.CENTER);
        panel.add(btnNorth,BorderLayout.NORTH);
        panel.add(btnSouth,BorderLayout.SOUTH);
        panel.add(btnWest,BorderLayout.WEST);
        panel.add(btnEast,BorderLayout.EAST);

        //将面板panel添加到窗口
        jf.setContentPane(panel);
        jf.setLocationRelativeTo(null);
        jf.setVisible(true);

    }

}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/89221/23/24329/24861/647d515aFe13de51c/4c6c36dbf502b4e0.jpg)

#### null（绝对布局）

> 官方 JavaDocsApi:
> https://docs.oracle.com/javase/8/docs/api/java/awt/Component.html
> https://docs.oracle.com/javase/8/docs/api/java/awt/Container.html

null，绝对布局。绝对布局没有特定一个布局管理器类来表示，给容器的布局管理器设置为
null，就表示使用绝对布局，即通过设置组件的坐标和宽高来布置组件。
绝对布局需要明确指定每一个组件的坐标和宽高，否则不显示。
对于使用其他布局时给组件设置坐标和宽高，一般会遵循以下两点:
使用其他布局时，如果在窗口显示之后，再添加新组件，则该组件也会被当做绝对布局对待
（即需要手动指定坐标和宽高）；
使用其他布局时，如果在窗口显示之后，修改之前添加的组件的坐标和宽高，则该组件也会
被当做绝对布局对待（即此时修改坐标和宽高会生效）。

创建一个使用绝对布局的容器:

~~~
// 创建一个容器，指定内容管理器为 null, 即使用绝对布局
JPanel panel = new JPanel(null);
或
// 创建一个容器
JPanel panel = new JPanel();
// 设置内容管理器为 null, 即使用绝对布局
panel.setLayout(null);
给组件设置坐标和宽高相的关方法（方法定义在 Component 组件基类中）:
// 设置组件的坐标
void setLocation(int x, int y)
void setLocation(Point p)
// 设置组件的宽高
void setSize(int width, int height)
void setSize(Dimension d)
// 设置组件的界限（一次性设置组件的 坐标 和 宽高）
void setBounds(int x, int y, int width, int height)
void setBounds(Rectangle rect)
~~~



~~~java
package layout.absulote;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试绝对布局
 * @author wenber
 *
 */
public class Hello {

	public static void main(String[] args) {
		
		//创建窗口
		JFrame jf=new JFrame("测试XY布局");
		jf.setSize(800,400);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		//创建中间容器
		JPanel panel=new JPanel(null);
		
		//创建按钮
		JButton btn1=new JButton("按钮1");
		//设置位置
		btn1.setLocation(100, 50);
		//设置大小
		btn1.setSize(180, 35);
		//将按钮添加到面板中
		panel.add(btn1);
		
		//创建按钮
		JButton btn2=new JButton("按钮2");
		btn2.setBounds(200, 200, 120, 40);
		panel.add(btn2);
		jf.setContentPane(panel);
		jf.setVisible(true);
		JButton btn3=new JButton("按钮3");
		//因为只有面板jpanel添加到窗口后才会有宽度
		btn3.setBounds(panel.getWidth()-100, panel.getHeight()-50, 80, 40);
		panel.add(btn3);
	}
	
}

~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/187617/28/37106/17644/647d5180F378da6fd/7f8fce07271b9674.jpg)



### Swing组件

###### 标签JLabel

~~~java
package component.lable;

import java.awt.Color;
import java.awt.Font;

import javax.swing.ImageIcon;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.SwingConstants;

/**
 * 测试标签JLabel
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        JFrame jf=new JFrame("测试JLabel");
        jf.setSize(600, 400);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        jf.setLocationRelativeTo(null);//在设置大小之后

        //创建中间容器
        JPanel panel=new JPanel(null);//FlowLayout

        //创建 显示文字
        JLabel lbl01=new JLabel();
        //设置内容
        lbl01.setText("阮老师最帅");
        lbl01.setFont(new Font("楷体",Font.PLAIN,18));
        lbl01.setBounds(200, 200, 100, 35);
        panel.add(lbl01);

        //显示图片
        JLabel lbl02=new JLabel();
        //给label添加图标
        lbl02.setIcon(new ImageIcon("image/title.png"));
        lbl02.setBounds(200, 80, 120, 120);
        panel.add(lbl02);

        //显示文字和图片
        JLabel lbl03=new JLabel();
        lbl03.setText("好吃的桔子");
        //设置字体颜色
        lbl03.setForeground(Color.GREEN);
        //设置字体
        lbl03.setFont(new Font("楷体",Font.PLAIN,18));
        lbl03.setIcon(new ImageIcon("image/title.png"));
        lbl03.setBounds(330, 80, 120, 150);
        //水平方居中
        lbl03.setHorizontalTextPosition(SwingConstants.CENTER);
        //竖直方向 最底下
        lbl03.setVerticalTextPosition(SwingConstants.BOTTOM);

        panel.add(lbl03);


        jf.setContentPane(panel);
        jf.setVisible(true);


    }

}

~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/116900/11/37186/31830/647d569bFb2933cb4/54349d6075da312f.jpg)

###### 按钮JButton

~~~java
package component.button;

import java.awt.Font;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试按钮
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		JFrame jf=new JFrame();
		jf.setSize(400, 300);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		//创建一个面板
		JPanel panel=new JPanel();
		//创建一个按钮
		final JButton btn=new JButton("测试");
		btn.setEnabled(false);//不可用
		btn.setFont(new Font("楷体",Font.PLAIN,18));
		final JButton btn2=new JButton("测试2");
		
		//创建监听器
		ActionListener listener=new ActionListener() {//匿名内部类 监听器
			@Override
			public void actionPerformed(ActionEvent e) {//监听到后通知该方法
				Object obj=e.getSource();//取事件源
				if(obj==btn){//根据事件源来区分
					System.out.println("测试");
				}else if(obj==btn2){
					System.out.println("测试2");
				}
				
			}
		};
		//添加注册监听事件
		btn.addActionListener(listener);
		btn2.addActionListener(listener);
		//添加监听事件
		/*btn2.addActionListener(new ActionListener() {//匿名内部类 监听器
			
			@Override
			public void actionPerformed(ActionEvent e) {//监听到后通知该方法
				System.out.println("收到点击");
			}
		});*/
		
		//将按钮添加到面板中
		panel.add(btn);
		panel.add(btn2);
		//将面板添加到窗口
		jf.setContentPane(panel);
		jf.setVisible(true);
	}

}

~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/126909/29/33633/48416/647d582cF1a1e0cf6/4fea40fb7aeb5fde.jpg)





~~~java
package component.button;

import java.awt.Dimension;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.image.ImageObserver;
import java.awt.image.ImageProducer;

import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;

/**
 * 测试自定义按钮
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		//创建窗口并设置
		JFrame jf=new JFrame("测试");
		jf.setSize(400, 300);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		//创建面板
		JPanel panel=new JPanel();
		//创建按钮
		final JButton btn=new JButton();
		btn.setPreferredSize(new Dimension(103, 103));
		
		//设置默认的图片显示
		btn.setIcon(new ImageIcon("image/normal.png"));
		//设置按钮图片显示
		btn.setPressedIcon(new ImageIcon("image/press.png"));
		//不绘制边框
		btn.setBorderPainted(false);
		
		btn.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				System.out.println("点击");
			}
		});
		
		//面板添加按钮
		panel.add(btn);
		
		jf.setContentPane(panel);
		jf.setVisible(true);
		
	}

}

~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/56184/12/23853/32576/647d588eF02f60230/a3d71ab5d64aa733.jpg)

###### 多选框

~~~java
package component.checkbox;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JCheckBox;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试复选框
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(400, 300);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //创建面板
        JPanel panel=new JPanel();

        //创建复选框
        final JCheckBox box01=new JCheckBox("上网");
        //设置与图片文件的间隙
        box01.setIconTextGap(20);
        //改变事件
		/*box01.addChangeListener(new ChangeListener() {
			
			@Override
			public void stateChanged(ChangeEvent e) {
				//取得事件源
				JCheckBox box=(JCheckBox) e.getSource();
				//打印选中框的内容
				System.out.println(box.getText());
			}
		});*/
        box01.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                //取得事件源
                JCheckBox box=(JCheckBox) e.getSource();
                System.out.println("你点我啦"+box.isSelected());
            }
        });

        JCheckBox box02=new JCheckBox("睡觉");
        box02.setSelected(true);//选中
        JCheckBox box03=new JCheckBox("打豆豆");
        JCheckBox box04=new JCheckBox("谈恋爱");
        JCheckBox box05=new JCheckBox("吃货");

        panel.add(box01);
        panel.add(box02);
        panel.add(box03);
        panel.add(box04);
        panel.add(box05);

        //将面板添加到窗口中
        jf.setContentPane(panel);
        jf.setVisible(true);



    }

}

~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/220375/33/31191/47557/647d58c7F2063f0c8/5ddd20c6e0debed4.jpg)

~~~java
package component.toggle;

import javax.swing.ImageIcon;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JToggleButton;
import javax.swing.SwingConstants;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试切换按钮
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        //新建窗口
        JFrame jf=new JFrame();
        jf.setSize(400,350);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //显示图片
        final JLabel lbl=new JLabel();
        lbl.setText("好吃的桔子");
        lbl.setIcon(new ImageIcon("image/title.png"));
        lbl.setHorizontalTextPosition(SwingConstants.CENTER);
        lbl.setVerticalTextPosition(SwingConstants.BOTTOM);
        //设置位置
        lbl.setBounds(140, 120, 150, 150);
        lbl.setVisible(false);

        //创建面板
        JPanel panel=new JPanel(null);//绝对布局

        //创建切换按钮
        JToggleButton btn=new JToggleButton("显示");
        btn.setBounds(150, 50, 100, 35);
        //添加事件
        btn.addChangeListener(new ChangeListener() {

            @Override
            public void stateChanged(ChangeEvent e) {
                JToggleButton btn=(JToggleButton) e.getSource();
                System.out.println(btn.getText()+"=="+btn.isSelected());
                //根据按钮是否选中来判断图片的显示
                if(btn.isSelected()){
                    btn.setText("隐藏");
                    lbl.setVisible(true);
                }else{
                    btn.setText("显示");
                    lbl.setVisible(false);
                }

            }
        });

        panel.add(btn);
        panel.add(lbl);

        jf.setContentPane(panel);
        jf.setVisible(true);
    }

}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/127863/26/37061/43867/647d5902F46df4f0b/6ecae3654d2744ac.jpg)

文本框

~~~java
package component.text_field;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.FocusEvent;
import java.awt.event.FocusListener;
import java.awt.event.KeyAdapter;
import java.awt.event.KeyEvent;
import java.awt.event.KeyListener;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;

/**
 * 测试文本输入框
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		//创建窗口
		JFrame jf=new JFrame();
		jf.setSize(400, 300);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		jf.setLocationRelativeTo(null);
		
		//创建面板
		JPanel panel=new JPanel();
		
		JLabel lbl=new JLabel("姓名：");
		lbl.setFont(new Font("楷体",Font.PLAIN,18));
		//创建文本域
		final JTextField txt=new JTextField();
		//指定初始大小
		txt.setPreferredSize(new Dimension(100, 35));
		txt.setFont(new Font("楷体",Font.PLAIN,18));
		
		//创建按钮
		JButton btn=new JButton("查询");
		//添加监听器
		btn.addActionListener(new ActionListener() {
			@Override
			public void actionPerformed(ActionEvent e) {
				System.out.println(txt.getText());
			}
		});
		
		//测试复制粘贴
		JButton btnCopy=new JButton("测试复制");
		btnCopy.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				txt.copy();
			}
		});
		//测试粘贴
		JButton btnPast=new JButton("测试粘贴");
		btnPast.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				txt.paste();
			}
		});
		
		//测试选
		JButton btnSelect=new JButton("测试选中");
		btnSelect.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				System.out.println("===");
				txt.setSelectionStart(0);//选中从0开始
				txt.setSelectionEnd(txt.getText().length());//到最后
				//txt.setSelectedTextColor(Color.GREEN);
			}
		});
		
		//添加焦点的监听
		txt.addFocusListener(new FocusListener() {
			@Override
			public void focusLost(FocusEvent e) {
				System.out.println("失去焦点");
			}
			
			@Override
			public void focusGained(FocusEvent e) {
				System.out.println("得到焦点");
			}
		});
		//添加键盘的监听
		txt.addKeyListener(new KeyAdapter() {
			//重写需要的方法
			public void keyTyped(KeyEvent e) {
				System.out.println("按下=="+e.getKeyChar());
			}
		});
		
		//将所有控件添加到面板中
		panel.add(lbl);
		panel.add(txt);
		panel.add(btn);
		panel.add(btnCopy);
		panel.add(btnPast);
		panel.add(btnSelect);
		
		jf.setContentPane(panel);
		jf.setVisible(true);
	}

}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/103167/21/41608/40040/647d59eaF17ed4dbb/bc89847b5fe2304e.jpg)

###### 密码框

~~~java
package component.password;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.SwingConstants;

/**
 * 测试密码输入框
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(300, 200);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel=new JPanel();

        JLabel lbl=new JLabel("请输入密码:");
        panel.add(lbl);

        //创建密码框
        final JPasswordField txtPwd=new JPasswordField(10);
        //设置密码框显示
        txtPwd.setEchoChar('8');//●
        txtPwd.setFont(new Font("楷体",Font.BOLD,18));//字体
        txtPwd.setHorizontalAlignment(SwingConstants.CENTER);//水平居中
        //字体颜色
        txtPwd.setForeground(Color.BLUE);
        //设置初始大小
        txtPwd.setPreferredSize(new Dimension(200, 30));
        panel.add(txtPwd);

        JButton btn=new JButton("登录");
        btn.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                char[] pwd=txtPwd.getPassword();
                String pwdString=new String(pwd);
                System.out.println("收到密码="+pwdString);
            }
        });
        panel.add(btn);

        //将面板添加到窗口中
        jf.setContentPane(panel);

        jf.setVisible(true);
    }

}

~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/137653/29/38068/42079/647d5aafF14cb0804/3100f97affb3db7a.jpg)

###### 多行文框JTextArea

~~~java
package component.textarea;

import java.awt.BorderLayout;
import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.JTextField;

/**
 * 测试多行文本框
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(400,300);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //创建面板
        JPanel panel=new JPanel(new BorderLayout());
        //创建多行文本框
        final JTextArea textArea=new JTextArea(8, 12);
        textArea.setPreferredSize(new Dimension(300, 250));
        //设置自动换行
        textArea.setLineWrap(true);

        //将多行文本框添加到滚动面板中
        panel.add(new JScrollPane(textArea),BorderLayout.CENTER);


        JPanel panel2=new JPanel();

        //用来输入内容
        final JTextField txt=new JTextField();
        txt.setPreferredSize(new Dimension(80, 35));
        panel2.add(txt);

        //发送
        JButton buttonSend=new JButton("添加到多行文本框中");
        buttonSend.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                //追加并换行
                textArea.append(txt.getText()+"\n");
            }
        });
        panel2.add(buttonSend);
        //取得内容
        JButton button=new JButton("测试取得内容");
        button.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                System.out.println(textArea.getText());
            }
        });
        panel2.add(button);


        //添加面板到最下面【面板中包含多个控件】
        panel.add(panel2,BorderLayout.SOUTH);



        jf.setContentPane(panel);
        jf.setVisible(true);
    }

}

~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/93386/7/39289/48707/647d5bffF251e7b16/30bfc9534c0022a9.jpg)

###### 下拉框

~~~java
import java.awt.event.ItemEvent;
import java.awt.event.ItemListener;

import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;

/**
 * 测试下拉框
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		//创建窗口
		JFrame jf=new JFrame();
		//设置
		jf.setSize(300,200);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		JPanel panel=new JPanel();
		//提示
		JLabel lbl=new JLabel("省份:");
		
		//给定数据
		String[] citys=new String[]{"湖南","湖北","河南","河北"};
		//创建下拉
		final JComboBox comboBox=new JComboBox(citys);
		comboBox.setEditable(true);
		
		//添加选中的监听器
		comboBox.addItemListener(new ItemListener() {
			
			@Override
			public void itemStateChanged(ItemEvent e) {
				//只处理选中的
				if(e.getStateChange()==ItemEvent.SELECTED){
					System.out.println("选中=="+comboBox.getSelectedIndex()+"=="+comboBox.getSelectedItem());
				}
			}
		});
		//设置comboBox默认选中
		comboBox.setSelectedIndex(1);
		
		//将内容添加到面板中
		panel.add(lbl);
		panel.add(comboBox);
		
		//将面板添加到窗口
		jf.setContentPane(panel);
		
		jf.setVisible(true);
	}

}
~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/200363/7/36553/39026/647d5e2cF38897feb/a4eb1e22548b3afe.jpg)

###### 多选下拉框

~~~java
package component.list;

import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.Vector;

import javax.swing.JButton;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JList;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.ListSelectionModel;

/**
 * 测试JLIST
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建作口
        JFrame jf=new JFrame();
        jf.setSize(300, 200);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        jf.setLocationRelativeTo(null);

        //面板
        JPanel panel=new JPanel();
        //显示标签
        JLabel lbl=new JLabel("我的女友列表：");
        panel.add(lbl);

        //定义一个finall list
        final JList list=new JList();
        //设置自定义大小
        list.setPreferredSize(new Dimension(100, 35));

        //允行可间断选择
        list.setSelectionMode(ListSelectionModel.SINGLE_INTERVAL_SELECTION);

        //Vector集合装数据
        Vector<String> dataList=new Vector<String>();
        dataList.add("凤姐");
        dataList.add("芙蓉");
        dataList.add("起坤");
        dataList.add("凤姐的表姐");
        //给下拉设置数据
        list.setListData(dataList);
        //将list添加到面板中
        //panel.add(list);
        //将list添加到滚动面板，再将滚动面板添加到panel中
        panel.add(new JScrollPane(list));

        //list.setSelectedIndex(2);//选中一个
        list.setSelectedIndices(new int[]{1,2});//选中多个

        JButton btn=new JButton("提交");
        btn.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                int[] indexs=list.getSelectedIndices();
                for(int i=0;i<indexs.length;i++){
                    System.out.println(indexs[i]);
                }
            }
        });
        //面板添加按钮
        panel.add(btn);

        jf.setContentPane(panel);
        jf.setVisible(true);


    }

}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/110541/35/37428/49527/647d5ed9Fffe889da/070ae025a3aaf2e2.jpg)

###### 进度条

~~~java
package component.progress;

import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JProgressBar;
import javax.swing.SwingConstants;
import javax.swing.Timer;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试进度条
 * @author wenber
 *
 */
public class Hello {
    //当前进度
    static int currentProgress=0;

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(500, 100);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel=new JPanel();
        //创建进度条
        final JProgressBar progressBar=new JProgressBar();
        //final JProgressBar progressBar=new JProgressBar(SwingConstants.VERTICAL,0,100);
        //添加监听
        progressBar.addChangeListener(new ChangeListener() {

            @Override
            public void stateChanged(ChangeEvent e) {
                System.out.println("value chagen==="+progressBar.getValue()+"=="+progressBar.getPercentComplete());
            }
        });

        //设置不确定模式【应用于不确定时间】
        //progressBar.setIndeterminate(true);

        //设置方向[竖直方向]默认水平方向
        //progressBar.setOrientation(SwingConstants.VERTICAL);

        progressBar.setPreferredSize(new Dimension(400, 40));
        //设置最大最小
        progressBar.setMinimum(0);
        progressBar.setMaximum(100);

        //绘制百分比文本刻度
        progressBar.setStringPainted(true);

        //添加到面板中
        panel.add(progressBar);
        //将面板添加到窗口
        jf.setContentPane(panel);

        //设置显示示
        jf.setVisible(true);

        //多线程 模拟延迟 每隔0.5秒改变进度一次
        new Timer(500,new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                currentProgress+=5;
                if(currentProgress>100){
                    currentProgress=0;
                }
                //改变进度
                progressBar.setValue(currentProgress);
            }
        }).start();
    }

}

~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/191169/3/35199/48711/647d5f83Fcbdd972b/464dc8b9a849d28a.jpg)

###### 滑块

~~~java
package component.slider;

import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JSlider;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试滑块组件
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		//创建窗口
		JFrame jf=new JFrame();
		//设置
		jf.setSize(400, 300);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		//创建面板
		JPanel panel=new JPanel(null);
		
		JLabel lbl=new JLabel("滑块");
		lbl.setBounds(10, 10, 80, 35);
		panel.add(lbl);
		
		//创建滑块
		final JSlider slider=new JSlider(0, 40, 20);
		slider.setBounds(100, 10, 250, 50);
		
		//添加监听器
		slider.addChangeListener(new ChangeListener() {
			
			@Override
			public void stateChanged(ChangeEvent e) {
				System.out.println("当前刻度变化="+slider.getValue());
			}
		});
		
		//设置主刻度间隔
		slider.setMajorTickSpacing(5);
		//设置次刻度间隔
		slider.setMinorTickSpacing(1);
		
		//绘制刻度
		slider.setPaintTicks(true);
		//绘制标签
		slider.setPaintLabels(true);
		
		panel.add(slider);
		
		//取得当前的刻度值
		System.out.println(slider.getValue());
		
		//将面板添加到窗口
		jf.setContentPane(panel);
		//显示
		jf.setVisible(true);
	}
}
~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/112067/37/39545/12380/647d5fbfF32cf8a46/9421a0eea1004f40.jpg)

###### 滑块2

~~~java
package component.slider;

import java.awt.Dimension;
import java.util.Hashtable;

import javax.swing.ImageIcon;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JSlider;
import javax.swing.SwingConstants;
import javax.swing.UIManager;
import javax.swing.UnsupportedLookAndFeelException;

/**
 * 测试自定义的刻度滑块
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException, UnsupportedLookAndFeelException {
        //设置样式为当前系统的样式【winddows】
        UIManager.setLookAndFeel(UIManager.getSystemLookAndFeelClassName());

        //创建窗口
        JFrame jf=new JFrame();
        //设置
        jf.setTitle("你喜欢哪种多一点点!");
        jf.setSize(600, 400);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        //创建面板
        JPanel panel=new JPanel();
        //自定义滑块
        final JSlider slider=new JSlider(0,100,50);
        //设置默认宽度
        slider.setPreferredSize(new Dimension(400, 300));
        slider.setMajorTickSpacing(5);
        slider.setMinorTickSpacing(1);

        slider.setPaintTicks(true);
        slider.setPaintLabels(true);

        Hashtable<Integer, JLabel> ht=new Hashtable<Integer, JLabel>();
        ht.put(0, new JLabel(new ImageIcon("image/boy.png")));
        ht.put(50, new JLabel(new ImageIcon("image/title.png")));
        ht.put(100, new JLabel(new ImageIcon("image/girl.png")));
        //将自定义的刻度标签添加到滑块中
        slider.setLabelTable(ht);

        //滑块方向
        //slider.setOrientation(SwingConstants.VERTICAL);

        //将滑块添加到面板中
        panel.add(slider);

        //添加面板到窗口
        jf.setContentPane(panel);
        //设置可见
        jf.setVisible(true);
    }

}
~~~

![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/190640/26/25591/35951/647d5ff1F463e7b7e/b610181bb11a62eb.jpg)

###### 表格

~~~java
import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTable;

public class TableDemo extends JFrame {
    private JTable table;

    public TableDemo() {
        setTitle("Table Demo");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        // 定义表格数据
        Object[][] data = {
		{"John", "Doe", 25, true},
                {"Jane", "Doe", 30, false},
                {"Bob", "Smith", 45, true},
                {"Susan", "Johnson", 50, false}
				};

        // 定义表格列名
        String[] columnNames = {"First Name", "Last Name", "Age", "Married"};

        // 创建表格组件
        table = new JTable(data, columnNames);

        // 将表格组件添加到滚动窗格中
        JScrollPane scrollPane = new JScrollPane(table);
        add(scrollPane);

        pack();
        setVisible(true);
    }

    public static void main(String[] args) {
        new TableDemo();
    }
}

~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/129354/32/33553/43144/647d6237F7f994f78/4e07a0c6981c36f2.jpg)

###### 单选框

~~~java
package component.radio;

import javax.swing.ButtonGroup;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JRadioButton;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试单选按钮
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(400, 300);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel=new JPanel();//flowlayout
        //创建按钮
        JRadioButton rBoy=new JRadioButton("男");
        //设置选中
        rBoy.setSelected(true);//设置选中
        System.out.println(rBoy.isSelected());
        //rBoy.setEnabled(false);//设置不用可
        JRadioButton rGirl=new JRadioButton("女");

        //添加监听事件
        rBoy.addChangeListener(new ChangeListener() {

            @Override
            public void stateChanged(ChangeEvent e) {
                JRadioButton btn=(JRadioButton) e.getSource();//取得事件源
                System.out.println("当前=="+btn.isSelected());//是否选中
            }
        });

        //创建组
        ButtonGroup btnGroup=new ButtonGroup();
        //添加到组中
        btnGroup.add(rBoy);
        btnGroup.add(rGirl);

        //添加到面板中
        panel.add(rBoy);
        panel.add(rGirl);

        jf.setContentPane(panel);
        jf.setVisible(true);

    }

}

~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/117042/39/37616/32510/647d6180Ffd16cbec/4fadc53092ed4807.jpg)



### JavaSwing面板组件

#### 普通面板Jpanel

~~~java
package panel.base;

import java.awt.FlowLayout;

import javax.swing.Box;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JPasswordField;
import javax.swing.JTextField;

/**
 * 测试常用面板
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        //设置
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //1 用流式布局
        JPanel panel01=new JPanel();
        panel01.add(new JLabel("帐号:"));
        panel01.add(new JTextField(20));

        //2:
        JPanel panel02=new JPanel();
        panel02.add(new JLabel("密码:"));
        panel02.add(new JPasswordField(20));

        //3:
        JPanel panel03=new JPanel();
        //流式布局 居中
        panel03.setLayout(new FlowLayout(FlowLayout.CENTER));
        panel03.add(new JButton("登录"));
        panel03.add(new JButton("找回密码"));

        //创建一个box竖直布局 且居中
        Box vBox=Box.createVerticalBox();
        vBox.add(panel01);
        vBox.add(panel02);
        vBox.add(panel03);

        jf.setContentPane(vBox);
        jf.pack();//重新整理大小 去除多余部分
        jf.setLocationRelativeTo(null);
        jf.setVisible(true);




    }

}

~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/168443/33/26105/35978/647d66e6F7713f035/61b2faf85bb41385.jpg)

#### 滚动面板Jscrollpanel

~~~java
package panel.scroll;

import java.awt.Color;
import java.awt.Font;
import java.awt.ScrollPane;

import javax.swing.JFrame;
import javax.swing.JScrollPane;
import javax.swing.JTextArea;
import javax.swing.ScrollPaneConstants;

/**
 * 测试滚动面板
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		//创建窗口
		JFrame jf=new JFrame();
		//设置
		jf.setSize(400, 300);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		//创建文本
		JTextArea txt=new JTextArea();
		//txt.setLineWrap(true);//自动换行
		txt.setFont(new Font("楷体",Font.PLAIN,22));//设置字体
		txt.setForeground(Color.BLUE);//颜色
		
		//创建滚动板 指定显示组件
		JScrollPane scrollPane=new JScrollPane(txt, 
				ScrollPaneConstants.VERTICAL_SCROLLBAR_ALWAYS,//总是显示
				ScrollPaneConstants.HORIZONTAL_SCROLLBAR_AS_NEEDED//需要时显示
				//ScrollPaneConstants.HORIZONTAL_SCROLLBAR_NEVER//从不显示
		);
		
		
		
		jf.setContentPane(scrollPane);
		jf.setVisible(true);
		
	}

}
~~~

![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/107261/3/41163/50939/647d6721F5fd0ae22/2f52a89076d82579.jpg)

#### 分隔面板

~~~java
package panel.split;

import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JSplitPane;

/**
 * 测试分隔面板
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建窗口
        JFrame jf=new JFrame();
        jf.setSize(400, 300);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        //创建分隔面板
        JSplitPane panel=new JSplitPane();

        //设置方向
        panel.setOrientation(JSplitPane.VERTICAL_SPLIT);//竖直方向 默认水平方向

        //设置左右组件
        panel.setLeftComponent(new JButton("左边的组件"));
        panel.setRightComponent(new JButton("右边的组件"));

        //设置快速关闭
        panel.setOneTouchExpandable(true);
        //拖动连续绘制
        panel.setContinuousLayout(true);

        //设置分隔条宽度
        panel.setDividerSize(20);
        //设置分隔条位置
        panel.setDividerLocation(100);

        //将分隔面板添加到窗口中
        jf.setContentPane(panel);
        jf.setVisible(true);

    }

}

~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/120808/36/38838/37495/647d6771Facf34800/120ee10ce823a120.jpg)

#### 选项卡面板

~~~java
package panel.tabbed;

import java.awt.Font;
import java.awt.GridLayout;

import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.JTabbedPane;
import javax.swing.SwingConstants;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;

/**
 * 测试切换面板
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		JFrame jf=new JFrame();
		jf.setSize(600, 400);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		jf.setLocationRelativeTo(null);
		
		//创建一个面板
		final JTabbedPane pane=new JTabbedPane();
		
		//创建1
		pane.addTab("个人信息", createPanel("阮老师"));
		
		//创建2
		pane.addTab("教育经历", createPanel("离清华1000公里的大学"));
		//插入指定位置
		//pane.insertTab("教育经历2", new ImageIcon("image/title.png"), createPanel("离华为2000公里的公司"), "OKK", 0);
		//移除
		//pane.remove(0);
		
		//创建3 标题加图标
		//pane.addTab("工作经历",new ImageIcon("image/title.png"), createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历1", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历2", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历3", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历4", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历5", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历6", createPanel("离华为2000公里的公司"));
		pane.addTab("工作经历7", createPanel("离华为2000公里的公司"));
		
		//添加监听
		pane.addChangeListener(new ChangeListener() {
			
			@Override
			public void stateChanged(ChangeEvent e) {
				System.out.println("当前选中了..."+pane.getSelectedIndex());
			}
		});
		//pane.setTabPlacement(JTabbedPane.LEFT);//tabbed标题位置
		pane.setTabPlacement(JTabbedPane.BOTTOM);
		//pane.setTabLayoutPolicy(JTabbedPane.SCROLL_TAB_LAYOUT);//滚动条
		pane.setTabLayoutPolicy(JTabbedPane.WRAP_TAB_LAYOUT);//换行包起来
		
		//第四个选项卡显示
		pane.setSelectedIndex(3);
		
		jf.setContentPane(pane);
		jf.setVisible(true);
	}
	
	
	public static JPanel createPanel(String text){
		//创建面板 一行一列 网络布局
		JPanel panel=new JPanel(new GridLayout(1, 1));
		//
		JButton btn=new JButton(text);
		btn.setFont(new Font("宋体", Font.PLAIN, 18));
		btn.setHorizontalAlignment(SwingConstants.CENTER);//内容居中
		panel.add(btn);
		return panel;
	}

}

~~~

![image.png](https://img12.360buyimg.com/ddimg/jfs/t1/6029/35/19260/57224/647d67a1F36abcfd6/c16825510c0f643f.jpg)



### Swing其他组件

#### frame

~~~java
package other.frame;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.ImageIcon;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.WindowConstants;

/**
 * 测试窗口
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		
		final JFrame jf=new JFrame("测试窗口");
		jf.setSize(800, 400);//大小
		jf.setLocationRelativeTo(null);//居中
		jf.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);//关闭退出
		//设置最大化 不允许
		jf.setResizable(false);
		
		//设置图标
		jf.setIconImage(new ImageIcon("image/title.png").getImage());
		
		JPanel pane=new JPanel();
		JButton btn=new JButton("弹出窗口");
		btn.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				//弹出新窗口
				openNew(jf);
			}
		});
		
		pane.add(btn);
		//将面板添加到窗口中
		jf.setContentPane(pane);
		//是否显示
		System.out.println(jf.isShowing());
		//可见
		jf.setVisible(true);
		//取得基于屏幕的位置
		System.out.println(jf.getLocationOnScreen().getX()+"=="+jf.getLocationOnScreen().getY());
		//是否显示
		System.out.println(jf.isShowing());
		
	}
	
	public static void openNew(JFrame jframe){
		JFrame jf=new JFrame("新窗口");
		jf.setSize(400, 200);//大小
		jf.setLocationRelativeTo(jframe);//依赖的指定窗口而居中
		jf.setDefaultCloseOperation(WindowConstants.DISPOSE_ON_CLOSE);//关闭并销毁 而不是退出jvm
		//设置最大化 不允许
		jf.setResizable(false);
		
		JPanel pane=new JPanel();

		//将面板添加到窗口中
		jf.setContentPane(pane);
		//可见
		jf.setVisible(true);
	}

}

~~~

![image.png](https://img13.360buyimg.com/ddimg/jfs/t1/223424/40/22068/21525/647d686bF94aab83b/4bda02706e9802e7.jpg)

#### 对话框

~~~java
package other.dialog;

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.Box;
import javax.swing.JButton;
import javax.swing.JFrame;
import javax.swing.JOptionPane;
import javax.swing.JPanel;

/**
 * 测试对话框
 * @author wenber
 *
 */
public class Hello {
	
	public static void main(String[] args) {
		final JFrame jf=new JFrame("测试对话框");
		jf.setSize(400, 300);
		jf.setLocationRelativeTo(null);
		jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		
		/**
		 * 1消息对话框
		 */
		JButton btn1=new JButton("showMessageDialog 消息框");
		btn1.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				JOptionPane.showMessageDialog(
						jf,
						"阮总最帅",
						"谁最帅",
						JOptionPane.INFORMATION_MESSAGE);
			}
		});
		
		/**
		 * 2消息对话框(警告)
		 */
		JButton btn2=new JButton("showMessageDialog 警告框");
		btn2.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				JOptionPane.showMessageDialog(
						jf,
						"请拿出你的IQ CQ QQ MQ",
						"打劫",
						JOptionPane.WARNING_MESSAGE);
			}
		});
		
		/**
		 * 3确认对话框
		 */
		JButton btn3=new JButton("showMessageDialog 确认对话框");
		btn3.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				//需要接收其结果
				/**
				 * JOptionPane.YES_OPTION
				 * JOptionPane.NO_OPTION
				 * JOptionPane.CANCEL_OPTION
				 * JOptionPane.CLOSE_OPTION
				 */
				int result=JOptionPane.showConfirmDialog(
						jf, 
						"你确认分手吗？其实我是富二代 只是没有跟你说",
						"分手快乐", 
						/**
						 * JOptionPane.YES_OPTION 是
						 * JOptionPane.YES_NO_OPTION 是 否
						 * JOptionPane.YES_NO_CANCEL_OPTION 是 否  取消
						 */
						JOptionPane.YES_NO_OPTION);
				System.out.println(result);
			}
		});
		
		//4 输入对话框 文本输入
		JButton btn4=new JButton("showInputDialog 输入对话框");
		btn4.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				String msg=JOptionPane.showInputDialog(
						jf, 
						"请输入你的银行密码", 
						"888888");
				System.out.println("收到密码="+msg);
			}
		});
		
		//5 输入对话框gh 下拉选择
		JButton btn5=new JButton("showInputDialog 下拉选择框");
		btn5.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				Object[] selectionValues={"中国","美国","日本"};
				//显示输入框 并得到返回的内容
				Object content=JOptionPane.showInputDialog(
						jf,
						"选择你想去的城市", 
						"个人去向", 
						JOptionPane.PLAIN_MESSAGE,
						null, 
						selectionValues,
						selectionValues[0]);
				System.out.println("你的选项为=="+content);
			}
		});
		
		//6选项对话框
		JButton btn6=new JButton("showOptionDialog 选项对话框");
		btn6.addActionListener(new ActionListener() {
			
			@Override
			public void actionPerformed(ActionEvent e) {
				Object[] selectionValues={"中国","美国","日本"};
				//显示输入框 并得到返回的内容
				int optionSelected=JOptionPane.showOptionDialog(
						jf,
						"选一种", 
						"买它买它买它", 
						JOptionPane.YES_NO_CANCEL_OPTION, 
						JOptionPane.ERROR_MESSAGE, 
						null,
						selectionValues,
						selectionValues[0]);
				System.out.println("你的选项为=="+optionSelected);
			}
		});
		
		
		
		//盒子布局
		Box vbox=Box.createVerticalBox();
		vbox.add(btn1);
		vbox.add(btn2);
		vbox.add(btn3);
		vbox.add(btn4);
		vbox.add(btn5);
		vbox.add(btn6);
		
		//创建一个面板
		JPanel panel=new JPanel();
		panel.add(vbox);
		
		jf.setContentPane(panel);
		jf.setVisible(true);
		
	}

}
~~~

#### 文件组件

~~~java
package other.chooser;

import java.awt.Component;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;

import javax.swing.JButton;
import javax.swing.JFileChooser;
import javax.swing.JFrame;
import javax.swing.JPanel;
import javax.swing.filechooser.FileNameExtensionFilter;

/**
 * 测试文件选择器
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {

        final JFrame jf=new JFrame("测试文件选择");
        jf.setSize(400, 250);
        jf.setLocationRelativeTo(null);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        JPanel panel=new JPanel();
        JButton btnOpen=new JButton("打开");
        btnOpen.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                //显示打开文件选择器
                showFileOpen(jf);
            }
        });

        JButton btnSave=new JButton("保存");
        btnSave.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                showFileSave(jf);
            }
        });
        //添加测试按钮
        panel.add(btnOpen);
        panel.add(btnSave);

        jf.setContentPane(panel);
        jf.setVisible(true);

    }

    /**
     * 显示文件保存
     * @param parent
     */
    static void showFileSave(Component parent){
        //创建
        JFileChooser chooser=new JFileChooser(new File("."));
        //设置默认名称
        chooser.setSelectedFile(new File("good.txt"));
        //显示
        int result=chooser.showSaveDialog(parent);
        //判断是否点了确定
        if(result==JFileChooser.APPROVE_OPTION){
            //取得文件【从chooser中取得文件所放置的位置 】
            File file=chooser.getSelectedFile();
            //打印绝对路径
            System.out.println(file.getAbsolutePath());
        }
    }

    /**
     * 打开文件选择器
     * @param parent
     */
    static void showFileOpen(Component parent){
        //创建文件选择器
        JFileChooser fileChooser=new JFileChooser();
        //设置当前文件夹位置
        fileChooser.setCurrentDirectory(new File("."));//.当前文件夹
        //设置选择模式
        fileChooser.setFileSelectionMode(JFileChooser.FILES_AND_DIRECTORIES);
        //是否多选
        fileChooser.setMultiSelectionEnabled(true);

        //添加过滤器======================================================1：描述后缀                     2：可变参数 允许的后缀
        fileChooser.addChoosableFileFilter(new FileNameExtensionFilter("压缩文件(*.zip,*.rar)","zip","rar"));
        //接收结果
        int result=fileChooser.showOpenDialog(parent);
        //判断是否选择了文件
        if(result==JFileChooser.APPROVE_OPTION){
            //取得选择文件
            File[] filse=fileChooser.getSelectedFiles();
            for (File file : filse) {
                System.out.println(file.getName());
            }
        }
    }
}

~~~



![image.png](https://img11.360buyimg.com/ddimg/jfs/t1/151609/27/25059/36425/647d693bF591424c5/04752050e777a786.jpg)

#### 颜色选择组件

~~~java
package other.chooser;

import java.awt.Color;
import java.awt.Dimension;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

import javax.swing.JButton;
import javax.swing.JColorChooser;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;

/**
 * 测试颜色选择器
 * @author wenber
 *
 */
public class Hello {

    public static void main(String[] args) {
        //创建主窗口
        final JFrame jf=new JFrame();
        jf.setSize(300, 300);
        jf.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        jf.setLocationRelativeTo(null);

        JPanel pane=new JPanel();

        //创建显示颜色的label
        final JLabel lbl=new JLabel();
        //设置大小
        lbl.setPreferredSize(new Dimension(150, 150));
        //设置透明
        lbl.setOpaque(true);
        pane.add(lbl);

        //创建按钮
        JButton btn=new JButton("选择颜色");
        btn.addActionListener(new ActionListener() {

            @Override
            public void actionPerformed(ActionEvent e) {
                //显示出颜色对话框
                Color color=JColorChooser.showDialog(jf, "请选择你喜欢的颜色", null);
                if(color!=null){
                    //lbl的颜色来自于选择框
                    lbl.setBackground(color);
                    //取得选择颜色的ARGB参数值
                    int alpha=color.getAlpha();
                    int red=color.getRed();
                    int green=color.getGreen();
                    int blue=color.getBlue();
                    //显示参数值
                    lbl.setText("A="+String.format("%02x", alpha)+","+String.format("#%02x%02x%02x", red,green,blue));


                }
            }
        });
        pane.add(btn);

        jf.setContentPane(pane);
        jf.setVisible(true);


    }

}

~~~

![image.png](https://img10.360buyimg.com/ddimg/jfs/t1/197702/36/34843/45831/647d6985F830fae24/99f229c89b1827f5.jpg)
![image.png](https://img14.360buyimg.com/ddimg/jfs/t1/86305/2/38593/4320/647d698cF52873b14/eaf7c24467884218.jpg)

.































