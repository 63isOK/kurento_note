# 介绍

这个库提供一个数据结构，可以存储任意深度嵌套的树，
在每个树叶上，用key进行索引

* 节点上有key来索引
* 节点上包含3部分：key value 和子节点列表(有序的)

用一个路径，可以访问任意节点，这个路径就是多个key的级联

第一个功能是转换： xml ini json < -- > tree

适合的场景是存储配置数据  
key 和 value可以是其他类型，不过一般都是string，转换功能也是基于string做的  

## 第一个功能点 - 读写

Boost.PropertyTree，提供的ds(data struct)是boost::property_tree::ptree

```c++
#include "boost/property_tree/ptree.hpp"
#include "boost/property_tree/xml_parser.hpp"

#include <string>
#include <set>
#include <exception>
#include <iostream>

namespace pt = boost::property_tree;

int main()
{
  std::string file;
  int level;
  std::set<std::string> module;

  const std::string config_path = "info.xml";

  try {
    // load
    {
      pt::ptree tree;
      pt::read_xml(config_path, tree);

      file = tree.get<std::string>("debug.filename");
      level = tree.get("debug.level", 0);
      for (auto& x : tree.get_child("debug.modules"))
        module.insert(x.second.data());

      std::cout << "filename:" << file << std::endl
        << "level:" << level << std::endl;

      for (auto& x : module)
        std::cout << x << std::endl;
    }

    // save
    {
      pt::ptree tree;

      tree.put("debug.filename", "abc");
      tree.put("debug.level", 12);
      tree.add("debug.module.module", "a");
      tree.add("debug.module.module", "b");
      tree.add("debug.module.module", "c");

      pt::write_xml("info2.xml", tree);
    }
  }
  catch (std::exception& e)
  {
    std::cout << "Error:" << e.what() << std::endl;
  }

  return 0;
}

```
编译：  
g++ -c -std=c++11 -g -I~/workspace/boost/boost_1_69_0  print_xml.cpp -o print_xml.o  
g++ -o print_xml print_xml.o -L~/workspace/boost/boost_1_69_0/stage/lib  

上例中展示了：
* 格式转换 xml和ptree
* 读数据：string int和子节点遍历
* 写数据：新增子节点

## 细说属性树

节点按key查找，遍历只能遍历子节点，节点不是有序的。  
可以从所有子节点生成一个试图，可以试图进行查操作。

key有可能是重复的

## 访问数据的3中方式

* 抛异常的版本
  找不到就抛异常
```c++
ptree pt;
float v = pt.get<float>("a.path.to.float.value");
```
* 带默认值版本
  找不到就使用默认值
```c++
ptree pt;
float v = pt.get("a.path.to.float.value", -1.f);
```
* 可选版本
  返回boost::optional
```c++
ptree pt;
boost::optional<float> v = pt.get_optional<float>("a.path.to.float.value");
```
这个版本用boost::optional来处理异常



