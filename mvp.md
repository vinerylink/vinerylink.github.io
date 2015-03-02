---
layout: page
title: Android MVP模式
---

![Android Model-View_Presenter Pattern](/assets/test.jpg)

无规矩不成方圆。[GUI Architectures](http://martinfowler.com/eaaDev/uiArchs.html) by Martin Fowler.

[M — Model from MVC/MVP in Android (Flow and Mortar)](https://medium.com/@artem_zin/m-model-from-mvc-mvp-in-android-flow-and-mortar-bd1e50c45395)推荐一篇好文，[An Investigation into Flow and Mortar](http://www.bignerdranch.com/blog/an-investigation-into-flow-and-mortar/) 以及他自己写过的一篇，[Advocating Against Android Fragments](http://corner.squareup.com/2014/10/advocating-against-android-fragments.html)

[Architecting Android…The clean way?](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/) We know that writing quality software is hard and complex: It is not only about satisfying requirements, also should be robust, maintainable, testable, and flexible enough to adapt to growth and change. This is where “the clean architecture” comes up and could be a good approach for using when developing any software application. [源码](https://github.com/android10/Android-CleanArchitecture)


[Model-View-Presenter in Android Intro](http://corneliudascalu.com/model-view-presenter-in-android/) note for [MVP for Android: how to organize the presentation layer](http://antonioleiva.com/mvp-android/) [译文：MVP for Android :如何组织“表现层”](http://www.iseekfree.com/Mvp-For-Android/) which take github sample [androidmvp](https://github.com/antoniolg/androidmvp), which implement an login activity mvp


## Inections vs Android Annotations

@Bean(className) Interface var;
@ViewById(R.id.btn_back) Class var;

@UiThread public void foo();
@Background public void foo();

@EBean public class clsName implement Interface {};

@ViewInterface Interface var;


## Reference
[An MVP Pattern for Android](http://magenic.com/BlogArchive/AnMVPPatternforAndroid)

[对 MVC 和 MVP 模式的疑惑](http://segmentfault.com/q/1010000000336459) 有个回答MVC向MVP进化史

[MVP Clean Architecture](https://android-arsenal.com/details/3/1210) 加精。 A Github sample project using Clean architecture and MVP.

[MVP(SC),MVP(PV),PM,MVVM 和 MVC 表现模式架构对比](http://www.cnblogs.com/beginor/archive/2010/03/20/1690711.html) 加精

[原文](http://www.codeproject.com/Articles/66585/Comparison-of-Architecture-presentation-patterns-M) Comparison of Architecture presentation patterns MVP(SC),MVP(PV),PM,MVVM and MVC

[开源项目Philm的MVP架构分析](http://www.lightskystreet.com/2015/02/10/philm_mvp/) 加精。

ChrisBannes的开源项目Philm，其整体架构是一套MVP的实现，因为自己也确实没有遇到过整个项目利用MVP搭建的架构，看到的更多是一些代码片段，这里就探讨Philm是如何结合Android实际问题来实现一种MVP架构。

[扩展AndroidAnnotations使用MVP模式编程](http://www.w2bc.com/Article/22247) 代码片段，Activity实现操作View的接口，（一个View可以有多个业务逻辑的P），P实现业务逻辑接口ITestActions, 注入View,Fragment,或者Activity(View接口的实现) ......

[Apply MVP Pattern for Android](http://icetea09.com/blog/2014/11/10/android-apply-mvp-pattern-for-android/) 代码demo

[Android-MVP-Decoupling](https://github.com/BlueMor/Android-MVP-Decoupling) sample project, 使用MVP设计模式使UI和业务(Interactor)完全解耦

[EasyMVP](https://github.com/JorgeCastilloPrz/EasyMVP) sample project, EasyMVP是一个实现MVP(Model-View-Presenter)模式的项目，另外还实现了Dagger scoping和兼容Material。

[介绍开源Presentation Model/MVVM/MVP框架 RoboBinding](http://www.apkbus.com/android-170527-1-1.html) 框架简介。 RoboBinding是一个实现Martin Fowler的Presentation Model构架模式的Android框架。 目标为减少android应用开发工作量；促进清晰的项目结构；使项目代码更易于测试。在Java,.Net等成熟的语言环境下都有对应的Presentation Model(MVVM/MVP微软社区的叫法)实现框架，之前由于Android刚开始，所以框架还比较少，我们就开发了这个框架(我是项目的开发者之一)。现在类似的框架慢慢的出现：[Android-Binding](https://github.com/gueei/AndroidBinding)， [Bindroid](https://github.com/depoll/bindroid)，[MvvmCross](https://github.com/MvvmCross/MvvmCross)。有兴趣的朋友可以了解一下。 [Youtube视频](Android MVP MVVM Presentation Model模式与基于RoboBinding应用)

[Android Common Design Patterns (MVC, MVVM, MVP)](http://www.elisecarmichael.com/2013/11/android-common-design-patterns-mvc-mvvm.html)

[Android中单元测试 Robolectric + MVP模式](http://www.xuebuyuan.com/2150943.html) 测试相关 TDD

[Android MVP 模式](http://www.shaojie.name/blog/2014/03/24/android-mvp/) 模式介绍及测试示例

[The Model-View-Presenter pattern](http://www.manning.com/sessa/50AH_Sample05.pdf) explain about splash screen with MVP

[Android Development - Harnessing Powers of MVP](http://blog.nilenso.com/blog/2013/09/10/android-native-mvp/) an login activity sample with unit test code, depend in IntelliJ Idea CE, JUnit, Robolectric, RoboGuice, Mockito, FEST

[Android MVP设计框架模板 之 漂亮ListView上拉刷新下拉加载更多，androidlistview](http://www.bkjia.com/Androidjc/943166.html) 高内聚，低耦合的用MVP的设计框架模板来实现一个ListView的上拉刷新下拉加载更多的功能。 代码demo

[Android中的MVP](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0227/2503.html) [代码demo](https://github.com/zhengxiaopeng/Rocko-Android-Demos/tree/master/android-mvp), 实现天气查询.

[Resources for Learning Dagger for Android](http://gregloesch.com/dev/2014/10/19/resources-for-learning-dagger-for-android.html)

[The Clean Architecture](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)

[Repository Pattern](http://martinfowler.com/eaaCatalog/repository.html)

[Domain Driven Design](http://martinfowler.com/eaaCatalog/repository.html) 

[Uncle Bob: Architecture is About Intent, not Frameworks](http://www.infoq.com/news/2013/07/architecture_intent_frameworks)

[Model–view–presenter WIKI](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter)



[50个Android开发技艺(20 使用MVP模式)](http://blog.csdn.net/vector_yi/article/details/24719873) 
### 一、MVP介绍
     随着UI创建技术的功能日益增强，UI层也履行着越来越多的职责。为了更好地细分视图(View)与模型(Model)的功能，让View专注于处理数据的可视化以及与用户的交互，同时让Model只关系数据的处理，基于MVC概念的MVP(Model-View-Presenter)模式应运而生。
     在MVP模式里通常包含4个要素：
     (1) View:负责绘制UI元素、与用户进行交互(在Android中体现为Activity);
     (2) View interface:需要View实现的接口，View通过View interface与Presenter进行交互，降低耦合，方便进行单元测试;
     (3) Model:负责存储、检索、操纵数据(有时也实现一个Model interface用来降低耦合);
     (4) Presenter:作为View与Model交互的中间纽带，处理与用户交互的负责逻辑。
(原文地址：http://blog.csdn.net/vector_yi/article/details/24719873) 

### 二、为什么使用MVP模式
     在Android开发中，Activity并不是一个标准的MVC模式中的Controller， 它的首要职责是加载应用的布局和初始化用户界面，并接受并处理来自用户的操作请求，进而作出响应。随着界面及其逻辑的复杂度不断提升，Activity类的职责不断增加，以致变得庞大臃肿。当我们将其中复杂的逻辑处理移至另外的一个类（Presneter）中时，Activity其实就是MVP模式中View，它负责UI元素的初始化，建立UI元素与Presenter的关联（Listener之类），同时自己也会处理一些简单的逻辑（复杂的逻辑交由Presenter处理）.
     另外，回想一下你在开发Android应用时是如何对代码逻辑进行单元测试的？是否每次都要将应用部署到Android模拟器或真机上，然后通过模拟用户操作进行测试？然而由于Android平台的特性，每次部署都耗费了大量的时间，这直接导致开发效率的降低。而在MVP模式中，处理复杂逻辑的Presenter是通过interface与View(Activity)进行交互的，这说明了什么？说明我们可以通过自定义类实现这个interface来模拟Activity的行为对Presenter进行单元测试，省去了大量的部署及测试的时间。

### 三、MVP与MVC的异同
     MVC模式与MVP模式都作为用来分离UI层与业务层的一种开发模式被应用了很多年。在我们选择一种开发模式时，首先需要了解一下这种模式的利弊：
     无论MVC或是MVP模式都不可避免地存在一个弊端：
          额外的代码复杂度及学习成本。
     这就导致了这两种开发模式也许并不是很小型应用。
     但比起他们的优点，这点弊端基本可以忽略了：
     (1)降低耦合度
     (2)模块职责划分明显
     (3)利于测试驱动开发
     (4)代码复用
     (5)隐藏数据
     (6)代码灵活性

     对于MVP与MVC这两种模式，它们之间也有很大的差异。有一些程序员选择不使用任何一种模式，有一部分原因也许就是不能区分这两种模式差异。以下是这两种模式之间最关键的差异：
     (参考文章：http://www.infragistics.com/community/blogs/todd_snyder/archive/2007/10/17/mvc-or-mvp-pattern-whats-the-difference.aspx)     
     MVP模式：
     View不直接与Model交互，而是通过与Presenter交互来与Model间接交互
     Presenter与View的交互是通过接口来进行的，更有利于添加单元测试
     通常View与Presenter是一对一的，但复杂的View可能绑定多个Presenter来处理逻辑     
     MVC模式：
     View可以与Model直接交互
     Controller是基于行为的，并且可以被多个View共享
     可以负责决定显示哪个View

### 四、利用MVP进行Android开发的例子
     说了这么多理论，现在轮到实践了。
     现在我们来实现这样一个Android上的Demo(如图)：可以从EditText读取用户信息并存取，也可以根据ID来从后台读出用户信息并显示。 
 
页面布局很简单，就不介绍了。下面根据MVP原则来进行编码：
先来看看java文件的目录结构：
 
可以发现，Presenter与Model、View都是通过接口来进行交互的，既降低耦合也方便进行单元测试。 

(1)首先我们需要一个UserBean，用来保存用户信息

    public class UserBean {
       private String mFirstName ;
       private String mLastName ;
       public UserBean (String firstName, String lastName) {
             this .mFirstName = firstName;
             this .mLastName = lastName;
       }
       public String getFirstName() {
             return mFirstName ;
       }
       public String getLastName() {
             return mLastName ;
       }
    }

(2)再来看看View接口：

     根据需求可知，View可以对ID、FirstName、LastName这三个EditText进行读操作，对FirstName和LastName进行写操作，由此定义IUserView接口：
    public interface IUserView {
       int getID();
       String getFristName();
       String getLastName();
       void setFirstName (String firstName);
       void setLastName (String lastName);
    }

(3)Model接口：

     同样，Model也需要对这三个字段进行读写操作，并存储在某个载体内(这不是我们所关心的，可以存在内存、文件、数据库或者远程服务器，但对于Presenter及View无影响),定义IUserModel接口：
    public interface IUserModel {
       void setID (int id);
       void setFirstName (String firstName);
       void setLastName (String lastName);
       int getID();
       UserBean load (int id);//通过id读取user信息,返回一个UserBean
    }

(4)Presenter:

     至此，Presenter就能通过接口与View及Model进行交互了：
       public class UserPresenter {
       private IUserView mUserView ;
       private IUserModel mUserModel ;

       public UserPresenter (IUserView view) {
             mUserView = view;
             mUserModel = new UserModel ();
       }

       public void saveUser( int id , String firstName , String lastName) {
             mUserModel .setID (id );
             mUserModel .setFirstName (firstName );
             mUserModel .setLastName (lastName );
       }

       public void loadUser( int id ) {
             UserBean user = mUserModel .load (id );
             mUserrView .setFirstName (user .getFirstName ());//通过调用IUserView的方法来更新显示
             mUserView .setLastName (user .getLastName ());
       }
    }
    
(5)UserActivity:

     UserActivity实现了IUserView及View.OnClickListener接口，同时有一个UserPresenter成员变量：
     public class UserActivity extends Activity implements OnClickListener ,
             IUserView {

       private EditText mFirstNameEditText , mLastNameEditText , mIdEditText ;
       private Button mSaveButton , mLoadButton ;
       private UserPresenter mUserPresenter ;

    重写了OnClick方法：
     @Override
       public void onClick(View v) {
             // TODO Auto-generated method stub
             switch ( v. getId()) {
             case R .id .saveButton :
                   mUserPresenter .saveUser (getID (), getFristName (),
                               getLastName ());
                   break ;
             case R .id .loadButton :
                   mUserPresenter .loadUser (getID ());
                   break ;
             default :
                   break ;
             }
       }

    可以看到，View只负责处理与用户进行交互，并把数据相关的逻辑操作都扔给了Presenter去做。而Presenter调用Model处理完数据之后，再通过IUserView更新View显示的信息。

View剩下的方法及UserModel类不是我们所关心重点，如果有兴趣可以在源码中查看。
[源码在这](http://download.csdn.net/detail/fwy19930618/7270921)