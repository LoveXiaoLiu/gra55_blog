+++
title = "设计模式 # 创建型 # 原型模式"
description = "创建型设计模式之一，适用于创建新对象成本比较高的场景，通过 clone 现有对象来创建新对象，以此降低成本"
author = "gra55"
categories = ["架构"]
tags = ["设计模式", "创建型设计模式", "原型模式", "2020"]
date = "2020-01-15"
+++

# 0x00 模式概述

原型模式用于创建重复的对象，该重复对象的创建成本比较高，所以原型模式通过 clone 现有对象来完成。

# 0x01 场景

适用于创建实例成本比较高的场景，eg：创建对象时需要先连接数据库，这个成本就很高，原型模式很适合。

# 0x02 解决方案

```php
// 克隆羊多利
class Sheep
{
    protected $name;
    protected $category;

    public function __construct(string $name, string $category = 'Mountain Sheep')
    {
        $this->name = $name;
        $this->category = $category;
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function setCategory(string $category)
    {
        $this->category = $category;
    }

    public function getCategory()
    {
        return $this->category;
    }
}

// client
$original = new Sheep('Jolly');
echo $original->getName(); // Jolly
echo $original->getCategory(); // Mountain Sheep

// Clone and modify what is required
$cloned = clone $original;
$cloned->setName('Dolly');
echo $cloned->getName(); // Dolly
echo $cloned->getCategory(); // Mountain sheep
```

# 0x03 总结

这个模式很简单，当创建的成本比较高时，就可以考虑使用 clone 的方式（原型模式）。

---
参考：

:pushpin: [设计模式（45种）](https://github.com/guanguans/notes/blob/master/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%EF%BC%8845%E7%A7%8D%EF%BC%89.md)

:pushpin: [Design patterns for humans 中文版](https://www.guanguans.cn/design-patterns-for-humans-cn/)