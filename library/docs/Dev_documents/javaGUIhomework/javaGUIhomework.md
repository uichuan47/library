# 12.Java GUI作业

```
- Frame窗口 ---> BoderLayout --> center 放置一个面板Panel Container
	- 面板Container
		- 放入标题
		- 放入四个按钮
```

```
- 创建所有的显示界面Frame
	- 主界面 setVisible true
	- 详情的计算界面 setVisible true
		- 点击按钮打开详情界面时：
			主界面setVisible -> false
			详情界面setVisible -> true
	- 关闭主界面时 -> System.exit(0)
```



思路：

- 主界面Caculator继承Frame

```
- loadFrame加载主界面
- 主界面中监听四个按钮
- start以及close分别初始化以及监听主界面关闭->结束程序
```



- 四个监听接口

```java
class MyActionListener1 implements ActionListener{...}
...
class MyActionListener4 implements ActionListener{...}    
```



每一个监听接口根据参数（模式）的不同，调用子界面对象是初始化时(调用构造器)传入的参数也不同

```java
class MyActionListener2 implements ActionListener {
    @Override
    public void actionPerformed(ActionEvent e) {
        MySonFrame mySonFrame = new MySonFrame(2);
        mySonFrame.loadSonFrame();
    }
}
```

- 子界面类的实现

```java
class MySonFrame extends Frame{
    private void start(){...}
    private int type;
    // 构造方法
    public MySonFrame(int type) {
        this.type = type;
        start();
        addWindowListener(new SonActionListener());
        if (type == 1) {
            process_1();
        } else if (type == 2) {
            process_2();
        }else if (type == 3){
            process_3();
        } else if (type == 4) {
            process_4();
        }
    }
   	// process_n是MySonFrame中的成员,实现了子页面的功能
    public void process_1(){
        ...
    }
    public void process_2(){...}
    public void process_3(){...}
    public void process_4(){...}
        
            
}
```

- MySonFrame中的process_n方法

```java
// 创建数学计算对象
Process math_master = new Process();

public void process_1() {
    // 新建一个容器
    Panel BtnContainer1 = new Panel();
    BtnContainer1.setBounds(50, 140, 400, 50);
    BtnContainer1.setBackground(Color.WHITE);
    add(BtnContainer1);

    // 新建三个输入框
    TextField num1 = new TextField(3);
    TextField num2 = new TextField(3);
    TextField num3 = new TextField(8);
    Button resbutton1 = new Button("->");
    Label label = new Label("最大公约数");
    //布局
    BtnContainer1.setLayout(new FlowLayout());
    BtnContainer1.add(num1);
    BtnContainer1.add(num2);
    BtnContainer1.add(label);
    BtnContainer1.add(resbutton1);
    BtnContainer1.add(num3);

    // ->按钮添加事件
    ProcessLinstener1 processLinstener1 = new ProcessLinstener1(num1, num2, num3);
    resbutton1.addActionListener(processLinstener1);

}
```

- 数学对象

```java
class Process {
    public int CaculateModel1(int number1, int number2) {
        while (number2 != 0) {
            int temp = number1 % number2;
            number1 = number2;
            number2 = temp;
        }
        return number1;
    }

    public int CaculateModel2(int m, int n){...}
    public int CaculateModel3(int m, int n){...}
    public int CaculateModel4(int m, int n){...}
}
```



**全部代码**

[Github🎈](https://github.com/uichuan47/JavaGuiHomework.git)



