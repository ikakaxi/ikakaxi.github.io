title: ' AndroidManifest.xml文件详解（service）'
author: superman
date: 2018-02-07 22:11:19
categories:
- android
tags:
- AndroidManifest
---

语法（SYNTAX）：
<!--more-->
```
<service
        android:enabled=["true" | "false"]
        android:exported["true" | "false"]         
        android:icon="drawable resource"
        android:label="string resource"
        android:name="string" 
        android:permission="string"         
        android:process="string">
</service>
```
**被包含于（CONTAINED IN）：**
<application>
**可能包含的元素（CAN CONTAIN）：**
<intent-filter>
<meta-data>

**说明（DESCRIPTION）：**
这个元素用于声明一个服务（Service类的子类）作为应用程序的组件之一。跟Activity不一样，服务没有可见的用户界面。它们被用于实现长时的后台操作，或提供能够被其他应用程序调用的通信API。
所有的服务都必须用清单文件中的<service>元素来代表。任何没有在清单中声明的服务都不能被系统看到，也不会被运行。

**属性（ATTRIBUTES）：**

**android:enabled**
这个属性用于指示该服务是否能够被实例化。如果设置为true，则能够被实例化，否则不能被实例化。默认值是true。
<application>元素有它自己的enabled属性，它的这个属性适用于应用中所有的组件，包括service组件。对于被启用的服务，<application>和<service>元素的enabled属性都必须是true（默认值都是true）。如果有一个元素的enabled属性被设置为false，该服务就会被禁用，而不能被实例化。

**android:exported**
这个属性用于指示该服务是否能够被其他应用程序组件调用或跟它交互。如果设置为true，则能够被调用或交互，否则不能。设置为false时，只有同一个应用程序的组件或带有相同用户ID的应用程序才能启动或绑定该服务。
它的默认值依赖与该服务所包含的过滤器。没有过滤器则意味着该服务只能通过指定明确的类名来调用，这样就是说该服务只能在应用程序的内部使用（因为其他外部使用者不会知道该服务的类名），因此这种情况下，这个属性的默认值是false。另一方面，如果至少包含了一个过滤器，则意味着该服务可以给外部的其他应用提供服务，因此默认值是true。
这个属性不是限制把服务暴露给其他应用程序的唯一方法。还可以使用权限来限制能够跟该服务交互的外部实体。

**android:icon**
这个属性定义了一个代表服务的图标，它必须要引用一个包含图片定义的可绘制资源。如果这个属性没有设置，则会使用<application>元素的icon属性所设定的图标来代替。
无论是<application>元素设置的图标，还是<service>元素所设置的图标，它们都是该服务所有的Intent过滤器的默认图标。

**android:label**
这个属性用于设定一个要显示给用户的服务的名称。如果没有设置这个属性，则会使用<application>元素的label属性值来代替。
无论是<service>设定的标签，还是<application>元素设定的标签，它们都是该服务所有的Intent过滤器的默认标签。
这个标签应用引用一个字符串资源，以便它能够像用户界面中的字符串一样能够被本地化。但是，为了开发应用程序方便，也可以使用原生字符串来设置这个属性。

**android:name**
这个属性用于指定实现该服务的Service子类的类名。它应该是完整的Java类名（如：com.example.project.RoomService）。但是，也可以使用简写（如：.RoomService），系统会把<manifest>元素中package属性所设定的值添加到简写名称的前面。
一旦发布了应用程序，就不应该改变这个名称（除非android:exported=”false”）。
这个属性没有默认值，名称必须要指定。

**android:permission**
这个属性定义了要启动或绑定服务的实体必须要有的权限。如果调用者的startService()、bindService()和stopService()方法没有被授予这个权限，那么这些方法就不会工作，并且Intent对象也不会发送给改服务。
如果这个属性没被设置，那么通过<appliction>元素的permission属性所设定的权限就会适用于该服务。如果<application>元素也没有设置权限，则该服务不受权限保护。

**android:process**
这个属性用于设定服务所运行的进程名称。通常，应用程序的所有组件都运行在给应用程序创建的进程中，进程名与应用程序的包名相同。<application>元素的process属性能够给应用程序的所有组件设置一个不同的默认名称。但是每个组件自己的process属性都能够覆盖这个默认值，这样允许把应用程序分离到不同的多个进程中。
如果这个属性值用“:”开头，则在需要的时候系统会创建一个新的，应用程序私有的进程，并且该服务也会运行在这个进程中。如果这个属性值用小写字母开头，那该服务就会运行在以这个属性值命名的全局进程中，它提供了使其工作的权限。这样就允许不同的应用程序组件来共享这个进程，从而降低资源的使用。

**被引入的版本（INTRODUCED IN）：**
API Level 1