title: android中判断服务或者进程是否存在
author: superman
date: 2018-02-07 22:31:42
categories:
- android
tags:
- API
- Service
---

1.判断进程是否存在
<!--more-->
```
	/**
	 * 判断是否在主进程,这个方法判断进程名或者pid都可以,如果进程名一样那pid肯定也一样
	 *
	 * @return true:当前进程是主进程 false:当前进程不是主进程
	 */
	public boolean isUIProcess() {
		ActivityManager am = ((ActivityManager) getSystemService(Context.ACTIVITY_SERVICE));
		List<ActivityManager.RunningAppProcessInfo> processInfos = am.getRunningAppProcesses();
		String mainProcessName = getPackageName();
		int myPid = android.os.Process.myPid();
		for (ActivityManager.RunningAppProcessInfo info : processInfos) {
			if (info.pid == myPid && mainProcessName.equals(info.processName)) {
				return true;
			}
		}
		return false;
	}
```

2.判断服务是否存在
```
	/**
	 * 判断service是否已经运行
	 * 必须判断uid,因为可能有重名的Service,所以要找自己程序的Service,不同进程只要是同一个程序就是同一个uid,个人理解android系统中一个程序就是一个用户
	 * 用pid替换uid进行判断强烈不建议,因为如果是远程Service的话,主进程的pid和远程Service的pid不是一个值,在主进程调用该方法会导致Service即使已经运行也会认为没有运行
	 * 如果Service和主进程是一个进程的话,用pid不会出错,但是这种方法强烈不建议,如果你后来把Service改成了远程Service,这时候判断就出错了
	 *
	 * @param className Service的全名,例如PushService.class.getName()
	 * @return true:Service已运行 false:Service未运行
	 */
	public boolean isServiceExisted(String className) {
		ActivityManager am = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
		List<ActivityManager.RunningServiceInfo> serviceList = am.getRunningServices(Integer.MAX_VALUE);
		int myUid = android.os.Process.myUid();
		for (ActivityManager.RunningServiceInfo runningServiceInfo : serviceList) {
			if (runningServiceInfo.uid == myUid && runningServiceInfo.service.getClassName().equals(className)) {
				return true;
			}
		}
		return false;
	}
```
---
#注意:
上面判断Service是否存在,用的uid和Service的类全名,网上我查到的资料,全部是用的pid,在Service和主进程是一个进程的时候,pid没有问题,但是如果Service是远程Service,和主进程就不是一个进程了,这时候用pid和Service的类全名进行判断就会判断错误