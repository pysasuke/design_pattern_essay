# 创建型模式

## 工厂方法模式

定义一个用于创建对象的接口，让子类决定实例化哪一个类。工厂方法使一个类的实例化延迟到其子类
自我总结：更具不同条件创建不同实例
```
  //定义一个生产超人的工厂
	public static ISuperMan createSuperMan(String type){
		//根据输入参数产生不同的超人
		if(type.equalsIgnoreCase("adult")){
			//生产成人超人
			return new AdultSuperMan();
		}else if(type.equalsIgnoreCase("child")){
			//生产小超人
			return new ChildSuperMan();
		}else{
			return null;
		}
	}
```

## 抽象工厂模式

为创建一组相关或相互依赖的对象提供一个接口，而且无需指定它们的具体类
自我总结：工厂为接口，具体实例创建由工厂实现类实现
```
public interface CarFactory {
	//生产SUV
	public ICar createSuv();
	//生产商务车
	public ICar createVan();
}
```
```
public class BenzFactory implements CarFactory {
	//生产SUV
	public ICar createSuv() {
		return new BenzSuv();
	}
	//生产商务车
	public ICar createVan(){
		return new BenzVan();
	}
}
```
```
public class BMWFactory implements CarFactory {
	//生产SUV
	public ICar createSuv() {
		return new BMWSuv();
	}
	//生产商务车
	public ICar createVan(){
		return new BMWVan();
	}
}
```

## 建造者模式

将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示
自我总结：建造者抽象类定义对象创建过程，具体的过程实现由不同子类完成，以达到不同实例的创建
```
public abstract class Builder {
	//定义一个超人的应用
	protected final SuperMan superMan = new SuperMan();
	//构建出超人的躯体
	public void setBody(String body){
		this.superMan.setBody(body);
	}
	//构建出超人的特殊技能
	public void setSpecialTalent(String st){
		this.superMan.setSpecialTalent(st);
	}
	//构建出超人的特殊标记
	public void setSpecialSymbol(String ss){
		this.superMan.setSpecialSymbol(ss);
	}
	//构建出完整的一个超人
	public abstract SuperMan getSuperMan();
}
```
```
public class AdultSuperManBuilder extends Builder {
	@Override
	public SuperMan getSuperMan() {
		super.setBody("强壮的躯体");
		super.setSpecialTalent("会飞行");
		super.setSpecialSymbol("胸前带S标记");
		return super.superMan;
	}
}
```
```
public class ChildSuperManBuilder extends Builder {
	@Override
	public SuperMan getSuperMan() {
		super.setBody("强壮的躯体");
		super.setSpecialTalent("刀枪不入");
		super.setSpecialSymbol("胸前带小S标记");
		return super.superMan;
	}
}
```
## 单例模式
确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例
自我总结：构造函数私有化，提供一个静态方法来获取唯一实例
```
public final class Singleton {
	private static Singleton singleton = null;
	//限制产生多个对象
	private Singleton(){
	}
	//通过该方法获得实例对象
	public synchronized static Singleton getSingleton(){
		if(singleton == null){
			singleton = new Singleton();//懒汉式
		}
		return singleton;		
	}
}
```
## 原型模式
用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象
自我总结：使用原型模式需要实现Cloneable接口，并重写clone()方法
```
public class Thing implements Cloneable{
	public Thing(){
		System.out.println("构造函数被执行了...");
	}
	@Override
	public Thing clone(){
		Thing thing=null;
		try {
			thing = (Thing)super.clone();
		} catch (CloneNotSupportedException e) {
			e.printStackTrace();
		}
		return thing;
	}
}
```
```
public class Client {
	public static void main(String[] args) {
		//产生一个对象
		Thing thing = new Thing();//构造函数被执行了...
		//拷贝一个对象
		Thing cloneThing = thing.clone();
	}
}
```
