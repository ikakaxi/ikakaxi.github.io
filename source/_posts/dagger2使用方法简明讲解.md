title: dagger2使用方法简明讲解
author: liuhc
tags:
  - 第三方框架
categories:
  - android
date: 2018-04-15 15:17:00
---
现在的公司项目用到了Dagger2，之前只是稍微了解一些，没有用过，然后查了查资料，整理如下，方便快速上手

#### 四个基本注解

1. **@Inject** 主要有两个作用，一个是使用在构造函数上，通过标记构造函数让Dagger2来使用（Dagger2通过Inject标记可以在需要这个类实例的时候来找到这个构造函数并把相关实例new出来）从而提供依赖，另一个作用就是标记在需要依赖的变量让Dagger2为其提供依赖。

   > **@Inject**注解的字段不能是private和protected的

2. **@Module** 用Module标注的类是专门用来提供依赖的。有的人可能有些疑惑，看了上面的@Inject，需要在构造函数上标记才能提供依赖，那么如果我们需要提供的类构造函数无法修改怎么办，比如一些jar包里的类，我们无法修改源码。这时候就需要使用Module了。Module可以给不能修改源码的类提供依赖，当然，能用Inject标注的通过Module也可以提供依赖。

   > 这里需要注意，Module和Inject这两个注解还是有区别的，@Inject使用在构造函数上的时候，这个构造函数有没有参数都可以，如果有参数的话这个Module也需要有其他Module或者@Inject构造函数提供实例，适合在提供该类自己的时候使用。但是如果用@Module的话，@Module注解的这个类需要有默认无参构造函数（显示隐式都可以），否则会报“”xxx must be set”。如果没有默认无参构造函数，就需要手动把这个Module的实例传入Component，一般在MVP模式里使用该方式，用来提供Activity实例给Presenter实例。
   >
   > 所以，如果该类只需要提供自己，建议直接使用@Inject函数，如果是用来提供其他类的实例，建议使用@Module的方式。

3. **@Provides** 用Provides来标注一个方法，该方法可以在需要提供依赖时被调用，从而把预先提供好的对象当做依赖给标注了@Inject的变量赋值。provides主要用于标注Module里的方法。

4. **@Component** 一般用来标注接口，被标注了Component的接口在编译时会产生相应的类的实例来作为提供依赖方和需要依赖方之间的桥梁，把相关依赖注入到其中。

#### 四个扩展注解

1. **@Qulifier** 这里有个概念，叫依赖迷失，就是在Module注解的类里，有2个Provides都提供某个类的实例，这时候不用**@Qulifier**注解的话Component会不知道用哪个实例，这时候就要使用**@Qulifier**，下面直接提供代码

   ```java
   @Qualifier
   @Retention(RetentionPolicy.RUNTIME)
   public @interface A {}
   ```

   ```java
   @Qualifier
   @Retention(RetentionPolicy.RUNTIME)
   public @interface B {}
   ```

   ```java
   @Module
   public class SimpleModule {

    @Provides
    @A
    Cooker provideCookerA(){
        return new Cooker("James","Espresso");
    }

    @Provides
    @B
    Cooker provideCookerB(){
        return new Cooker("Karry","Machiato");
    }
   }
   ```

   ```java
   public class ComplexMaker implements CoffeeMaker {
       Cooker cookerA;
       Cooker cookerB;

       @Inject
       public ComplexMaker(@A Cooker cookerA,@B Cooker cookerB){
           this.cookerA = cookerA;
           this.cookerB = cookerB;
       }
   }
   ```

2. **@Named** 和**@Qulifier**一样，并且**@Named**就是继承**@Qulifier**的，而且用起来比**@Qulifier**方便，示例代码如下：

   ```java
   @Module
   public class MainModule {

       @Provides
       @Named("red")
       public Cloth getRedCloth() {
           Cloth cloth = new Cloth();
           cloth.setColor("红色");
           return cloth;
       }

       @Provides
       @Named("blue")
       public Cloth getBlueCloth() {
           Cloth cloth = new Cloth();
           cloth.setColor("蓝色");
           return cloth;
       }

       @Provides
       public Clothes getClothes(@Named("blue") Cloth cloth){
           return new Clothes(cloth);
       }
   }
   ```

   ```java
   public class MainActivity extends AppCompatActivity {
    ...
    @Inject
    @Named("red")
    Cloth redCloth;
    @Inject
    @Named("blue")
    Cloth blueCloth;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        tv.setText("我现在有" + redCloth + "和" + blueCloth );
    }
   }
   ```

