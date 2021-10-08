+++
title = "开发 Python 第三方包，上传到 PyPI"
description = "如何开源一个 Python 第三方包"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-07-09"
[[images]]
  src = "img/2019/07/zgr-bwbj.jpeg"
  alt = "张国荣在《霸王别姬》中的幕后照"
  stretch = "horizontal"
+++

## 0x00 初始化 skeleton

开源一个 Python 第三方包，需要配置很多额外的东西（pip 需要的 setup.cfg，CI/CD 需要的 .travis.yml 等），这些东西完全没必要自己来逐一创建。

所以我们需要一个脚手架来快速创建项目的 skeleton。这里我推荐使用 [cookiecutter-X 系列工具](https://github.com/cookiecutter/cookiecutter)。

我这里开发 pypi 包，使用 [cookiecutter-pypackage](https://github.com/audreyr/cookiecutter-pypackage) 模板。

```shell
cookiecutter https://github.com/audreyr/cookiecutter-pypackage.git
```

## 0x01 生态配置

创建完项目 skeleton 以后，需要关联 Travis 账号、ReadTheDocs 账号等。步骤如下：

+ 将 repo 添加到 [GitHub](https://github.com) 中
+ 将该 repo 添加到 [Travis CI](https://travis-ci.org/) 中（需要注册 [Travis](https://travis-ci.org/) 账号）
+ 安装 dev requirement.txt 到虚拟开发环境中：`pip install -r requirements_dev.txt`
+ 注册项目到 [PyPI](https://pypi.org/) 中（需要注册 [PyPI](https://pypi.org/) 账号）
  + 生成 tar&wheel 包：`python setup.py sdist bdist_wheel`
  + 上传包到 [PyPI](https://pypi.org/) 中：`python -m twine upload dist/*`
+ 将该 repo 添加到 [ReadTheDocs](https://readthedocs.org/) 中

## 0x02 迭代开发

+ 更新项目代码，开发新 features
+ 使用 [bumpversion](https://github.com/peritus/bumpversion) 升级版本：`bumpversion --current-version 0.1.0 minor setup.py`
+ 上传包到 [PyPI](https://pypi.org/) 中

