# 双重检测单例模式笔记

最近用静态代码扫描工具检查代码，提示一段第三方平台代码中存在数据冲突隐患，看了下问题所在代码，就是一段典型的双重检测单例模式，大概如下：

```
class Singleton {

	private static Singleton instance;
	
	private Singleton(){
		//...
	}
	
	public static Singleton getInstance(){
		if(instance == null){  //1
			synchronized(Singleton.class){
				if(instance == null){
					instance = new Singleton();   //2
				}
			}
		}
		return instance; //3
	}
}
```
奇怪，这段代码不是非常经典的双重检测单例模式呢，而且代码来自第三方平台，这个会有什么问题呢？会不会是代码扫描工具误报了？

在网上搜了下，终于搞明白了由于JAVA内存模型的乱序处理局限，这种双重检测单例模式真的可能出现小概率单例失效问题，非常隐蔽，记录原因如下：
在 //2 这行代码上，实际上有三个执行步骤：

1、分配Singleton对象所需的内存空间。

2、通过Singleton构造函数对上面的内存空间进行初始化

3、将分配内存空间的地址赋值给instance 引用。

由于虚拟机认为上面的2、3步不存在前后依赖关系（注：1、2步是存在的），所以有可能变成1、3、2步来执行。

试想：在多线程环境下，如果首先执行的线程A在完成了1、3步后，被执行到 //1 处的线程B抢占，这个时候if（instance == null）会判false直接跳转到 //3 处return,将这个引用交给外面的getInstance调用方，而由于第2步还未执行完成，外面拿到的引用对象实际上还未完全初始化，导致运行时出现未知异常。

针对这个问题，解决方案有下面几种：
1、继续沿用双重检测单例模式的写法，给instance加上volatile修饰，保证其不被乱序执行，即 

```
class Singleton {

	private static volatile Singleton instance;
	
	private Singleton(){
		//...
	}
	
	public static Singleton getInstance(){
		if(instance == null){ 
			synchronized(Singleton.class){
				if(instance == null){
					instance = new Singleton();  
				}
			}
		}
		return instance;
	}
}

```

2、静态初始化单例模式，这种写法有个缺陷就是当还未实际需要调用到getInstance方法时，单例对象就可能被实例化了，浪费了内存空间

```
class Singleton {

	private static Singleton instance = new Singleton();
	
	private Singleton(){
		//...
	}
	
	public static Singleton getInstance(){
		return instance;
	}
}

```

3、静态内部类包裹单例模式，这种写法既有和双重检测一样的使用时才实例化的优点，也避免了双重检测的乱序执行缺陷，不需要用synchronized和volatile进行同步控制，值得推荐：

```
class Singleton {

	static class SingletonHolder{
	//JAVA语言规范保证了加载类时，对其静态成员初始化在单线程执行完成前不会进行线程切换，从而保证了实例的唯一性
		public static Singleton instance = new Singleton();  
	}
	
	private Singleton(){
		//...
	}
	
	//在调用到getInstance方法后才会加载SingletonHolder这个静态内部类
	public static Singleton getInstance(){
		return SingletonHolder.instance;
	}
}

```