3. **@Scope** 局部单例，意思就是在被注入类里只有一个该类的实例，局部范围是啥，那就是它生命周期范围内。直接上代码

   ```java
   //PerActivity.java
   @Scope
   @Retention(RetentionPolicy.RUNTIME)
   public @interface PerActivity {}
   ```

   ```java
   //ActivityModule.java
   @Module
   public class ActivityModule {

    @Provides
    CoffeeShop provideCoffeeShop(){
        return CoffeeShop.getInstance();//一个普通的单例
    }

    /**
    * 直接在这里说结果，@PerActivity是用@Scope注解的，除了在这里注解还需要在用到该Module类的Component的类名上方也要注解，然后该实例在注入到某个类里的时候用同一个Component就会不管有几个字段都会只有一个实例。注意：如果用不同的Component实例的话仍然会新的CookerFactory实例，单例CookerFactory只存在一个Component实例里。所以叫局部单例。
    */
    @Provides
    @PerActivity
    CookerFactory provideCookerFactory(){
        return new CookerFactory();
    }

    @Provides
    CookerFactoryMulty provideCookerFactoryMulty(){
        return new CookerFactoryMulty();//非单例
    }
   }
   ```

   ```java
   //CoffeeShop.java
   public class CoffeeShop {
    private static CoffeeShop INSTANCE;

    private CoffeeShop(){
        Log.d("TAG","CoffeeShop New Instance");
    }

    public static CoffeeShop getInstance(){
        if(INSTANCE == null){
            INSTANCE = new CoffeeShop();
        }
        return INSTANCE;
    }
   }

   //CookerFactory.java
   public class CookerFactory {

    public CookerFactory(){
        Log.d("TAG","CookerFactory New Instance");
    }
   }

   //CookerFactoryMulty.java
   public class CookerFactoryMulty {

    public CookerFactoryMulty(){
        Log.d("TAG","CookerFactoryMulty New Instance");
    }
   }
   ```

   ```java
   //除了在Module的Provides方法里写上@Scope还需要在Component类名上方写上，这里自定义的@Scope名字叫PerActivity
   @PerActivity
   @Component(modules = {ActivityModule.class})
   public interface ActivityComponent {
   	void inject(MainActivity simpleActivity);
   }
   ```

   ```java
   public class MainActivity extends Activity {

    ActivityComponent activityComponent;

    @Inject
    CoffeeShop coffeeShop1;

    @Inject
    CoffeeShop coffeeShop2;

    @Inject
    CookerFactory cookerFactory1;

    @Inject
    CookerFactory cookerFactory2;

    @Inject
    CookerFactoryMulty cookerFactoryMulty1;

    @Inject
    CookerFactoryMulty cookerFactoryMulty2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        activityComponent = DaggerActivityComponent.builder()
            //下面这句话可以不写，因为Module有默认构造函数。如果Module的构造器里有参数，并且该参数不是注入进去的，就需要用类似下面的方法手动设置实例到Component中
                .activityModule(provideModule())
                .applicationComponent(MyApplication.getComponent()).build();
        activityComponent.inject(this);
        coffeeFactory.run();
    }
    
    private ActivityModule provideModule(){
        return new ActivityModule();
    }
   }
   ```

   运行结果

   ```
   07-11 16:53:27.978    1927-1927/? D/TAG﹕ CoffeeShop New Instance
   07-11 16:53:27.978    1927-1927/? D/TAG﹕ CookerFactory New Instance
   07-11 16:53:27.978    1927-1927/? D/TAG﹕ CookerFactoryMulty New Instance
   07-11 16:53:27.978    1927-1927/? D/TAG﹕ CookerFactoryMulty New Instance
   ```

4. **@Singleton** 该注解继承**@Scope**，用的时候区别就是不用去自定义**@Scope**了，比如上面定义**@PerActivity**的这步就不需要了，其他的用法和使用**@PerActivity**一模一样，也是在Component类名上面和Module的Provides方法里都写上注解。

> **注意注意注意：**再次提醒，局部单例是在同一个Component实例提供依赖的前提下才有效的，不同的Component实例只能通过Component依赖才能实现单例。也就是说，你虽然在两个Component接口上都添加了PerActivity注解或者Singleton注解，但是这两个Component提供依赖时是没有联系的，他们只能在各自的范围内实现单例
> **在@Inject标注的构造器上使用局部单例直接在类名上声明作用范围（类名上添加@Singleton或自定义Scope）**

