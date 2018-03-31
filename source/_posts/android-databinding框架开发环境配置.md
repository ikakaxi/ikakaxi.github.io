title: android databinding框架开发环境配置
author: superman
date: 2018-02-07 18:17:19
categories:
- android
tags:
- 第三方框架
---

这几天学dagger2,无意中看到Data Binding Library这么个东西,Data Binding 框架如果能够推广开来，也许 **RoboGuice、ButterKnife** 这样的依赖注入框架会慢慢失去市场，因为在 Java 代码中直接使用 View变量的情况会越来越少。网上有很多配置databinding的方法,有些是错误的,所以在此记录一下
<!--more-->

    配置databinding
如果直接在xml使用@{}肯定会出错的,网上很多只说了怎么用,直接用@{}肯定会报错,我们首先要配置databinding,方法如下:

**现在只需在 gradle 中加入 databinding 就可以使用了，之前的 plugin 和 classpath 都不需要了，现在的 databinding 作为 support lib 存在，所以使用之前需要去 SDK Manager 中更新 support 包。：**
```
android {
  ...
  dataBinding {
    enabled = true
  }
  ...
}
```

然后我们就可以使用Data Binding了,具体使用方法网上有很多,我就不再赘述了