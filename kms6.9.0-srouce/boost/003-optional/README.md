# 介绍

Boost.Optional

optional是一个模板类，用于表示可选对象(可能为空的对象，也就是包含无效值)

optional对象提供了full value 语义，适合传值，和使用在stl容器内部，
只需要头文件的库

## 充当返回值

```c++
  boost::optional<int> oi = xxx;
  if (oi){}

  int a = *oi;

```

表示返回值可以是int，或是无效值，
在if等判断的上下文中，boost::optional会转换成bool类型，
未初始化的optional对象，用*来取值，行为是未定义的，
这时可以使用BOOST_ASSERT

有三种获取值的方式：
* if (boost::optional<T> i = xx) {T a = *i;}
* T a = *i;   // 只有确保i有值时才能这样处理
* try {T a = *i;} catch (const boost::bad_optional_access&) {}

如果有还有默认值的存在(如果值无效就取个默认值,类似ini的处理方式)：
* T a = i.value_or(xxx);  // 无值就取xxx

如果不要默认值，也可以设置一个回调来处理：
* T a = i.value_or_eval(function point);

值无效时，optional的值是boost::none

## 延时负载优化

lazy load optimization

充当成员变量，在使用时才进行初始化

为了避免一些默认构造执行，也可以用optional来实现