#### 依赖：dependencies

Component依赖Component的情况下，两个Component的@Scope不能相同，否则会编译错误，为什么这么设计我还不是很清楚，有知道的小伙伴请告诉我谢谢。

依赖的示例代码如下：

```java
//Person.java
public class Person {
}

//PerActivity.java
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface PerActivity {}

//BaseModule.java
@Module
public class BaseModule {

   @Singleton
   @Provides
   public Person providePerson(){
      return new Person();
   }
}

//Module1.java
@Module
public class Module1 {

}

//Module2.java
@Module
public class Module2 {

}

//BaseComponent.java
@Singleton
@Component(modules = BaseModule.class)
public interface BaseComponent {
	public Person providePerson();
}

//Component1.java
@PerActivity
//@Singleton
//因为依赖（dependencies）的BaseComponent中用到了@Singleton，所以这个Component就不能再用了，否则会编译错误，为什么这么设计还不是很清楚
@Component(modules = Module1.class,dependencies = BaseComponent.class)
public interface Component1 {
	void inject(TestScopeActivity1 simpleActivity);
}

//Component2.java
@PerActivity
//@Singleton 为什么不能用？原理同上
@Component(modules = Module2.class,dependencies = BaseComponent.class)
public interface Component2 {
	void inject(TestScopeActivity2 simpleActivity);
}

//MyApplication.java
public class MyApplication extends Application {

	private static BaseComponent baseComponent;

	@Override
	public void onCreate() {
		super.onCreate();
		baseComponent = DaggerBaseComponent.builder().build();
	}

	public static BaseComponent getBaseComponent() {
		return baseComponent;
	}
}

//TestScopeActivity1.java
public class TestScopeActivity1 extends Activity {

	@Inject
	Person p = null;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		DaggerComponent1.builder().baseComponent(MyApplication.getBaseComponent()).build().inject(this);
		TextView textView = findViewById(R.id.textView);
		textView.setText(p.toString());
		textView.setOnClickListener(new View.OnClickListener() {
			@Override
			public void onClick(View v) {
				startActivity(new Intent(TestScopeActivity1.this,TestScopeActivity2.class));
			}
		});
	}
}

//TestScopeActivity2.java
public class TestScopeActivity2 extends Activity {

	@Inject
	Person p = null;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		DaggerComponent2.builder().baseComponent(MyApplication.getBaseComponent()).build().inject(this);
		TextView textView = findViewById(R.id.textView);
		textView.setText(p.toString());
	}
}
```

------

#### daggar2如何选择依赖呢，按照这样的顺序

当Component调用inject方法的时候，会搜索被注入类中用**@Inject**注解的字段，然后会在该Component中查找在**@Component**(modules=。。。)注解中注册的Module，如果搜索到Module有**@Provides**注解的方法提供该**@Inject**注解的字段所需的实例，就调用相应的方法完成注入，否则就查找所有用**@Inject**注解构造函数的类，如果找到就调用相应的构造函数完成注入，如果在获得实例的时候还需要获取参数的实例，再按照刚才的流程依次注入参数实例

画个简单的流程，如下所示

Component.inject->在Component搜索Module->找到就调用**@Provides**注解的方法提供实例

​				->没找到就搜索**@Inject**注解的构造函数

​				->都找不到就报错。。。

都没找到肯定就报错了。。。但是会优先寻找Component注册的Module，而**@Inject**注册的构造器可以调用任何Component的inject方法完成注入，因为**@Inject**注册的构造器不需要在Component里注册，这里和Module有区别，Module是需要在某个Component中注册的，而**@Inject**不需要

#### 注意：

如果一个字段需要被@Inject注入，那么这个字段的类型必须有@Inject构造函数或者有Module来提供它（还需要在Component添加inject方法或者添加module=[xxx:class]），如果@Inject一个父类，而子类的构造函数有@Inject标记是没用的，类型必须严格对应

------

举一个MVP中使用Dagger2的示例，我就不贴代码了，直接看下面这个链接好了：

https://www.jianshu.com/p/5a936942db2a

参考：

https://dreamerhome.github.io/2016/07/11/dagger%20for%20code/

https://blog.csdn.net/it_zouxiang/article/details/53471192