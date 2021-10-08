+++
title = "设计模式 # 创建型 # 构建器模式"
description = "创建型设计模式之一，适用于创建对象由多个步骤组成的类型，防止构造函数无限扩大"
author = "gra55"
categories = ["架构"]
tags = ["设计模式", "创建型设计模式", "构建器模式", "2020"]
date = "2020-01-15"
+++

# 0x00 模式概述

构建器模式用来创建复杂的对象，该复杂对象由多个步骤构建而来。

为了分离这种复杂性，新增一个对象 Builder 来一步一步创建最终的对象，这种创建实例的方式真的是非常优雅，后续如果进行需求变更，需要修改的内容就很少。

# 0x01 场景

创建一个复杂的对象时，基础的对象不会变化，但是其组合会经常变化，此时使用构建器模式就非常有用。

# 0x02 解决方案

```php
// 复杂对象
class Burger
{
    protected $size;

    protected $pepperoni = false;
    protected $lettuce = false;
    protected $tomato = false;

    public function __construct(BurgerBuilder $builder)
    {
        $this->size = $builder->size;
        $this->pepperoni = $builder->pepperoni;
        $this->lettuce = $builder->lettuce;
        $this->tomato = $builder->tomato;
    }
}

// 构造器类
class BurgerBuilder
{
    public $size;

    public $pepperoni = false;
    public $lettuce = false;
    public $tomato = false;

    public function __construct(int $size)
    {
        $this->size = $size;
    }

    public function addPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function addLettuce()
    {
        $this->lettuce = true;
        return $this;
    }

    public function addTomato()
    {
        $this->tomato = true;
        return $this;
    }

    public function build(): Burger
    {
        return new Burger($this);
    }
}

// client
$burger = (new BurgerBuilder(14))
                    ->addLettuce()
                    ->addTomato()
                    ->build();
```

# 0x03 总结

这个模式很简单，简单理解就是适用于**复杂对象的构造函数会经常变更的场景**。

---
参考：

:pushpin: [设计模式（45种）](https://github.com/guanguans/notes/blob/master/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%EF%BC%8845%E7%A7%8D%EF%BC%89.md)

:pushpin: [Design patterns for humans 中文版](https://www.guanguans.cn/design-patterns-for-humans-cn/)