---
layout: post
title: '修改anaconda/conda的路径'
date: 2020-01-16
author: naykun
cover: ''
tags: Tools
---

from [修改anaconda/conda的路径](http://marvin.zone/2018-06-04-change_conda_path/)

假设旧路径为`/home/OLD/anaconda3/`，新路径为`/home/NEW/anaconda3/`

移动anaconda文件到新的路径下

```bash
mv /home/OLD/anaconda3 /home/NEW/anaconda3
```

修改Anaconda的环境变量

(1) 打开`.bashrc` (`vim ~/.bashrc`)

(2) 修改新PATH为 `export PATH="/home/NEW/anaconda3/bin:$PATH"`

修改conda的路径
(1) 打开conda配置文件`vim /home/NEW/anaconda3/bin/conda`

(2) 修改文件头的python路径
从原有的

```bash
#!/home/OLD/anaconda3/bin/python
```

修改为

```bash
#!/home/NEW/anaconda3/bin/python

```

更新一下conda配置

如果不更新，conda命令虽然可以运行，但是会找不到`.condarc`文件，还是会有问题，更新方式为：`conda update conda`
