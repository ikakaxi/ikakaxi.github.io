title: 'Calendar的DAY_OF_MONTH, DAY_OF_YEAR, DATE的区别'
author: superman
date: 2018-02-07 22:27:01
categories:
- android
tags:
- API
---

```
cal1.add(Calendar.DAY_OF_MONTH,1);  
cal1.add(Calendar.DAY_OF_YEAR,1);  
cal1.add(Calendar.DATE,1); 
```
<!--more-->
就单纯的add操作结果都一样，因为都是将日期+1 
就没有区别说是在月的日期中加1还是年的日期中加1 
但是Calendar设置DAY_OF_MONTH和DAY_OF_YEAR的目的不是用来+1 
将日期加1，这通过cal1.add(Calendar.DATE,1)就可以实现 
DAY_OF_MONTH的主要作用是cal.get(DAY_OF_MONTH)，用来获得这一天在是这个月的第多少天 
Calendar.DAY_OF_YEAR的主要作用是cal.get(DAY_OF_YEAR)，用来获得这一天在是这个年的第多少天。 
同样，还有DAY_OF_WEEK，用来获得当前日期是一周的第几天