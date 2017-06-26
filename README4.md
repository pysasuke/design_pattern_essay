# 行为型模式
## 策略模式
定义一组算法，将每个算法都封装起来，并且使它们之间可以互换

自我总结：多种实现类实现同一个接口，环境类包含一个接口的引用，通过环境类调用接口方法，客户端只需要调用环境类方法即可，但根据传入的实现类不同产生的结果也说不同。
```
public class BackDoor implements IStrategy {
	public void operate() {
		System.out.println("找乔国老帮忙，让吴国太给孙权施加压力");
	}
}
```
```
public class Context {
	//构造函数，你要使用那个妙计
	private IStrategy straegy;
	public Context(IStrategy strategy){
		this.straegy = strategy;
	}
	//使用计谋了，看我出招了
	public void operate(){
		this.straegy.operate();
	}
}
```
```
public class ZhaoYun {
	 //赵云出场了，他根据诸葛亮给他的交代，依次拆开妙计
	public static void main(String[] args) {
		Context context;
		
		//刚刚到吴国的时候拆第一个
		System.out.println("---刚刚到吴国的时候拆第一个---");
		context = new Context(new BackDoor()); //拿到妙计
		context.operate();  //拆开执行
		System.out.println("\n");
		
		//刘备乐不思蜀了，拆第二个了
		System.out.println("---刘备乐不思蜀了，拆第二个了---");
		context = new Context(new GivenGreenLight());
		context.operate();  //执行了第二个锦囊了
		System.out.println("\n");
		
		//孙权的小兵追了，咋办？拆第三个
		System.out.println("---孙权的小兵追了，咋办？拆第三个---");
		context = new Context(new BlockEnemy());
		context.operate();  //孙夫人退兵
		System.out.println("\n");
		/*
		 *问题来了：赵云实际不知道是那个策略呀，他只知道拆第一个锦囊，
		 *而不知道是BackDoor这个妙计，咋办？  似乎这个策略模式已经把计谋名称写出来了
		 *
		 * 错！BackDoor、GivenGreenLight、BlockEnemy只是一个代码，你写成first、second、third，没人会说你错!
		 * 
		 * 策略模式的好处就是：体现了高内聚低耦合的特性呀，缺点嘛，这个那个，我回去再查查
		 */
	}
}
```
## 模板方法模式
定义一个操作中的算法的骨架，而将一些步骤延迟到子类中。使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤
```
public abstract class HummerModel {
	/*
	 * 首先，这个模型要能够被发动起来，别管是手摇发动，还是电力发动，反正
	 * 是要能够发动起来，那这个实现要在实现类里了
	 */
	protected abstract void start();
	//能发动，那还要能停下来，那才是真本事
	protected abstract void stop();
	//喇叭会出声音，是滴滴叫，还是哔哔叫
	protected abstract void alarm();
	//引擎会轰隆隆的响，不响那是假的
	protected abstract void engineBoom();
	//那模型应该会跑吧，别管是人退的，还是电力驱动，总之要会跑
	final public void run() {
		//先发动汽车
		this.start();
		//引擎开始轰鸣
		this.engineBoom();
		//要让它叫的就是就叫，喇嘛不想让它响就不响
		if(this.isAlarm()){
			this.alarm();
		}
		//到达目的地就停车
		this.stop();
	}
	
	//钩子方法，默认喇叭是会响的
	protected  boolean isAlarm(){
		return true;
	}
}
```
```
public class HummerH1Model extends HummerModel {
	private boolean alarmFlag = true;  //是否要响喇叭
	@Override
	protected void alarm() {
		System.out.println("悍马H1鸣笛...");
	}
	@Override
	protected void engineBoom() {
		System.out.println("悍马H1引擎声音是这样在...");
	}
	@Override
	protected void start() {
		System.out.println("悍马H1发动...");
	}
	@Override
	protected void stop() {
		System.out.println("悍马H1停车...");
	}
	protected boolean isAlarm() {
		return this.alarmFlag;
	}
	//要不要响喇叭，是有客户的来决定的
	public void setAlarm(boolean isAlarm){
		this.alarmFlag = isAlarm;
	}
}
```
## 观察者模式
定义对象间一种一对多的依赖关系，使得每当一个对象改变状态，则所有依赖于它的对象都会得到通知并被自动更新

