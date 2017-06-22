# 结构型模式
## 适配器模式
将一个类的接口变换成客户端所期待的另一种接口，从而是原本因接口不匹配而无法在一起工作的两个类能够在一起工作

自我总结：适配器类继承需适配的类并实现需适配类想调用方法所在的接口，实际中使用适配器实例进行工作，旨在对象转化
```
public class Adapter extends Adaptee implements Target {
	public void request() {
		super.doSomething();//原有的业务逻辑
	}
}
```
## 装饰器模式
动态地给一个对象添加一些额外的职责。就增加功能来说，装饰模式相比生产子类更为灵活

自我总结：装饰者类中必然存在一个private变量指向需装饰者的抽象构件，一般装饰兄弟类，隶属于同一个家族,旨在保证被修饰的对象功能比原始对象丰富(当然，也可以减弱)，旨在加强对象的功能
```
public abstract class Decorator extends Component {
	private Component component = null;
	//通过构造函数传递被修饰者
	public Decorator(Component _component){
		this.component = _component;
	}
	//委托给被修饰者执行
	@Override
	public void operate() {
		this.component.operate();
	}
}
```
```
public class ConcreteDecorator1 extends Decorator {
	//定义被修饰者
	public ConcreteDecorator1(Component _component){
		super(_component);
	}
	//定义自己的修饰方法
	private void method1(){
		System.out.println("method1 修饰");
	}
	//重写父类的Operation方法
	public void operate(){
		super.operate();
		this.method1();
	}
}
```
```
public class Client {
	public static void main(String[] args) {
		Component component = new ConcreteComponent();
		//第一次修饰
		component = new ConcreteDecorator1(component);
		//第二次修饰
		component = new ConcreteDecorator2(component);
		//修饰后运行
		component.operate();
		/*
		do Something
		method1 修饰
		method2 修饰
		 */
	}
}
```
## 代理模式
为其他对象提供一种代理以控制对这个对象的访问

自我总结：代理模式是把当前的行为或功能委托给其他对象执行，代理类负责接口限定，可以在执行委托对象方法之前或者之后进行其他操作
```
public class RunnerAgent implements IRunner {
	private IRunner runner;
	public RunnerAgent(IRunner _runner){
		this.runner = _runner;
	}
	//代理人是不会跑的
	public void run() {
		Random rand = new Random();
		if(rand.nextBoolean()){
			System.out.println("代理人同意安排运动员跑步");
			runner.run();
		}else{
			System.out.println("代理人心情不好，不安排运动品跑步");
		}
	}
}
```
## 外观模式
要求一个子系统的外部与其内部的通讯必须通过一个统一的对象进行。外观模式提供一个高层次的接口，使得子系统更易于使用
```
public class Facade {
	//被委托的对象
	private ClassA a = new ClassA();
	private ClassB b = new ClassB();
	private Context context = new Context();
	//提供给外部访问的方法
	public void methodA(){
		this.a.doSomethingA();
	}
	public void methodB(){
		this.b.doSomethingB();
	}
	public void methodC(){
		this.context.complexMethod();
	}
}
```
```
public class Context {
	//委托处理
	private ClassA a = new ClassA();
	private ClassC c = new ClassC();
	//复杂的计算
	public void complexMethod(){
		this.a.doSomethingA();
		this.c.doSomethingC();
	}
}
```
## 桥接模式
将抽象和实现解耦，使得两者可以独立地变化
```
public class ConcreteImplementor1 implements Implementor{
	public void doSomething(){
		//业务逻辑处理
	}
	public void doAnything(){
		//业务逻辑处理
	}
}
```
```
public class RefinedAbstraction extends Abstraction {
	//覆写构造函数
	public RefinedAbstraction(Implementor _imp){
		super(_imp);
	}
	//修正父类的行文
	@Override
	public void request(){
		/*
		 * 业务处理....
		 */
		super.request();
		super.getImp().doAnything();
	}
}
```
```
public class Client {
	public static void main(String[] args) {
		//定义一个实现化角色
		Implementor imp = new ConcreteImplementor1();
		//定义一个抽象化角色
		Abstraction abs = new RefinedAbstraction(imp);
		//执行行文
		abs.request();
	}
}
```
## 组合模式
将对象组合成树形结构以表示“部分-整理”的层次结构，使得用户对单个对象和组合对象的使用具有一致性
```
public abstract class Component {
	//个体和整体都具有的共享
	public void doSomething(){
		//编写业务逻辑
	}	
	//增加一个叶子构件或树枝构件
	public abstract void add(Component component);
	//删除一个叶子构件或树枝构件
	public abstract void remove(Component component);
	//获得分支下的所有叶子构件和树枝构件
	public abstract ArrayList<Component> getChildren();
}
```
```
public class Leaf extends Component {	
	@Deprecated
	public void add(Component component) throws UnsupportedOperationException{
		//空实现,直接抛弃一个“不支持请求”异常
		throw new UnsupportedOperationException();
	}
	@Deprecated
	public void remove(Component component)throws UnsupportedOperationException{
		//空实现
		throw new UnsupportedOperationException();
	}
	@Deprecated
	public ArrayList<Component> getChildren()throws UnsupportedOperationException{
		//空实现
		throw new UnsupportedOperationException();		
	}
}
```
```
public class Composite extends Component {
	//构件容器
	private ArrayList<Component> componentArrayList = new ArrayList<Component>();
	//增加一个叶子构件或树枝构件
	public void add(Component component){
		this.componentArrayList.add(component);
	}
	//删除一个叶子构件或树枝构件
	public void remove(Component component){
		this.componentArrayList.remove(component);
	}
	//获得分支下的所有叶子构件和树枝构件
	public ArrayList<Component> getChildren(){
		return this.componentArrayList;
	}
}
```
## 享元模式
使用共享对象可有效地支持大量的细粒度的对象
