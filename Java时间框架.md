Java时间架构
---
**时间架构图：**

![](https://i.imgur.com/3OyyaLO.jpg)

* **milliseconds 表示毫秒**

 milliseconds = “实际时间” - “1970-01-01 00:00:00”

* **Locale 表示区域**

* **TimeZone 表示时区**

* **Calendar 表示日期/时间**

使用 Calendar 类来操作“年月日时分秒”

Calendar是一个抽象类，Calendar.getInstance() 获取Calendar实例时，实际上返回的是 GregorianCalendar 对象

* **Date 表示日期/时间**

相应方法已经被废除

* **DateFormat是格式化/解析“日期/时间”的工具类**

通过 DateFormat 类来格式化和解析日期字符串，将Date转换成我们想要的String字符串

DateFormat是一个抽象类，getInstance(), getDateInstance()和 getDateTimeInstance() 等获取DateFormat实例时，实际上是返回的SimpleDateFormat对象


---

### Calendar ###

获取Calendar 实例的方法：

1. **Calendar.getInstance()** 返回的一个GregorianCalendar对象，GregorianCalendar是Calendar的一个实现类，它提供了世界上大多数国家/地区使用的标准日历系统。

2. **Calendar.getInstance(TimeZone timezone, Locale locale)** 是返回“对应时区(zone) 或 地区(local)等所使用的日历”