自我总结：被观察者内部包含一个储存观察者的容器，可以添加、删除、通知所有观察者，观察者实现Observer接口并实现update()方法，当被观察者发生某些动作时通知所有观察者调用update()方法。
```
public abstract class Subject {
	//定一个一个观察者数组
	private Vector<Observer> obsVector = new Vector<Observer>();
	//增加一个观察者
	public void addObserver(Observer o){
		this.obsVector.add(o);
	}
	//删除一个观察者
	public void delObserver(Observer o){
		this.obsVector.remove(o);
	}
	//通知所有观察者
	public void notifyObserver(){
		for(Observer o:this.obsVector){
			o.update();
		}
	}
}
```
```
public class SltoSaleCeaseSubject extends Subject {
	//具体的业务
	public void doSomething(){
		System.out.println("双色球停止销售");
		/*
		 * do something
		 */
		super.notifyObserver();
	}
}
```
```
public class Merchant1Observer implements Observer {
	private String name;
	public Merchant1Observer(String name){
		this.name = name;
	}
	//实现更新方法
	public void update() {
		System.out.println(name + "接收到信息，并进行处理！");
	}
}
```
也可以引入java.util包：被观察者继承Observable类，观察者实现Observer接口并实现update方法
```
public class HanFeiZi extends Observable{
	//韩非子要吃饭了
	public void haveBreakfast(){
		System.out.println("韩非子:开始吃饭了...");
		//通知所有的观察者
		super.setChanged();
		super.notifyObservers("韩非子在吃饭");
		
	}
	//韩非子开始娱乐了,古代人没啥娱乐，你能想到的就那么多
	public void haveFun(){
		System.out.println("韩非子:开始娱乐了...");
		super.setChanged();
		this.notifyObservers("韩非子在娱乐");
	}
}
```
```
public class LiuSi implements Observer{
	//刘斯，观察到韩非子活动后，自己也做一定得事情
	public void update(Observable observable,Object obj){
		System.out.println("刘斯：观察到韩非子活动，开始动作了...");
		this.happy(obj.toString());
		System.out.println("刘斯：真被乐死了\n");
	}
	//一看韩非子有变化，他就快乐
	private void happy(String context){
		System.out.println("刘斯：因为" +context+",——所以我快乐呀！" );
	}
}
```
## 迭代子模式
将一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作
## 责任链模式
使多个对象都有机会处理请求，从而避免了请求的发送者接受者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递请求，直到有对象处理它为止。

自我总结：每个处理者都包含一个指向下一个处理者的引用和一个是否由自己处理的标识，如果标识匹配，则自己处理，如果不匹配则交由下一个处理者处理
```
public abstract class Handler {
	private Handler nextHandler;
	// 每个处理者都必须对请求做出处理
	public final Response handlerMessage(Request request) {
		Response response = null;
		// 判断是否是自己的处理级别
		if (this.getHandlerLevel() == request.getRequestLevel()) {
			response = this.echo(request);
		} else { // 不属于自己的处理级别
			// 判断是否有下一个处理者
			if (this.nextHandler != null) {
				response = this.nextHandler.handlerMessage(request);
			} else {
				// 没有适当的处理者，业务自行处理
			}
		}
		return response;
	}
	// 设置下一个处理者是谁
	public void setNext(Handler _handler) {
		this.nextHandler = _handler;
	}
	// 每个处理者都有一个处理级别
	protected abstract int getHandlerLevel();
	// 每个处理者都必须实现处理任务
	protected abstract Response echo(Request request);
}
```
```
public class Request {
	private int level;
	public Request(int level){
		this.level = level;
	}
	//请求的等级
	public int getRequestLevel(){
		return level;
	}
}
```
```
public class Client {
	public static void main(String[] args) {
		// 声明出所有的处理节点
		Handler pm = new PM();
		Handler dm = new DM();
		Handler vp = new VP();
		// 设置链中的阶段顺序,1-->2-->3
		pm.setNext(dm);
		dm.setNext(vp);
		// 提交请求，返回结果
		Response response1 = pm.handlerMessage(new Request(1));

		Response response2 = pm.handlerMessage(new Request(2));
		
		Response response3 = pm.handlerMessage(new Request(3));
		/*
		员工的请求level是：1
		项目经理的答复是：同意
		员工的请求level是：2
		部门经理的答复是：同意
		员工的请求level是：3
		副总的答复是：同意
		 */
	}
}
```
## 命令模式
将一个请求封装成一个对象，从而让你使用不同的请求把客户端参数化，对请求排队或者记录请求日志，可以提供命令的撤销和恢复功能

