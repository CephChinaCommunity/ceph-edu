
# 一、说明
本章内容主要用于介绍如何构建本地文档开发环境，以及基本markdown语法。

# 二、开发环境构建
## 2.1 工具说明
| 工具名称 | 用途                                             |
|:--------:|--------------------------------------------------|
|   git    | 代码管理工具，本文档基于git管理，使用gitbook展示 |
|   vim    | 编辑器，也可以用其它工具                         |
| gitbook  | 用于文档展示                                     |
|  nodejs  | gitbook运行依赖                                  |
| calibre  | 文档格式转换，可转换为pdf、epub、html等          |

## 2.2 常用命令
| 功能                | 命令                 |
|---------------------|----------------------|
| 安装 GitBook        | npm i gitbook-cli -g |
| 初始化 GitBook 项目 | gitbook init         |
| 安装 GitBook 依赖   | gitbook install      |
| 开启 GitBook 服务   | gitbook serve        |
| 打包 GitBook 项目   | gitbook build        |
| GitBook 命令行查看  | gitbook -help        |
| GitBook 版本查看    | gitbook -V           |

 
## 2.3 工具安装
### 2.3.1 Linux环境
nodejs安装：
```
// 安装node包，也可以安装最新版
# cd /var/opt
# wget https://npm.taobao.org/mirrors/node/latest/node-v12.13.0-linux-x64.tar.gz
# tar xvf node-v12.13.0-linux-x64.tar.gz
# mv node-v12.13.0-linux node

// 将node配置写入环境变量
# tail -n 2 /etc/profile
export NODE_HOME=/var/opt/node
export PATH=$NODE_HOME/bin:$PATH
```


>说明：在Ubuntu20.4版本下，使用gitbook生成pdf文档时，会出现QWebEngineProfile模块报错，安装最新版本Calibre即可。

Calibre最新版本安装：
```
sudo -v && wget -nv -O- \
https://download.calibre-ebook.com/linux-installer.sh | \
sudo sh /dev/stdin
```


### 2.3.2 Windows环境
方法同Linux。


# 三、markdown语法
>参考：
- https://www.jianshu.com/p/q81RER
- https://www.jianshu.com/p/191d1e21f7ed/

## 3.1 常用标记

| 标记符号                                                               | 标记说明             |
|------------------------------------------------------------------------|----------------------|
| \#                                                                     | 标题，几级就有几个\# |
| \*斜体\*                                                               | *斜体*               |
| \*\*粗体\*\*                                                           | **粗体**             |
| \~\~删除线\~\~                                                         | ~~删除线~~           |
| -------                                                                | 分隔线               |
| \-                                                                     | 列表                 |
| \`code\`                                                               | 行内代码             |
| \`\`\`block\`\`\`                                                      | 代码块               |
| \>                                                                     | 引用，可嵌套         |
| \[\!\[哈哈\]\(pics/pic.png "图片描述"\)\]\(http://124.com "链接描述"\) | 插入图片，带链接     |
| \- \[x\] 完成                                                          | - [x] 完成           |
| \- \[ \] 未完成                                                        | - [ ] 未完成         |

## 3.2 特殊字符
特殊字符使用ASCII码进行转义，方法为：&#{字符ASCII十进制表示};

如：

“&#124;”的ASCII码十进制表示为124，那么表示方法为：&#38;&#35;124;

>说明：在Ubuntu下使用ascii命令即可查询所有字符的ASCII码表示。

