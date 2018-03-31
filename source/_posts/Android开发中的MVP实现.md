---
title: Android开发中的MVP实现
date: 2018-02-07 17:07:35
categories:
- android
tags:
- 设计模式
---
最近想要重构代码，因为项目需要给几个学校使用，而每个学校的界面是有差别的，但是功能几乎一模一样，虽然用gradle的分支可以做到代码的差异，但是公共部分的代码逻辑也越来越多，所以想重构一下，最近比较火的MVP模式看了一下，觉得很合适，不过网上很多资料也是抄来抄去，有的还有错误，就想按照自己的理解把MVP模式描述一下，方便他人理解，先说一下大家最熟悉的MVC设计模式吧

<!-- more -->

![深度截图20161205163205.png](http://upload-images.jianshu.io/upload_images/545982-9f8a3ef4059afdbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


MVC模式解耦合了M层和V层，M层和V层通过C层来交互

然后看一下MVP，我下面的文字以I开头的是接口的意思(手打的图好累...)


![深度截图20161205160152.png](http://upload-images.jianshu.io/upload_images/545982-13e68eadd0829c53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


MVP和MVC最大的区别是P层代替了以前的C层，控制的不再是具体的实现而是接口，这样不管是多人开发还是频繁的UI更改，都不会影响P层，只要C和V层的接口不变，UI的改动只需要更改V层的实现而已，C层的实现都不需要改,这样代码就很清晰，而且方便测试，因为逻辑层和视图层完全分离了，下面用代码来说话，还是先来MVC模式的，用登录这个例子吧，android已经很好的把C层和V层分开了，我们写的xml相当于V层，Activity相当于C层

```
public interface ICallback{
  void receive(boolean success);
}
```
```
public class LoginModel{
  public void login(String name,String password,ICallback callback){
    WebApi.login(name,password,callback);
  }
}
```
```
public class LoginActivity extends Activity{
  private LoginModel mLoginModel;
  private EditText mUserNameEt;
  private EditText mPasswordEt;
  private Button mSubmitBtn;
  public void onCreate(......){
    mLoginModel = new LoginModel(...);
    mSubmitBtn.setOnClickListener(new OnClickListener(View view){
        mLoginModel.login(mUserNameEt.get...,mPasswordEt.get...,new ICallback(){
          public void receive(boolean success){
            if(success){
              startActivity(new Intent(this,MainActivity.class));
              finish();
            } else {
              Toast.makeText(this,"登录失败",Toast.LENGTH_SHORT).show();
            }
          }
        });
    });
  }
}
```

我们再来看一下用MVP模式怎么写,文章下面的评论有哥们说写个契约类,我查了一下,写个契约类是比较方便,所以下面的代码是我改过的
```
public interface ILoginContract {

  public interface ILoginModel{
    public void login(String name,String password,ICallback callback);
  }

  public interface ILoginPresenter{
    public void login(String name,String password);
  }

  public interface ILoginView{
    public void showDialog();
    public void dismissDialog();
    public void showToast(String message);
    public void navigateToMain();
  }
}
```
```
public class PresenterImpl implements ILoginPresenter,ICallback{
  private ILoginView mLoginView;
  private ILoginModel mLoginModel;

  public PresenterImpl(ILoginView loginView){
    this.mLoginView = loginView;
    this.mLoginModel = new LoginModelImpl();
  }
  
  public void login(String name,String password){
    if(isEmpty(name)||isEmpty(password)){
        this.mLoginView.showToast("用户名或密码不能为空");
        return;
    }
    this.mLoginModel.login(name,password,this);
  }

  public void receive(boolean success){
    if(success){
      this.mLoginView.navigateToMain();
    }else{
      this.mLoginView.showToast("登录失败");
    }
  }

  private boolean isEmpty(String text){
    return text==null||"".equals(text)?true:false;
  }
}
```
```
public class LoginActivity extends Activity implements ILoginView{
  private IPresenter mPresenter;
  private EditText mUserNameEt;
  private EditText mPasswordEt;
  private Button mSubmitBtn;
  public void onCreate(......){
    mPresenter = new PresenterImpl(this);
    mSubmitBtn.setOnClickListener(new OnClickListener(View view){
        mPresenter.login(mUserNameEt.getText().toString(),
                          mPasswordEt.getText().toString());
    });
  }

  public void showDialog(){
    //显示一个转圈的dialog;
  }

  public void dismissDialog(){
    //隐藏转圈的dialog;
  }

  public void showToast(String message){
    Toast.makeText(this,message,Toast.LENGTH_SHORT).show();
  }

  public void navigateToMain(){
    startActivity(new Intent(this,MainActivity.class));
    finish();
  }
}
```

MVP的类和代码确实要多一些，但是这些是非常值得的，可以看出现在的代码逻辑要比MVC模式写起来更清晰，代码测试也更简单，甚至可以在没有页面效果图只有功能的时候完成功能，UI的改变也不会影响任何的业务代码。

以上MVP设计模式是个人理解，如果有误请指正，谢谢，共同探讨，共同进步