自我总结：命令基类包含所有接受者的引用，环境类包含一个命令基类的引用，通环境类达到请求者和接受者解耦
```
public class ConcreteCommand1 extends Command {
	//声明自己的默认接收者
	public ConcreteCommand1(){
		super(new ConcreteReciver1());
	}	
	//设置新的接收者
	public ConcreteCommand1(Receiver _receiver){
		super(_receiver);
	}	
	//每个具体的命令都必须实现一个命令
	public void execute() {
		//业务处理
		super.receiver.doSomething();
	}
}
```
```
public class ConcreteReciver1 extends Receiver{
	//每个接受者都必须处理一定的业务逻辑
	public void doSomething(){
		System.out.println("ConcreteReciver1 do it");
	}
}
```
```
public class Invoker {
	private Command command;
	//受气包，接受命令
	public void setCommand(Command _command){
		this.command = _command;
	}
	//执行命令
	public void action(){
		this.command.execute();
	}
}
```
```
public class Client {
	private static Invoker invoker;
	private static Command command;
	
	public static void main(String[] args) {
		//首先声明出调用者Invoker
		invoker = new Invoker();
		//定义一个发送给接收者的命令
		command = new ConcreteCommand1();
		//把命令交给调用者去执行
		invoker.setCommand(command);
		invoker.action();
		/*
		ConcreteReciver1 do it
		 */
	}
}
```
## 备忘录模式
在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态
```
public class GameNode {
	//游戏节点的状态
	private String state = "";
	//改变游戏节点的状态
	public void changeState(){
		this.state = "获取开奖号码";
	}
	public String getState() {
		return state;
	}
	public void setState(String state) {
		this.state = state;
	}
	//保留一个备份
	public Memento createMemento(){
		return new Memento(this.state);
	}
	//恢复一个备份
	public void restoreMemento(Memento _memento){
		this.setState(_memento.getState());
	}
}
```
```
public class Caretaker {
	//备忘录对象
	private Memento memento;
	public Memento getMemento() {
		return memento;
	}
	public void setMemento(Memento memento) {
		this.memento = memento;
	}
}
```
```
public class Memento {
	//游戏节点的状态
	private String state = "";
	//通过构造函数传递状态信息
	public Memento(String _state){
		this.state = _state;
	}
	public String getState() {
		return state;
	}
	public void setState(String state) {
		this.state = state;
	}
}
```
## 状态模式
当一个对象内在状态改变时允许其改变行为，这个对象看起来像改变了其类

自我总结：不同状态对象只处理自己状态需处理的动作，其他动作则由环境类和状态类共同进行状态切换后的状态对象执行。Context的状态是一个State类型，所以无论State派生出多少个子类，都能成为Context的状态。至于Context的状态变化，就在State子类的Handle方法中实现。例如ConcreateState1的handle方法，可以将Context的状态赋值成ConcreteState2对象，ConcreteState2的handle方法，可以将Context的状态赋值成ConcreteState3（图中没有）对象……一次类推。这样就将一个复杂的状态变化链，分解到每一步状态对象中
```
public class Context {
	//定义状态
	public final static State STATE1 = new ConcreteState1();
	public final static State STATE2 = new ConcreteState2();
	//当前状态
	private State CurrentState;
	//获得当前状态
	public State getCurrentState() {
		return CurrentState;
	}
	//设置当前状态
	public void setCurrentState(State currentState) {
		this.CurrentState = currentState;
		//切换状态
		this.CurrentState.setContext(this);
	}
	//行为委托
	public void handle1(){
		this.CurrentState.handle1();
	}
	public void handle2(){
		this.CurrentState.handle2();
	}
}
```
```
public abstract class State {
	//定义一个环境角色，提供子类访问
	protected Context context;
	//设置环境角色
	public void setContext(Context _context){
		this.context = _context;
	}
	//行为1
	public abstract void handle1();
	//行为2
	public abstract void handle2();
}
```
```
public class ConcreteState1 extends State {
	@Override
	public void handle1() {
		//本状态下必须处理的逻辑
		System.out.println("ConcreteState1 handle1 do it");
	}
	@Override
	public void handle2() {
		//设置当前状态为stat2
		super.context.setCurrentState(Context.STATE2);
		//过渡到state2状态，由Context实现
		super.context.handle2();
	}
}
```
## 访问者模式
封装一些作用于某种数据结构中的各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作
```
public class KungFuRole implements Role {
	//武功天子第一的角色
	public void accept(AbsActor actor){
		actor.act(this);
	}
}
```
```
public abstract class AbsActor {
	//演员都能够演一个角色
	public void act(Role role){
		System.out.println("演员可以扮演任何角色");
	}	
	//可以演功夫戏
	public void act(KungFuRole role){
		System.out.println("演员都可以演功夫角色");
	}
}
```
```
public class OldActor extends AbsActor {
	//不演功夫角色
	public void act(KungFuRole role){
		System.out.println("年龄大了，不能演功夫角色");
	}
}
```
```
public class Client {

	public static void main(String[] args) {
		//定义一个演员
		AbsActor actor = new OldActor();
		//定义一个角色
		Role role = new KungFuRole();
		//开始演戏
		role.accept(actor);//年龄大了，不能演功夫角色
	}
}
```
## 中介者模式
用一个中介对象封装一系列的对象交互，中介者使各对象不需要显示地相互作用，从而使其耦合松散，而且可以独立地改变它们之间的交互
```
public abstract class Colleague {
	protected Mediator mediator;
	public Colleague(Mediator _mediator){
		this.mediator = _mediator;
	}
}
```
```
public class ConcreteColleague1 extends Colleague {
	//通过构造函数传递中介者
	public ConcreteColleague1(Mediator _mediator){
		super(_mediator);
	}
	//自有方法 self-method
	public void selfMethod1(){
		//处理自己的业务逻辑
	}
	//依赖方法 dep-method
	public void depMethod1(){
		//处理自己的业务逻辑
		//自己不能处理的业务逻辑，委托给中介者处理
		super.mediator.doSomething1();
	}
}
```
```
public abstract class Mediator {
	//定义同事类
	protected ConcreteColleague1 c1;
	protected ConcreteColleague2 c2;
	
	//通过getter/setter方法吧同事类注入进来
	public ConcreteColleague1 getC1() {
		return c1;
	}
	public void setC1(ConcreteColleague1 c1) {
		this.c1 = c1;
	}
	public ConcreteColleague2 getC2() {
		return c2;
	}
	public void setC2(ConcreteColleague2 c2) {
		this.c2 = c2;
	}
	
	//中介者模式的业务逻辑
	public abstract void doSomething1();
	public abstract void doSomething2();
}
```
```
public class ConcreteMediator extends Mediator {
	@Override
	public void doSomething1() {
		//调用同事类的方法，只要是public方法都可以调用
		super.c1.selfMethod1();
		super.c2.selfMethod2();
	}
	public void doSomething2() {
		super.c1.selfMethod1();
		super.c2.selfMethod2();
	}
}
```
## 解释器模式
给定一个语言, 定义它的文法的一种表示，并定义一个解释器，该解释器使用该表示来解释语言中的句子
```
public abstract class Expression {
	//解析公式和数值,其中var中的key值是是公式中的参数，value值是具体的数字
	public abstract int interpreter(HashMap<String,Integer> var);
}
```
```
public abstract class SymbolExpression extends Expression {
	protected Expression left;
	protected Expression right;
	//所有的解析公式都应只关心自己左右两个表达式的结果
	public SymbolExpression(Expression _left,Expression _right){
		this.left = _left;
		this.right = _right;
	}
}
```
```
public class AddExpression extends SymbolExpression {
	public AddExpression(Expression _left,Expression _right){
		super(_left,_right);
	}
	//把左右两个表达式运算的结果加起来
	public int interpreter(HashMap<String, Integer> var) {
		return super.left.interpreter(var) + super.right.interpreter(var);
	}
}
```
